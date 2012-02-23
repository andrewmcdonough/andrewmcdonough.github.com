---
layout: post
title: "Ruby Poetry"
date: 2012-02-23 21:02
comments: true
categories: 
---

On Tuesday evening, I gave a talk at the <a href='http://lrug.org'>London Ruby Users Group (LRUG)</a> entitled "Ruby Poetry".  Inspired by <a href='http://twitter.com/hlame'>Murray Steele's</a> "<a href='http://skillsmatter.com/podcast/ajax-ria/my-first-ruby'>My First Ruby</a>" talk at a previous LRUG, I decided to tell a story about a small progam I wrote about five years ago, when I was fairly new to ruby.  At the time, I had just made the transition to ruby after years as Java developer, and I was amazed at how easy it was to solve problems without having to write very much code.

The story starts when I was invited to a party.  The party was a themed <a href='http://en.wikipedia.org/wiki/Burns_supper'>Burns Night</a> dinner.  These celebrations are common in Scotland to celebrate the life and poetry of <a href='http://en.wikipedia.org/wiki/Robert_Burns'>Robert Burns</a>.  I had been invited to the same party the previous year, and remembered back to enjoying the haggis and whisky.  One thing I remembered that had not felt particularly comfortable with, is that I had been asked to bring a poem to read.  When I got there, most people had written their own, and I felt bad for lacking creativity.  I decided that this time around, I would try to be creative in my own way, harnessing the power of ruby to help me.  Rather than telling the story again here, I'll just link to my talk, which was kindly videoed by <a href='http://skillsmatter.com'>Skills Matter</a>:

<a href='http://skillsmatter.com/podcast/home/lrug-custom-documentation-generators'>Ruby Poetry Presentation Video</a>

### The code
``` ruby generate_topical_rhyming_couplets
#!/usr/bin/env ruby

require 'open-uri'
require 'rss'

# Open the list of feeds
fh = open("feeds.txt")
feeds = fh.read.split "\n"

# For each feed, get the headlines
headlines = []
feeds.each do |feed|
  begin
    file = open(feed)
    rss = RSS::Parser.parse(file.read)
    feed_headlines = rss.items.map {|i| i.title}
    headlines += feed_headlines
  rescue
    puts "Error with #{feed}"
  end
end

# Remove duplicates and strip some prefixes
headlines = headlines.uniq!
headlines = headlines.map {|h| h.gsub /^(AUDIO|VIDEO): /,""}

# Test for couplets
def couplet?(a,b)
  as = a.gsub /\W/,"" # Ignore punctuation
  bs = b.gsub /\W/,""
  as[-3,3] == bs[-3,3] &&
    !(as[-5,5] == bs[-5,5]) &&
    (as.length - bs.length).abs < 10 &&
    (as.length > 30 && as.length < 70) &&
    (bs.length > 30 && bs.length < 70)
end

# Sort the headlines by the last three letters
headlines.sort! {|a,b|  a[-3,3] <=> b.reverse[-3,3] }

# Iterate over the headlines, testing in pairs for couplets
i = 0
while (i < headlines.length-2) do
  a = headlines[i]
  b = headlines[i+1]
  if couplet?(a,b)
    i += 1
    puts "#{a}\n#{b}\n\n"
  end
  i += 1
end

```

### Full source on github
I used the program above on the day, but while I was tuning my algorithm, I wrote the headlines to a file, and read them back in while I made changes:
<a href='https://github.com/andrewmcdonough/ruby-poetry'>https://github.com/andrewmcdonough/ruby-poetry</a>

### My Slides

I used <a href='https://github.com/schacon/showoff'>showoff</a> for my slides.  I much prefer it over Keynote or Powerpoint, as it allows you to write your slides in markdown, and keep them in version control.  I have published my presentation on Heroku, and the source for it is <a href='https://github.com/andrewmcdonough/ruby-poetry-presentation'>on github</a>:

<a href='http://ruby-poetry.heroku.com'>http://ruby-poetry.heroku.com</a>

Thanks to <a href='http://twitter.com/joelchippindale'>Joel Chippendale</a> for introducting me to showoff in his LRUG lightning talk two years ago.  I borrowed his <a href='https://github.com/mocoso/showing-off-with-ruby/blob/master/2020.js'>2020.js code</a> for the automatic slide transitions.

### The final poem, as generated on the day of the talk*
<div class='paper'>Eurozone agrees second Greek bail-out
Haye trainer rules out Chisora bout

Man released after Sunday arrest
South Sudan puts Beijing policies to the test

Young carers need more support
Green light for £100m golf resort

Lord of the Flies redesigned
'Typosquatting' prize firms fined

Football with history of conflict
Travel by train though the Lake District

Storm at C-word in BBC weather forecast
Top Irish dancers set for Belfast

Are there more strikes on the way?
I love my ID card. Can they really be taking it away?

London 'second best study city'
Oil sector 'needs tax stability'

Ants remember their enemy's scent
That's enough 'kicking ass' Mr President

One-minute World News
Performance poetry - your reviews
</div>

Note that these were the best generated couplets that were generated, and I reordered a few of the pairs, but not the pairs themselves.
