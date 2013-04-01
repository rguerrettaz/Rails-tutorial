# Routing
## There are three ways to create an HTTP response in Rails
* <a href="#rendering">Render</a>
* <a href="#redirect">Redirect_to</a>
* <a href="#head">Head</a>

<h2 id="rendering">Rendering</h2>

### Sinatra way

```ruby
get '/users/:id' do
  @user = User.find(params[:id])

  erb :show_users
end
```

### Rails way

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

### Ways to render
```ruby
render :edit
render :action => :edit
render 'edit'
render 'edit.html.erb'
render :action => 'edit'
render :action => 'edit.html.erb'
render 'books/edit'
render 'books/edit.html.erb'
render :template => 'books/edit'
render :template => 'books/edit.html.erb'
render '/path/to/rails/app/views/books/edit'
render '/path/to/rails/app/views/books/edit.html.erb'
render :file => '/path/to/rails/app/views/books/edit'
render :file => '/path/to/rails/app/views/books/edit.html.erb'
```

######Implicit rendering of a route 
NOTE: it automatically looks in the folder of the view for a matching file e.g. index route => index.html.erb
This is how you would normal do a render explicitly
```ruby
def index
  @user = User.find(id)
  render :index
end
```
But in rails we have implicit rendering because rails will imply the correct route from def name
```ruby
def index
  @user = User.find(id)
end
```

### Rendering Options

There are 4 options you can pass while a rendering
* :content_type
* :layout
* :status
* :location


##### Rendering JSON

Sinatra Way
```ruby
get '/index' do
  content_type :json
  @user = User.find(id)
  @user.to_json
end
```

Rails Way

```ruby
def index
@user = User.find(id)
render :json => @user
end
render :json => @variable_to_be_converted
```
NOTE: you do not need to use to_json, you also do not need to specify content type in rails


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
