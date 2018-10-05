# [model] 테이블 컬럼 추가 삭제 코드

migrate 시행 한 파일은 변경해도 migrate 가 안먹힌다.

아래 코드처럼 파일을 만든 후 migration 작업을 시행해 주어야 한다. 

##### 테이블 컬럼 추가

```ruby
 rails g migration AddColumnsToUser col1:integer col2:integer .. etc.
```

##### 테이블 컬럼 삭제

```ruby
rails g migration RemoveColumnsFromUser column_name:data_type
```

