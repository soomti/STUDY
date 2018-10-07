## [Rails] rails-post-crud-1

간단하게 제목, 내용이 들어간 게시판을 만들어 보자.



## 프로젝트 생성

bash

```ruby
$ rails new mypost
```



## Model

#### 모델 생성하기

모델을 생성해주기 위해 사용하는 명령어이다.

bash

```ruby
# rails generate model [modelname] 
# rails에 modelname 이름을 가진 일반적인 모델 파일을 생성해 주세요.
$ rails g model post
```

레일즈의 모델 이름 생성 규칙은 단수여야 한다. 

##### 생성 결과

```ruby
Running via Spring preloader in process 13454
      invoke  active_record
      create    db/migrate/20181005155917_create_posts.rb
      create    app/models/post.rb
      invoke    test_unit
      create      test/models/post_test.rb
      create      test/fixtures/posts.yml
```

위 명령을 실행하면 여러 파일이 만들어준다. 

###### 파일 알아보기 

> db/migrate/20181005155917_create_posts.rb
>
> 마이그레이션 파일은 데이터베이스 스키마를 변경해 주는 파일이다.
>
> 레일즈는 편리하게  _sql_ 문을 사용하지 않아도 스키마 설정을 할 수 있는데,
>
>  _migration_ 폴더 내에 스키마에 대한 변경/추가에 대한 작업을 해주는 파일을 만들어 준다
>
> 이후 ` rails db:migration` 명령어를 사용하면, 그에 대한 작업이 완료된다. 
>
> 하나의 파일은 스키마 변경 작업에 대한 하나의 버전이 된다. 
>
> app/models/post.rb
>
> 만들어진 모델에 대한 관계, 유효성에 관련된 내용들을 명시해 두는 파일이다.

#### 모델 컬럼 추가하기

새로 만들어진 마이그레이션 파일에 컬럼을 생성해준다

```ruby
# Posts 란 모델을 생성해준다
class CreatePosts < ActiveRecord::Migration[5.2]
  # 변경 작업
  def change
    # :posts 라는 모델에 여러 테이블을 생성한다.
    create_table :posts do |t|
      # type :name 
      # string 타입의 title 컬럼을 생성한다
      t.string :title
      # text 타입의 content 컬럼을 생성한다
      t.text :content
      t.timestamps
    end
  end
end
```

#### 마이그레이션 파일 적용하기

아래 명령어를 통해 적용 시켜준다.

```ruby
$ rake db:migrate
```

한번 마이그레이션 작업을 진행한 파일 하나의 버전이기 때문에 

여기서 컬럼을 추가하거나, 삭제하고 싶은 경우 다른 파일을 생성해서 만들어준다.



## controller 생성하기

컨트롤러는 여러 기능을 모아둔다. 

하나의 컨트롤러에서 여러 기능을 처리할 수 있다. 하지만 100개가 넘어가면 찾기도 힘들고 뒤죽박죽이기 때문에

공통적인 기능들을 묶어 각각의 컨트롤러로 분리한다. 

#### 컨트롤러 생성하기

게시판에 대한 기능을 하는 컨트롤러를 생성해주기 위한 명령어를 사용해보자.

bash

```ruby
# posts 라는 이름을 가진 일반적인 컨트롤러를 생성해주세요.
$ rails g controller posts
```

이 명령어를 사용하면, 컨트롤러 안에 **액션 메서드**를 만들 수 있다.



#### **액션 메서드**

컨트롤러 안에 넣는 여러 작업 기능 단위이다.

사용자가 요청을 하면, 요청에 대한 _컨트롤러#액션_ 을 찾아 코드 내용이 실행한 후

그 이후에 해당 액션과 연결된 뷰 파일로 이동한다. 

```ruby
class PostsController < ApplicationController
    # view o
    # 액션 메서드
    def index
    end

    def show
    end

    def new
    end

    def edit
    end

    # view x
    def create
    end

    def update 
    end

    def destroy 
    end  
end
```

위와 같은 경우,

 **글목록, 등록창, 수정창, 상세보기**는 사용자가 직접 보는 페이지이나,

사용자가 입력한 글에 대한 _create,update,delete_ 는 view 가 필요없는 작업이다. 

나중에 뷰가 없으면 에러가 나므로, 작업이 끝난 이후에는 다른 액션으로 리다이렉션 시켜줘야한다. 



이제, 액션 메서드 중 뷰가 필요한 메서드만 뷰를 만들어주자!

## view 생성하기

뷰는 사용자가 보는 화면이다. 액션을 실행한 후 연결된 뷰가 있다면 보여준다.

게시판에 대한 **글목록 보기, 등록 폼, 수정 폼, 상세 폼** 은 

사용자가 직접 보는 페이지 이므로 뷰를 만들어 주어야 한다. 

##### views/posts/index.html.erb

