---
layout: post
title: "Foolproof Guide to Github Pages with a Custom Domain"
author: "Ian Zapolsky"
comments: true
category: posts
---

Recently I spent some time wading through the internet's primordial soup of CS help sources - Stack 
Overflow commments, random blogs (like this one!), and source documentation that leaves something to
be desired - to set up this site, which is hosted on Github Pages with a custom domain. I've since 
decided to migrate the hosting of my [personal website][ianzapolsky.com] to Github Pages as well,
because it's such a great platform and allows for almost instantatneous editing and changing. Instead 
of using clumsy cPanels and browser-based editors, you can just make edits to your site files 
locally in whatever text editor you want, add, commit, push, and it's live. However, there are 
a couple tricky spots to getting your custom domain Github Pages hosted site up and running. 
But since I've done this setup process twice now, I'm going to cover it from scratch, 
so I can garuntee that by the end of this, your website will work too!

I'm only going to assume one thing for this tutorial: that you already have a Github account. If you
don't that's no problem, just go set one up and follow their super clear instructions verbatim. Then
come back here.

Now, first thing's first. If you don't own the domain you want to use, go buy it.  Having used
[GoDaddy][godaddy] before, I can tell you that it kind of sucks, and is in no way meant for 
anyone who considers themself to be even a shadow of a hacker. I've also tried [DreamHost][dreamhost]
which has a much cleaner interface than GoDaddy, but doesn't offer that much more in the way of 
features. Finally, I've read good things about [Namecheap][namecheap], but have never personally 
tried it. Ultimately, these services all do the same things, and where you buy your domain from 
doesn't matter, as long as they aren't some scam company.

Once you own your domain, pull up your Github account page and create a new repository. Name
it your-username.github.io. The Github account for this blog is haikus-in-c, so the Github repository
that this site is hosted out of is named haikus-in-c.github.io. It must be named this way for
Github Pages reasons, and it is important. Now, set up a version on your local machine:

	$ mkdir ~/your-username.github.io
	$ cd ~/your-username.github.io
	$ git init
	$ touch CNAME 	// add this file, but leave it blank for now
	$ git commit -m 'first commit'
	$ git remote add origin https://github.com/your-username/your-username.github.io.git
	$ git push origin master

Excellent. Now you have a repository on your local machine pointing to your-username.github.io on
Github's servers. This directory will hold the body of your site, and it's kind of like a more advanced
version of a public\_html directory.

I say more advanced because Github Pages repositories are also capable of building Jekyll sites. 
I'm going to make a bold assumption that your site is powered by [Jekyll][jekyll], but if it's not,
have no fear. I only make this assumption because *a lot* of Github Pages users use Jekyll, because
Github actually uses Jekyll to serve every page it hosts. If you are using Jekyll, I'll assume you've
already set it up (if you haven't there's [great documentation][jekylldocs] out there). I recommend
using the [Jekyll test site][jekylltest] to get started with Github Pages, because it's guaranteed to
build every time.
	
If you're not using Jekyll, or even if you are and don't want to deal with the test site, just load a 
simple index.html file into your your-username.github.io directory with some test content in it. This
is just there to test that everything is functioning properly with Github Pages before we move on. Once
index.html is added and edited, add, commit, and push from the github pages directory:
	
	$ git add *
	$ git commit -m 'added test site'
	$ git push origin master

Now, in your web browser of choice navigate to www.your-username.github.io. If you're looking at the 
result of whatever HTML, Jekyll, or other types of files you just pushed to the your-username.github.io 
repository, then you're ready to move on. If you're looking at a 404 page, or some other type of error, it's
probably one of two things. Either the Github servers haven't updated to see your freshly pushed data yet
(though in my experience this always happens really quickly), or your site didn't build for some reason. You
should also recieve an email from Github when this happens. There are a number of possible problems that
can cause this to happen: check the [software versions][githubversions] that the Github Pages' servers use
and make sure they're not causing any problems with your site. If that's not it, try building your site
on your local machine (if you're using Jekyll this is really easy). See the [github docs][githubdocs] for
more troubleshooting.

Once you've got a site showing at www.your-username.github.io you're finally ready to add your custom domain.
cd back into your-username.github.io and, using your favorite text editor, change CNAME to contain your
domain name. Use [ours][haikusCNAME] as an example. Then type:
	
	$ git add *
	$ git commit -m 'added domain to CNAME'
	$ git push origin master

Now, go back to the site where you bought your domain from and navigate to whichever account page allows you to
manage your domains. Then click on the link that says something like "DNS" or "Edit DNS". You can learn more
about DNS [here][DNS], but basically what we're doing here is telling the internet that anytime it recieves
a request for your domain name, it should point whoever is sending that request to the Github Pages
servers, which is where your site now lives.

Using the DNS editing options, add an A record from your domain's root apex that points to the IP address 204.232.175.78, 
which is Github Pages' server, and a CNAME record from www that points to your root apex (or the hostname of your site).

Note that there is a difference between the way GoDaddy and DreamHost handle the root apex and the www. 
If you're using GoDaddy, you must add an A record from '@' (the root) pointing to the IP address given above, and 
a CNAME record pointing from 'www' to @. If you're using DreamHost, you have to add an A record with no name (which
DreamHost automatically interprets to be the root apex) that points to the Github IP address, and then 
a CNAME record pointing from 'www' to your-username.github.com (as opposed to @). Below are screenshots of the DNS edit screens looking the way they should for GoDaddy, then DreamHost.

GoDaddy: note that '@' (top) is set to point to 204.232.175.78 and 'www' (bottom) points to '@'
![GoDaddy](/images/godaddy_img.png)
DreamHost: note that there's an A record with a blank name pointing to 204.232.175.78 (which means the same
as GoDaddy's '@') and a CNAME record pointing from 'www' to ianzapolsky.github.com.
![DreamHost](/images/dreamhost_img.png)

Wait a while for these DNS changes to propogate through the system, and your Github Pages hosted site should
be live on your custom domain! 

[ianzapolsky.com]:http://ianzapolsky.com/
[godaddy]:http://www.godaddy.com/
[dreamhost]:http://www.dreamhost.com/
[namecheap]:http://www.namecheap.com/
[jekyll]:http://jekyllrb.com/
[jekylldocs]:http://jekyllrb.com/docs/home/
[jekylltest]:https://github.com/jekyll/test-site
[githubversions]:https://github.com/github/pages-gem/blob/master/github-pages.gemspec#L16
[githubdocs]:https://help.github.com/categories/20/articles
[haikusCNAME]:https://github.com/haikus-in-c/haikus-in-c.github.io/blob/master/CNAME
[DNS]:http://en.wikipedia.org/wiki/Domain_Name_System
