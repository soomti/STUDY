# [Rails] rails-post-crud2

이전 글은 약간의 야매가 존재하는 게시판 만들기었다. 

지금은 html 문법을 써서 http 메서드를 다양하게 활용할 수 없다는 점 

csrf 설정을 안해준 점, 중복 코드가 많다는 점에서 개선할 여지가 많다. 

이걸 개선하는 게시판을 만들어 보자. 

## Routes

##### http method 알고가기

rails 는 restful 한 라우팅의 규격이 존재한다.

![image-20181006021043074](/var/folders/pf/kbm0n5257mnc8h_wqkr67gvw0000gn/T/abnerworks.Typora/image-20181006021043074.png)

##### get

데이터를 url 에 전달하는 방식. get 의 경우 데이터를 전달하면

```ruby
url.com/url?id="1234"&password="2345" 
```

이런 방식으로 데이터가 전달된다. 보통 데이터를 검색하거나 조회할때 사용한다.

##### post

모든 데이터를 전달할 때, get 방식으로 전달하게 된다면 비밀번호와 같은 데이터가 넘어가면 위험하게 된다. 

따라서 데이터를 url 이 아닌 header 값에 넘겨 데이터를 가져가는 방식이다. 보통 데이터베이스에 값을 등록할 경우 값을 사용하는 방식이다.

##### put/patch

post 와 비슷하지만, 글을 새로 등록하는게 아닌, 글을 수정할 경우 사용한다. 

put 의 경우 데이터를 전체적으로 교체하고, patch 는 부분적으로 교체하는 차이가 있다. 

##### delete 

데이터베이스를 삭제하는 경우 사용하는 메서드

 

하지만, 이 전 게시글에서 사용한 라우팅을 확인하면 get 과 post 뿐이다. html 에서 제공하는 `form` 태그는 `get`,`post`  메서드만 인식하기 때문에 http 메소드를 전부적으로 사용할 수 없다. 

따라서 Rails에서 사용하는 폼 헬퍼를 사용해야지 _restful_ 하게 짤 수 있다. 

레일즈에서는 게시판을 통해 

```
resources :posts
```

명령어를 를 통해 CRUD 를 레스트풀하게 제공해주는 규격이 존재한다.

이 명령어는 

```ruby
Rails.application.routes.draw do
  resources :posts
  # get '/posts/ => 'posts#index'
  # get '/posts/new' => 'posts#new'
  # post '/posts' => 'posts#create'
  # get '/posts/:id' => 'posts#show'
  # get '/posts/:id/edit' => 'posts#edit'
  # patch/put 'posts/:id' => 'posts#update'
  # delete '/posts/:id' => 'posts#destroy'
end
```

아래의 주석과 동일하다. 



## routes 확인 

라우트를 위 처럼 변경한 후 어떤식으로 url 이 제공되는지 알아보자.

url 을 확인하는 방법은 터미널 명령어

```ruby
$ rake routes
```

또는

```
http://localhost:3000/rails/info
```

이 주소에서 확인할 수 있다.



이제 이 url 에 맞는!  레일즈 양념을 맞춘 게시판을 만들어보자. 

![image-20181006151106701](/var/folders/pf/kbm0n5257mnc8h_wqkr67gvw0000gn/T/abnerworks.Typora/image-20181006151106701.png)

이렇게 만들어진다! 

이 http 메서드를 사용하기 위해서는 html의 form 을 바꿔야하는데 

이땐 rails 에서 제공하는 view helper 를 사용해야한다. 



## helper 란?

view helper는 레일즈에서 뷰를 쉽게 짜세요~! 라고 제공해주는 기능이다.

a 태그에대한 헬퍼, input 에 대한 헬퍼 등 여러 html 에 대한 태그를 제공해준다. 

이 중 우리가 사용할 폼 헬퍼를 사용해서 헬퍼에 대한 간단한 이해를 해보자.

new.html.erb 에

```erb
<%= form_tag do %>
<% end %>
```

를 써보고 실행 시켜보자. 

이후 페이지 소스보기를 해보면, 

new.html.erb

```erb
<form action="/posts/new" accept-charset="UTF-8" method="post">
    <input name="utf8" type="hidden" value="&#x2713;" />
    <input type="hidden" name="authenticity_token" value="H3zhS3x6r1rL5K9lUUjJFsEnv+PcRjNtm38+zlAbFVETqPLMBTIfEknri0daQgOAqggH2vOQcpCs3HUDpgwAfw==" />
</form>
```

페이지 소스보기를 보면 변경된 것을 확인할 수 있다. 

2줄의 코드가 엄청엄청 길어진 걸 알 수 있다!



## form helper 

폼 헬퍼에서 2개의 input 태그를 만들어주었다 이 태그는 무엇일까?

##### 폼 헬퍼는 자동으로,

