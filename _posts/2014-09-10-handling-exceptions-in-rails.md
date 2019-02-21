---
layout: post
title: "Handling Exceptions in Rails"
description: ""
category: Rails
tags:
  - rails
---
Yesterday, I had a task about handling exceptions in Rails. In addition, I need to catch all exceptions happening in runtime. So I searched solutions in the Internet and got advices from my friend.

Sum up, We should setup a class to handle exceptions. There are 3 steps:

###Step 1: Generate the **ErrorsController**
*app/controllers/errors_controller.rb*
{% highlight ruby %}
class ErrorsController < ActionController::Base
  before_action :fetch_exception

  ERRORS = {
    '500' => 'Internal Server Error',
    '502' => 'Bad Gateway',
    '503' => 'Service Unavailable',
    '504' => 'Gateway Timeout'
  }

  def exception
    render :exception, status: @status_code
  end

  private

  def fetch_exception
    @exception = env['action_dispatch.exception']
    @status_code = ActionDispatch::ExceptionWrapper.new(env, @exception).status_code
    @error_type = ERRORS[@status_code.to_s]
  end
end
{% endhighlight %}
###Step 2: Create a view
*app/views/errors/exception.json.jbuilder*
{% highlight ruby %}
json.type @error_type
json.message "Server errors - something went wrong."
{% endhighlight %}
###Step 3: Modify config
*config/application.rb*

{% highlight ruby %}
config.exceptions_app = ->(env) { ErrorsController.action(:exception).call(env) }
{% endhighlight %}
*config/environments/development.rb*

{% highlight ruby %}
config.consider_all_requests_local = false
{% endhighlight %}
*config/environments/test.rb*

{% highlight ruby %}
config.consider_all_requests_local = false
config.action_dispatch.show_exceptions = true
{% endhighlight %}


That's all, and thanks for your concern. If you have any questions, please don't hesitate to discuss with me :).

<del>p/s: I hate this Jekyll theme, it makes code lines of step 1 so ugly. I'm sorry and promise to find alternative theme as soon as.</del>
