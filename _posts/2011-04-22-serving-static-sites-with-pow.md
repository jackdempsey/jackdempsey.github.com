--- 
layout: post
title: "Hosting a Static Site with Pow"
---

# {{ page.title }}

I'm a big fan of [pow](http://pow.cx) and [jekyll](http://jekyllrb.com/) and thought it'd be interesting to host the local copy of my blog with pow. Here's a simple way to do it.

<strike>Pow deals exclusively with rack apps. So, create a config.ru in the directory for your blog that looks like this:</strike>

**Update:** Sam just let me know that rackup file isn't needed:
  *"Pow automatically serves static files in the public directory of your application. It's possible to serve a completely static site without a config.ru file as long as it has a public directory."*

Create a public dir which contains your blogs content. In my case this meant symlinking \_site to public:

    $ ln -s _site public

Make sure you ignore the public dir if using jekyll:

    $ cat _config.yml 
    pygments: true
    exclude:
    - Rakefile
    - README.textile
    - public

Add the directory to pow. Either symlink it into .pow or use the powder gem inside the directory:

    $ gem install powder
    $ powder 

At this point you should be good to visit http://blogdirectoryname.dev and see your site, properly making use of css etc as well (the html I generate for the actual site never used to look right locally given absolute paths).

HTH
