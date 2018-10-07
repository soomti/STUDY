# [Rails] rails-post-crud2

이전 글에서 언급한대로, 

지금은 html 문법을 써서 http 메서드를 사용할 수 없다는점 

csrf 설정을 안해준 점, 

중복코드 등의 문제점등을 개선하기 위해서

만든 게시판에 레일즈 양념을 뿌려보자!

## Routes

##### http method 알고가기

rails 는 restful 한 라우팅의 규격이 존재한다.

![image-20181006021043074](/var/folders/pf/kbm0n5257mnc8h_wqkr67gvw0000gn/T/abnerworks.Typora/image-20181006021043074.png)

하지만, 이 전 게시글에서 사용한 라우팅을 확인하면 get 과 post 뿐이다. 

이유는 html 에서 제공하는 `form` 태그는 `get`,`post`  메서드만 인식하기 때문에, 나머지는 뭘 해줘도 get/post 로 간다.

그래서 Rails에서 사용하는 폼 헬퍼를 사용해야지 _restful_ 하게 짤 수 있다. 

우리는 이 _restful_ 한 방식을 사용하기 위해 routes.rb 를 

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

로 바꾸면

## 변한 routes 확인 

![image-20181006151106701](/var/folders/pf/kbm0n5257mnc8h_wqkr67gvw0000gn/T/abnerworks.Typora/image-20181006151106701.png)

이렇게 만들어진다! 

url 과 path 를 토대로 기본 레일즈 양념을 쳐보자 

## form helper 란?

폼 헬퍼를 일단 알아보면

new.html.erb 에

```erb
<%= form_tag do %>
<% end %>
```

를 써보고 실행 시켜보자. 

new.html.erb

```erb
<form action="/posts/new" accept-charset="UTF-8" method="post">
    <input name="utf8" type="hidden" value="&#x2713;" />
    <input type="hidden" name="authenticity_token" value="H3zhS3x6r1rL5K9lUUjJFsEnv+PcRjNtm38+zlAbFVETqPLMBTIfEknri0daQgOAqggH2vOQcpCs3HUDpgwAfw==" />
</form>
```

페이지 소스보기를 보면 변경된 것을 확인할 수 있다.

##### 폼 헬퍼는 자동으로,

- utf-8 을 강제하는 코드 
- csrf 에 대한 보안 기능을 하는 코드.

를 만들어준다. 

html 코드를 폼 헬퍼로 바꿔주자!

폼 헬퍼는 여러가지가 존재하는데 _form_tag_ 가 아닌 _form_for_ 를 사용하여 변경하고자 한다. 

## form_for

form_for 는 form_tag 와 달리 모델을 연결시켜 주는 폼 헬퍼이다.

따라서 컨트롤러에서 객체를 보내주어야 한다. 

##### form for helper

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

##### 따라서 _new,edit_ 을 위 코드로 바꿔준다.

###### 참고사항

> 이때 이렇게 html 코드에서 폼 헬퍼로 바꾸게 되면 parameter 값이 
>
> ```ruby
> Parameters: {"utf8"=>"✓", "authenticity_token"=>"8PUOxE3HtJfGZ5jy5JwJ1kV7ZLDXFDDfzVxBFuwXj/T8IR1DNI8E30RovNDvlsNALlTcifjCcSL6/wrbGgCa2g==", "post"=>{"title"=>"asf", "content"=>"asdf"}, "commit"=>"Update Post", "id"=>"2"}
> ```
>
> 이런식으로 바뀐다. 현재 이 바꾼 부분 때문에 실행해도 data 가 넘어가지 않으므로 주의해주고
>
> 만약 값을 받아보고 싶다면
>
> controller 에서 받는 `params[:title]` 와 같은 내용들을 전부,
>
>  `params[:posts][:title]`로 바꿔주면 값을 확인할 수 있다.



## form 변경 후 

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

