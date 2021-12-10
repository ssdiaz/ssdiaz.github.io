---
layout: post
title:      "**Sinatra Project**"
date:       2021-12-10 12:03:42 -0500
permalink:  sinatra_project
---


For my Sinatra portfolio project, I created *Task App*, a task application where a user can create and maintain their daily tasks (like a to-do list), and a user can view the tasks of others. 

I'd like to use this blog to summarize the purpose of each file in my project, which the [Corneal](https://thebrianemory.github.io/corneal/) gem helped to create. 

If you'd like to follow along, check out my project @[github.com/ssdiaz/task_list_application](https://github.com/ssdiaz/task_list_application).  Thanks for reading!


### Gemfile
The `Gemfile` lists all the gems (libraries of Ruby code) required for my project. I added to this file along the way, each time running `bundle install` to create `Gemfile.lock`; this file notes the version no. of the gems installed in case of future incompatible gem updates. 

Here is a list of gems I used:
```
* gem 'sinatra'     #web framework
* gem 'activerecord'     #maps the database and model classes
* gem 'sinatra-activerecord'     #gives us access to Rake and database support for Sinatra
* gem 'rake'     #easily create database files
* gem 'require_all'     #loads all Ruby files in a specified folder of the root directory, without needing to list each file
* gem 'sqlite3'     #adapter for our SQL database
* gem 'shotgun'     #runs a local server and re-cycles with each click
* gem 'pry'     #console used for debugging
* gem 'bcrypt'     #for secure passwords; NOTE: must save password attributes as 'password_digest'
* gem 'sinatra-flash'     #displays messages to the user
* gem 'tux'     #interactive console that pre-loads our database and ActiveRecord relationship
```

### config/environment.rb
This file is responsible for loading our environment in the order it’s presented. 

First, you need to install your gems with `require 'bundler/setup'`, and use `Bundler.require(:default)` to load the gems into your environment.  

Then, you need to connect `ActiveRecord` to your database. The adapter is our server-side framework engine, *sqlite*, and our database, *database.sqlite*, is located in the `db` directory.  
```
 ActiveRecord::Base.establish_connection(
     :adapter => "sqlite3",
     :database => "db/database.sqlite"
 )
```

Lastly, we use `require_all 'app'` to require all of the Ruby files located in the `app` directory that follows the **MVC** structure (**m**odels-**v**iews-**c**ontrollers). This must load after we set up our database connection, otherwise it will cause errors in our controllers.

### config.ru
This file ultimately runs our app. It’s a Rack based file using the `.ru` extension.  It details the environment with `require './config/environment'` and loads the Sinatra library with `require ‘sinatra’`.

Sinatra uses Rack for it’s middleware, `use Rack::MethodOverride`, which runs between our app and the server.  `MethodOverride` interrupts every request sent and received by our application, and if it finds a request matching the name defined, it will set the request type based on that value attribute.  

Next, I specify which controllers to use and which one to run. The controllers must be placed after the middleware so they know how to process the *PATCH* and *DELETE* routes listed. My two child controllers are `users_controllers.rb` and `tasks_controllers.rb`, which inherit from my parent controller, `applicationcontroller.rb`, that I set as `run`.
```
 use UsersController
 use TasksController
 run ApplicationController
```

### Rakefile
Rake is a Ruby gem that helps us manage our database from the command line. You can create rake tasks in the `Rakefile` to use in your console.

First you need to connect to the database – but you did this already in `config/environment.rb`, so just require it with `require_relative './config/environment'`

You also need to `require 'sinatra/activerecord/rake' ` to access Rake tasks from the Sinatra ActiveRecord gem.

It's helpful to define a console to run your `pry` environment, accessed by typing `rake console` in your terminal. 
```
 task :console do
   Pry.start
 end
```

### Models
Next, I created my model classes. For this project, I kept them pretty basic. I have a **User** and **Task** model, each inheriting from `ActiveRecord::Base` and their relationships defined - users `has_many :tasks` & tasks `belong_to :user`. 

