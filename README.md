# Rails-kickoff

This is Le Wagon's kick-off roadmap for the lead dev (in charge of setup / deployment / collaboration).

## Rails new

Create the project locally and on Github

```bash
$ rails new YOUR_APP_NAME -T --database=postgresql
$ cd YOUR_APP_NAME
$ git init
$ git add .
$ git commit -m "rails new"
$ hub create
$ git push -u origin master
```

Make sure you have the relevant ruby and rails version set in the `Gemfile`

```ruby
# Gemfile
source 'https://rubygems.org'
ruby "2.2.0"
gem 'rails', '4.2.0'
````

Commit 

```bash
$ git add .
$ git commit -m "setting ruby and rails versions"
```

Create the db locally

```bash
$ rake db:create
```

## Development gems

Add debug gems

```ruby
# Gemfile
group :development do
  gem "better_errors"
  gem "binding_of_caller"
  gem "quiet_assets"
  gem "pry-rails"
  gem "pry-byebug"
end
```

and commit

```bash
$ bundle install
$ git add .
$ git commit -m "Adding development gems"
```

## Front-end setup

Add front-end gems

```ruby
# Gemfile
gem 'bootstrap-sass'
gem 'font-awesome-sass'
gem 'simple_form'
```

Generate simple form Bootstrap 3 initializer

```
$ bundle install
$ rails generate simple_form:install --bootstrap
```

Add Bootstrap sprockets directive in `application.js`

```javascript
// app/assets/javascripts/application.js

//= require jquery
//= require jquery_ujs
//= require bootstrap-sprockets
//= require_tree .
```

Add viewport in layout `application.html.erb`

```erb
<!-- app/views/layouts/application.html.erb -->
<head>
  <!-- Add these line for detecting device width -->
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
</head>
```

Add dynamic meta tags in your layout 

```erb
<!-- app/views/layouts/application.html.erb -->
<head>
  <title><%= yield(:title) || "Your App name" %></title>
  <meta name="description" content="<%= (yield(:description) || "").squish %>">
  <!-- [...] -->
</head>
```

Switch javascript application tag at the end of the `body` and set an `:after_js` block of code for page-specific javascript code

```erb
<!-- app/views/layouts/application.html.erb -->
<body>
  <!-- [...] -->

  <%= javascript_include_tag 'application' %>
  <%= yield(:after_js) %>
</body>
```

Load our custom LeWagon's stylesheets:

```bash
$ rm -rf app/assets/stylesheets
$ curl -L https://github.com/lewagon/rails-stylesheets/archive/master.zip > app/assets/stylesheets.zip
$ cd app/assets && unzip stylesheets.zip && rm stylesheets.zip && mv stylesheets-master stylesheets && cd ../..
```

And commit 

```bash
$ git add .
$ git commit -m "Finishing front-end setup"
```

## Figaro

Make figaro install on master branch (to avoid leaks of API keys in `application.yml`!!)

```ruby
# Gemfile
gem "figaro"
```

Then add `application.yml` in `.gitignore`

```bash
$ bundle install
$ figaro install
$ git add .
$ git commit -m "setting figaro on master branch"
```

## Github

Push your setup on Github

```bash
$ git push origin master
```

Then add your team-mates as collaborators and make them clone the repo!

## Heroku 

```ruby
# Gemfile
gem 'rails_12factor', group: :production
gem 'puma',           group: :production
```

```bash
$ bundle install
$ curl -L https://gist.github.com/ssaunier/24bc1c4db955c19e3289/raw/install.sh | bash
$ git add .
$ git commit -m "Prepared for Heroku"
```

```bash
$ heroku create YOUR_APP_NAME --region eu
$ git push heroku master
```

## Devise

```ruby
# Gemfile
gem 'devise'
```

```bash
$ bundle install
$ rails generate devise:install
```

```ruby
# config/initializers/devise.rb

# ==> Mailer Configuration
config.mailer_sender = 'contact@yourcompany.com'
```

Generate user model

```bash
$ rails generate devise User
$ rake db:migrate
```

Get a cool navbar and notices

```erb
<!-- app/views/layouts/application.html.erb -->
<%= render 'shared/navbar' %>
<%= render 'shared/notices' %>
<%= yield %>
```

Create `shared/_navbar.html.erb` with [our template](https://github.com/lewagon/awesome-navbars/blob/master/templates/_navbar_with_login.html.erb)

And add `shared/_notices.html.erb` with Bootstrap alerts

```erb
<!-- app/views/shared/_notices.html.erb -->
<% if notice %>
  <div class="alert alert-info alert-dismissible" role="alert">
    <button type="button" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true">&times;</span></button>
    <%= notice %>
  </div>
