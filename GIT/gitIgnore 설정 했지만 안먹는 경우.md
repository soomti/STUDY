# gitignore 설정 했지만 안먹는 경우

`.gitignore` 에 파일을 설정했지만, 잘 안먹는 경우가 존재한다. 

git 에서 사용하는 cache 파일에 기록이 변경되지 않는 경우 때문에 아래의 명령어를 사용해본다.

```bash
# 깃캐시에 있는 파일 제거 
$ git rm --cached filename

# 모든 파일을 삭제할 경우에는 `.` 사용 
git rm -r --cached .
```

git 설정하기

```bash
git add .
git commit -m ".gitignore is now working
```

