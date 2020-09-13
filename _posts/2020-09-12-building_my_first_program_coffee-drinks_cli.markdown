---
layout: post
title:      "Building my first program, coffee-drinks CLI"
date:       2020-09-12 18:28:02 -0400
permalink:  building_my_first_program_coffee-drinks_cli
---


I've weighed whether I should write about one specific tech aspect of my CLI against describing the process as a whole and ultimately decided that my takeaways from this project were more big picture than anything else and so here we are: a start to finish look at my CLI journey.

### takeaways
* build the CLI from scratch (w/o using Bundler) 
* create an outline and build one step at a time
* Nokogiri means the whole wide web is your oyster (but only if the site is decently organized)
* read other people's code: it's a great way to learn


## Building

After completing nothing but Learn.co labs for the last month I felt like I was swimming without floaties for the first time when I went to set up my project files. Sure, I'd seen it done countless times in walk-through videos and lectures, but initializing something on my own was different. 

At first I created a generic CLI folder and repo because I wasn't 100% set on what my topic was going to be and I just wanted to get set up so I could start playing around. (This is the reason the repo that actually has my project's commit history is called "cli-project" instead of something specific.) Then I watched some more videos and decided I wanted to make my CLI into a gem and was going to build the whole thing using Bundler. So I made another project folder. 

After spending an hour or more playing around in the files Bundler created for me I still wasn't able to get a simple `puts` method to run and was starting to get more and more stressed about the fact that I hadn't yet started the meat of my project. So I ditched the Bundler files and went back to the generic CLI I created.

Long story short: I now have two github repos that both house my completed CLI project: [the one that I used to build it](https://github.com/saschakala/cli-project) and [the one created by Bundler](https://github.com/saschakala/coffee-drinks), which I then copy/pasted my finished project into in order to turn it into a gem.

I know I did it the long and uncessary way, but I'm happy I did. Setting up my own files and playing around with the dependencies, creating an environment, manually entering the shebang and gems I needed in the appropriate places, and experimenting with the different ways to `require` files from the lib folder (`require_relative` vs the `require-all` gem) gave me an understanding of the way the files communicate and need to be structured that I wouldn't have otherwise. 

Plus, after immersing myself in the structure of my files and buildling a program for 5 days (versus creating code to pass Learn tests), I developed an understanding of and comfort level with breaking things that I didn't have previously, which allowed for me to go back to that Bundler file, get it to work, and use it to publish my gem.

### Getting the Bundler file to work

1. `chmod` 

Using `ls -lah` I discovered that the run file I created didn't have executable permissions, meaning I could only read and write it but not run it. There are two ways I could've fixed this:

`chmod 777 file-name` *or* `chmod +x file-name`

`chmod 777` changes the permissions on the file in question to be universally readable, writable, and executable (universally meaning by all users). 

`chmod +x'` adds the permission of being executable by all users to whatever other permissions already exist.

I used `chmod 777` to set the permissions on my run file.


2. `Gem::InvalidSpecificationException`

It was this error initially that caused me to ditch the Bundler file for the project file I had created. I googled around and wasn't able to find a solution that didn't seem very complex and over my head. 

Upon coming back to it 5 days later I realized that all I needed to do was go into my project's `gemspec` file and change the `spec.metadata["source_code_uri"] = ` to be equal to the project's repo on github.



## One method at a time

Blank pages have always given me anxiety. So, the idea of creating something from scratch and doing so in a language I only just started to learn was stressful. The only way I was able to start putting characters on the page was by realizing pretty quickly that it's literally impossible for me to ever "know everything" about coding (which is why Google exists) and [this video](https://www.youtube.com/watch?time_continue=3190&v=_lDExWIhYKI&feature=emb_logo). I watched this video from start to finish and then kept it open in a tab on my browser throughout the entire process, and every step of the way it offered a tidbit that helped me out of a rut.

The outline I built served as a rough map of where the user would start and where they would end up. After creating this bird's eye view as a guide to make sure everything else I did was colored within the lines of this framework, I set about creating the baby steps that would make it happen. I found that anytime I started to think about the big picture I would get too overwhelmed to envision a way to make it happen, so I stopped zooming out and only ever focused on one method at a time.

I built the skeleton of my project without any real data. I created fake instances of my `Drinks` class in order to get all the parts working, so that when I did add my `Scraper` class it would populate an already-functional CLI.

**Everytime I added a new line of code I ran my file. **

I want to debug as I go, not sift through error messages at the end of multiple new methods trying to figure out where exactly my syntax, conditional loops, and method communication was breaking.



## Nokogiri & Scraping

I ended up scraping 3 different sites for my CLI data, but in order to get 3 good ones I scraped many more. By the end I had a method.

* I inspected the page to make sure that it was built hierarchically. If the data I needed to grab didn't have a parent `div` or `li` and some easily targetable `.class`, `#id` or attribute of some kind, it was a no go.

Scraping works in this circumstance because you can iterate over each instance that exists within that targeted parent tier and collect the name/other data from each iteration. 

(Even if the site was built hierarchically and I was able to iterate through and grab a `name` with which to initialize each `Pastry.new(name)` with (the data for the `Pastry` class gave me the most trouble), that didn't mean the rest of the content was accessible to me. Sometimes the site was nested and labeled up to a point, and then I'd get to the actual description and there was nothing good to latch onto it with.)

* If the site appeared scrapable I would set a `class variable` equal to the `url`, create a new method and then go about targeting the parent class. 

> `def self.scrape_new
> doc = Nokogiri::HTML(open(URL))
> binding.pry
> end`

I discovered that an easy way for me to test scrapability was to use

`doc.css("target-attributes-for-parent-tier").css("target-attributes-for-name").text`

because, if it worked, my code was set up for me to copy/paste the initial `.css` target and the `.css` target that I'd pass through in the block to acquire/set the name. (The same thing worked for all the other variables.)


## Studying others' code

A non-insignificant portion of the time I spent on this project was studying similar programs and looking at the ways other people chose to approach methods and structure. It helped me in two ways.

1. Seeing others' logic helps me think through my own.

I'm not used to building a system from scratch, it's a new muscle for me. There are still times when a method or a piece of logic seems "obvious" and so I don't think to include it. (But nothing is obvious to a computer in a world where literally nothing exists yet - is the thing I keep reminding myself.) So seeing the way others have systematically moved through creating a CLI that has no holes helped me envision the steps I'd need to take to do the same.

2. I don't know what I don't know. 

Sure, I can google code snippets to help me solve my problem, but that only gets me as far as making X do Y. 

Is there a more elegant way to achieve X doing Y? Is there a method that I don't know exists and so I'd never think to search for it that would increase the user experience of my gem? Is there a way for me to refactor that only becomes clear in seeing someone else implement it in their own code? 

Reading code that isn't mine makes me a better coder and a better learner. It exposes me to new things I'd otherwise not have seen, forces me to google methods I don't know or don't understand, and allows me to practice logic by explaining back to myself why something works.