```erb
<h1>글 목록 보기</h1>
<a href="/posts/new">게시글 작성하기</a>
<hr>
<% @posts.each do |post| %>
<p>
    <%= post.id %>
</p>
<p>
    <%= post.title %>
</p>
<p>
    <%= post.content %>
</p>
<p>
    <%= post.created_at %>
</p>
<p>
    <%= post.updated_at %>
</p>
<a href="/posts/<%=post.id%>"> 게시글 보기 </a>
<a href="/posts/<%=post.id%>/destroy">게시글 삭제</a>

<a href="/posts/<%=post.id%>/edit"> 게시글 수정</a>
<hr />
<% end %>
```

글 목록을 보여주는 뷰이다. 컨트롤러에 `@post = Post.all`  로 가져온 값을 반복문을 통해 뿌려준다.

`a` 태크는 다른 페이지로 이동하게 만들어주는 html 태그이다. _get_ 방식으로 `href` 에 정의된 주소를 통해 url 을 보내준다.

##### views/posts/new.html.erb

```erb
<h1>게시판</h1>
<p>글을 써주세요</p>
<form action="/posts" method="post">
    제목 : 
    <input type="text" name="title"/> <br/> 
    내용 : 
    <textarea name="content"></textarea> <br/> 
    <input type="submit" value="글 등록하기" />
</form>
```

게시글 등록을 위한 폼 페이지이다.

`form` 은 다른 _url_로 현재 입력받은 데이터를 넘겨주는 태그이다.

 `form` 안에 `input` 태그의 데이터를 넘겨주는데, `name` 속성을 통해 넘겨준다.

##### views/posts/edit.html.erb

```erb
<h1>게시판</h1>
<p>글을 수정해주세요</p>
<form action="/posts/<%=@post.id%>" method="post">
    <!-- <input type="hidden" name="id" value="<%# @post.id %>"> -->
    <input type="text" name="title" value="<%=@post.title%>" />
    <br /> 
    내용 :
    <textarea name="content"><%=@post.content%></textarea><br />
    <input type="submit" value="게시글 수정하기 " />
</form>
```

##### views/posts/show.html.erb

```erb
<p>
  <%= @post.id %> 번째 게시글</p>
<p>제목 :
  <%= @post.title %>
</p>
<p>내용 :
   <%= @post.content %>
</p>
<a href="/posts/<%=@post.id%>/destroy">게시글 삭제</a>
<a href="/posts/<%=@post.id%>/edit"> 게시글 수정</a>
```



## Routes.rb 작성

사용자가 어떤 동작을 했을때, 

어떤 기능(액션메서드)을 시행할지 맵핑해주는 파일이다.

```ruby
Rails.application.routes.draw do
  root 'posts#index'
  get 'posts' => 'posts#index'
  get 'posts/new' => 'posts#new'
  get 'posts/:id/edit' => 'posts#edit'
  get 'posts/:id' => 'posts#show'
  post 'posts' => 'posts#create'
  post 'posts/:id' => 'posts#update'
  get 'posts/:id/destroy' => 'posts#destroy'
end
```

http method _get, post, put, patch_ 가 존재하지만, 

html form 태그에서는 _get/post_ 만 제공해주기 때문에, 위처럼 사용하였다.

이후 변경이 필요하다.

## controller 기능 추가

위에 내용들을 토대로 액션메서드에 기능을 만들어보자

```ruby
class PostsController < ApplicationController
    # view o
    def index
        # 모든 값을 가져온다.
        @posts = Post.all
    end

    def show
        post_id = params[:id]
        @post = Post.find(post_id)
    end

    def new
        @post = Post.new
    end

    def edit
        post_id = params[:id]
        @post = Post.find(post_id)
    end

    # view x
    def create
        @post = Post.new
        @post.title = params[:title]
        @post.content = params[:content]
        @post.save 
        redirect_to "/posts"
    end

    def update 
        post_id = params[:id]
        @post = Post.find(post_id)
        @post.title = params[:title]
        @post.content = params[:content]
        @post.save
        redirect_to "/posts/#{post_id}"
    end

    def destroy 
        post_id = params[:id]
        @post = Post.find(post_id)
        @post.destroy
        redirect_to "/posts"
    end  
end
```

하지만 _create,update,destroy_ 들의 경우  뷰가 없기 때문에 에러가난다.

따라서 `redirect_to` 를 명시해준다. 'get' 메서드로 간다.



## application.rb

현재 이 게시판은 csrf 적용을 안 해놓은 상태이기 때문에 에러가 발생한다.

이 에러를 없애기 위해 아래 코드를 주석처리한다.

```ruby
class ApplicationController < ActionController::Base
  # Prevent CSRF attacks by raising an exception.
  # For APIs, you may want to use :null_session instead.
  # protect_from_forgery with: :exception
end
```

이렇게 작성을 하고 나면 rails 를 사용한 하나의 게시판이 돌아가게 된다!

이후 레일즈 스럽게 만들어 보면서 바꿔보자!