I also added some validations thanks to the `ActiveRecord` and `bcrypt` gems. For example, to save a new user to the database, a **User** must have a name, email, password, and the email must be unique. 
```
 has_secure_password     #from bcrypt gem 
 validates :name, :email, presence: true     #from ActiveRecord gem
 validates :email, uniqueness: true     #from ActiveRecord gem
```

### db (Database Directory)
Once you have Rake installed and connected to your database, you can build your schema. Create a new migration with `rake db:create_migration NAME=___`, then create your table in the `change` method provided with the file creation.

Once you are set up, run `rake db:migrate` to create your database and run your migrations. This will create  `schema.rb`. Any edits made to this file should come from migrations and not from manual intervention. If you delete `schema.rb`, just run `rake db:migrate` and it will create again. 

Just note that migrations will only run once and not each time you run `rake db:migrate` - hence why you should make changes via a new migration file, for example, creating a new migration to `change_column` or `add_column`.

You'll also notice the database file was automatically created as `database.sqlite`.

### seeds.rb
This is where you create dummy data to load into your database, based on your models and tables. Load the data with `rake db:seed`.

### **Controllers** -  The Main Event
Once your database is working, you can build the meat of your project - **the controllers**. To keep your project organized, each model should have its own controller to specify the routes specific to their class responsibility. 

As mentioned, I used 3 controllers - `users_controller.rb` and `taskscontroller.rb`, which inherit from `application_controller.rb`; anything defined in `application_controller.rb` will be available to `users_controller.rb` and `taskscontroller.rb`.

`application_controller.rb` inherits from `Sinatra::Base`, and uses `register Sinatra::Flash` to display messages in the views. In `application_controller.rb`, I defined my helper methods so the other controllers could call these methods directly. I also defined the `configure` block:
```
  configure do
     set :views, 'app/views'     #tells the controller where the view templates are located
     set :public_folder, 'public'     #tells the controller where the stylesheet is located
     enable :sessions    #tells Sinatra to enable cookie sessions; how we track if the user is the logged in
     set :session_secret, "secret"    # I didn't actually use "secret", I have something else here ;) 
    end
```

Each page you want displayed needs a matching route defined in the appropriate controller. Make sure the controller route maps to the view by checking in the browser before you spend time coding your view. 

### Views
If you're using Ruby in your view templates, make sure you wrap the code in **erb** tags: `<%= substitution - will display to the user! %>` & `<% scripting - won't display! %>`. If you need to display something from the database, like a user's name, you will most likely need an @instance variable defined in the corresponding controller route.

The naming of your view templates is very important if you want to keep with the **MVC** architecture. 
* `index.rb` - displays (indexes) all items 
* `show.rb` - shows the details of a specific item
* `new.rb` - contains a form to post a new item
* `edit.rb` - contains a form to edit (patch) or delete an existing item

AND, be sure to protect your users in the view templates as well! For example, my task app allows a user to delete a task they own. Since HTML is accessible in the browser, another user can potentially hack into someone else's task and delete it - unless you protect your delete form and make sure only the `current_user` (a helper method) has access to delete. 

Helper Method (in `application_controller.rb`)
```
  helpers do
     def current_user     #returns current user (obj) or nil
        User.find_by_id(session[:user_id])
     end

     def check_user_for(task)     #returns true if the task's user belongs to current user
        task.user == current_user
     end
 end
```


View template:
```
 <% if check_user_for(task) %>
     <%# DELETE TASK %>
          <form method="POST" action="/tasks/<%=task.id%>"> 
          <input id="hidden" type="hidden" name="_method" value="delete">
          <td><input type="submit"  value="Delete"></td>
     </form>
 <% end %>
```

### public/stylesheets/main.css
The last thing I want to mention is that the stylesheet in the public directory holds all my `CSS` code, which styles the `HTML` in my view templates. This helps give my website personality. I found styling my page to be such a joy and really loved building out this file, although it's easy to spend too much time here if you're on a schedule. The internet really shined here, and most of my code was created from [w3schools](https://www.w3schools.com/howto/default.asp).

One thing I noticed was that my CSS updates wouldn't always reflect in the browser after being changed and saved, say from changing the background color of a header from *red* to *pink*. However, I found that clearing my browsing data seemed to do the trick.


