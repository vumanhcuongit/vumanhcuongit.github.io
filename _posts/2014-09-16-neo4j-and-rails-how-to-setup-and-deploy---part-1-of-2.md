---
layout: post
title: "Neo4j and Rails, Part 1 of 2: How to setup an application and deploy it to Heroku"
description: ""
category: Rails
tags: [neo4j, rails]
excerpt: "This past week, I had a chance to use Graph Database with Rails. At the first time, I felt hard to set up as well as apply Neo4j in Rails application. So I write this tutorial to share my experience that may help you through bad days ~*yaoming*~."
---

<iframe width="50%" height="100" scrolling="no" frameborder="no" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/92895430"></iframe>

This past week, I had a chance to use Graph Database ([Neo4j](http://www.neo4j.org/)) with Rails. At the first time, I felt hard to set up as well as apply Neo4j in Rails application. So I write this tutorial to share my experience that may help you through bad days ~*yaoming*~.

According to my plan, I'll write 2 parts:

* How to setup an application and deploy it to Heroku.
* How to declare relationships and write query in Neo4j.

## Part 1: How to setup an application and deploy it to Heroku

## Setup Neo4j

First things first, we need to install Neo4j on our computer. This is a [link](http://neo4j.com/download/) to download Neo4j. We can run this command to check if it successfully installed:

{% highlight powershell %}
$ neo4j start
{% endhighlight %}

Now, we can check out `http://localhost:7474`. If it's installed successfully, it displays like this:

![Neo4j Browser](http://s7.postimg.org/mar3rbvcr/neo4j_browser.png)

Now, we create rails app for demo purpose:

{% highlight powershell %}
$ rails new neo4j_playground
{% endhighlight %}

And then, we inlucde neo4j gem in this `Gemfile`:

{% highlight ruby %}
gem 'neo4j', github: 'andreasronge/neo4j'
{% endhighlight %}

When we `bundle`, it should n't get errors. After that, we create a controller, model and views:

{% highlight powershell %}
rails generate scaffold Contact name:string email:string
{% endhighlight %}

Now, we're going to use model the Neo4j way, not ActiveModel way. Let's replace model with this below code:

{% highlight ruby %}
class Contact
  include Neo4j::ActiveNode

  property :name
  property :email
end
{% endhighlight %}

You should notice that this model doesn't inherit from ActiveRecord::Base as usual. Instead, we include this thing called `Neo4j::ActiveNode` which is the ActiveRecord replacement module for Rails.

We don't use `ActiveRecord` so we don't run any migrations either. Let's delete `db/migrate` folder.

Go to `config/application.rb` and use this code:

{% highlight ruby %}
require File.expand_path('../boot', __FILE__)

require "rails"

%w(
  neo4j
  action_controller
  action_mailer
  sprockets
).each do |framework|
  begin
    require "#{framework}/railtie"
  rescue LoadError
  end
end

Bundler.require(*Rails.groups)

module Neo4jPlayground
  class Application < Rails::Application
    config.generators do |g|
      g.orm :neo4j
    end

    config.neo4j.session_type = :server_db
    config.neo4j.session_path = ENV['GRAPHENEDB_URL'] || 'http://localhost:7474'
  end
end
{% endhighlight %}

Alright, we no longer use `require "rails/all"` and use `%w( ... ).each { ... }` instead. Because `rails/all` loads five modules. Rails, ActiveRecord, ActionController, ActionMailer, and Sprockets but as I mentioned, we don't need *ActiveRecord*, so we replace `active_record` with `neo4j`.

Look inside `Neo4j::Application`, you’ll see it using some neo4j configuation methods:

  * `session_type` we have two ways to install Neo4j `:server_db` and `:embeddable_db`. [Read More](https://github.com/neo4jrb/neo4j-core/wiki#installation).
  * `session_path` is the location of our database. If we’re on Heroku, we’ll be using `GrapheneDB`, but we hook up to `http://localhost:7474` if we’re on development/test.

Well, We did a lot of things. To complete **Setup Neo4j** section, we'll configure some things.

We comment some code lines:

{% highlight ruby %}
config/environments/development.rb
# config.active_record.migration_error = :page_load
{% endhighlight %}

{% highlight ruby %}
config/environments/production.rb
#config.active_record.dump_schema_after_migration = false
{% endhighlight %}

Within the `Rakefile`, add this line:

{% highlight ruby %}
require 'neo4j/tasks/neo4j_server'
{% endhighlight %}

Now `rails s` and it works ~*bravo*~. Now you can use **CRUD** for *Contact object*.

## Deploy application to Heroku

Well done, Let's deploy to Heroku:

{% highlight powershell %}
$ git init
$ git add .
$ git commit -m "initial commit"
$ heroku create
$ git push heroku master
{% endhighlight %}

Then, you'll get ... this error:

{% highlight powershell %}
Preparing app for Rails asset pipeline
Running: rake assets:precompile
rake aborted!
Errno::ECONNREFUSED: Connection refused - connect(2) for "localhost" port 7474
{% endhighlight %}

If you get it, you're on the right way :). Because we store database in `http://localhost:7474` on development. However, on production, we need to use `GrapheneDB` instead. So we set up our Graph Database:

{% highlight powershell %}
$ heroku addons:add graphenedb:chalk
{% endhighlight %}

Notice: *chalk* is a free package. [Read More](https://addons.heroku.com/graphenedb)

Let's push to production again:

{% highlight powershell %}
$ git push heroku master
{% endhighlight %}

Wow, we deploy it sucessfully !. You're the nice guy because you're so ... patient :)

![Patient](http://s17.postimg.org/njccrvp9r/Pissed_Tobey_Maguire.jpg)
