# Boots - Rails + webpacker + bootstrap
15 steps to create a rails app based on Ruby 2.5.1, Rails 5.2.0, Webpacker and Bootstrap 4.1.2
## 1. Check Ruby and Rails version
```console
$ ruby -v
ruby 2.5.1p57 (2018-03-29 revision 63029) [x86_64-darwin17]
$ rails -v
Rails 5.2.0
```
## 2. Create new app with webpack / without sprockets
```console
$ rails new boots --skip-sprockets --webpack
...
$ cd boots
```
## 3. Create controller
```console
$ rails generate controller Welcome index
...
$ code .
```
## 4. Adjust root
```ruby
# config/routes.rb
# tip: at the top to be matched first
...
Rails.application.routes.draw do
  root to: 'welcome#index'
  ...
```
## 5. Check rails application
```console
$ rails s
...
```
## 6. Switch to browser and open url 
```
http:\\localhost:3000
```
## 7. Add Bootstrap, JQuery, .. 
```console
$ yarn add bootstrap@4.1.2 jquery popper.js
...
```
## 8. Fix webpack environment
```javascript
// config/webpack/environment.js :
const { environment } = require('@rails/webpacker');

const webpack = require('webpack');
environment.plugins.prepend('Provide', new webpack.ProvidePlugin({
  $: 'jquery',
  jQuery: 'jquery',
  Popper: ['popper.js', 'default'] 
}));

module.exports = environment;
```
## 9. Create scss and adjust js root files
```scss
// Create new file app/javascript/packs/bootstrap.scss
@import '~bootstrap/dist/css/bootstrap';
```

```js
// Create new folder app/javascript/plugins and file app/javascript/plugins/bootstrap.js
import 'bootstrap/dist/js/bootstrap';

$(function () {
  console.log('Hello World from Webpacker');
});
```

```js
// Edit existing file app/javascript/packs/application.js
import 'plugins/bootstrap';
```
## 10. Change includes to webpack installation and add layout files
```erb
<%# /app/views/layouts/application.html.erb %>
<!DOCTYPE html>
<html>
  <head>
    <title>Boots</title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
    <%= stylesheet_pack_tag 'bootstrap', media: 'all', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <%= render 'layouts/menu' %>
    <div class='container'>
      <%= render 'layouts/messages' %>
      <%= yield %>
    </div>
  </body>
</html>
```
## 11. Add layout file for messages 
```erb
<%# /app/views/layouts/_messages.html.erb %>
<%# Rails flash messages styled for Bootstrap 4.0 %>
<% flash.each do |name, msg| %>
  <% if msg.is_a?(String) %>
    <div class="alert alert-<%= name.to_s == 'notice' ? 'success' : 'danger' %>" role="alert">
      <button type="button" class="close" data-dismiss="alert" aria-hidden="true">&times;</button>
      <%= content_tag :div, msg, :id => "flash_#{name}" %>
    </div>
  <% end %>
<% end %>
```
## 12. Add first Bootstrap component: Menu
```erb
<%# /app/views/layouts/_menu.html.erb %>
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Navbar</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav mr-auto">
      <% Dir[Dir.pwd+"/app/views/layouts/menus/_*.html.erb"].sort.each do |filename| %>
        <% if !File.directory? filename %>
          <% if File.basename(filename)[0] == "_" %>
            <%= render "layouts/menus/#{File.basename(filename)[1..-1]}" %>
          <% end %>
        <% end %>
      <% end %>
    </ul>
    <form class="form-inline my-2 my-lg-0">
      <input class="form-control mr-sm-2" type="search" placeholder="Search" aria-label="Search">
      <button class="btn btn-outline-success my-2 my-sm-0" type="submit">Search</button>
    </form>
  </div>
</nav>
```
```erb
<%# /app/views/layouts/menus/_a1.html.erb %>
      <li class="nav-item active">
        <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
      </li>
```
```erb
<%# /app/views/layouts/menus/_a2.html.erb %>
      <li class="nav-item">
        <a class="nav-link" href="#">Link</a>
      </li>
```
```erb
<%# /app/views/layouts/menus/_a3.html.erb %>
      <li class="nav-item dropdown">
        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
          Dropdown
        </a>
        <div class="dropdown-menu" aria-labelledby="navbarDropdown">
          <a class="dropdown-item" href="#">Action</a>
          <a class="dropdown-item" href="#">Another action</a>
          <div class="dropdown-divider"></div>
          <a class="dropdown-item" href="#">Something else here</a>
        </div>
      </li>
```
```erb
<%# /app/views/layouts/menus/_a4.html.erb %>
      <li class="nav-item">
        <a class="nav-link disabled" href="#">Disabled</a>
      </li>
```
## 13. Compile webpacker (not required)
```console
$ rails webpacker:compile
...
```
## 14. Start application
```console
$ rails s
...
```
## 15. Open application inside browser
```
http:\\localhost:3000
```
## 16. Add-on: Add images
```console
$ mv r1200gs.jpg app/javascript/packs/images/r1200gs.jpg
$ mv r1200rt.jpg app/javascript/packs/images/r1200rt.jpg
$ mv schwalbe.png app/javascript/packs/images/schwalbe.png
```
## 17. Include images in view
```erb
<%# /app/views/welcome/index.html.erb %>
<h1>Welcome#index</h1>
<div class="card-deck">
  <div class="card" style="width: 18rem;">
    <img class="card-img-top" src="<%= asset_pack_path 'packs/images/r1200gs.jpg'  %>" alt="Card image cap">
    <div class="card-body">
      <h5 class="card-title">Card title</h5>
      <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
      <a href="#" class="btn btn-primary">Go somewhere</a>
    </div>
  </div>

  <div class="card" style="width: 18rem;">
    <img class="card-img-top" src="<%= asset_pack_path 'packs/images/schwalbe.png'  %>" alt="Card image cap">
    <div class="card-body">
      <h5 class="card-title">Card title</h5>
      <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
      <a href="#" class="btn btn-primary">Go somewhere</a>
    </div>
  </div>

  <div class="card" style="width: 18rem;">
    <img class="card-img-top" src="<%= asset_pack_path 'packs/images/r1200rt.jpg'  %>" alt="Card image cap">
    <div class="card-body">
      <h5 class="card-title">Card title</h5>
      <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
      <a href="#" class="btn btn-primary">Go somewhere</a>
    </div>
  </div>
</div>
```
## 18. Compile webpacker
```console
$ rails webpacker:compile
...
```
## 19. Start application
```console
$ rails s
...
```
## 20. Open application inside browser
```
http:\\localhost:3000
```