<% end %>
<% if alert %>
  <div class="alert alert-warning alert-dismissible" role="alert">
    <button type="button" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true">&times;</span></button>
    <%= alert %>
  </div>
<% end %>
```

Customize devise views:

```bash
$ rails g devise:views
```

```erb
<!-- app/views/devise/registrations/new.html.erb -->
<div class="container">
  <div class="row">
    <div class="col-sm-6 col-sm-offset-3">
      <h2>Sign up</h2>
      <%= simple_form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
        <%= f.error_notification %>

        <%= f.input :email, required: true, autofocus: true %>
        <%= f.input :password, required: true, hint: ("#{@minimum_password_length} characters minimum" if @validatable) %>
        <%= f.input :password_confirmation, required: true %>
        <%= f.button :submit, "Sign up", class: "btn btn-primary" %>
      <% end %>

      <%= render "devise/shared/links" %>
    </div>
  </div>
</div>
```

```erb
<!-- app/views/devise/sessions/new.html.erb -->
<div class="container">
  <div class="row">
    <div class="col-xs-12 col-sm-6 col-sm-offset-3">
      <h2>Log in</h2>

      <%= simple_form_for(resource, as: resource_name, url: session_path(resource_name)) do |f| %>
          <%= f.input :email, required: false, autofocus: true %>
          <%= f.input :password, required: false %>
          <%= f.input :remember_me, as: :boolean if devise_mapping.rememberable? %>
          <%= f.button :submit, "Log in", class: "btn btn-primary" %>
      <% end %>

      <%= render "devise/shared/links" %>
    </div>
  </div>
</div>
```

Force login on your app controller

```ruby
# app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  before_action :authenticate_user!
end
```

And commit your work

```bash
$ git add .
$ git commit -m "Finish devise integration"
```

## Facebook-connect

```ruby
# Gemfile
gem 'omniauth-facebook'
```

```bash
$ bundle install
$ figaro install
```

Create you facebook app and set your development keys in `application.yml`

```yaml
development:
  FB_ID: "3************1"
  FB_SECRET: "4************e"
```

Configure devise

```ruby
# config/initializers/devise.rb

Devise.setup do |config|
  config.omniauth :facebook, ENV["FB_ID"], ENV["FB_SECRET"], scope: 'email'
end
```

Change your devise routing to handle omniauth callbacks

```ruby
# config/routes.rb
devise_for :users, controllers: { omniauth_callbacks: "users/omniauth_callbacks" }
```

Change `User` model 

```ruby
# app/models/user.rb

class User < ActiveRecord::Base
  devise :omniauthable, :omniauth_providers => [ :facebook ]
end
```

Add facebook fields to `users` table

```
$ rails g migration AddOmniauthToUsers provider uid picture name token token_expiry:datetime
$ rake db:migrate
```

And set a method on the `User` model to record these fields from facebook callbacks

```ruby
class User < ActiveRecord::Base
  def self.find_for_facebook_oauth(auth)
    where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
      user.provider = auth.provider
      user.uid = auth.uid
      user.email = auth.info.email
      user.password = Devise.friendly_token[0,20]  # Fake password for validation
      user.name = auth.info.name
      user.picture = auth.info.image
      user.token = auth.credentials.token
      user.token_expiry = Time.at(auth.credentials.expires_at)
    end
  end
end
```

Integrate this in your callbacks controller

```ruby
# app/controllers/users/omniauth_callbacks_controller.rb

class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
  def facebook
    user = User.find_for_facebook_oauth(request.env["omniauth.auth"])

    if user.persisted?
      sign_in_and_redirect user, event: :authentication
      set_flash_message(:notice, :success, :kind => "Facebook") if is_navigational_format?
    else
      session["devise.facebook_data"] = request.env["omniauth.auth"]
      redirect_to new_user_registration_url
    end
  end
end
```

Change a bit your navbar code

```erb
<!-- Somewhere in your navbar -->
<!-- Regular login -->
<li>
  <%= link_to "Connexion", new_user_session_path %>
</li>
<!-- Facebook login -->
<li>
  <%= link_to "Connect with Facebook", user_omniauth_authorize_path(:facebook) %>
</li>
```

And insert your facebook profile in navbar

```erb
<!-- Somewhere in your navbar -->
<% if user_signed_in? %>
  <%= image_tag current_user.picture, class: "img-avatar" %>
<% end %>
```

Commit your work:

```bash
$ git add .
$ git commit -m "adding fb connect"
```

When pushing on heroku, run the migrations and set the API keys of your production FB app on Heroku

```
$ git push heroku master
$ heroku run rake db:migrate
$ heroku config:set FB_ID=************ FB_SECRET=*********
```



