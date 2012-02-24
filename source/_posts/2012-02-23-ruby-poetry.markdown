---
layout: post
title: "Ruby Poetry"
date: 2012-02-23 21:02
comments: true
categories: 
---

On Tuesday evening, I gave a 20 slide 'lightning talk' at the <a href='http://lrug.org'>London Ruby Users Group (LRUG)</a> entitled "Ruby Poetry".  Inspired by <a href='http://twitter.com/hlame'>Murray Steele</a>'s "<a href='http://skillsmatter.com/podcast/ajax-ria/my-first-ruby'>My First Ruby</a>" talk at a previous LRUG, I decided to tell a story about a small progam I wrote about five years ago, when I was fairly new to ruby.  At the time, I had just made the transition to ruby after years as Java developer, and I was amazed at how easy it was to solve problems without having to write very much code.

The story starts when I was invited to a party.  The party was a themed <a href='http://en.wikipedia.org/wiki/Burns_supper'>Burns Night</a> dinner.  These celebrations are common in Scotland to celebrate the life and poetry of <a href='http://en.wikipedia.org/wiki/Robert_Burns'>Robert Burns</a>.  I had been invited to the same party the previous year, and remembered back to enjoying the haggis and whisky.  One thing I remembered that had not felt particularly comfortable with, is that I had been asked to bring a poem to read.  On the evening, instead of brining poems, most people had written their own, and I felt bad for lacking creativity.  I decided that this time around, I would try to be creative in my own way, and write a ruby program to generate some topical poetry from the day's news headlines.  I won't go into too much detail here, as my five minute talk  was kindly videoed by <a href='http://skillsmatter.com'>Skills Matter</a>:

<a href='http://skillsmatter.com/podcast/home/lrug-custom-documentation-generators'>Ruby Poetry Presentation Video</a>

## The final code
The code below is what I used to generate the couplets on the day.  While I had been tuning my algorithm, I wrote the headlines to a file so I didn't have to reload them eachc time. The full source is <a href='https://github.com/andrewmcdonough/ruby-poetry'>on github</a>.

``` ruby generate_topical_rhyming_couplets
#!/usr/bin/env ruby
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

As the couplet finding algoritm was fairly crude, only looking for matches of the last three letters, and not the phonetic reprentation, I allowed myself to pick the best couplets as generated on the day.  To demonstrate my code, I reran the program on the day of my talk, and read the best couplets it generated at the end:

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

I particularly like the last one, which like the others, was genuinely generated on the day.

So the two talks I have given at LRUG have been entitled Ruby Poetry and <a href="rubygolf-presentation.heroku.com">Ruby Golf</a>.  What Ruby &lt;insert word here&gt; should I do next?
