---
layout: post
title: "Dictionary Optimization with the Google Chrome Omnibox"
author: "Ian Zapolsky"
comments: true
category: posts
---

Here's a fairly common scenario I have been running into this semester: I'm 
reading something in Spanish at the computer, I come across a word I don't know.
I look at it, contemplate looking it up, and continue reading. It's borderline
terrifying that I'm so spoiled by the speed of modern technology that I consider
the time it takes to open a new browser tab, google my favorite Spanish-English 
dictionary site, and enter the word I'm looking for to be prohibitive, but I do.

But perhaps this superhuman level of laziness is what will ultimately drive the
next wave of technological advances necessary to achieve the ultimate informational nirvana
toward which the internet and modern search engine power is moving, a world
in which you can instantly recieve information about things you look at in real
time.

I recently decided to take my own small step toward this goal by writing two
simple but effective Google Chrome extensions to help optimize the process
of translating words and phrases between Spanish and English, and conjugating
Spanish and English verbs.

<!--more-->

Both extensions utilize the Chrome Omnibox, the URL bar at the top of the browser, 
and a builtin API component for Javascript written for Chrome extensions.
By specifying a keyword, we can reserve a namespace in this URL bar to interact with some Javascript
code that we provide. In this case, our Javascript will do nothing more than open a new
tab on either a Spanish-English dictionary site, or a Spanish-English verb conjugation site,
with the results of the query entered after the Omnibox keyword. 

Take a look at the code below for FastTranslate. First we create the manifest file,
which Chrome uses to gather basic information from all extensions. In this file
we also specify the reserved Omnibox keyword which will trigger the Javascript
contained in background.js.

{% highlight yaml %}
{
  "name": "Fast Translate",
  "description" : "To use, type 'trans', then two spaces, and then a word or phrase
                   in either spanish or english that you want to translate.",
  "version": "1.0",
  "permissions": [ "tabs", "http://www.linguee.es/" ],
  "background": {
    "scripts": ["background.js"]
  },
  "omnibox": { "keyword" : "trans" },
  "manifest_version": 2
}
{% endhighlight %}

Now when a user types "trans" into the URL bar, the Omnibox will effectively
transform into the equivalent of a query bar on any site we specifiy in our
Javascript file. In this case, we will use a Spanish-English dictionary site.

{% highlight javascript %}
// background for fast_translate
// by Ian Zapolsky (09.10.13)

// quick omnibox extension that links any query directly to a query
// on linguee, my favorite online translator


chrome.omnibox.onInputChanged.addListener(
  function(text, suggest) {
    console.log('inputChanged: ' + text);
    suggest([
      {content: text + "hola", description: " spanish word: hola"},
      {content: text + "hello", description: " english word: hello"},
      {content: text + "como estás", description: " spanish phrase: como estás"},
      {content: text + "how are you", description: " english phrase: how are you"}
    ]);
  });

chrome.omnibox.onInputEntered.addListener(
  function(text) {
    console.log('inputEntered: ' + text);
    var url = "http://www.linguee.es/espanol-ingles/search?source=auto&query="+text;
    chrome.tabs.create({ url:url });
  });
{% endhighlight %}

The code behind FastConjugate, the conjugation extension I wrote, is almost identical,
with the query URL changed to the conjugation website. Check out both of these
tools, and download packaged .crx versions to use yourself, [here][spanish_extensions].

While simple, these tools are really effective at optimizing a simple task, one
that due to the internet and the power of computers is getting closer and closer
to being instantaneous.

[spanish_extensions]:https://github.com/ianzapolsky/spanish_extensions