**view** 에서 코드가 중복될 경우를 방지하기 위해  partial page 를 만들 수 있다



## Partial page

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

기본적으로 `a` 태그는 `get` 방식으로만 요청을 보낼 수 있다. 이에 

```erb
<%= link_to 'Show', post %></td>
<%= link_to 'Edit', edit_post_path(post) %></td>
<%= link_to 'Destroy', post, method: :delete, data: { confirm: 'Are you sure?' } %>
```

이렇게 변환할 수 있다. 



## Controller 

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

저번에 작성했던 코드들 중복되는 코드들이 많아보인다! 이걸 줄여보자



## Controller - 중복 코드 줄이기

프로그래밍은 중복 코드를 줄이는게 생명이다.  

코드를 없애보자!!!

#### 중복코드 1

```ruby
post_id = params[:id] # 중복
@post = Post.find(post_id) # 중복
```

이 코드는 _show/edit/update/destroy_ 에서 다 쓰이는 코드다. 

이 때, before_action 메서드를 사용해서 한번에 사용할 수 있다.

##### before_action

해당 컨트롤러에 맵핑된 액션을 사용자가 요청했을 경우,

이 메서드가 존재하면, 바로 거치지 않고 여기서 명령한 메서드를 먼저 실행한다.

```ruby
# show/edit/update/destroy 액션 실행 전, :set_post 액션을 실행해라
before_action :set_post, only: [:show, :edit, :update, :destroy]
```

set_post 를 만들어주자.

외부에서 접근할 수 없도록 위에 `private` 를 명시했다. 이 아래에 생성한 메서드들은 외부에서 접근이 불가능하다

```ruby
private
def set_post
  @post = Post.find(params[:id])
end
```

이렇게 설정을 하면 위 코드가 아래처럼 바뀌게 된다.

### 중복코드 2

```ruby
@post.title = params[:title] # 중복
@post.content = params[:content] # 중복
```

_create/update_ 에서 중복되는 코드들이다. 한번에 처리해보자

##### post_params

private 아래에 코드를 작성해준다. 

```ruby
def post_params
  # 사용자가 전달해 주는 값 중 post 에 대한 params 만 가져오고, 그 중 title,content 의 값만 허가한다.
  params.require(:post).permit(:title, :content)
end
```

이렇게 하는 이유는 중복을 줄여줄 뿐만 아니라, 만약 사용자가 이상한 값들 추가로 넣어서 보내도 이를 방지 할 수 있다!



#### after

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

깔끔해졌다! 



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

레일즈 양념화 끝!̆



## Json 으로 만들어주기

레일즈는 jbuilder 를 사용하는게 가장 간편하다!

view 파일이 생기는 단점이 있지만, 편하니까 알아보자

_post.json.jbuilder

```ruby
json.extract! post, :id, :title, :content, :created_at, :updated_at
json.url post_url(post, format: :json)
```

index.json.jbuilder

```ruby
json.array! @posts, partial: 'posts/post', as: :post
```

show.json.jbuilder

```ruby
json.partial! "posts/post", post: @post
```



##### controller

```ruby
 def create
    @post = Post.new(post_params)

    respond_to do |format|
      if @post.save
        format.html { redirect_to @post, notice: 'Post was successfully created.' }
        format.json { render :show, status: :created, location: @post }
      else
        format.html { render :new }
        format.json { render json: @post.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /posts/1
  # PATCH/PUT /posts/1.json
  def update
    respond_to do |format|
      if @post.update(post_params)
        format.html { redirect_to @post, notice: 'Post was successfully updated.' }
        format.json { render :show, status: :ok, location: @post }
      else
        format.html { render :edit }
        format.json { render json: @post.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /posts/1
  # DELETE /posts/1.json
  def destroy
    @post.destroy
    respond_to do |format|
      format.html { redirect_to posts_url, notice: 'Post was successfully destroyed.' }
      format.json { head :no_content }
    end
  end
```

