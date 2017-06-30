---
layout: post
title:  "My first Ruby CLI gem! travel_inspiration"
date:   2017-06-26 11:53:34 -0400
---


In brainstorming for the theme of my Ruby CLI gem, I toyed with a few ideas.  I love Travel / Food / Arduino projects, and all three themes have plenty of resources available for scraping.  So I asked myself:  **What problem will you like to solve?**

I decided I will tackle the problem:  **Where should I explore next?**

For some people, they have a bucketlist of places they want to visit before they die.   But for others who don't have a list in mind, how will they decide which countries they want to visit next?   

For my parents, I noticed they often plan trips based on a theme.  For architecture and history, they decided to visit the UK and France.  For sakura blossom viewing, they planed a Spring trip to Japan.  And when they wanted a more active trip, they visited my sisters in Seattle and South California.

Going through [Lonely Planet's website,](https://www.lonelyplanet.com/) I noticed there's a Travel Inspiration section on the homepage!

![](https://preview.ibb.co/fVDniQ/Lonely.png)

1. There are 12 themes
2. Upon selecting a theme, the [next page](https://www.lonelyplanet.com/adventure-travel/) displays tips, blogs, videos, related tours, and 6 recommended destinations.
3. Selecting a destination, the [next page](https://www.lonelyplanet.com/new-zealand) displays top experiences, survival guide filled with useful information and related articles!


## Let's review the project requirements:

1. Provide a CLI
2. CLI must provide access to data from a web page.
3. The data provided must go at least one level deep, generally by showing the user a list of available data and then being able to drill down into a specific item.
4. The CLI application can not be a Music CLI application as that is too similiar to the other OO Ruby final project. Also please refrain from using Kickstarter as that was used for the scraping 'code along'. Look at the example domains below for inspiration.
5. Use good OO design patterns. You should be creating a collection of objects - not hashes.
6. For bonus points, instead of just creating an application, create a gem and for extra bonus points try publishing it to RubyGems.*

Reviewing the project requirements, I was confident I can achieve all points using the Lonely Planet website as my primary source of content.

I decided to name my Ruby CLI Gem: **travel_inspiration**

## My Ruby Gem provides:
1. A CLI with an interfact that users can interact with

2. Scrape data from Lonely Planet's website

3. Provide three level of data!  First, the CLI displays 12 travel themes.  User will select a theme, and the top 6 destinations will be shown (second level).  User can then select a destination to view more information, and the CLI will display the Best Time to Visit, country introduction summary, and more details about the country (third level).

4. The Ruby Gem is a travel themed application

5. Following the Single Responsibility Principle (SRP), I spliit my code up to four files:
**cli.rb** : code handling CLI display logic and user input
**themes.rb** : code scraping first level of data, displaying 12 themes for user selection
**destinations.rb** : code scrpaing second level of data, displaying 6 destinations for user selection
**country.rb** : code scrpaing third level of data, displaying country information and seasonality data.

6. I published my gem to RubyGems, you can find [travel_inspiration here!](https://rubygems.org/gems/travel_inspiration) . You can also view my[ Github repo code](https://github.com/lisaychuang/travel_inspiration_gem), and a [video demo](https://youtu.be/anf_NTuTVuQ) here!

## How do I create a Ruby Gem?

First step to creating a Ruby gem is, well, learning how to create a Ruby Gem!  A quick Google search returns over 1million results! 

In Avi's video walkthrough, he mentioned we can use Budler to create a gem. Following the [Budler guide](https://bundler.io/v1.14/guides/creating_gem.html), I created a Ruby gem:

```bundle gem travel_inspiration```

This command pre-populated a basic directory with:
```
- bin
**- lib**
.gitignore
**travel-inspiration.gemspec**
Gemfile
License.txt
Rakefile
**README.md**

```

## What files are pre-populated by bundle gem?

### /bin directory

In the ```bin``` directory, there are two files: ```console , setup```

Following the pre-populated comments in the ```console``` file, I uncommented the line ```require "pry"``` as I will be using Pry during development.

### /lib directory

In the ```lib``` directory, there is a sub directory ```/travel_inspiration```

I'm treating this as my ```app``` folder, where all ruby files ```.rb``` will go.   The bundler gem has also created  ```version.rb``` file where I will note version changes of each gem release.

```
module TravelInspiration
  VERSION = "0.1.3"
end

```

Another pre-populated file was ```travel_inspiration.rb``` . For a smaller, single-purpose app, you can put all code in this file.

However, I know I will be following the Single Responsibility Principle and creating different ```.rb``` files for each of my content scraper and the CLI logic from my planning.  So I simply used this file to define all dependencies:

```
#OpenURI is a wrapper for Net::HTTP, Net::HTTPS and Net::FTP
#Nokogiri parses and searches XML/HTML

require 'open-uri'     
require 'nokogiri'      

#all other file dependencies

require "travel_inspiration/version"  
require "travel_inspiration/themes"
require "travel_inspiration/cli"
```

### Other pre-populated files

*  ```.gitignore```  defines paths to ignore in git versions

*  ```Gemfile```  [define a project gems dependency](http://b.j15e.com/ruby/2013/06/28/gemfile-gemspec-explained.html), deal with dependencies version and lock versions to use the same environment across developpers & deployments.

When using a Gemfile, bundler is resolving each gem's gemspec and gather them all to find out what conflicts might occurs and what which most up to date version of a gem is compatible among all your dependencies.

**Taking a closer look, I noted ```Gemfile``` includes ```gemspec ``` as path to other gem and library dependencies.  **

```source "https://rubygems.org"

# Specify your gem's dependencies in travel_inspiration.gemspec
gemspec

```


* ```travel_inspiratoin .gemspec``` is the way to define a ruby gem, publish it on rubygems.org and install them with the ```gem``` command.


In this file, there's many pre-populated fields and comments. This is where I defined attributes of my gem such has the gem name, authors details, gem summary & description, executables, require paths and dependency versioning.

```
lib = File.expand_path("../lib", __FILE__)
$LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)
require "travel_inspiration/version"

Gem::Specification.new do |spec|
  spec.name          = "travel_inspiration"
  spec.version       = TravelInspiration::VERSION
  spec.authors       = ["Lisa Huang"]
  spec.email         = ["lisa.yc.huang@gmail.com"]

  spec.summary       = %q{"A ruby gem to view travel inspiration by themes"}
  spec.description   = %q{"A ruby gem to view Lonely Planet travel inspiration by themes. You will learn about the top 6 destinations!"}
  spec.homepage      = "https://github.com/lisaychuang/travel_inspiration_gem"
  spec.license       = "MIT"

  spec.files         = `git ls-files -z`.split("\x0").reject do |f|
    f.match(%r{^(test|spec|features)/})
  end
  spec.bindir        = "bin"
  spec.executables   = ["inspire_me"]
  spec.require_paths = ["lib"]

  spec.add_development_dependency "bundler", "~> 1.15"
  spec.add_development_dependency "rake", "~> 10.0"
  spec.add_development_dependency "pry", "~> 0.10.4"
  spec.add_dependency "nokogiri", "~> 1.8"
  spec.add_dependency "colorize", "~> 0.8.1"
end

```

It was important to note that there are[ two types of dependencies](http://guides.rubygems.org/patterns/#semantic-versioning):  runtime & development.

**Development dependencies** are useful for other developers who wants to make modifications to your gem, if your gem is open to contributions. RubyGems guide notes:

> When you specify development dependencies, another developer can run gem install --dev your_gem and RubyGems will grab both sets of dependencies (runtime and development). Typical development dependencies include test frameworks and build systems.

I have included ```bundler, rake, and pry```  as development dependencies.

**Runtime dependencies** are what your gem needs to work for end users who installs your gem!

I have included ```nokogiri and colorize```  as development dependencies.

[Colorize](https://github.com/fazibear/colorize) is a Ruby gem developed by [Michał Kalbarczyk](Michał Kalbarczyk), an engineer based on Poland. It is a:

> Ruby gem for colorizing text using ANSI escape sequences. Extends String class or add a ColorizedString with methods to set text color, background color and text effects.

I included this gem in my CLI logic, highlighting questions requesting user input / input / returned results.  This gave me flexibility to **design the presentation view of my CLI.**



* ```README.md``` finally, this is the file that will display on my Github repo by default!  In this file, I have included a quick introduction to the gem, how users can download the gem, how developers can check out the repo, contribution guidelines and license information!













