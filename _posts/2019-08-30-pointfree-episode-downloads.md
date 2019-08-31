---
layout: post
title: Pointfree Episode Downloads
slug: pointfree-episode-downloads
date: 2019-08-30 19:21 -0700
---
# {{ page.title }}
A quick guide on how I download pointfree.co episodes. For some reason,
vimeo streams very poorly to the laptop in my home office. This makes
watching pointfree.co episodes problematic with constant buffering or
downgrades in quality that make following text difficult. There are many
ways to download the videos like browser extensions, but I tend to stray
away from that alternative due to a little bit of paranoia. Plus it just
seems wise to keep the browser as light as possible.

Downloading the videos consist of roughly three parts: getting the
cookie for authorization, extracting episode links, and downloading.
Thankfully the largest part, downloading is offload to a convenient python
app called
[youtube-dl](https://ytdl-org.github.io/youtube-dl/index.html). Despite
the name, it handles many formats, vimeo being one of them. The tool does
a lot since it has to get and parse the various metadata and figure out
how to download the chunks.

### Cookie Extraction

Extracting the cookie from the browser is the equivalent of logging in.
The easiest way is again to simply use a browser plugin. The youtube-dl
FAQ suggest
[these](https://github.com/ytdl-org/youtube-dl/blob/master/README.md#how-do-i-pass-cookies-to-youtube-dl).
After you run the plugin just save the file somewhere so you can feed it
into youtube-dl later.

Now if you want to do it the hard way, which, not gonna lie, I tried
because tin-foil-hat 🐣. I am always a little wary of a browser extension
that needed permissions to "Access your data for all websites", even
when they have good reason. Anyways the alternative is you can go to
developer tools, inspect the storage tab, and extract the "pf_session" cookie
and place that content into a plain text file.


![Cookie Storage](/assets/browser-cookie-storage.png)

<!--more-->

Just remember to use the [Netscape cookie
format](http://www.cookiecentral.com/faq/#3.5), which of course I did
not do and ended up giving in and just using the plugin. That being
said, it's easy enough to remove after export. Also quick audit of the
[sauce](https://github.com/lennonhill/cookies-txt/blob/master/background.js)
didn't reveal anything nefarious.

### Episode Link Extraction

While there are many excellent html parsers that could probably be used
in your favorite scripting language, it had been awhile since I'd last
used one and honestly felt a like a little too much overhead. Especially
since I only needed the last dozen or so episodes, if you need more and
it would probably be wise to invest into making this more robust. So
instead I just googled around til I found one that used sed.

{% highlight shell %}
curl -sL pointfree.co | sed -n 's/.*href="\([^"]*\).*/https:\/\/www.pointfree.co\1/p'
{% endhighlight %}

Also what makes this nice is you could just pipe this into youtube-dl.
Regex kung-fu could narrow down this down to just episodes or even
ranges of episodes. Piping to a file and editing in your favorite text
editor also works.

### youtube-dl, aka the easy part

Relatively easy anyways... there is currently a bug in youtube-dl where
it will crash out trying to decompress an empty response from a HEAD
request. Which means you'll have to build from source and manually apply
the patch in
[https://github.com/ytdl-org/youtube-dl/issues/7181](https://github.com/ytdl-org/youtube-dl/issues/7181#issuecomment-148377328).
I think all the dependencies can be obtained from homebrew.  Now that
all of that all the parts are collected, it's relatively straight
forward to just feed all the parts into youtube-dl and walk away. There
is ample documentation via `--help` or the website, but the short of it is:


{% highlight shell %}
youtube-dl \
--cookies /path/to/cookies.txt \
-a /path/to/urls.txt
# -v for verbose
{% endhighlight %}


protip: youtube-dl uses `~/.config/youtube-dl/config` so you could store your
favorite options in there.

{% highlight shell %}
$ cat ~/.config/youtube-dl/config \
-o ~/Movies/%(title)s.%(ext)s \
--cookies ~/path/to/cookies.txt
{% endhighlight %}




