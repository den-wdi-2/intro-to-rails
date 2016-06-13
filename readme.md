<!--
Market: SF
-->

![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png)

# Intro to Rails

### Why is this important?
*This workshop is important because:*
- Rails is one of the most popular web development frameworks of all time, known for allowing remarkably  quick builds, it's manageable learning curve, and giant community.
- Rails strict conventions create apps that look familliar, so that any rails programmer can understand a new rails app with minimum effort.

### Objectives
*After this lesson, students will be able to:*

- Create a new Rails app
- Describe similarities between Sinatra and Rails routing
- Generate a controller
- Create RESTful routes & actions
- Create erb views
- Generate activerecord models

### Where should we be now?
*Before this lesson, students should already be able to:*

- Build a Sinatra application
- Explain MVC
- Use ActiveRecord and Postgres to interact to persist data.

## Intro: What is Rails

Rails was created in 2003 by David Heinemeier Hansson, while working on the code base for Basecamp, a project management tool by 37signals. David extracted Ruby on Rails and officially released it as open source code in July of 2004. Despite rapid iteration of the Rails code base throughout the years, it has stuck to three basic principles:

* Ruby Programming Language
* Model-View-Controller Architecture
* Programmer Happiness

Rails was created with the goal of increasing programmers' happiness and productivity levels. In short, with Rails you can get started with a full-stack web application by quickly creating pages, templates and even query functions. Rails heavily emphasizes "Convention over Configuration." This means that a programmer only needs to specify and code out the non-standard parts of a program. Even though Rails comes with its own set of tools and settings, you're certainly not limited to library of rails commands and configurations. Developers are free to configure their applications however they wish, though adopting conventions is certainly recommended.

#### A Look Back

As we look back at the history of Rails, let's review some of the more significant releases over the years.

