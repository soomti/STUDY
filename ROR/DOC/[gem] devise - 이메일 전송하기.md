# devise - 이메일 전송하기

레일즈를 이용해서 로그인을 만들때 가장 유용한 gem `devise ` 에 대한 심화! 

패스워드 찾기 - 이메일을 연결해보자 



## password 찾기 

디바이스에서 제공해주는 비밀번호 찾기는 이메일로 토큰을 보낸 후, 그 이메일에서 `url` 을 전달하는 방식을 사용한다.

```erb
<%- if devise_mapping.recoverable? && controller_name != 'passwords' && controller_name != 'registrations' %>
  <%= link_to "비밀번호 찾기", new_password_path(resource_name) %>
<% end -%>
```

이런 메서드를 제공해 주는데, _new_password_path_ 파일을 가보면

##### password/new.html.erb

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

이 나오고, 클릭시**_host 가 정확하지 않다_ ** 와 같은 에러 페이지가 뜬다.

디바이스에서 많이 제공해주지만, 이메일 연동을 직접 해주어야 한다. 

가장 쉽게 이메일 전송하는 방법은 `mailgun` 을 사용하는 방식이기 때문에 mailgun 을 사용해보자.



## mailgun 설정 

```ruby
gem 'mailgun_rails'
```

https://app.mailgun.com/

에 가입 후 새로운 도메인을 만들어준다. 

테스트의 경우, mailgun 에서 sandbox 를 기본적으로 하나 제공해주는데 그걸로 테스트를 해도 무방하다

이걸 사용할때,  __Authorized Recipients__ 를 추가한 값만 메일이 보내지니 주의하자! 



## host 설정

이메일을 보내기 위해 host 설정을 해주어야 한다!

##### development.rb

```ruby
config.action_mailer.default_url_options = { :host => "http://localhost:3000/" }
config.action_mailer.delivery_method = :mailgun
config.action_mailer.mailgun_settings = {
  api_key: Rails.application.credentials.mailgun_api_key,
  domain: Rails.application.credentials.mailgun_domain,
}
```

개발 환경이여서 :host 를 _localhost_ 로 만들어줬다.

mailgun 에서 만든 domain key 안에 api_key, domain 을 넣어주면 된다. 

위에는 레일즈 5.2 에서 제공해주는 credentials.yml 을 사용한 내용이고

_figaro gem_ 또는 _secret.yml_ 을 사용해준다. 

`production` 모드 사용시 프로덕션에 대한 내용에도 추가해준다! 



##### devise.rb

어떤 메일로 보낼지 설정을 해준다.

```ruby
  config.mailer_sender = 'openull.ssomee@gmail.com'
```

이렇게만 설정해주면 메일 알아서 감! 끝!