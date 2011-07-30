--- 
layout: post
title: "Rails 3.1: Understanding Sprockets and CoffeeScript"
---

{{ page.title }}
================

[Peter Cooper](http://www.petercooper.co.uk/) recently posted a great intro 
[How to Play with Rails 3.1, CoffeeScript and All That Jazz Right Now](http://www.rubyinside.com/how-to-rails-3-1-coffeescript-howto-4695.html).
I'd like to follow on from his post with some more examples and specific challenges I faced last night as I hacked an existing
rails 3 app into the structure of 3.1.

Stylesheets
-----------

Let's start with some simple changes. We now have two locations for our stylesheets: ``vendor/assets/stylesheets`` and ``app/assets/stylesheets``.
The same way you used to put external lib code into ``vendor/plugins`` you can now put those externally required css files into ``vendor/assets/stylesheets``. For example, I'm using the [jquery-tokeninput](http://loopj.com/jquery-tokeninput) project, 
and have put the tokeninput css files into the vendor dir:

    example_app rails31 > ls -l vendor/assets/stylesheets/token-input
    total 32
    -rw-r--r--  1 jack  staff   2.5K Apr 21 23:46 token-input-facebook.css
    -rw-r--r--  1 jack  staff   4.6K Apr 21 23:46 token-input-mac.css
    -rw-r--r--  1 jack  staff   2.0K Apr 21 23:46 token-input.css

You then tell sprockets to require these files in your main application.css:

{% highlight css %}
example_app rails31 > cat app/assets/stylesheets/application.css 
/*
 * FIXME: Introduce SCSS & Sprockets
 *= require "style" 
 *= require_tree ../../../vendor/assets/stylesheets/token-input
 */
{% endhighlight %}

The require_tree bit seems ugly. Hopefully I've done something stupid and there's a cleaner way to bring in those stylesheets.

You'll also see ``require "style"`` in that file as well. That file exists in the main ``app/assets/stylesheets`` directory:

    example_app rails31 > ls -l app/assets/stylesheets
    total 16
    -rw-r--r--   1 jack  staff   128B Apr 22 10:20 application.css
    -rw-r--r--   1 jack  staff   1.8K Apr 21 23:46 style.css.scss

Sprockets will load that file and convert it into css and package it up, so when your browser requests ``/assets/application.css``, 
you'll get one file which contains the style.css content, as well as token-input.

To include the stylesheet in your app, you can use the stylesheet_link_tag helper, which is now sprocket aware:

{% highlight haml %}
example_app rails31 > cat app/views/layouts/application.html.haml 
!!! 5
%html
  %head
    %title Example App
    = stylesheet_link_tag :application
    = javascript_include_tag :application
    = csrf_meta_tag 
{% endhighlight %}

To enable sprockets, make sure you have this at the bottom of your ``config/application.rb``:

{% highlight ruby %}
# Enable the asset pipeline
    #...
    config.assets.enabled = true
  end
end
{% endhighlight %}

When you have sprockets enabled, then that ``stylesheet_link_tag :filename`` will look in ``/assets`` for filename.css in the ``app/assets/stylesheets`` folder, 
and then munge things together as explained.



I actually spent some time trying to pass stylesheet_link_tag :defaults, :all, etc. until I realized that what you specify there will be looked for in the assets folder, and then read by Sprockets
to actually build the content for that resource.

Javascripts
-----------

The same ideas hold true for the javascript side. When you generate a new Rails 3.1 app, jquery is a default, and you'll see it in the
``vendor/assets/javascripts`` directory:

    example_app rails31 > ls -l vendor/assets/javascripts
    total 480
    -rw-r--r--  1 jack  staff   207K Apr 21 21:31 jquery.js
    -rw-r--r--  1 jack  staff    22K Apr 21 23:46 jquery.tokeninput.js
    -rw-r--r--  1 jack  staff   4.4K Apr 21 21:31 jquery_ujs.js

You get jquery 1.5 at this point, though I imagine that will change soon as 1.6 is right around the corner. 

You'll also see jquery_ujs. This file is the new rails.js. Finally, you'll see my jquery.tokeninput.js as well. 

### Update

This has changed with new versions of Rails. The javascript files are no
longer copied into your application, but served out of the
``vendor/assets/javascripts`` directory in the jquery-rails gem. See 
https://github.com/rails/jquery-rails/tree/master/vendor/assets/javascripts
for info.

To use these files, setup a ``app/assets/javascripts/application.js`` like so:

{% highlight javascript %}
example_app rails31 > cat app/assets/javascripts/application.js 
// FIXME: Tell people that this is a manifest file, 
// real code should go into discrete files
// FIXME: Tell people how Sprockets and CoffeeScript works
//
//= require jquery
//= require jquery_ujs
//= require jquery.tokeninput
//= require_tree .
{% endhighlight %}

_Side note: the FIXME bits are from the Rails team. This stuff is brand new, and will likely get better explanations over time._

The first three requires bring in the files in ``vendor/assets/javascripts``, while the last ``require_tree .`` tells 
sprockets to require everything in the same directory. The require order is alphabetical, so you may have to move things around or be
more specific to correctly get your dependencies in order.

It looks like the requires know about the ``vendor/assets/javascripts`` directory, and yet it didn't seem to work for the 
stylesheet tokeninput case. Will have to dig into it more.

CoffeeScript & SCSS
-------------------

Using these techs is as easy as naming your files appropriately. One file I didn't expressly call out in my application.js file is the initializing file for the token input code:

    example_app rails31 > ls -l app/assets/javascripts 
    total 24
    -rw-r--r--  1 jack  staff   245B Apr 21 23:46 application.js
    -rw-r--r--  1 jack  staff    66B Apr 21 23:46 initialize.token-input.js.coffee

The leading initialize. is just my own convention to clarify what the file is doing. It's quite simple:

{% highlight javascript %}
example_app rails31 > cat app/assets/javascripts/initialize.token-input.js.coffee 
$ -> $('#friends').tokenInput '/friends.json', crossDomain: false
{% endhighlight %}

At first I forgot the leading ``$ ->`` and was surprised when the token input code wasn't initialized. The JS was generated correctly, but of course
it wasn't wrapped in a document ready bit, so remember to keep that piece in there. 

The same sort of thing works for SCSS files: the standard rails naming convention of name.generated_mime_type.type_of_file is in play
here as well, so just create a foo.css.scss file and let sprockets do the rest. 


Wrap Up
-------

I like these new changes. You don't have to use them, but for those that have been clamoring to make JS more of a respected citizen in 
a rails app, your day has come. 

I always find it hard to learn a topic, then forget that knowledge when writing up a how-to, so if I've glossed over something here
or there are other bits that confuse you, please drop a comment. One thing I didn't go into here was the new app/assets/images directory. Maybe in 
a future post.

Update
------

Hopefully the above was helpful. If you're looking for a "real" app to take a try at, check out this example I made with Rails 3.1, Node.js, CoffeeScript, and Socket.IO: [Edgeside Chat](https://github.com/jackdempsey/edgeside_chat)
