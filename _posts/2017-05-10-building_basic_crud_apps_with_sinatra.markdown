---
layout: post
title:  Building Basic CRUD apps with Sinatra
date:   2017-05-10 17:38:10 -0400
---


Two months in, and I've finished my second project, a Sinatra applicaiton useing basic CRUD (Create, Read, Update, Destroy) techniques and RESTful URLs.

![Jessica Fletcher - Cup of Tea](http://i.imgur.com/5V2ohTU.gif)

You can check out my project at [https://github.com/abonner1/sinatra-booksellers-project](https://github.com/abonner1/sinatra-booksellers-project).

It's not very pretty without CSS, but it's backend is more than functional. The mechanics are all there with the option of expanding the application later as new features are added. The premise of my app is an online used and antiquarian bookseller where any user can add listings of books they are willing to sell.

# Under the Hood
Here's the structure of the app:

```
|---- app
|       |---- controllers
|       |---- models
|       |---- views
|---- config
|       |---- environment.rb
|---- db
|       |---- migrate
|       |---- development.sqlite
|       |---- schema.rb
|       |---- test.sqlite
|---- spec
|       |---- controllers
|       |---- models
|       |---- spec_helper.rb
|---- .rspec
|---- config.ru
|---- Gemfile
|---- LICENSE
|---- Rakefile
|---- README.md
```

I started out using this as an opportunity to try out some TDD (Test Driven Development), but found I need to read up on some details of the process and familiarize myself with the DSL rspec before continuing. I just got an ebook recommended by my Flatiron instructors and will give that a whirl in the coming weeks.

So you can see the gems I used, here is the entire gemfile:

```
source "https://rubygems.org"

# gem "rails"
gem 'sinatra'
gem 'activerecord', :require => 'active_record'
gem 'sinatra-activerecord', :require => 'sinatra/activerecord'
gem 'rake'
gem 'require_all'
gem 'sqlite3'
gem 'thin'
gem 'shotgun'
gem 'pry'
gem 'bcrypt'
gem 'tux'

group :test do
  gem 'rspec'
  gem 'capybara'
  gem 'rack-test'
  gem 'database_cleaner', git: 'https://github.com/bmabey/database_cleaner.git'
end
```

# Creating Valid Models
I tried to keep it manageable with the models. For the sake of this project, a user `has_many` books, and a book `belongs_to` a user.

I'm grateful I took a little time between the Fwitter project and starting on my profolio project since my code review for Fwitter came through with some enlightening insights that made validations a whole heck of a lot easier. Instead, of checking for blank fields manually like this in the controller:

```
!params.values.all? {|v| !v.blank?}
```

... I was able to make validations in the model itself that wouldn't allow record creations and record updates unless the required fields were proven to be filled. It took only one line of code:

```
validates_presence_of :username, :email, :password_digest
```

This macro makes the User class answerable to the method `.valid?`, which will return true if a record to be created or updated matches validation requirements and false otherwise. When a new record doesn't pass validations, it will not be added (or updated) in the database, but no error is raised that outrightly breaks your program. It simply doesn't migrate the change.

# Making the Magic Happen
A perfect example of how this works plays out in my BooksController:

```
get '/books/new' do
    if logged_in?
      erb :'/books/create_book'
    else
      redirect to '/login'
    end
  end

  post '/books' do
    book = current_user.books.create(params[:book])
    if book.valid?
      redirect to "/books/#{book.id}"
    else
      redirect to '/books/new'
    end
  end
```

This "new" action has both a "get" and a "post" route. The get route will only render the `create_book.erb` view when it returns true that a user is logged into their account. Let's focus though on the post route.

Here we see that the book is first created by implicitly adding a newly instantiated book object to the current_users books array. This is a little misleading. The `.create` method is actually doing two things: `Book.new(params[:book])` and `book.save`. If the validations fail (in this case if a user does not have a username, email, or password), only the `.new` method is called, initializing a ruby instance but not saving it, which allows us to call `book.valid?` in the next line even when the instance is not saved in the database. If, however, the validations return true an instance of the book class is both intitialized and saved.

# Dynamic Layouts
Another feature I tested out in this Sinatra project was making dynamic layouts. I wanted to see if there was a way to make a menu the user would see when logged out and another when logged in. Here you can see this was accomplished with a simple if/else statement.

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Bookseller</title>
  </head>
  <body>
    <%if logged_in?%>
    <div><a href="/books">Home</a> - <a href="/users/<%=current_user.slug%>"><%=current_user.slug%></a> - <a href="/logout">Log Out</a></div>
    <%else%>
    <div><a href="/login">Log In</a> - <a href="/">Home</a> - <a href="/signup">Sign Up</a></div>
    <%end%>

    <%= yield %>

  </body>
</html>
```

The major differences in layout appear in the home page, which directs a user to the `/` view when logged out and the `/books` view when logged in. Also, a logged-in user can view there own account.

# Conclusion
Check out my app in github for more details on the rest of the CRUD app. You'll see in the books controller I labeled each action in order to seperate out what parts of the app are doing what. You may notice that I added functionality for updating account informaiton that even includes validating changes with a password. While it works, I want to explore the details of that more closely.

Who knows? Maybe this is the makings of the next abebooks or biblio.com. Propbably not, but I can dream. Cheers!
