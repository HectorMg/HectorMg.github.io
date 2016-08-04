---
layout: single
title: Creating a bank simulation application \#2&#58; Setup
category: development
---

Hey guys! Welcome to part two of my bank simulation application series.
In this post I'll guide you guys through some basic setup for the
application. I'll keep this post short and simple, and I won't go too
deep into the details or explaining how and why certain things work
because I assume you either already have some experience using Ruby on
Rails or its something that you can easily run a Google search on.

## Setup

First of all, I'll create a git repository for my new project. I use
Github, so I'll just create my new repository which you can follow
[here](https://github.com/HectorMg/bank-simulation-app.git). I named the
repo "bank-simulation-app", because I think that pretty much sums the
project up.

I'll be using Rails 5 for this project, and if you plan on following through, I
suggest you do the same. Make sure Rails 5 is installed on your machine
before creating your new rails project. For information on how to
install Ruby on Rails 5, I suggest you visit [the official Ruby on Rails
website](http://rubyonrails.org).

Now, I'll create a new rails application in my local machine.
I'm using the `-T` tag to create the project without a test directory,
not because I'm not testing, but because I prefer RSpec over Rails'
default Test::Unit.

```bash
$ rails new -T bank-simulation-app
```

Then I'll navigate to my new rails app, initialize it as a git
repository, and add remote origin to link it to my Github repo. While
I'm here, I'll go ahead and push my whole local repo to the remote
master branch.

```bash
$ cd bank-simulation-app/
$ git init
$ git remote add origin https://github.com/HectorMg/bank-simulation-app.git
$ git add .
$ git commit -m "Initializes repository"
$ git push origin master
```

Now that my basic rails app is up on Github, I'll create a new branch
called `setup` where I'll add some configuration.

```bash
$ git checkout -b setup
```

The first file I'll be modifying is the Gemfile.

#### Gemfile
```ruby
source 'https://rubygems.org'

gem 'rails', '~> 5.0.0'
gem 'sqlite3'
gem 'puma', '~> 3.0'
gem 'sass-rails', '~> 5.0'
gem 'uglifier', '>= 1.3.0'
gem 'coffee-rails', '~> 4.2'
gem 'therubyracer', platforms: :ruby
gem 'jquery-rails'
gem 'turbolinks', '~> 5'
gem 'jbuilder', '~> 2.5'


group :development, :test do
  gem 'byebug', platform: :mri
  gem 'rspec-rails'
  gem 'factory_girl_rails'
  gem 'shoulda-matchers'
end

group :development do
  gem 'web-console'
  gem 'listen', '~> 3.0.5'
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
end

gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```
Other than eliminating unnecessary comments and a couple of the default
gems, I only kept what I think is necessary. Make sure to uncomment the
'therubyracer' gem, and if you plan on running tests with RSpec, go
ahead and add the three gems I added to the development/test group.

I'll be writing tests for my project, however, I won't go through any of that
in my blog posts, so if you wish to test your application, you can look
through my [repo](http://github.com/HectorMg/bank-simulation-app) and
try to understand (and replicate) my testing suite as it is updated in
each branch.

Even though I won't be walking you through the actual testing, I'll
still walk you through the RSpec setup I'll be using. First, we'll go
ahead and install RSpec.

```bash
$ rails generate rspec:install
```

Then I'll add the shoulda-matcher gem's configuration to the
`rails_helper.rb` file.

#### spec/rails_helper.rb
```ruby
RSpec.configure do |config|
  .
  .
  .
  Shoulda::Matchers.configure do |config|
    config.integrate do |with|
      with.test_framework :rspec
      with.library :rails
    end
  end
end
```

That's pretty much it for the project's initial setup! In the next post
I'll be talking about the application and we'll start the actual
development!

I hope you were able to follow along with the initial setup, if you have
any questions, please go ahead and email me, or if you think there's a
problem I've missed, go ahead and open up an issue on [Github](https://github.com/HectorMg/bank-simulation-app/issues)!

Either way, if you'd like, you can clone my working repository up to
this point and follow the rest of this series from there!

`$ git clone https://github.com/HectorMg/bank-simulation-app.git -b setup`
