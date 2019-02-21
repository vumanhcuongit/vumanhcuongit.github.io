---
layout: post
title: "Design notification system with Elixir, Postgres and Cachex"
description: ""
categories:
  - System Design
tags:
 - elixir
 - postgresql
 - database
excerpt: "Let’s explore how to design an Elixir application using GenStage, Postgres and Cachex to get the latest data, prepare data, send notifications to users, and caching."
hashtag: notification_system
---

One feature Yojee brings to its users is the chance to get latest events triggered by drivers/partners that requires their attention and/or action.

Here's a quick list of example interactions:

  - driver accepted a job  (eg. Driver John accepted job#1)
  - driver rejected a job  (eg. Driver Nguyen rejected job#1)
  - partner transfered an item (eg. Company A transferred item#1)

Whenever a user is partly one of these interactions, we want to update his notification feed, update the number of unseen and unread notifications and listen to changes in realtime.

Let’s explore how to design an Elixir application using GenStage, Postgres and Cachex to get the latest data, prepare data, send notifications to users, and caching.


Here’s what the rest of this post looks like:
- [Introduction to notification](#introduction)
- [High level design](#design)
- [Database schema](#database)
- [Events](#events)
- [Processing pipeline](#pipeline)
- [PubSub service](#pubsub)
- [Notifcation service](#notification-service)
- [Memory cache](#cache)

## <a name="introduction"></a>Introduction to notification

*Activity* is the block of content which are stored in a notification feed box. It follows the specification of [Activity Streams](https://www.w3.org/TR/activitystreams-core/) which makes a developer’s job much easiser. Every activity stores at least:

- Actor (the user triggers action)
- Verb (the action, eg. accepted, rejected, transferred)
- Object (the object the action is related to)
- Time (the time of the activity)

For example

{% gist 187a8be2369d2c700932c5a911fc3383 %}

*The notification feed box*

![notification feed box](https://i.ibb.co/GsccHFJ/Screen-Shot-2019-02-17-at-12-32-28-pm.png)

## <a name="design"></a>High level design

![high_level_design](https://i.ibb.co/0fRjD9j/activity-diagram-1.png)

### <a name="database"></a>Database schema

![db_schema](https://i.ibb.co/LJvZcBq/activity-stream-db-1.png)

Example data:

{% gist d52cb8f04807297540c8251474caec24 %}

### <a name="events"></a>Events
Using [GenStage](https://hexdocs.pm/gen_stage/GenStage.html) to send and/or receive raw data where `Events` is a `producer` that will emits raw data, which are parsed and packed into activities by `consumer` `EventHandlers`.

These are examples of raw event and activity:

{% gist d1866d8d49d3efed8e82d1c2bab0469f %}

### <a name="pipeline"></a>Processing pipeline

There is also a simple pipeline defined in `ProcessingPipeline` where it first saves the activity into database. Then the activity is sent to user in the next stage. Finally, the activity also is written into memory cache.

![ProcessingPipeline](https://i.ibb.co/PgTsWTz/Untitled-Diagram-3.png)

{% gist 194c808bffdff049d3bc278d2ab86c82 %}

### <a name="pubsub"></a>PubSub service

To be able to broadcast the messages to all users in our application, we are going to use the `phoenix_pubsub` with PG2 PubSub adapter. We just need to add it in the dependencies and configure it in our phoenix app.

{% gist ac91ae54f7a76be57c108125e9e2ecf3 %}

### <a name="notification-service"></a>Notification service

The NotificationService has the following business logic:

 - return notifications of specific user
 - return the number of unseen count
 - mark a notification as read/unread
 - mark all notifications as seen

### <a name="cache"></a>Memory cache

Using [Cachex](https://github.com/whitfin/cachex) to store the user's notification information in the memory cache:

 - keep only several hundred activity ids for each user in the memory cache
 - store activity ids has not been read
 - store the number of ids has not been seen

The cache structure is:

{% gist 90f07924420a57172060a95d2390ce98 %}

Some CacheService's functions:

{% gist 28a55453d377233fa31f31c50d3d975a %}

### Recap

In this article, we’ve followed a ActivityStream specification to create the notification's content and we’ve seen how to use Cachex to write data into memory cache.


*happy hacking*
