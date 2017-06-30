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


## Time to create my Ruby CLI gem

To start, I will need to scrape the THEME data from  [Lonely Planet's website,](https://www.lonelyplanet.com/) website.  I created a new file in my ```/lib``` directory: ```themes.db``` 

### Themes.rb

The purpose of this file is to: SCRAPE Lonely Planet homepage's Travel Inspiration THEMES.

This is achieved through three class methods:  

1. **self.list_theme_names**
   RETURN the result from scraping, listing 12 themes

2. **self.scrape_themes**
    * Access [Lonely planet homepage](https://www.lonelyplanet.com) through open-uri
		* Parse webpage to XML using Nokogiri
		* Select the corresponding themes carousel slides
		* Loop through themes, creating a new instance per theme assigning name and theme URL
		* Push each theme into an array: themes_list
		* Return themes_list array

3. **self.url_for_theme_name(theme_name)**
   Create the URL for each theme, used to scrape the top 6 destinations in ```destinations.rb```

In hindset, I didn't need the ```self.url_for_theme_name(theme_name)``` method since I have already stored theme URLs in each instance for step 2.


```
require 'nokogiri'
require 'open-uri'

module TravelInspiration
    class Theme
        attr_accessor :name, :url

        def self.list_theme_names
            self.scrape_themes
        end

        def self.scrape_themes
            themes_list = []
            doc = Nokogiri::HTML(open("https://www.lonelyplanet.com/"))
            themes = doc.search('div.slick-track a.slick-slide') #selects carousel slides
            themes.each_with_index{|theme, index|
                if index < 12 then
                    new_theme = TravelInspiration::Theme.new #create theme instance
                    new_theme.name = theme.css("p").text     #assign theme name
                    new_theme.url = theme.attr("href")       #assign theme url for destination #scraping
                    themes_list[index] = new_theme
                end
            }
            themes_list
        end

        def self.url_for_theme_name(theme_name)
            "https://www.lonelyplanet.com/#{theme_name.downcase.gsub!(/[\s,]+/,"-")}/"
        end
    end
end
```


### Destinations.rb

The purpose of this file is to: SCRAPE each THEME website for TOP DESTINATIONS.

This is achieved through two class methods:  

1. **self.list_destination_names(theme_name)**
   RETURN the result from scraping, listing 6 destinations

2. **self.scrape_destinations(theme_name)**
    * Access selected [THEME webpage](https://www.lonelyplanet.com/adventure-travel/) through open-uri
		* Parse webpage to XML using Nokogiri
		* Select the TOP DESTINATIONS section
		* Loop through destinations, creating a new instance per destination assigning name, continent and destination URL
		* Push each theme into an array:  list
		* Return list array


```
require 'nokogiri'
require 'open-uri'

module TravelInspiration
    class Destination
        attr_accessor :name, :continent, :url
        
        def self.list_destination_names(theme_name)
            self.scrape_destinations(theme_name)
        end

        #scrape data using URL
        def self.scrape_destinations(theme_name)
            list = []

            url = TravelInspiration::Theme.url_for_theme_name(theme_name)
            doc = Nokogiri::HTML(open(url))
            destinations = doc.search('div.SightsList-wrap a') #selects 6 destinations 
            
            destinations.map.with_index{ |destination, index|
                new_destination = TravelInspiration::Destination.new #create destination instance
                new_destination.name = destination.css('h5').text
                new_destination.continent = destination.css('p').text
                new_destination.url = destination.attr("href")
                list[index] = new_destination
            } 
            list.sort_by! {|obj| [obj.continent, obj.name]}
            list
        end
    end
end
```

### Country.rb

The purpose of this file is to: SCRAPE each COUNTRY website for seasonality information and country information.

This is achieved through five instance methods:  


1. **initialize**
    Create new instance of Country
 
2. **country_info**
   * Return both seasonality and country information 

3. **url**
    Create the URL for each country
		
In hindset, I didn't need the this method since I have already stored country URLs in each instance in ```destinations.rb```

4. **scrape_info**
    * Access selected [COUNTRY webpage](https://www.lonelyplanet.com/taiwan) through open-uri
		* Parse webpage to XML using Nokogiri
		* Select the [COUNTRY INTRODUCTION](https://www.lonelyplanet.com/taiwan/introduction) section
		* Assign summary quote and country information to each instance


5. **scrape_season**
    * Access selected COUNTRY webpage through open-uri
		* Parse webpage to XML using Nokogiri
		* Select the [SURVIVAL GUIDE - WEATHER](https://www.lonelyplanet.com/taiwan/weather) section
		* Assign high season, low season, shoulder season to each instance


```
require 'nokogiri'
require 'open-uri'
require_relative 'themes.rb'
require_relative 'destinations.rb'

module TravelInspiration
    class Country
        attr_accessor :name, :url, :high_season, :low_season, :best_visit_season, :summary_quote, :details
 
        def initialize(name)
            @name = name
        end    

        def country_info
            scrape_season #set seasonality information
            scrape_info #set country name, summary_quote and details

            puts "\t#{@best_visit_season}!
                  #{@high_season}
                  #{@low_season}\n"
            puts "\n#{@summary_quote}".blue.bold
            puts @details
        end

        #scrape country information
        def scrape_info
            doc = Nokogiri::HTML(open(url))

            info_url = doc.search("#introduction article.love-letter a").attr("href").text
            info_page = Nokogiri::HTML(open(info_url))

            @summary_quote = info_page.search("div.copy--body p.copy--feature").text
            
            subtitles = info_page.search("div.copy--body h2").to_a
            subtitles = subtitles.map {|s| s.text}

            descriptions = info_page.search("div.copy--body p.copy--body").to_a
            descriptions = descriptions.map {|d| d.text}
            descriptions.reject!{|item|
                item.downcase.end_with?("writer")
            }

            @details = subtitles.map.with_index{|title, index|
                 "\n" + title.upcase.red + "\n" + descriptions[index] + "\n"
            }
        end

        #scrape best time to visit from Country/weather website
        def scrape_season
            doc = Nokogiri::HTML(open(url))
            
            seasonality_url = doc.search("#survival-guide ul li:nth-child(2) a").attr("href").text #get country weather url
            weather_pg = Nokogiri::HTML(open(seasonality_url))
            
            #get high, low, and best time to visit information
            @high_season = weather_pg.search("div.card--page__content p:nth-child(1)").text
            @low_season = weather_pg.search("div.card--page__content p:nth-child(5)").first.text

            shoulder_season = weather_pg.search("div.card--page__content p:nth-child(3)").first.text
            @best_visit_season = "Best time to visit " + shoulder_season[16..-1]
        end

        def url
            downcased = @name.downcase
            dasherized = downcased.gsub(/[\s,]+/,"-")
            "https://www.lonelyplanet.com/" + dasherized
        end
    end
end
```

### CLI.rb

Finally, all the logic are tied together in ```cli.rb``` !

The purpose of this file is to: DEFINE the logic of what is displayed in the CLI based on user input

This is achieved through seven instance methods:  


1. **start**
    INITIATE gem 
 
2. **list_themes**
   * Welcomes user and explain the purpose of this gem.
   * Prompt user to select a TRAVEL THEME
   * Display 12 THEMES (from ```themes.rb```)

3. **select_theme(theme_arr)**
    * User will select a theme with number 1 -12
    * Control flow based on user input:  
    * IF the input is valid, the corresponding 6 TOP DESTINATIONS will be displayed 
    * IF the input is valid, prompt user to select a valid theme

4. **list_destinations(theme_name)**
    * Display 6 COUNTRIES (from ```destinations.rb```)
    * Prompt user to select a COUNTRY

5. **select_country(country_arr)**
    * User will select a theme with number 1 - 6
    * Control flow based on user input:  
    * IF the input is valid, the corresponding COUNTRY INFORMATION will be displayed 
    * IF the input is valid, prompt user to select a valid theme
    
6. **country_details(chosen_country)**
    * Display selected COUNTRY INFORMATION (from ```country.rb```)
    * Prompt user to select another COUNTRY or EXIT
 
7. **goodbye**
    * Display message to indicate end of CLI Gem


```
require_relative 'themes.rb'
require_relative 'destinations.rb'
require_relative 'country.rb'
require 'colorize'
require 'readline'

module TravelInspiration
    class CLI
        
        def start
            list_themes
        end

        def list_themes
            puts "Hello! Which travel inspiration will you like to explore for your next trip?".green.bold
            puts "\nPlease select a theme from 1 - 12".blue.bold
            theme_arr = TravelInspiration::Theme.list_theme_names
            puts  theme_arr.map.with_index{|theme, index|
                "\t#{index+1}. #{theme.name}"
            }
            puts "--------------------------------------------".black.on_white #horizontal divider
            select_theme(theme_arr)
        end

        def select_theme(theme_arr)
            input = nil

            while true # user_input != "exit"
                input = Readline.readline("Select theme 🛣 ", true).strip

                goodbye if input.downcase === "exit"
                input = input.to_i 

                if input > 0 && input < 13
                    puts "--------------------------------------------".black.on_white #horizontal divider
                    theme_name = theme_arr[input-1].name.strip
                    puts "Here are the top 6 destinations for #{theme_name.upcase}\n".green.bold
                    list_destinations(theme_name)
                else
                    puts "That selection is not valid. Please select a theme from 1 - 12, or type exit."
                end
            end
        end

        def list_destinations(theme_name)
            puts "Please select a destination from 1 - 6".blue.bold
            country_arr = TravelInspiration::Destination.list_destination_names(theme_name)

            puts country_arr.map.with_index{|d, index|
                "\t#{index+1}. #{d.name}, #{d.continent}"
                }
            puts "--------------------------------------------".black.on_white #horizontal divider
            select_country(country_arr)
        end

        def select_country(country_arr)
            user_input = nil
            while true # user_input != "exit"
                user_input = Readline.readline("Select country 🗺 ", true).strip

                goodbye if user_input.downcase === "exit"

                country_choice = user_input.to_i 

                if country_choice > 0 && country_choice < 7
                    puts "--------------------------------------------".black.on_white #horizontal divider
                  
                    #select country = array[country_choice]
                    chosen_country = country_arr[country_choice-1].name.upcase

                    puts "Here's some information about #{chosen_country}, and the best time to travel there!".green.bold
                    country_details(chosen_country)
                else
                    puts "That selection is not valid. Please select a country from 1-6, or type exit."
                end
            end
        end

        def country_details(chosen_country)
            country = TravelInspiration::Country.new(chosen_country)
            puts country.country_info        
        end

        def goodbye
            puts "\nFarewell, traveler! May the wind take you somewhere new!".red.on_white.bold
            exit
        end
    end
end

puts TravelInspiration::CLI.new.start
```


## How to publish a gem on RubyGems.org

Now that I have completed my Ruby CLI gem, it is time to publish it! A quick Google search led me to [RubyGems Guides to publish a gem](http://guides.rubygems.org/publishing/).

1. Create a RubyGem account

2. Update my Gem's version ```lib/version.rb``` to 0.1.0 , following [semantic versioning patten.](http://guides.rubygems.org/patterns/#semantic-versioning)

3. Use gem command ```gem push travel-inspiration-0.1.0.gem``` and enter my RubyGems account credentials

[And I have published my gem!](https://rubygems.org/gems/travel_inspiration)

However, after publishing my gem, I realized a user who downloads my gem doesn't have a quick way to start the gem.  This led me to create a new file in ```/bin``` that will create a new instance of the CLI gem

### bin/inspire_me

The purpose of this file is to: ENABLE user to start the CLI with a simple command, ```inspire_me```

This is achieved by calling the method ```start``` , which creates a new instance of CLI class and display the first user prompt.

```
#!/usr/bin/env ruby

require "bundler/setup"
require "travel_inspiration"

TravelInspiration::CLI.new.start
```

### travel_inspiration.gemspec

It was also important to update my gemspec file to specify the ```/bin``` directory of my ```inspire_me```  executable file!

```
  spec.bindir        = "bin"
  spec.executables   = ["inspire_me"]
  spec.require_paths = ["lib"]
```


## Travel_inspiration Ruby Gem!

Finally, my CLI gem is ready to use!  Go on, try it for yourself 😃

You can[ install this gem](https://github.com/lisaychuang/travel_inspiration_gem) by typing the following prompt in terminal:

```gem install travel_inspiration```

Be inspired!