- utf-8 을 강제하는 코드 
- csrf 에 대한 보안 기능을 하는 코드.

를 만들어준다. 짱좋음! 

이러한 폼 헬퍼는 위에서 예시한 form_tag 하나가 아닌, form_for, form_with 등 여러가지 종류가 있다.  

폼 또한 검색, 게시글 등록/수정 할때 보내는 목적에 따라 달라지기때문에 레일즈는 여러가지 헬퍼를 제공해준다.

우리는 게시판을 만들것이므로, 가장 적합한   _form_tag_ 가 아닌 _form_for_ 를 사용하여 변경해보자!

## form_for

form_for 는 form_tag 와 달리 모델을 연결시켜 주는 폼 헬퍼이다.

따라서 컨트롤러에서 객체를 보내주어야 한다. 

new.html.erb 와 edit.html.erb 의 form 을 아래처럼 바꿔준다.

##### new.html.erb , edit.html.erb

```erb
<%= form_for(@post) do |f| %>
  <div class="field">
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </div>
  <div class="field">
    <%= f.label :content %><br>
    <%= f.text_area :content %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
```

`@post` 안에 모델 객체를 넣어주면, 알아서 헬퍼에 알맞는 값이 연결된다! 

###### 참고사항

> html form 태그에서, rails form helper 로 바꾸는 경우 parameter 값이 
>
> ```ruby
> Parameters: {"utf8"=>"✓", "authenticity_token"=>"8PUOxE3HtJfGZ5jy5JwJ1kV7ZLDXFDDfzVxBFuwXj/T8IR1DNI8E30RovNDvlsNALlTcifjCcSL6/wrbGgCa2g==", "post"=>{"title"=>"asf", "content"=>"asdf"}, "commit"=>"Update Post", "id"=>"2"}
> ```
>
> 이런식으로 바뀐다. 현재 이 바꾼 부분 때문에 이 상태에서 서버를 돌리면 
>
> 실행해도 data 가 변경되지않는다.
>
> 만약 값을 받아보고 싶다면
>
> controller 에서 받는 `params[:title]` 와 같은 내용들을 전부,
>
>  `params[:posts][:title]`로 바꿔주면 값을 확인할 수 있다.



위에서 변경한 상태라면 현재 파일이 아래와 같을것이다.

new.html.erb

```erb
<h1>게시판</h1>
<p>글을 써주세요</p>
<%= form_for(@post) do |f| %>
  <div class="field">
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </div>
  <div class="field">
    <%= f.label :content %><br>
    <%= f.text_area :content %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
```

edit.html.erb

```erb
<h1>게시판</h1>
<p>글을 수정해주세요</p>
<%= form_for(@post) do |f| %>
  <div class="field">
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </div>
  <div class="field">
    <%= f.label :content %><br>
    <%= f.text_area :content %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
```

이렇게 할 경우 **form**  부분이 중복된다!! 



**view** 에서 코드가 중복될 경우, 중복 코드를 제공하기 위해 **partial page** 를 만들 수 있다.



## Partial page

파셔 페이지는 중복되는 코드를 공통적으로 사용하기 위해 만든 부분 페이지이다. 

##### 파셔 페이지는 무조건 언더바 로 시작해야한다.

_form.html.erb 

```erb
<%= form_for(@post) do |f| %>
  <div class="field">
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </div>
  <div class="field">
    <%= f.label :content %><br>
    <%= f.text_area :content %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
```

로 만든 후, 이 파일을 **render** 메서드를 사용하여 가져올 수 있다. 

가져올 땐 언더바 를 사용하지 않는다! 

```erb
<%= render 'form' %>
```

#### after

이렇게 되면 

##### new.html.erb

```erb
<h1>게시판</h1>
<p>글을 써주세요</p>
<%= render 'form' %>
```

##### edit.html.erb

```erb
<h1>게시판</h1>
<p>글을 수정해주세요</p>
<%= render 'form' %>
```

로 줄여진다!





## link_to

_link_to_ 는 a 태그 관련한 _view helper_ 이다. 

html `a` 태그는 get 방식으로만 보낼 수 있지만, _link_to_ 를 사용하면 _delete_ 메서드를 사용할 수 있다. 

show.html.erb

```erb
...
<a href="/posts/<%=@post.id%>/destroy">게시글 삭제</a>
<a href="/posts/<%=@post.id%>/edit"> 게시글 수정</a>
```

index.html.erb

```erb
...
<a href="/posts/<%=post.id%>"> 게시글 보기 </a>
<a href="/posts/<%=post.id%>/destroy">게시글 삭제</a>
<a href="/posts/<%=post.id%>/edit"> 게시글 수정</a>
<hr />
```

이 코드를 link_to 로 바꿔주자.

