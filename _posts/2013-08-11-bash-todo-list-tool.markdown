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

Unfortunately, my desktop isn't so clean. I've got all sorts of music and image files lying around,
in addition to miscellaneous stuff that's not related at all to programming. So, I decided to heavily
edit this todo script to accept several command line options that allow a user to set a priority for
a task (top - mid - low), print all the tasks in the todo list, organized by priority, and remove any
task. All the task files, which are the exact same empty files created with the touch command as in
Holman's original, are organized under one directory at ~/Desktop/todo. Within todo, there are three
more directories, todo/top, todo/mid, and todo/low. Each of these represents one priority category.

With my script, any time you're at the shell you can type:

	$ todo -t top-priority-thing
	$ todo -m mid-priority-thing
	$ todo -l low-priority-thing
	$ todo other-top-priority-thing

The options -t, -m, and -l add a given task to directories top, mid, and low, respectively. If we run
todo without any option, our task will be automatically added to the top priority category. This is all
good, but we still have a problem when we want to peruse our list. It's all bundled up inside directories,
and none of us want to cd back and forth or fiddle with finder to see everything. That's where this 
prompt comes in, which prints the todo list to the shell in an organized manner.
	
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

Boom! Finally, one last thing. Removing tasks is probably the easiest operation to complete using just
regular command line tools. However, for those who are lazy, there's this command, which deletes
any task you want, so long as you provide the priority directory.

	$ todo -r mid/mid-priority-thing

That's all, a little todo tool to help you keep track of the stuff you're doing. Thanks to Zach for
getting the ball rolling. For those interested, here's the [full version of my adapted todo script]
[todo].

[article]:http://zachholman.com/posts/inbox-zero-everything-zero/
[todo]:https://github.com/haikus-in-c/haikus-in-c/blob/master/2013.8/todo/todo
