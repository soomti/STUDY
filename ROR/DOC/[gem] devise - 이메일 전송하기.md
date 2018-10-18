# devise - 이메일 전송하기

레일즈를 이용해서 로그인을 만들때 가장 유용한 gem `devise ` 에 대한 심화! 

이메일 보내는걸 만들어보자! 



## mailgun을 이용한 비밀번호 찾기 

디바이스에서 제공해주는 비밀번호 찾기는 이메일로 토큰을 보낸 후, 그 이메일에서 `url` 을 전달하는 방식을 사용한다.

```erb
<%- if devise_mapping.recoverable? && controller_name != 'passwords' && controller_name != 'registrations' %>
  <%= link_to "비밀번호 찾기", new_password_path(resource_name) %>
<% end -%>
```

이런 메서드를 제공해 주는데, new_password_path 를 가보면

```erb
<h2>Forgot your password?</h2>

<%= form_for(resource, as: resource_name, url: password_path(resource_name), html: { method: :post }) do |f| %>
  <%= devise_error_messages! %>

  <div class="field">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true, autocomplete: "email" %>
  </div>

  <div class="actions">
    <%= f.submit "Send me reset password instructions" %>
  </div>
<% end %>

<%= render "devise/shared/links" %>
```

이렇게 이메일, 전송하는 페이지를 제공해준다. 그냥 누르면  

host  가 정확하지 않다는 메세지가 나온! 프로젝트 자체에 이 연결을 해주는건 직접 해야한다! 

가장 쉽게 이메일 전송하는 방법은 `mailgun` 을 사용하는 방식이다.



## mailgun 을 사용한 이메일 보내기

##### gem file 설치

```ruby
gem 'mailgun_rails'
```

https://app.mailgun.com/

에 가입 후 새로운 도메인을 만들어준다. 만약 테스트 계정일경우에는, sandbox 를 기본적으로 하나 제공해주는데 그걸로 테스트를 해도 무방하다. 하지만 테스트 계정일 경우 _Authorized Recipients_ 를 추가한 값만 메일이 보내지니 주의하자! 

##### host 설정

이메일을 보내기 위해 development.rb 에 host 설정을 해주어야 한다. 

mailgun 에서 키를 받으면 해당 `api key`와 `domain` 을 제공하는데 그 값을 넣어주면 된다. 

`Rails.application.credentials.mailgun_api_key` 는 rails 5.2 이상에서 제공해주는 걸 쓴거고,

`figaro gem`  또는, rails 4 에서 제공하는 `secret.yml` 을 사용해도된다!

development 모드기 때문에 `http://localhost:3000` 을 사용해 줬지만, 프로덕션에서는 실제 url 을 사용해야 함을 생각하자

```ruby
config.action_mailer.default_url_options = { :host => "http://localhost:3000/" }
config.action_mailer.delivery_method = :mailgun
config.action_mailer.mailgun_settings = {
  api_key: Rails.application.credentials.mailgun_api_key,
  domain: Rails.application.credentials.mailgun_domain,
}
```

##### devise.rb

어떤 메일로 보낼지 설정을 해준다.

```ruby
  config.mailer_sender = 'openull.ssomee@gmail.com'
```