```erb
# 'show' 는 버튼에 나올 내용, post 는 rails_url 에서 제공해주는 path 이름이다.
<%= link_to 'Show', post %></td>
# edit_post_path(post) 로 하면, 이 괄호안에는 모델 객체가 들어간다. 
# 이 객체의 id 를 가지고 해당하는 url로 이동한다.
<%= link_to 'Edit', edit_post_path(post) %></td>

<%= link_to 'Destroy', post, method: :delete, data: { confirm: 'Are you sure?' } %>
```

이렇게 변환할 수 있다. 



## Controller 

데이터를 가공하는 컨트롤러를 줄여보자!

#### before

```ruby
class PostsController < ApplicationController
    # view o
    def index
        # 모든 값을 가져온다.
        @posts = Post.all
    end

    def show
        post_id = params[:id] # 중복 
        @post = Post.find(post_id) # 중복
    end
    def edit
        post_id = params[:id] # 중복 
        @post = Post.find(post_id) # 중복
    end
    
    def new
        @post = Post.new
    end

    # view x
    def create
        @post = Post.new
        @post.title = params[:user][:homepage] # 중복
        @post.content = params[:content] # 중복
        @post.save 
        redirect_to "/posts"
    end

    def update 
        post_id = params[:id] # 중복
        @post = Post.find(post_id) # 중복 
        
        @post.title = params[:title] # 중복
        @post.content = params[:content] # 중복
        @post.save
        redirect_to "/posts/#{post_id}"
    end

    def destroy 
        post_id = params[:id] # 중복
        @post = Post.find(post_id) # 중복
        
        @post.destroy
        redirect_to "/posts"
    end  
end
```

저번에 작성했던 코드들 중복되는 코드들에 주석을 달아보았다. 

이걸 줄여보자!



## 중복코드 1

```ruby
post_id = params[:id] # 중복
@post = Post.find(post_id) # 중복
```

이 코드는 _show/edit/update/destroy_ 에서 다 쓰이는 코드다. 

이 때, before_action 메서드를 사용해서 한번에 사용할 수 있다.

## before_action

해당 컨트롤러에 맵핑된 액션을 사용자가 요청했을 경우,

이 메서드가 존재하면, 바로 거치지 않고 여기서 명령한 메서드를 먼저 실행한다.

```ruby
# show/edit/update/destroy 액션 실행 전, :set_post 액션을 실행해라
before_action :set_post, only: [:show, :edit, :update, :destroy]
```

set_post 라는 메서드를 만들어준다. 만들어주자.

외부에서 접근할 수 없도록 위에 `private` 를 명시했다. 이 아래에 생성한 메서드들은 외부에서 접근이 불가능하다.

##### private 는 언제 끝나나요?

이 private 밑에 다른 설정을 하지 않으면 `private` 가 지속된다. `public` 또는 `protected` 의 설정도 있다. 

```ruby
private
def set_post
  @post = Post.find(params[:id])
end
```

이렇게 설정을 하면 위 코드가 아래처럼 바뀌게 된다.

## 중복코드 2

```ruby
@post.title = params[:title] # 중복
@post.content = params[:content] # 중복
```

_create/update_ 에서 중복되는 코드들이다. 한번에 처리해보자

## post_params

private 아래에 코드를 작성해준다. 

```ruby
def post_params
  # 사용자가 전달해 주는 값 중 post 에 대한 params 만 가져오고, 그 중 title,content 의 값만 허가한다.
  params.require(:post).permit(:title, :content)
end
```

이렇게 하는 이유는 중복을 줄여줄 뿐만 아니라, 만약 사용자가 이상한 값들 추가로 넣어서 보내도 이를 방지 할 수 있다!

이렇게 고치면, 아래의 코드처럼 깔끔해진다!



```ruby
class PostsController < ApplicationController
    before_action :set_post, only: [:show, :edit, :update, :destroy]
    # view o
    def index
        @posts = Post.all
    end
    
    def show
    end
    
    def edit
    end   
    
    def new
        @post = Post.new
    end
    # view x
    def create
        @post = Post.new(post_params)
        @post.save 
        redirect_to "/posts"
    end

    def update         
        @post.update(post_params)
        redirect_to "/posts/#{post_id}"
    end

    def destroy        
        @post.destroy
        redirect_to "/posts"
    end 
    
    private
    def set_post
      @post = Post.find(params[:id])
    end  
    def post_params
      params.require(:post).permit(:title, :content)
    end
end
```



## redirect_to 변경해주기

리디렉션을 레일즈에서 제공하는 path 를 사용하여 만들어준다. 

##### create / update

new 와 edit 의 경우, 등록 시 상세 보기 페이지로 이동한다. 

이때 저장한 객체 리디렉션 시켜주면 레일즈가 알아서 상세보기로 변환시켜준다.

```ruby
redirect_to @post
```

##### delete

삭제한 경우, 메인 url 로 보내기 때문에

```ruby
redirect_to posts_url
```

로 보내준다.



