---
layout: post
title: "todo -t write-todo-blog-post"
author: "Ian Zapolsky"
comments: true
category: posts
---

Recently I read an [article by Zach Holman][article], an engineer at Github and the guy 
whose jekyll theme I adapted to build this site, about a simple (read: *very* simple) 
todo script he wrote to keep track of his tasks. Here's how it works: any time you're at 
the shell, you can type:

	$ todo thing-i-have-to-do

This touches a file at ~/Desktop/thing-i-have-to-do. Here's the code of the script itself.

{% highlight bash %}
#!/bin/sh
set -e

touch ~/Desktop/"$*"
{% endhighlight %}

Obviously, this tool assumes that the user keeps a tidy Desktop and cleans it regularly. Otherwise,
it just wouldn't be feasible to keep track of all the todo files. In his own words, [Zach writes][article], 

> I keep a clean desktop: under three icons at a time, preferably. It made sense to use my
> desktop as a kind of bucket I need to empty....

Unfortunately, my desktop isn't so clean. <!--more-->I've got all sorts of music and image files lying around,
and I'm too lazy to clean it all up. So, I decided to heavily edit this todo script to keep all the
task files, which are the exact same emtpy files created with the touch command as in Holman's original,
organized by priority under one directory at ~/Desktop/todo. Within todo, there are more directories, todo/top,
todo/mid, and todo/low. Each of these represents one priority category.

With my script, any time you're at the shell you can type:

	$ todo -t top-priority-thing
	$ todo -m mid-priority-thing
	$ todo -l low-priority-thing
	$ todo other-top-priority-thing

The options -t, -m, and -l add a given task to directories top, mid, and low, respectively. If we run
todo without any option, our task will be automatically added to the top priority category. 

The -p option prints the todo list to the shell in an organized manner 

	$ todo -p

Assuming our previous input, this would return:

{% highlight bash %}
/// TOP PRIORITY ///
  - other-top-priority-thing
  - top-priority-thing

/// MID PRIORITY ///
  - mid-priority-thing

/// LOW PRIORITY ///
  - low-priority-thing
{% endhighlight %}

Boom! Finally, removing tasks is probably the easiest operation to complete using just
regular command line tools. But since laziness was a major design principle in this
project I added the -r option, which searches the priority directories for a file and
deletes it if it finds it.

	$ todo -r mid-priority-thing

That's all, a little todo tool to help you keep track of the stuff you're doing. Thanks to Zach for
getting the ball rolling. For those interested, here's the [full version of my adapted todo script]
[todo].

[article]:http://zachholman.com/posts/inbox-zero-everything-zero/
[todo]:https://github.com/haikus-in-c/haikus-in-c/blob/master/2013.8/todo/todo
