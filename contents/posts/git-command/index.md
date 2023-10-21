---
title: Git 기본 사용법
description: "Git 사용법"
date: 2023-10-21
update: 2023-10-21
tags:
  - git
series: "기본 사용법"
---

## Git 기본 명령어
- 기본 용어 설명
> Git에서는 아래 3개의 Tree(트리) 데이터를 이용하여 버전 관리를 수행 함
<br/> HEAD(헤드) : 마지막 커밋 스냅 샷, 다음 커밋의 부모 커밋
<br/> Index(인덱스) : 다음에 커밋 할 스냅 샷, Staging Area에 저장.
<br/> Working Directory(작업 디렉토리) : 샌드박스
<br/> Git Directory : Git 로컬 저장소, Staging Area와 커밋 데이터의 영구 저장공간

```
git init
```
폴더에 Git Directory를 생성, .git 폴더가 생성됨

```
git status
```
git의 저장소 트리 상태에 대한 비교 정보를 출력, 작업 디렉토리와 인덱스, 인덱스와 헤드를 비교하여 출력함.
<br> 작업 디렉토리와 인덱스와의 차이는 빨간색으로, 인덱스와 헤드와의 차이는 녹색으로 표시됨

```
.gitignore
```
현재 작업 디렉토리에서 관리할 필요가 없는 파일 혹은 폴더 정보를 설정, 해당 파일에 포함된 대상은 버전 관리에서 제외됨
(static 파일, node 등 library 공통모듈 등)

```
git add -A or git add .
```
작업 디렉토리의 변경 내용으로 인덱스 트리에 반영(스냅 샷)

```
git commit
```
인덱스 트리에 등록된 내용을 저장소에 영구적으로 저장하고 헤드가 해당 스냅샷을 가르키게 됨.
> -m "코멘트" : 텍스트 편집기를 사용하지 않고 커밋 코멘트 추가
<br/> --amend : 마지막 커밋 내용에 현재 수정 사항 반영

```
git reset --mixed HEAD~ or [hash값]
```
헤드가 가리키는 커밋 포인터를 이동 시킨 후(롤백), 인덱스의 스냅 샷을 헤드가 가리키는 커밋으로 업데이트 함. git commit과 git add 명령을 롤백 
> --mixed : 플래그가 없는 경우 기본 동작
<br/> --soft : 헤드의 포인터만 이동시키고 인덱스 스냅샷을 업데이트 하지는 않음
<br/> --hard : 워킹 디렉토리의 내용까지 업데이트, 커밋하지 않은 모든 내용 삭제됨 (주의~)
<br/> HEAD^ : 최신 커밋, HEAD~n : n번째 커밋으로 롤백
<br/> hash값 : 해시 값에 해당하는 특정 커밋으로 롤백

```
git revert hash
```
특정 커밋으로 되돌리는 명령어, 커밋 취소에 대한 이력이 추가 됨, 원격 저장소에 커밋 후 푸시된 내용을 취소시 협업 중인 경우 revert로 실행. 로컬에서만 취소하는 경우 reset 사용

```
git log
```
커밋 관련 로그 표시, 현재 HEAD와 브랜 커밋 정보 표시
> -p : 수정된 전체 내용 확인

```
git diff
```
두 트리 개체의 차이 비교
> 

```
git rebase -i HEAD~n # HEAD 에서부터 n 번째 커밋 수정
```
여러개의 커밋 메시지 수정


## 브랜치 관리

```
git branch
```
로컬 브랜치 정보 출력, 브랜치 이름 앞에 * 표시된 브랜치가 checkout된 브랜치 임
> -r : 원격 브랜치 정보 확인
<br/> -a : 로컬 및 원격 브랜치 전체 확인 

```
git branch [branch명]
```
새로운 브랜치 생성

```
git branch -d [branch명]
```
해당 브랜치 삭제
> -D : 강제 삭제 플래그, 병합하지 않은 브랜치도 삭제 가능

```
git checkout [branch명]
```
현재 작업 폴더를 해당 브랜치로 전환
> -b : 브랜치 생성과 함께 해당 브랜치로 전환
<br/> -t origin/[브랜치명] : 원격 브랜치를 가져와서 로컬 브랜치를 생성후 전환

```
git merge [branch명]
```
지정한 브랜치와 현재 브랜치를 병합함


## 임시 저장

```
git stash
```
현재 작업 중인 변경사항을 커밋하지 않고 저장
```
git stash list
```
stash 했던 목록을 표시함
```
git stash drop [stash명]
```
저장한 stash를 삭제, 목록에 표시된 stash 중에 삭제 할 stash를 지정 


## 원격 저장소 관련

- 기본 용어 설명

> Remote branch : 원격 저장소의 브랜치 
<br/> Remote Tracking breanch : 원격 저장소의 브랜치를 참조하는 원격 브랜치의 복사본, 로컬 저장소에 위치 (origin/main)
<br/> Tracking branch : 로컬 브랜치중에 원격 브랜치를 추적하는 브랜치

```
git remote add origin https://github.com/[계정정보]/[저장소명]
```
원격 저장소를 설치하고 설치된 정보로 위 명령어 실행

```
git remote -v
```
현재 설정된 원격 저장소 정보 확인

```
git branch --set-upstream-to=[원격트래킹 브랜치]
git branch -u [원격트래킹 브랜치]
```
현재 브랜치를 원격 트래킹 브랜치와 매핑 함.

```
git push or git push origin [저장소명]
```

원격 저장소를 현재 브랜치로 업데이트 함.
>-f : 강제 업데이트

```
git push origin :[브랜치명]
```

원격 브랜치 삭제

```
git remote update
```
모든 원격 저장소의 원격 참조 브랜치를 업데이트함. 
> -p : 참조되지 않은 사항 삭제

```
git fetch
```
현재 로컬에 위치한 브랜치가 참조하는 원격 저장소의 모든 원격 참조 브랜치 정보를 업데이트 함.
> -p : 참조되지 않은 사항 삭제 
<br/> --all : 모든 원격 저장소의 브랜치 정보를 업데이트
<br/> * 원격 저장소에 있는 브랜치를 로컬에도 자동 생성, 단, 커밋 내용은 병합하지 않음

```
git pull
```
원격 저장소의 내용을 업데이트하고 로컬 저장소에 변동 사항을 병합함, git fetch + git merge

```
git clone [원격저장소주소] [저장위치]
```
원격 저장소를 로컬에 복제