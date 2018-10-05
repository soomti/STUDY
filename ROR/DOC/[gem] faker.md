# [gem] Faker 

이 gem은 임의의 이름, 주소, 폰 번호, 이메일 주소 등등 여러 더미를 디비에 저장하고 싶을 때, 

편하게 해주는 라이브러리이다!

##  getting started

gemfile.rb

```ruby
gem 'faker'
```

install

```
$ bundle install
```

example

```ruby
require 'faker'

Faker::Name.name      #=> "Christophe Bartell"

Faker::Internet.email #=> "kirsten.greenholt@corkeryfisher.info
```



