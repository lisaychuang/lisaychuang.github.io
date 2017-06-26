---
layout: post
title:  "My first Ruby CLI gem! travel-inspiration"
date:   2017-06-26 15:53:33 +0000
---


In brainstorming for the theme of my Ruby CLI gem, I toyed with a few ideas.  I love Travel / Food / Arduino projects, and all three themes have plenty of resources available for scraping.  So I asked myself:  **What problem will you like to solve?**

I decided I will tackle the problem:  **Where should I explore next?**

For some people, they have a bucketlist of places they want to visit before they die.   But for others who don't have a list in mind, how will they decide which countries they want to visit next?   

For my parents, I noticed they often plan trips based on a theme.  For architecture and history, they decided to visit the UK and France.  For sakura blossom viewing, they planed a Spring trip to Japan.  And when they wanted a more active trip, they visited my sisters in Seattle and South California.

Going through [Lonely Planet's website,](https://www.lonelyplanet.com/) I noticed there's a Travel Inspiration section on the homepage!

1. There are 12 themes
2. Upon selecting a theme, the [next page](https://www.lonelyplanet.com/adventure-travel/) displays tips, blogs, videos, related tours, and 6 recommended destinations.
3. Selecting a destination, the [next page](https://www.lonelyplanet.com/new-zealand) displays top experiences, survival guide filled with useful information and related articles!


# Let's review the project requirements:

*1. Provide a CLI
2. CLI must provide access to data from a web page.
3. The data provided must go at least one level deep, generally by showing the user a list of available data and then being able to drill down into a specific item.
4. The CLI application can not be a Music CLI application as that is too similiar to the other OO Ruby final project. Also please refrain from using Kickstarter as that was used for the scraping 'code along'. Look at the example domains below for inspiration.
5. Use good OO design patterns. You should be creating a collection of objects - not hashes.
6. For bonus points, instead of just creating an application, create a gem and for extra bonus points try publishing it to RubyGems.*

Reviewing the project requirements, I was confident I can achieve all points using the Lonely Planet website as my primary source of content.

I decided to name my Ruby CLI Gem: **travel-inspiration**

# My Ruby Gem provides:
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

![](https://youtu.be/anf_NTuTVuQ)



