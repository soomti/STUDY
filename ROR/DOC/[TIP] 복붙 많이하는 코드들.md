# [TIP] 복붙을 많이하는 코드들

login logout

```ruby
# views/devise/menu/_login_items.html.erb
<% if user_signed_in? %>
  <li>
  <%= link_to('Logout', destroy_user_session_path, method: :delete) %>        
  </li>
<% else %>
  <li>
  <%= link_to('Login', new_user_session_path)  %>  
  </li>
<% end %>
```

