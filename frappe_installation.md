# Frappe Framework 설치 : Ubuntu 22.04기준

# 1. 필수 프로그램 설치

## 1.1. apt repository 업데이트
```
sudo apt-get update
```

## 1.2. 파이썬 Repo 설치
```
sudo add-apt-repository -y ppa:deadsnakes/ppa
```

## 1.3. 파이썬 설치 3.10
```
sudo apt-get install -y python3.10
```

## 1.4. PIP 설치
```
sudo apt install -y python3-pip
```

## 1.5. 파이썬 가상환경 설치
```
sudo apt install -y python3.10-venv
```

## 1.6. 마리아 DB 설치
```
sudo apt install -y mariadb-server-10.6
sudo apt install -y libmysqlclient-dev
```

## 1.7. 레디스 서버 설치
```
sudo apt install -y redis-server
```

## 1.8. Nodejs 설치
```
curl -s https://deb.nodesource.com/setup_18.x | sudo bash
sudo apt-get install -y nodejs
```
## 1.9. Yarn package관리자 설치
```
curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarnkey.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update && sudo apt-get install yarn
```

## 1.10. nginx 및 supervisor 설치
```
sudo apt install nginx supervisor
```

## 1.11. gunicorn wsgi gateway 인터페이스 서버설치
```
pip install gunicorn
```

## 1.12. PDF 변환용 모듈 설치
```
sudo apt-get install -y xvfb libfontconfig wkhtmltopdf
apt-get install poppler-utils
```

## 1.13. frappe-bench 설치
```
sudo pip install frappe-bench
```

# 2. 프로그램 설정

## 2.1. Maria DB 설정

### 서버 설정
unicode 사용으로 변경

> sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```
[mysqld] 
character-set-client-handshake = FALSE
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
```

> sudo nano /etc/mysql/mariadb.conf.d/50-mysql-clients.cnf
```
default-character-set = utf8mb4
```

### DB 시작
> sudo /etc/init.d/mariadb start 
> sudo mysql_secure_installation
``` 
1. root password : Root 비밀번호 입력
2. unix_socket : N 입력
3. root password : Y or N 선택 입력
4. remove anonymous users : Y 입력
5. disallow root login remotely : Y or N 선택 입력
6. remove test db : Y 입력
7. reload privilege tables : Y 입력
```

### DB 재시작
```
sudo /etc/init.d/mariadb restart
sudo /etc/init.d/mariadb status
```

### DB 비번 변경시 (참고)
```
sudo service mariadb start --skip-grant-tables --skip-networking
sudo mysql -u root
ALTER USER 'root'@'localhost' IDENTIFIED BY '새 비밀번호';
FLUSH PRIVILEGES;
```

## 2.2. frappe-bench 초기화
```
bench init frappe-bench
```

## 2.3 Supervisor 설정

### 설정 링크 생성

```
cd ~/frappe-bench
bench setup supervisor --yes
sudo ln -s `pwd`/config/supervisor.conf /etc/supervisor/conf.d/frappe-bench.conf
```

### 서비스 등록
```
sudo systemctl enable supervisor
```

## 2.4. Nginx 설정

### 설정파일 생성 및 링크 생성
```
cd ~/frappe-bench
bench setup nginx
sudo ln -s `pwd`/config/nginx.conf /etc/nginx/conf.d/frappe-bench.conf
```

### 서비스 등록
```
sudo systemctl reload nginx
```
> 파일 검증 : sudo nginx -t


## 2.5 Redis 설정

> /etc/sysctl.conf
```
vm.overcommit_memory = 1
```


# 3. 앱 생성 및 사이트 등록

## 3.1 새 앱 생성
```
bench new-app 앱명
```

## 3.2 새 사이트 생성
```
bench new-site 사이트명
```

## 3.3 기본 사이트 등록
```
bench use 사이트명
```
## 3.5 사이트에 앱 설치
```
bench --site [사이트명] install-app [설치할 앱이름]
```

## 3.4 개발 모드로 실행 
```
bench start
```

# 5. ODBC 및 FreeDTS 설치
```
sudo apt-get install -y unixodbc unixodbc-dev libodbc1 odbcinst1debian2 tdsodbc
sudo apt-get install -y freetds-bin freetds-common freetds-dev libct4 libsybdb5
```

## 5.1. ODBC 설정

> /etc/odbcinst.ini

```
[ODBC]
Trace=No
TraceFile=/tmp/odbc.log

[MariaDB Unicode]
Driver=libmaodbc.so
Description=MariaDB Connector/ODBC(Unicode)
Threading=0
UsageCount=1

[FreeTDS]
Description=FreeTDS
Driver=/usr/lib/x86_64-linux-gnu/odbc/libtdsodbc.so
UsageCount=1
```

## 5.2 DSN 설정 (사용시)
> /etc/odbc.ini
```
[MSSQL]
Description=MSSQL Connect on FreeTDS
Driver=FreeTDS
Server=[서버 주소]
Port=[서버포트]
Database=[DB명]
Trace=yes
TraceFile=/tmp/mssql.log
UserName=[아이디]
Password=[비밀번호]
```

# 6. 기타

## 6.1 Production Mode 설정
모든 설정이 완료된 상태에서 실행하면 사이트에 대한 nginx 설정파일 갱신되며, site-enabled의 default 링크는 삭제됨
```
sudo bench setup production [사용자]
```

## 6.2 홈 권한 설정
Production 모드 설정 후 Assets 디렉토리에 접근이 안 될 경우 home user 폴더 권한 설정 변경(Others 실행추가)
```
sudo chmod 701 /home/[사용자]
```

## 6.3 sudo 유저 추가
```
sudo usermod -aG sudo [사용자]
sudo passwd [사용자]
```

## 6.4 폰트 설치
레포트를 PDF로 출력할 때 한글 깨짐이 발생하는 경우 한글 폰트 설치
```
font_master.zip 파일에서 /etc/fonts와 /usr/share/fonts 폴더를 변경 할 것

```

## 6.5 Frappe 캐시 삭제
bench console에서 아래 명령어 실행
```
frappe.cache().flushall()
```
