--- 
layout: post
title: "Hosting a Static Site with Pow"
---

# {{ page.title }}

I'm a big fan of [pow](http://pow.cx) and [jekyll](http://jekyllrb.com/) and thought it'd be interesting to host the local copy of my blog with pow. Here's a simple way to do it.

Pow deals exclusively with rack apps. So, create a config.ru in the directory for your blog that looks like this:
{% highlight ruby %}
run lambda {|env| [200, {}, ['']}
{% endhighlight %}

Create a public dir which contains your blogs content. In my case this meant symlinking \_site to public:
{% highlight bash %}
$ ln -s _site public
{% endhighlight %}

Make sure you ignore the public dir if using jekyll:
{% highlight bash %}
git/jackdempsey master > cat _config.yml 
pygments: true
exclude:
- Rakefile
- README.textile
- public
{% endhighlight %}

Add the directory to pow. Either symlink it into .pow or use the powder gem:
{% highlight bash %}
$ powder 
{% endhighlight %}

At this point you should be good to visit http://blogdirectoryname.dev and see your site, properly making use of css etc as well (the html I generate for the actual site never used to look right locally given absolute paths).

HTH
