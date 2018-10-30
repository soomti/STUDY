# [Rails] rails-first-api

##### 1. 프로젝트 생성

```bash
$ rails new rails-first-api
```

##### 2. 게시판 생성

````bash
$ rails g controller api/v1/posts title content
````

posts_controller.rb

```ruby
class Api::V1::PostsController < ApplicationController
  def index
    @posts = Post.all
  end
end
```



##### 3. routes 설정

routes.rb 에 추가해야함. api 는 다른 routes를 타기 때문에 namespace 를 걸어줘 설정을 해준다.

```ruby
  # api
  namespace :api do
    namespace :v1 do
      resources :posts
    end
  end
```

##### 4. view json 추가

index.json.jbuilder

```ruby
json.array! @posts.each do |p|
    json.id p.id
    json.title p.title 
    json.content p.content
    json.created_at p.created_at.to_s[5..9].gsub("-",".")
    json.user_nickname p.user.nickname
    json.comment_count p.comments.count
    if current_user
        bookmark = Bookmark.where(user_id: current_user.id,post_id: p.id).exists?
        json.bookmark bookmark
    end
end
```

##### 5. api_controller 추가

```ruby
$ rails g controller api/v1/Api
```

api/v1/api_controller.rb

```ruby
class Api::V1::ApiController < ApplicationController
    before_action :set_default_format

    private 
    def set_default_format
        request.format = :json
    end
end
```

##### 6. posts_controller 상속 변경

```ruby
class Api::V1::PostsController < ApiController
  def index
    @posts = Post.all
  end
end
```

