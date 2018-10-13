# Ruby On Rails 설치 가이드 - Window10

```
레일즈 가이드에서는, 윈도우는 Linux 가상 머신에 레일즈를 설치하고 사용하는 것을 권장하고 있다.
다행히 Windows10부터는 리눅스 운영체제의 bash 환경을 사용할 수 있도록
Windows Subsystem for Linux(WSL로 불림)를 지원한다!
```

## Windows Subsystem for Linux(WSL) 설치

WSL? 

```
Window 운영 체제에서 유닉스 명령어 체계를 사용할 수 있게 만들어주는 환경.
가을 크리에이터 업데이트 이후부터 많은 안정화가 이루어져있기 때문에, Windows10 을 가을 크리에이터 버전까지 업데이트를 해야한다. 
```

> 안정화를 위해,  **1709(2017년 9월에 배포된 가을 크리에이터 업데이트) 또는 162xx 빌드 이상**이어야 한다.



설정 - 업데이트 및 보안 - 개발자용

**개발자모드** 선택!

1. 제어판 > 모든 제어판 항목 > 프로그램 및 기능 > Windows 기능 켜기/끄기

   **Linux용 Windows 하위 시스템을 클릭** 하여 설치 후 재부팅을 해준다.

   이후 윈도우에 bash 검색하면 해당 값이 나온다.

   

2. 윈도우 스토어에서 우분투를 설치한다.

   실행이 완료되면 바로 사용을 위한 계정 생성 프롬프트를 볼 수 있는데

   원하는 user name과 passwd를 설정하면 바로 사용이 가능하다.

   

3. 시작 메뉴에서 ‘bash’ 타이핑 후 실행.



linux 환경을 만들었으면 ruby 를 설치한다. 



## Ruby 설치

##### 루비에 필요한 종속 코드 설치 

```bash
sudo apt-get update
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev
```


##### rbenv 를 통한 루비 설치

`rbenv` 는 루비 버전별로 실행환경을 관리하는 툴이다.

 `rvm` 이라는 다른 관리 툴도 있지만, rails 에서는 `rbenv`  를 권장하고 있다.

```bash
$ cd
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
$ echo 'eval "$(rbenv init -)"' >> ~/.bashrc
$ exec $SHELL

$ git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
$ echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
$ exec $SHELL

$ rbenv install 2.4.4
$ rbenv global 2.4.4
$ ruby -v
```

`ruby 2.4.4`가  잘 출력되면 잘 설치 된 것이다.

##### bundler 설치

```bash
$ gem install bundler
```


## Rails 설치

```bash
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ sudo apt-get install -y nodejs
$ gem install rails -v 4.2.10
$ rbenv rehash
```
설치 확인

```bash
$ rails -v
# Rails 4.2.10
```

`Rails 4.2.10` 이 잘 출력되면 성공!



## 프로젝트 생성

```bash
$ cd mnt/c
$ mkdir -p code
$ rails new myapp # c 에 프로젝트 생성됨.
```