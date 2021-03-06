# Intro to Rails

### Objectives

_After this lesson, students will be able to:_

* Create a new Rails application
* Describe similarities between Sinatra routing & Rails routing
* Generate a controller
* Create actions/methods for a RESTful controller
* Create views
* Generate a model
* Create routes

### Preparation

_Before this lesson, students should already be able to:_

* Execute ruby code in `irb`
* Explain the difference between HTTP request/request
* Explain MVC
* Describe SQL
* Write SQL select statements

## Intro: What is Rails (10 mins)

Rails was created in 2003 by David Heinemeier Hansson, while working on the code base for Basecamp, a project management tool by 37signals. David extracted Ruby on Rails and officially released it as open source code in July of 2004. Despite rapid iteration of the Rails code base throughout the years, it has stuck to three basic principles:

* Ruby Programming Language
* Model-View-Controller Architecture
* Programmer Happiness

Rails was created with the goal of increasing programmers' happiness and productivity levels. In short, with Rails you can get started with a full-stack web application by quickly creating pages, templates and even query functions.

Rails heavily emphasizes **_"Convention over Configuration."_** This means that a programmer only needs to specify and code out the non-standard parts of a program. Even though Rails comes with its own set of tools and settings, you're certainly not limited to library of rails commands and configurations. Developers are free to configure their applications however they wish, though adopting conventions is certainly recommended.

