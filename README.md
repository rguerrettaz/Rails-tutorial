# Routing
## There are three ways to create an HTTP response in Rails
* <a href="#rendering">Render</a>
* <a href="#redirect">Redirect_to</a>
* <a href="#head">Head</a>

<h2 id="rendering">Rendering</h2>

##### Sinatra way

```ruby
get '/users/:id' do
  @user = User.find(params[:id])

  erb :show_users
end
```

##### Rails way

```ruby
class UsersController < ApplicationController
  def show
    @user = User.find(params[:id])

    # We can actually omit this call to render
    # Rails will render app/views/users/show.html.erb by default
    render :show
  end
end
```

### Rendering Layouts

<h2 id="redirect">Redirect</h2>

##### Sinatra way

```ruby
```
##### Rails way

```ruby
```

<h2 id="head">Head</h2>




```ruby
```


# Partials

# Helpers
