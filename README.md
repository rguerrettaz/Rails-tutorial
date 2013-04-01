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
* :layout (probably the most used)
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


### Rendering Layouts in Rails

###Layout
```ruby
render :layout => 'special_layout'
render :layout => false
```
#####Force assign layout of all routes inside
```ruby
class ProductsController < ApplicationController
  layout "inventory"
  #...
end
```
#####For entire application
```ruby
class ApplicationController < ActionController::Base
  layout "main"
  #...
end
```
#####Set runtime layout by using a colon in layout and "name" for actual route
```ruby
class ProductsController < ApplicationController
  layout :products_layout
 
  def show
    @product = Product.find(params[:id])
  end
 
  private
    def products_layout
      @current_user.special? ? "special" : "products"
    end
 
end
```

#####Set layout with a proc and gets inherited if another controller inherits from ProductsController

```ruby
class ProductsController < ApplicationController
  layout Proc.new { |controller| controller.request.xhr? ? 'popup' : 'application' }
end
```
#####Conditonal layout
```ruby
class ProductsController < ApplicationController
  layout "product", :except => [:index, :rss]
end
```
#####All layouts can be overridden, sent down from parent controller
```ruby
class ApplicationController < ActionController::Base
  layout "main"
end
```
```ruby
class SpecialPostsController < ApplicationController
  layout "special"
end
```

#####Render multiple layouts 
FILE: layout.html.erb
```ruby
<html>
<head>
  <style type="text/css"><%= yield :stylesheets %></style>
</head>
<body>
</body>
</html>
```
FILE: news.html.erb
```ruby
<% content_for :stylesheets do %>
  #top_menu {display: none}
  #right_menu {float: right; background-color: yellow; color: black}
<% end %>
```

<h2 id="redirect">Redirect</h2>

##### Sinatra way

```ruby
redirect '/'
redirect_to '/'
redirect back
```
##### Rails way

```ruby
redirect_to photos_url
redirect_to :back
redirect_to photos_path, :status => 301
```
NOTE: one option are :status => 302, :

###Render vs Redirect - FIGHT!!!
#####Render allows for another render to occur if you have one specified after, redirect forces a "return" so code afterwards is not executed

```ruby
def show
  @book = Book.find(params[:id])
  if @book.special?
    render :action => "special_show"
  end
  render :action => "regular_show"
end
```

```ruby
def show
  @book = Book.find(params[:id])
  if @book.special?
    render :action => "special_show" and return
  end
  render :action => "regular_show"
end
```


<h2 id="head">Head</h2>
#####An explicit way to specify render 

```ruby
render :nothing => true
```

```ruby
head :bad_request
head :created, :location => photo_path(@photo)
#Useful for redirecting
```


# Partials

# Helpers
###Sinatra Way
```ruby
helpers do
  def kneel
    "Kneel noobs!"
  end
end
```

###Rails Way

```ruby
module ApplicationHelper
  def kneel
    "Kneel Scrubs"
  end
end
```

###Rails magic asset tag helpers for rendering
*"auto_discovery_link_tag"
*"javascript_include_tag"
*"stylesheet_link_tag"
*"image_tag"
*"video_tag"
*"audio_tag"
