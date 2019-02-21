---
layout: post
title: "Neo4j and Rails, Part 2 of 2: How to declare relationships and write query in Neo4j"
description: ""
category: Rails 
tags: [neo4j, rails]
excerpt: "We've set up an application with Neo4j and Rails and deployed it to Heroku. From here, we're going to see the ways to declare relationships and write query in Neo4j."
---

<iframe width="50%" height="100" scrolling="no" frameborder="no" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/59131562"></iframe>

Welcome back ! If you missed the [first part](http://vumanhcuongit.github.io/rails/2014/09/16/neo4j-and-rails-how-to-setup-and-deploy---part-1-of-2/), you might want to go back and catch up first.

In previous part, we've set up an application with Neo4j and Rails and deployed it to Heroku. From here, we're going to see the ways to declare relationships and write query in Neo4j.

## Declare Relationships
In Neo4j, every Model object is a node so all has_many/has_one method calls begin with declaration of direction.

Those can be:

* `:in`
* `:out`
* `:both`

So what's that ? This image will tell to you clearly.

![neo4j direction](http://s24.postimg.org/5i15rocud/neo4j_direction.jpg)

`:both` is used when direction is no importance, such as "give me all" or "traverse all" relationships that are either INCOMING or OUTGOING.

Here below are some examples to make it clearer:

  * One Service has many Contacts, so there's an incoming relationship starting from Service.
  * Likewise, one Contact belongs to one Service, so Contact object gets an outgoing relationship.

Now you've understood Neo4j directions. Next, I'll show you how to declare relationship between two models.

{% highlight ruby %}
class Movie
  include Neo4j::ActiveNode
  property :name

  # One Movie has many People
  has_many :in, :people, origin: movie
end
{% endhighlight %}

{% highlight ruby %}
class Person
  include Neo4j::ActiveNode
  property :fullname

  # One Actor acts in One Movie
  has_one :out, :movie, type: :act_in, model_class: Movie
end
{% endhighlight %}

Definition of terms:

  * `origin:` is optionally but the benefit of this is that changing the **relationship types** declared on Person will not require updates to Movie
  * `type:` is type of relationship between two Model object. Why do we need it ? it helps distinguish *relationships* between models. We're in the graph database so there're many relationships between two models.
  ![type relationships](http://s30.postimg.org/oaaq6lu35/type_relationships.jpg)
  * `model_class:` specifies a target class. It's required if you have model subclasses:

  {% highlight ruby %}
  class Movie
    include Neo4j::ActiveNode
    # Does n't use model_class, it's okay
    has_many :in, :people
    # Uninitialized Constant CrazyFans
    has_many :in, :crazy_fans

    # Solve it
    has_many :in, :crazy_fans, model_class: Person
  end
  {% endhighlight %}

### Complex relationships:
If you have to set up complex relationships, you need to use [ActiveRel](https://github.com/neo4jrb/neo4j/wiki/Neo4j%3A%3AActiveRel). You can read this [intructions](https://github.com/neo4jrb/neo4j/wiki/Neo4j%3A%3AActiveRel) to setup this feature. It's easy, and just a *class defining relationship between models*.

I just explain some terms that you may get confused with:

* `from_class` is a class having outgoing relationship.
* `to_class` is a class having incoming relationship.

So if I set up ActiveRel for the above example, `from_class` is Person and `to_class` is Movie.

## How to write the query on Neo4j
You still write some simple queries in *ActiveRecord* like `Movie.find(24)` but  I recommend you to write queries in [Cypher](http://docs.neo4j.org/chunked/stable/cypher-query-lang.html) style.

{% highlight ruby %}
# Find movie what has name 'Inception'
result = Movie.query_as(:movie)
        .where(movie: {name: 'Inception'})
result.last
=> #<Movie name: "Inception">
{% endhighlight %}

{% highlight ruby %}
# Find all actors who act in Inception movie
result = Movie.query_as(:movie)
        .match('movie<-[:act_in]-(actor:Person)')
        .where(movie: {name: 'Inception'})
        .pluck(:actor)
result.last
=> #<Person fullname: "Leonardo Dicaprio">
{% endhighlight %}

You need to understand some important things:

  * you must use **aliases** for query: `:movie`, `:actor`
  * `<-[:act_in]-` this's a **type** of relationship. Direction of the arrow is a **direction of graph** not **relationship** (incoming & outgoing relationship). Don't get confused.

  ![graph direction](http://s28.postimg.org/8gllpfk59/graph_direction.jpg)

I mentioned complex relationships so let's write query for that. 

I assume that we have ActiveRel named **Role** what defines role of actor in a movie.

{% highlight ruby %}
class Role
  include Neo4j::ActiveRel

  from_class Person
  to_class Movie
  type 'belongs_to'

  property :name, type: String
end
{% endhighlight %}

{% highlight ruby %}
# Find production designers of a film
Movie.query_as(:movie)
      .match('movie<-[rel:belongs_to]-(person:Person)')
      .where(rel: {name: 'Production designer'})
      .pluck(:person)
{% endhighlight %}


Bravo, that's all. I've finished the brief introduction about Neo4j and Rails. I hope that you're interested in Neo4j :). Thanks for your concern and if you have any questions, please don't hesitate to leave me comments :).

##References

[Neo4jRB's Wiki](https://github.com/neo4jrb/neo4j/wiki)

[Learn Cypher - the Neo4j query language](http://www.neo4j.org/learn/cypher)

[Getting Started with Neo4j, Ruby 2.1.2, and Rails 4.1.1](http://blog.benmorgan.io/post/88913410501/getting-started-with-neo4j-ruby-2-1-2-and-rails-4-1-1)