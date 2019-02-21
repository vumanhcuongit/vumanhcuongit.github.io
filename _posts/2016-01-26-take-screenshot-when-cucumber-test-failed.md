---
layout: post
title: "Take a screenshot any time Cucumber's scenario failed on CircleCI"
description: ""
categories:
  - Testing
tags:
 - cucumber
 - screenshot
 - circleci
excerpt: "Last month, our team faced a *headache* issue relating to running Cucumber's test on Circle CI"
---

> I used to spend hours on debugging Cucumber's tests on CircleCI but now I do well.

My company uses [Cucumber](https://cucumber.io/) and Selenium for running acceptance tests. Whenever one of scenarios is not passed on CircleCI, we'll run it on the local computer to fix it (ya I know developer's life is too short to wait for running all tests completely on local computer :yaoming:). However, last month when we ran failed scenarios on local computer, they passed without any errors. It's so weird!

As it turns out, running Cucumber's tests in local mode is very different from Continuous Integration mode. Therefore, I needed to know what the hell was happening with Cucumber's tests on CircleCI. To spot the cause of failing tests easier, I would like to get screenshot of current webpage any time a scenario fails.

I add some code in [After hooks](https://github.com/cucumber/cucumber/wiki/Hooks) to tell Cucumber to take a screenshot when a scenario fails.

{% highlight ruby %}
# features/support/env.rb

After do |scenario|
  if scenario.failed?
    timestamp = "#{Time.zone.now.strftime('%Y-%m-%d-%H:%M:%S')}"
    screenshot_name = "screenshot-#{scenario.title}-#{timestamp}.png"
    screenshot_path = "#{Rails.root.join('tmp/capybara')}/#{screenshot_name}"
    Capybara.page.save_screenshot(screenshot_path)
  end
end
{% endhighlight %}

Now when a test fails, I can see a screenshot appear in the specified directory

![screenshot in development mode](http://s13.postimg.org/4pfmwcezb/Screen_Shot_2016_01_26_at_4_35_07_PM.png)

Well, That's it.

Hold on, have I said that I need screenshots on CircleCI?

Fortunately, CircleCI provides [**$CIRCLE_ARTIFACTS**](http://blog.circleci.com/build-artifacts/) folder, in which we can write files and it will show all files in this folder after build is finished. I'll update the code a little bit so I can store and open screenshots at **$CIRCLE_ARTIFACTS**.

{% highlight ruby %}
# features/support/env.rb

After do |scenario|
  if scenario.failed?
    timestamp = "#{Time.zone.now.strftime('%Y-%m-%d-%H:%M:%S')}"
    screenshot_name = "screenshot-#{scenario.title}-#{timestamp}.png"
    screenshot_path = "#{ENV.fetch('CIRCLE_ARTIFACTS', Rails.root.join('tmp/capybara'))}/#{screenshot_name}"
    Capybara.page.save_screenshot(screenshot_path)
  end
end
{% endhighlight %}

The trick is that using [fetch (Hash)](http://ruby-doc.org/core-2.3.0/Hash.html#method-i-fetch) to get the directory of **$CIRCLE_ARTIFACTS** by **ENV["CIRCLE_ARTIFACTS"]** whose value provided by CirceCI. In the other hand, if we're running tests in developement mode or value of **ENV["CIRCLE_ARTIFACTS"]** is empty, screenshots will be stored in *tmp/capybara*.

After the build is finished, we can see screenshots on tab **Artifacts**.

![screenshot in CircleCI](http://s27.postimg.org/5czg9bpur/screenshot_on_Circle_CI.png)

Hope, you will save a lot of time debugging. Happy hacking!