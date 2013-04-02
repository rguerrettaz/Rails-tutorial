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
#####Rails Way
Just like Sinatra, file names for partials will have leading-underscore.
```ruby
<%= render "menu" %>
```
will render a file named _menu.html.erb

###Partial Layouts
A partial can use its own layout file, just as a view can use a layout.
```ruby
<%= render :partial => "link_area", :layout => "graybar" %>
```
layouts for partials follow the same leading-underscore naming.
Partials are very useful in rendering collections. When you pass a collection to a partial via the :collection option, the partial will be inserted once for each member in the collection:
index.html.erb
```ruby
<%= render :partial => "product", :collection => @products %>
```
_product.html.erb
```ruby
<p>Product Name: <%= product.name %></p>
```

# Helpers

* <a href="#link_to">link_to</a>
* <a href="#form_for">form_for</a>
* <a href="#image_tag">image_tag</a>

###Sinatra Way
```ruby
helpers do
  def kneel
    "Kneel noobs!"
  end
end
```
/helpers/application_helper.rb
###Rails Way

```ruby
module ApplicationHelper
  def kneel
    "Kneel Scrubs"
  end
end
```

###Rails magic asset tag helpers for rendering
* (auto_discovery_link_tag)
* (javascript_include_tag)
* (stylesheet_link_tag)
* (image_tag)
* (video_tag)
* (audio_tag)

#####Examples
layout.html.erb
```ruby
#loops through all js files in public asset folder and appends them to head 
<%= javascript_include_tag :all, :recursive => true %>
#joins all js files below and appends as a single file
<%= javascript_include_tag "main", "columns", :cache => "cache/main/display" %>
```

 
<h2 id="link_to">Link_to</h2>
It is an almost Rails convention that you should use Ruby code rather than HTML to create links as a matter of style and flexibility via routes.rb
Example:
<strong>Sinatra way:<strong>
```ruby
<div class="links">
  <ul>
    <li><a href="/users/login">Log in</a></li>
    <li><a href="/users/signup">Sign up</a></li>
  </ul>
</div>
```
 
<strong>Rails way:<strong>
```ruby
<div class="right-nav">
    <%= link_to "Log in",  '#', :remote => true,
                class: "nav-link", id: "login" %>
    <%= link_to "Sign Up", '#', :remote => true,
                class: "nav-link", id: "signup" %>
  </div>
```
 
Other ways to user link_to
* link_to_if
* link_to_unless
* link_to_unless_current
 
 
<h2 id="form_for">Form_for</h2>
 
The form_for functionality is helpful in creating single form to either add a new item or edit an existing item. The model object's content changes in the two cases (referring to @new_todo) but the form_for code remains the same.
 
form_for is essentially associated with a model object. In this example we are going to use a TodoController and a @new_todo instance as seen below.
```ruby
class TodosController < ApplicationController
  def index
    @todo_items = Todo.all
    @new_todo = Todo.new
  end
```
 
Example use of form_for:
```ruby
<%= form_for @object, :url => { :action => "create" }, :html => {:class => "nifty_form"} do |f| %>
  <%= f.text_field :title %>
  <%= f.text_area :body, :size => "60x12" %>
  <%= f.submit "Create" %>
<% end %>
```
 
For this to work we need to update our add method in the TodosController to the below:
```ruby
def add
  todo = Todo.create(:todo_item => params[:todo][:todo_item])
  redirect_to :action => 'index'
end
```
 
### form_for to html example
 
form_for code:
```ruby
<%= form_for @article, :url => { :action => "create" }, :html => {:class => "nifty_form"} do |f| %>
  <%= f.text_field :title %>
  <%= f.text_area :body, :size => "60x12" %>
  <%= f.submit "Create" %>
<% end %>
```
 
resulting HTML
```ruby
<form accept-charset="UTF-8" action="/articles/create" method="post" class="nifty_form">
  <input id="article_title" name="article[title]" size="30" type="text" />
  <textarea id="article_body" name="article[body]" cols="60" rows="12"></textarea>
  <input name="commit" type="submit" value="Create" />
</form>
```
 
 
<h2 id="image_tag">Image_tag</h2>
 
No time, only one example:
 
How to display an image inside a link_to
```ruby
<%= link_to image_tag("about.gif", :border=>0), :action => 'about' %>
```


# RESOURCES

### Helpers

[Form helpers](http://guides.rubyonrails.org/form_helpers.html), 
[Forms](http://www.codelearn.org/ruby-on-rails-tutorial/3/Introducing-forms,-migrations-&-ajax-in-Rails/2/form_for-in-Rails),
[form_for](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-form_for),
[Helpers in Rails Stack Overflow](http://stackoverflow.com/questions/4786099/helpers-in-rails),
[Nested Attributes](http://currentricity.wordpress.com/2011/09/04/the-definitive-guide-to-accepts_nested_attributes_for-a-model-in-rails-3/),
[image_tag](http://apidock.com/rails/v3.2.8/ActionView/Helpers/AssetTagHelper/image_tag),
[link_to](http://cch4rails.blogspot.com/2009/09/rails-basics-linkto.html),
[link_to deeeeeep & detailed and stuffy like a humid day in Florida](http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#M001565),
[fields_for](http://apidock.com/rails/ActionView/Helpers/FormHelper/fields_for)


### Partials & Rendering

[Layouts & rendering](http://guides.rubyonrails.org/layouts_and_rendering.html)




