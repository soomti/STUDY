# [view] page title 설정하기

웹 사이트의 title 은 페이지 별로 다른 title 을 가지고 있어야지 네이버 웹 마스터 도구에서 잘 설정 가능한듯

application_helper.rb

```ruby
def title(page_title)
  content_for(:title) { page_title }
end
```

application.html.erb

```erb
<title><%= content_for?(:title) ? content_for(:title) : "Default Title" %></title>
```

title.html.erb

```erb
<% title "Reading #{@post.name}" %>
```