Release | Year | Features
----- | ------------
**1.0** | Dec 2005 | Basic framework, not too much bigger than Sinatra
**2.0** | Dec 2007 | HTTP basic authentication with cookie store sessions
**2.1** | Nov 2008 | multi-language support through [i18n](http://whatis.techtarget.com/definition/internationalization-I18N), Ruby 1.9 suport
**2.3** | Mar 2009 | Templating with erb, handlebars, etc. built in.
**3.0** | Aug 2010 | New router for controller, mailer controller (built in email support), CRSF protection
**3.1** | Aug 2011 | support for jQuery, SASS, CoffeeScript, Sprockets with Assets Pipeline. **note:** Sprockets loads the files specified, processes them if necessary, concatenates them into one single file and then compresses them
**4.0** | June 2013 | Strong Parameters, Turbolinks
**4.2** | Dec 2014 | Asynchronous mailers, major performance upgrade for ActiveRecord, web console (Rails version of Tux) built in
**5.0** | Right Now | ActionCable (Websockets) built in, API mode built in, ruby 2.2.1 support, Turbolinks is faster.


* Rails 4.0 (June 2013) - Ruby 2.0 preferred; 1.9.3+ required,
* Rails 4.2 (Dec 2014) -
![](http://guides.rubyonrails.org/images/rails4_features.png)
* Rails 5 (Happening now) -

Over the years, Rails has indeed made it easier for beginners to dive into web development and build large complex applications. Some popular websites built on Rails include Twitter (at one point), GitHub and, of course, 37signals' very own Basecamp. Although it has often been criticized for performance and bloat, Rails continues its iterations with an ever-growing developer community and a vibrant ecosystem.

## Demo: A blog in 10 mins

![](http://i.giphy.com/KI9oNS4JBemyI.gif)

The goal of the next few minutes is to show the power that Rails gives us – it's actually possible to create a website with a lot of the functionality you've seen in our Sinatra app – forms, links, database, and MVC structure – in less than 5 minutes. We will not detail each step for this app, but we will create a dynamic website in 5 mins by typing the following commands.

```bash
rails new blog_app -d postgresql
cd blog_app
rails generate scaffold posts title:string content:text author:string
rake db:create
rake db:migrate
rails server
```

Now we'll head over to `localhost:3000/posts`. All of our REST actions are live!

## What is Rails? -- Some details [here](./what-is-rails.md)

## Differences between Sinatra and Rails - Discussion
Take a few minutes to discuss in your table the differences and similarities

## Installing Rails

```bash
  gem install rails
```

## Lets make a Rails App!

#### Create a Rails app with Postgres

For this introduction, we want to create a simple app: a cookbook! The specs for this app are as follows:

* Display a list of all recipes
* Create new recipes and edit existing recipes
* Delete recipes

Rails follow a pattern called "convention over configuration" - this means that by default, a Rails app expects you to follow specific patterns and folder structures. This means you need to learn these conventions, but also means that once you learn them, you save time by not having to setup a lot of the configuration you'd otherwise need to set up manually.

This structure may look a bit complex – there are a lot of files, specific naming conventions, and some nested files and folders. We generally don't create this structure manually, but instead use the Rails command line tool, which initializes the app for us:

```bash
  rails new cookbook -d postgresql
```

Great! We just created the initial folder structure for a Rails app, and because we added the option `-d postgresql`, this app will be initialized with PostgreSQL for the database engine – which we will use later on in the lesson.

> **Note:** By default, if you *do not* add any option for the database, Rails will create the app with SQLite3. While you are working in a local development environment (localhost), you won't notice much of a difference between SQLite3 and PostgreSQL.

> Once your app is in production on a remote server, you will *not* use SQLite, and they will often use PostgreSQL. A best practice in web development is to keep development and production environments as similar as possible, so we recommend using PostgreSQL from the start.

Now, let's go into the cookbook folder:

```bash
  cd cookbook
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

#### Discussion:
Talk to your table about the file structure. We have been making our Sinatra projects look like Rails as much as is reasonable, so much will be familiar. What is unfamiliar?

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
	get "/books" do
		# Here is the code that will be executed when the client requests /books for example:
    @books = Book.all
    erb :index
	end
```

This is handy for us as developers, because it allows us to keep everything in the same place - routing and controller logic - but if the app grows it can get unreadable. Imagine, for example, an app that has 20 or 30 different routes... your main routes file could contain a lot of complex code.

Rails has a "routing engine" that separates the routing logic from the controller logic (what we want to happen when routes are requested). The configuration for this routing engine is in the file `config/routes.rb`.

```ruby
#config/routes.rb
Rails.application.routes.draw do
  get "/books", to: 'books#index'
end
# Everything between the `do` and the `end` will be code related to handling routes for the current application.


#app/controllers/books_controller.rb
class BooksController < ApplicationController
  # this will render the /views/books/index.html.erb view by default!
  def index
    @books = Book.all
  end

  # controller methods that simply render the appropriate view file and don't need any dynamic variables just look like this:
  def new
  end
end
```


Later on in this lesson we will go into detail about handling routes inside a Rails application.

#### Generate a controller

As Rails is an MVC framework, we will need to have controllers to handle requests and call the database through models.

In Rails, the controllers are files inside the folder `app/controllers`. If you open this folder, you will see that one controller is already here: the file `application_controller.rb`. This controller does not directly handle HTTP requests, but rather serves as a link between all the controllers we will create, `application_controller.rb` will be the parent of all the controllers in our app.

There are 3 different ways to create a controller in Rails:

1. We can manually create a file and write the ruby code inside it.
2. We can use a generator called `controller`, using `rails g controller CONTROLLER_NAME [ACTIONS]`. For instance, if we want to create a controller for the resource `recipes` with an action and a view for `index` and `show`, we would type in the console `rails g controller recipes index show`. This command would create a bunch of files and modify some others:

   * The controller itself, `recipes_controller.rb` inside `app/controllers`
   * The views for each method , in this case
     * `app/views/index.html.erb`
     * `app/views/show.html.erb`
   * The routes handlers for these two actions will also be added to config/routes.rb :
     * `get '/recipes/index', to: 'recipes#index'`
     * `get '/recipes/show', to: 'recipes#show'`

3. We can use the `scaffold_controller` generator. This generator will create the same files as the previous generator but with a REST logic for routes, controller and views.

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

Rails has a generator called `scaffold` that will create the whole MVC structure for a resource, let's say that inside the cookbook app, we want the `Recipe` resource to have a title and a content field, we would type:

```ruby

rails g scaffold recipe title content:text

```

Running this command will generate a lot of files, including the controller, the views, the model, and the migration. It will also update the routes file.

Take a look at the controller, it has all the RESTful methods, and these methods already contain the code to query the database through the model `Recipe`.

#### Create Views

There is no specific generator that will create only a view file, but you can add them manually into the appropriate folder inside views.

For instance, if we want to add a view file `about` for the resource `Recipe`, we can create a file `about.html.erb` in `app/views/recipes`.

If a view is "static" - as in, it doesn't use any instance variables created in the controller - you can just create a route for this view and rails will render it in the browser even if there is no method in the controller:

In config/routes.rb

```ruby
get "/recipes/about", to: 'recipes#about'
```

If there is a file `about.html.erb` in `app/views/recipes`, this file will be automatically rendered when you call `localhost:3000/recipes/about`


#### Generate a model

Sometimes, you will need a model but not the related controller, in which case, you will use the model generator:

```
rails g model MODEL_NAME [fields]
```

This will generate the model by itself along with the migration containing all the fields and the data types if you wrote them in the console.

###Check for Understanding

<details>
  <summary>What does `rails new` do? What does `rails generate` do? What about scaffold?</summary>
  <p>Rails new creates a new rails app with the basic folder structure in a blink of an eye! Rails generate can generate different portions of your app like controllers, models. Scaffold is an all in one that generates models, controllers and even migration and test files for a particular resource!</p>
</details>

## Closing Thoughts
- No one ever understood Ruby on Rails an hour after they started their first app!

## Additional Resources
- [Rails Guides](http://guides.rubyonrails.org/)
- Check out [Rails Conference lectures](https://www.google.com/search?q=rails+conference&oq=rails+conference+&aqs=chrome..69i57j0l5.4088j0j4&sourceid=chrome&ie=UTF-8#q=rails+conference&tbm=vid)
- [RailsTutorial.org](https://www.railstutorial.org/)
- [Zero-to-Sixty: Creating and Deploying a Rails App in Under an Hour](http://code.tutsplus.com/tutorials/zero-to-sixty-creating-and-deploying-a-rails-app-in-under-an-hour--net-8252)
