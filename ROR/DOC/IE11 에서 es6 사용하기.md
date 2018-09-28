# [error] jquery is not working ie11

### problem

처음에는 datepicker 문제인줄 알았다. 

```
datapicker is not working ie11
```

로 계속 검색했다 ㅠㅠ 맥이어서 확인도 못하고, 뭣도 못하고 

그래서 친구껄로 원격제어를 해보니

```
$ is not a function 
```

이 걸리고, 

애뮬레이터 돌리니

```js
a=>alert('b')
```

여기!̆̈ **arrow function** 에서 문제가 나는거였다 



IE11 는 es6를 지원을 안하는데, 

이를 위해 **babel** 이라는 친구가 es6을 확장자를 보고 알아서 es5  문법으로 바꿔준다! 

무튼 바꿔야 ie11 고객님들께서 (크롬쓰세요) 결제가 가능하니까! 바꿔주었당

## solve

1. gem 설치

   ```ruby
   gem 'sprockets'
   gem 'sprockets-es6'
   ```

   ```
   $ bundle install 
   $ bundle install --without development test  (production)
   ```

2. es6 문법인 친구들 확장자를 `.es6` 으로 변경한다.

   ```ruby
   index.es6 # index.js 
   ```

3. application.rb

   ```ruby
   # application.rb
   # [...]
   require "action_view/railtie"
   require "sprockets/railtie"
   # require "rails/test_unit/railtie"
   require 'sprockets/es6'
   # [...]
   ```

   