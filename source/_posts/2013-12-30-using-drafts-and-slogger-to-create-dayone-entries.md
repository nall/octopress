---
layout: post
title: "Using Drafts and Slogger to Create DayOne Entries"
date: 2013-12-30 15:09:09 -0800
comments: true
categories: productivity
---

## The Problem
For a while I've wanted to be able to quickly jot down things that have
happened to me during the day (micro-updates) and have all of those sucked into
[Day One](http://dayoneapp.com) on a per-day basis. Unfortunately, Day One
doesn't provide any sort of URL callback handler to allow appending to an
entry.

## The Solution
Lack of appending ability in Day One left me with creating some temporary file
that could be updated during the day and creating a single Day One entry from
the contents of that file each day.

<!-- more -->
I wanted to use [Drafts](http://agiletortoise.com/drafts) to capture my
micro-updates. I use it for other actions already, so I'm familiar with it. It
has a huge selection of post-processing actions available and provides ways to
customize those actions. Specifically, it allows access to
[Dropbox](http://dropbox.com) and that's where I'll keep the temporary file of
micro-updates.

Once I have a file of micro-updates I need to create a Day One entry. This
lands squarely in the land of cron/launchd. There's already a utility to suck
in data from various sources and create Day One entries. It's called
[Slogger](http://brettterpstra.com/projects/slogger/) and I'm already using it
for logging [FitBit](http://www.fitbit.com) and [Untappd](http://untappd.com)
data. I just needed to write a plugin that would handle my temporary file.

With those pieces in place, I could capture micro-updates in Drafts, have them
appended to a file kept on Dropbox and have Slogger create appropriate Day One
entries each night when it runs. Sounds good! Here are the pieces.

## Nuts & Bolts

### Drafts Action
Below is the importable Drafts action to create & append to the temporary file on Dropbox. The files are stored at:
{% codeblock %}
/Apps/Drafts/DailyLogs/DayOne-YYYY-MM-DD.md
{% endcodeblock %}

To import this, click on the below URL in iOS:

[drafts://x-callback-url/import_action?type=dropbox&name=Daily%20Log&path=%2FApps%2FDrafts%2FDailyLogs%2F&filenametype=2&filename=DayOne-%5B%5Bdate%7C%25Y-%25m-%25d%5D%5D&ext=md&writetype=2&template=%23%23%20%5B%5Bdate%7C%25H%3A%25M%3A%25S%5D%5D%20-%20%5B%5Btitle%5D%5D%0A%0A%5B%5Bbody%5D%5D%0A](drafts://x-callback-url/import_action?type=dropbox&name=Daily%20Log&path=%2FApps%2FDrafts%2FDailyLogs%2F&filenametype=2&filename=DayOne-%5B%5Bdate%7C%25Y-%25m-%25d%5D%5D&ext=md&writetype=2&template=%23%23%20%5B%5Bdate%7C%25H%3A%25M%3A%25S%5D%5D%20-%20%5B%5Btitle%5D%5D%0A%0A%5B%5Bbody%5D%5D%0A)

Now when you choose that action, your Drafts text will be appended to the
current day's file preceded by a timestamp. It's easy enough to tweak that
template to your liking once you've imported it into Drafts.

### Slogger plugin
To setup Slogger, I'll refer you to the [Slogger website](http://brettterpstra.com/projects/slogger/).
This assumes you have it up and running.

The Slogger plugin isn't specific to Drafts or Dropbox (though it's configured
for this by default). It simply looks for files with a particular pattern and
generates Day One entries from them. The plugin is available at
[basicfile.rb](https://github.com/nall/Slogger/blob/master/plugins_disabled/basicfile.rb).
You can drop that file in your Slogger **plugins** directory and you're good to
go. The defaults should work with the rest of this blog post, but you may want
to customize the options:

#### basicfile\_dir
This is the directory in which to search for files. Currently the files must be
named DayOne-YYYY-MM-DD.md. 

#### basicfile\_tags
This is a set of tags to apply to each daily entry in Day One. The default is
\#dailylog

#### basicfile\_archive
This is a boolean that specifies whether temporary files are archived after
being processed. If set to true, files are copied to:
{% codeblock %}
/Apps/Drafts/DailyLogs/Archive
{% endcodeblock %}

If set to false, the files are removed after processing.

### Bonus: Alfred Integration
Drafts is great and all, but what if you want to capture something while in
OSX? I use [Alfred](http://www.alfredapp.com) for all of my OSX keyboard-fu, so
I wrote a small workflow to append to the temporary file. It's available
[here](http://nall.github.io/files/DayOneLog.alfredworkflow).


## Summary
With all of this in place, I now have a way to quickly jot down daily updates
and have those all logged into Day One at a daily granularity. I hope this
saves someone the work if they've wanted a similar functionality!