![](https://s3-us-west-2.amazonaws.com/student-resources/uploads/lecture/Screen+Shot+2017-06-09+at+10.04.20+AM.png)

#### A Look Back

As we look back at the history of Rails, let's review some of the more significant releases over the years.

* Rails 1.0 (Dec 2005) - Mostly polishing up and closing pending tickets from the first release along with the inclusion of Scriptaculous 1.5 and Prototype 1.4.
* Rails 1.2 (Jan 2007) - REST and generation HTTP appreciation
* Rails 2.0 (Dec 2007) - Better routing resources, multi-view, HTTP basic authentication, cookie store sessions
* Rails 2.0 (Nov 2008) - i18n, thread safe, connection pool, Ruby 1.9, JRuby
* Rails 2.3 (Mar 2009) - Templates, Engines, Rack
* Rails 3.0 (Aug 2010) - New query engine, new router for controller, mailer controller, CRSF protection
* Rails 3.1 (Aug 2011) - jQuery, SASS, CoffeeScript, Sprockets with Assets Pipeline
* Rails 3.2 (Jan 2012) - Journey routing engine, faster development mode, automatic query explains, tagged loggin for multi-user application
* Rails 4.0 (June 2013) - Rails 4.2: Active Job, Asynchronous Mails, Adequate Record, Web Console, Foreign Keys.
* Rails 5.0 (June 2016) - Notable additions in Rails 5.0 include an option for an API-only application suitable for use as a backend to JavaScript or mobile applications. Also Action Cable for live features such as chat and notifications.

Over the years, Rails has indeed made it easier for beginners to dive into web development and build large complex applications. Some popular websites built on Rails include Twitter (at one point), GitHub and, of course, 37signals' very own Basecamp. Although it has often been criticized for performance and bloat, Rails continues its iterations with an ever-growing developer community and a vibrant ecosystem.

[Built With Rails](https://skillcrush.com/2015/02/02/37-rails-sites/)

## Demo: FlavorTown™️

The goal of the next few minutes is to show the power that Rails gives us – it's actually possible to create a website with a lot of the functionality you've seen in our Sinatra app – forms, links, database, and MVC structure – in less than 5 minutes. We will not detail each step for this app, but we will create a dynamic website in 5 mins by typing the following commands.

```bash
rails new welcome-to-flavortown && cd welcome-to-flavortown
rails generate scaffold Taco name num_ingredients:integer
rake db:migrate
rails server
```

Let's look at the contents of this folder (using `ls`), and take a look at the files and folders that were magically created by the `rails new` command:

```
├── app
├── bin
├── config
├── db
├── lib
├── log
├── public
├── test
├── tmp
└── vendor
```

Some details about this structure:

* 90% of the web app code will be inside the folder `app`, including all of our model, view, and controller logic.
* `config` contains all the credentials for the DB and other 3rd party services, all the deployment settings, and the specs about how to serve this app over HTTP.
* `db` will contain all of your migrations

We will describe the other folders in later lessons, and for the next couple of weeks, you will primarily write code inside the folders described above.

```ruby
rails db:migrate # <-- this might be `rake db:migrate` if you're using <= rails v. 4.2
rails server
```

Now we'll head over to `localhost:3000/tacos`. All of our REST actions are live!

Rails follow a pattern called **"convention over configuration"** - this means that by default, a Rails app expects you to follow specific patterns and folder structures. This means you need to learn these conventions, but also means that once you learn them, you save time by not having to setup a lot of the configuration you'd otherwise need to set up manually.

This structure may look a bit complex – there a lot of files, specific naming conventions, and some nested files and folders. We generally don't create this structure manually, but instead use the Rails command line tool, which initializes the app for us:

> **Note:** By default, if you _do not_ add any option for the database, Rails will create the app with SQLite3. While you are working in a local development environment (localhost), you won't notice much of a difference between SQLite3 and PostgreSQL.

> Once your app is in production on a remote server, you will _not_ use SQLite, and they will often use PostgreSQL. A best practice in web development is to keep development and production environments as similar as possible, so we recommend using PostgreSQL from the start.

#### Rails Routing vs. Sinatra Routing

As you know, a "route" is a combination of **the path** that was requested and **the HTTP verb** that was used to request that path.

```
                                          -----> Model <----> DB
                                         |         |
            response        request      |         |
   Browser <-------- router -------> controller <--
                             GET         ^
                             PUT         |
                             POST         -----> view <----> html/images/css/js
                             DELETE
```

When we've used Sinatra, we were managing the routes and the code executed for a specific route in the same place:

```ruby
	get "/tacos" do
		# Here is the code that will be executed when the client requests /tacos
	end
```

This is handy for us as developers, because it allows us to keep everything in the same place - routing and controller logic - but if the app grows it can get unreadable. Imagine, for example, an app that has 20 or 30 different routes... your main routes file could contain a lot of complex code.

Rails has a "routing engine" that separates the routing logic from the controller logic (what we want to happen when routes are requested). The configuration for this routing engine is in the file `config/routes.rb`.

```ruby
Rails.application.routes.draw do
  # get 'tacos', to: 'tacos#index'
  resources :tacos
end
```

Everything between the `do` and the `end` will be code related to handling routes for the current application.

#### Generate a controller

Rails is an MVC framework, we will need to have controllers to handle requests and call the database through models––remember that all of our models inherit from `ActiveRecord::Base`

In Rails, the controllers are files inside the folder `app/controllers`. If you open this folder, you will see that one controller is already here: the file `application_controller.rb`. This controller does not directly handle HTTP requests, but rather serves as a link between all the controllers we will create, `application_controller.rb` will be the parent of all the controllers in our app.

There is 3 different ways for creating a controller in Rails:

1.  We can manually create a file and write the ruby code inside it.
2.  We can use a generator called `controller`, using `rails g controller CONTROLLER_NAME [ACTIONS]`. For instance, if we want to create a controller for the resource `tacos` with an action and a view for `index` and `show`, we would type in the console `rails g controller tacos index show`. This command would create a bunch of files and modify some others:

    * The controller itself, `tacos_controller.rb` inside `app/controllers`
    * The views for each method , in this case
      * `app/views/index.html.erb`
      * `app/views/show.html.erb`
    * The routes handlers for these two actions will also be added to config/routes.rb :
      * `get '/tacos/index', to: 'tacos#index'`
      * `get '/tacos/show', to: 'tacos#show'`

3.  We can use the `scaffold_controller` generator. This generator will create the same files as the previous generator but with a REST logic for views, controller and views.

#### Create methods for a RESTful controller

We've already defined what a RESTful resource is, let's see how to implement it in a rails app.

As a reminder, a RESTful resource will include 7 methods:

* Index
* Show
* New
* Create
* Edit
* Update
* Delete

Rails has a generator called `scaffold` that will create the whole MVC structure for a resource, let's say that inside the tacos app, we want the `Taco` resource to have a name and a numb_ingredients field, we would type:

```bash
rails g scaffold Taco name num_ingredients:integer
# remember that if we don't specify data type, it defaults to string
```

Running this command will generate a lot of files, including the controller, the views, the model, and the migration. It will also update the routes file.

Take a look at the controller, it has all the RESTful methods, and these methods already contain the code to query the database through the model `Taco`

We don't want all the files created with the scaffold and in practice you won't actually use it so let's delete it. Why use a machete when we can use a scalpel? The scaffold just does **too much**

```ruby
rails d scaffold taco
```

Let's go ahead and generate a new Tacos controller.

#### Create Views

There is no specific generator that will create only a view file, but you can add them manually into the appropriate folder inside views.

For instance, if we want to add a view file `about` for the resource `Taco`, we can create a file `about.html.erb` in `app/views/tacos`.

If a view is "static" - as in, it doesn't use any instance variables created in the controller - you can just create a route for this view and rails will render it in the browser even if there is no method in the controller:

In config/routes.rb

```ruby
get "/tacos/about", to: 'tacos#about'
```

If there is a file `about.html.erb` in `app/views/tacos`, this file will be automatically rendered when you call `localhost:3000/tacos/about`

#### Implicit vs Explicit Rendering

Thanks to Rails conventions, we do not need to specify the view file to render in our controller. This is known as implicit rendering

In app/controllers/tacos

```ruby
  def index
  end
```

However, if we wanted to be more explicit or if we wanted to render a view template that does not correspond with the action name we could do the following:

```ruby
  def index
    render 'hot_dog'
    # This will render hot_dog.html.erb
  end
```

#### Generate a model

Sometimes, you will need a model but not the related controller, in this case, you will use the model generator:

```bash
rails g model MODEL_NAME [fields]
```

This will generate the model by itself along with the migration containing all the fields and the data types if you wrote them when you typed the console.

---

If we wanted to generate a model that _belongs to_ another: `rails generate model Taco restaurant:references`

This will add the `belongs_to :restaurant` macro to our Taco model and create a migration for the taco table.

#### Generate a migration

In Sinatra you had to create your migrations by hand. As with most things in Rails, theres an generator for that.

```bash
rails g migration add_ingredients_to_tacos ingredients
```

By following certain conventions like the one above, rails will generate a migration that specifically adds the age column to your existing tacos column.

```ruby
class AddIngredientsToTacos < ActiveRecord::Migration[5.1]
  def change
   add_column :tacos, :ingredients, :string
  end
end
```

#### Conclusion

Rome wasn't built in a day and neither can someone learn Rails in a day. Keep coding and it will all come together.

[Rails Guides](http://guides.rubyonrails.org/index.html)

_Content for this readme taken from work originally done by Johann Kerr_
