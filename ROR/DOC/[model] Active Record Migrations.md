# [model] Active Record Migrations

마이그레이션 파일은 데이터베이스 스키마를 변경해 주는 파일이다.

레일즈는 편리하게  _sql_ 문을 사용하지 않아도 스키마 설정을 할 수 있는데,

 _migration_ 폴더 내에 스키마에 대한 변경/추가에 대한 작업을 해주는 파일을 만들어 준다

이후 ` rails db:migration` 명령어를 사용하면, 그에 대한 작업이 완료된다. 

하나의 파일은 스키마 변경 작업에 대한 하나의 버전이 된다. 



## 마이그레이션 파일 생성 규칙 

20181005155917_create_posts.rb

-  타임스탬프 를 붙혀 변경 시간을 기록한다

- _create_  를 통해,생성인지 추가인지, 제거 작업인지 알 수 있다

- *posts* 변경되는 모델을 명시한다.

- example

  ```ruby
  class CreatePosts < ActiveRecord::Migration[5.2]
    def change
      create_table :posts do |t|
        t.string :title
        t.text :content
        t.timestamps
      end
    end
  end
  ```

  

## 스키마 적용

마이그레이션 작업을 통해 적용시켜준다.

```ruby
$ rake db:migrate
```

이 작업을 한 후, 적용된 파일에 컬럼을 추가하거나, 삭제하여도 적용되지 않기 때문에,

모델의 컬럼에 대한 수정이 필요한 경우, 다른 파일을 생성해서 만들어주어야 한다.

#####  

## 컬럼 추가시

제목에 이 마이그레이션이 어떤 작업인지 명시한다

##### 1. Add/Remove

추가인지/삭제인지 구분

##### 2. PartNumber

추가되는 컬럼 이름

##### 3. To/From

add 시 to, remove 시 from 사용

##### 4. model

추가되는 모델 이름을 말해준다.

```ruby
$ rails generate migration AddPartNumberToProducts
```

```ruby
class AddPartNumberToProducts < ActiveRecord::Migration[5.0]
  def change
    add_column :products, :part_number, :string
  end
end
```





```ruby
 rails g migration AddColumnsToUser col1:integer col2:integer .. etc.
```

##### 테이블 컬럼 삭제

```ruby
rails g migration RemoveColumnsFromUser column_name:data_type
```

