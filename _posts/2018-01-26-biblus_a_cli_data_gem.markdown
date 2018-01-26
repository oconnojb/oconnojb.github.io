---
layout: post
title:      "BIBLUS: a CLI Data Gem"
date:       2018-01-26 20:48:43 +0000
permalink:  biblus_a_cli_data_gem
---


Biblus is a Gem I developed for Flatiron's CLI Data Gem project. It can tell the user the "Bible verse of the day" as scraped from biblegateway.com and then also allow the user to read the full text of the chapter the verse is from, if they so choose. Someday, I hope to add a look up functionality that would allow a user to type in a book, chapter, and verse number and it would show the user that text, but I haven't gotten that functionality working yet!

The main files for my gem were created by the bundler. In the terminal, I ran `bundle gem biblus` and *BOOM!* there was my project structure!

You can check out the whole project [here](https://github.com/oconnojb/biblus) if you want, but I'll also be pasting relevant snippets right here if you just want to read along!

### Gemfile

The fist file I edited was the Gemfile. I knew there would be some gems that I would need to be using for this project, and I wanted to make sure I had them connected in before I got going. Bundler started my Gemfile with this code:

```
source "https://rubygems.org"

git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

#Specify your gem's dependencies in biblus.gemspec
gemspec
```

Becasue I didn't want to break anything unnecessairily, instead of editing this, I just specified the gems I wanted to include below it. It is quite possible that I could have deleted the given code and just used my own, but I didn't want to risk it. I added:

```
source "https://rubygems.org"

gem 'rspec'
gem 'pry'
gem 'require_all'
gem 'nokogiri'
```

I probably did'nt need to include rspec, but I'm thinking that as I add further functionality to this gem, I might benefit from a test suite, so I threw it in!

## run-biblus
The bin directory used to contain a file called console, I renamed it to run-biblus. This is the file that the user interacts with. It really only does one thing *(ok, well, it requires some files before it does this one thing)*:

```
Biblus::CLI.new.call
```

run-biblus makes a new CLI and tells it to start going! 

## Biblus::CLI
This file is *biblus/lib/biblus/cli.rb*
Ready for a line-by-line of the CLI?

*I'm only providing line numbers for the lines I want to talk about. The line numbers will not coencide with the line numbers within the code if you are reading along on github*

Fist, my code:

```
1 class Biblus::CLI
2  attr_accessor :todays_passage

3  def initialize
4    @todays_passage = Biblus::BibleScraper.new.scrape_todays_passage
     end

5  def call
6    welcome
7    sleep(1)
8    menu
9    input_manager
     end

10  def welcome
11    puts "Welcome to the Bible!"
12    puts "Today we have a great passage from #{@todays_passage.book} for you!"
13    puts "I can also look up any bible verse for you!"
       end

14  def menu
15    puts "***"
16    puts "To see today's passage, type 'today'"
         puts "To get started looking up a verse, type 'lookup' (note: not functional yet)"
         puts "And, you can always type 'menu' or 'exit'"
         puts "***"
       end

17  def display_todays_passage
          puts "***"
18    puts "#{@todays_passage.text}"
19    puts "#{@todays_passage.book} #{@todays_passage.chapter}:#{@todays_passage.verse[0]}"
         sleep(1)
         puts "*"
20    puts "If you liked today's passage, you can type 'full' to read the rest of #{@todays_passage.book} #{@todays_passage.chapter}"
         puts "***"
       end

21  def update_todays_passage
         puts "***"
22    puts "Sorry! I didn't realize I was out of date!"
23    hm = @todays_passage
24    @todays_passage = Biblus::BibleScraper.new.scrape_todays_passage
         sleep(1)
25    if hm.text == @todays_passage.text
            puts "***"
            puts "Hey! Turns out I had the correct passage all along!"
            display_todays_passage
         else
             puts "***"
             puts "Here, I should have the right passage now!"
             display_todays_passage
         end
     end

26  def display_full_chapter
27    full_chapter_array = Biblus::BibleScraper.new.scrape_full_chapter(@todays_passage.link_to_full)
          puts "***"
28    puts "#{@todays_passage.book} #{@todays_passage.chapter}"
         puts "*"
         sleep(1)
29    i=0
30    full_chapter_array.each do |verse|
31      if (verse != "") && (verse != ", ")
32         i+=1
33         puts "#{i}. #{verse.gsub(/\u00A0/, "")}"
              sleep(0.25)
            end
          end
          puts "***"
        end

34  def input_manager
35    input = gets.strip
        if input.upcase == "TODAY"
          display_todays_passage
          input_manager
        elsif input.upcase == "UPDATE"
          update_todays_passage
          input_manager
        elsif input.upcase == "MENU"
          menu
          input_manager
        elsif input.upcase == "FULL"
          display_full_chapter
          input_manager
        elsif input.upcase == "LOOKUP" || input.upcase == "LOOK UP"
          puts "***"
          puts "I'm sorry, that functionality hasn't been prgrammed yet!"
          puts "Ask me something else!"
          puts "***"
          input_manager
        elsif input.upcase == "EXIT" || input.upcase == "END"
          puts "***"
          puts "Amen."
          puts "***"
        else
          puts "***"
          puts "I'm not sure what you mean by that... try again!"
          input_manager
        end
      end
    end
```

And now, the commentary, line-by-line :)

1. I define the class `Biblus::CLI`
2. I wanted the CLI itself to store the passage of the day so that we can access it throughout the program. I used `attr_accessor` to make an `@todays_passage` instance variable that I will be able to use within my CLI.

3. The initialize method is run right when the CLI is created.
4. Right away, `@todays_passage` is set to the passage of the day. This is accomplished through a BibleScraper object (`Biblus:BibleScraper`). #scrape_todays_passage is a method that scrapes the passage of the day from biblegateway.com and returns a Passage object with some attributes scraped from the web. We'll talk more about the `BibleScraper` and `Passage` objects later.

5. Call is the method that gets run by the user in `run-biblus`. Remember the line `Biblus::CLI.new.call`? This method calls other methods specified below. I wanted to make the call method as abstract as possible.
6. the `welcome` method welcomes the user
7. I wanted interacting with my CLI to feel like interacting with a librarian. I didn't want it to be instantaneous. Otherwise, I have a difficult time following the output. I use `sleep(number_of_seconds)` throughout the applicaiton to slow down the computer and *hopefully* make the program easier to interact with. 
8. the `menu` method displays the menu
9. the `input_manager` method facilitates the input logic for the CLI.

10. The `welcome` method!
11. I wanted my program to sound cheery and informal. Here, I welcome the user to the Bible :)
12. Because the CLI instantiates with `@todays_passage` set to a Passage object, the welcome message can give the user a little teaser about the passage of the day. *I should take a moment to mention that the `Passage` object has the followin attributes:
`@book`, `@chapter`, `@verse`, `@text`, `@link_to_full`. In line 12, welcome draws on the `@todays_passage.book` so that the user sees something like "Today we have a great passage from Genesis for you!"*

13. This line references that extended functionality that I am hoping to add down the line.

14. The `menu` method!
15. I have this `puts "***"` sprinkled throughout my code as well as sleep time. It's purpose is to aid readability in the console. With these lines added, it becomes easier to follow the information being presented by the program. Once the program starts printing full Bible chapters to the console, these stars help a reader orient him/herself within all the text.
16. The next few lines are the menu, they clue the user into the commands that the computer can respond to.

17. `display_todays_passage` is the method that gets called when a user types "today".
18. The first thing printed to the console is the passage's text.
19. Below the text, the computer prints the citation for the passage.
20. After waiting a second, the computer offers to show the user the rest of the chapter. Using some string interpolation, the computer can more accurately tell the user what it is offering to do for them.

21. Let's say a user leaves this program running overnight and tries to interact with it the next day. `update_todays_passage` comes in handy!
22. A little humor, but also a way to let the user know that the program is working for them.
23. This line is to facilitate a little extra humor I threw into this method *(see #25)*
24. This is the main point of this method. It sets `@todays_passage` to the output of a new BibleScraper which scrapes todays passage. This is the same line of code that the CLI auto-runs on instantiation.
25. This if statement facilitates the punchline to #23. If the user ran the method unnecessarily, it gives them a bit of sass before showing them the passage again. Or, if it was actually in need of updating, it gives an apologetic-ish message to the user before showing the passage.

26. `display_full_chapter` shows the user the full chapter of the Bible that the passage of the day is taken from. The method is designed to work in conjunction with a method within the BibleScraper class called `scrape_full_chapter(link)`. We'll talk about it in more detail later, but for now know that it returns an array of the whole chapter separated by verse.
27. This line encapsulates the interaction described in #26. It sets that array returned by `scrape_full_chapter` to a new variable: `full_chapter_array`. It passes `scrape_full_chapter` the link to the full chapter using the `@link_to_full` attribute of the `Passage` object. The `BibleScraper`'s `scrape_todays_passage` method is responsible for setting the `@link_to_full` attribute.
28. The computer gives the citation before launching into the chapter.
29. a counting variable to help display the chapter
30. tells the computer that we are going to iterate through that array and do something with each verse.
31. If that verse isn't blank and it doesn't only have a comma in it the rest of the code runs. I originally omitted this if statement, but unfortunately my array has some blank spaces and commas that are unhelpful to the user. This if statement will keep those useless lines from printing.
32. Up the counter at the beginning so the first one will use` i=1` instead of `i=0`. *Alternatively, I bet I could have set `i=1` in line 29 and increased `i` after the output (see #33)*
33. This line prints the number of the verse followed by the verse. Oddly enough, the scraper starts each verse with `\u00A0`, so I used `.gsub(/\u00A0/, "")` to take it out of there!

34. The `input_manager` method handles the user input.
35. It starts off by getting input from the user.
36. A set of if statements help the computer determine what the user is trying to do. Using `.upcase` on the input makes it so capitalization doesn't matter. The program won't break if someone leaves capslock on. The conditions for my `if`s and `elsif`s match up with the options presented in the menu. "FULL" and "UPDATE" aren't in the menu. FULL, the computer prompts it later in the interface *(after showing them the verse of the day, it asks if they want to read the whole chapter)* but a savvy user could jump to it wheneve they want. UPDATE probably won't be used much because I don't anticipate users leaving my program running overnight. It would be an easy fix to add it to the menu if I so chose at a later date.
37. You will notice that each option reruns `input_manager` at the end to keep the program from closing out. Only the EXIT *(or END, just in case a user forgets how to exit and types end instead)* command doesn't rerun `input_manager` becasue thats when we want the program to close out.

## Passage
Passage objects represent an exerpt from the Bible. Each passage has a book that it comes from, a chapter that the passage is in, and a verse number (or set of numbers). It also has a text attribute for the text of the passage and an attribute called `@link_to_full` which is the link to the full chapter. The code for the Passage class is pretty simple but has one funky bit in it, that isn't quite relevant yet. I am picturing that, later on in this program's life, it will be able to look up multiple verses, like this: Genesis 7:3-8. So I instantiate each Passage with it's verse attribute set to an empty array. I have a feeling this will help me when I want to add the look up feature, but I haven't planned that feature out yet, so it could be scrapped by the end. Check out the Passage class, I keep it in biblus/lib/biblus/passage.rb:

```
class Passage
  attr_accessor :book, :chapter, :verse, :text, :link_to_full
	
                #chapter is a number
                #verse is an array of numbers

  def initialize
    @verse = []
  end
end
```

## Biblus::BibleScraper
*biblus/lib/biblus/bible-scraper.rb*

I think it would be easiest to do a line-by-line of the BibleScraper, so lets dive in!!

```
1 require 'nokogiri'
    require 'open-uri'
    require 'pry'

2  class Biblus::BibleScraper
3    attr_accessor :todays_passage, :todays_chapter_array

4    def scrape_full_chapter(link)
5       doc = Nokogiri::HTML(open(link))
6       full_chapter = []
7       chapter = doc.css(".passage-content p")
8       chapter.each do |p|
             full_chapter << "#{p.text}"
          end
9       str_ch = full_chapter.join(" ")
10    @todays_chapter_array = str_ch.split(/\d+/)
11    @todays_chapter_array
      end

12  def scrape_todays_passage
13    doc = Nokogiri::HTML(open("https://www.biblegateway.com/"))
14    @todays_passage = Passage.new
15    @todays_passage.text = doc.css(".votd-box p").text
16    citation = doc.css(".votd-box a").first.text.split
17    @todays_passage.book = citation[0]
18    sp_array = citation[1].split(":")
19    @todays_passage.chapter = sp_array[0]
20    @todays_passage.verse << sp_array[1]
21    @todays_passage.link_to_full = "https://www.biblegateway.com"+doc.css(".votd-actions-component a")[1]['href']

22    @todays_passage
        end
      end
```

1. I start off by requiring the gems I know I will need. I put these gems in the Gemspec file, but better safe than sorry!!

2. Defining the `BibleScraper` namespace
3. Some attributes that the scraper has. I don't know why they are instance variables, the code would definitely work with regurlar variables, but I think it might come in handy down the line, so I just went with it.

4. `scrape_full_chapter(link)` was the second method I defined, and is more complex than `scrape_todays_passage`. This method is responsible for scraping the text to the full chapter of the verse of the day. It only works when you pass it the link to the full passage. *For more info, see #27 in Biblus::CLI*
5. `doc` is now a nodeset with the full html of whatever link was passed to the method.
6. Here I am just starting an empty array called `full_chapter`, I'll use it in a few lines.
7. The `chapter` variable is now a nodeset with all of the `p` elements within the element with the class `passage-content`. These `p` elements contain the words we need.
8. Becasue we only need the text from each `p`,  this block goes through all the `p`'s in the chapter and pushes their text to the `full_chapter` array.
9. Lines 9 and 10 facilitate an issue I was having where the text is unruly to read when printed to the console. Because the `p`'s sometimes contain one verse and sometimes contain multiple, it was getting messy. The purpose of 9 and 10 is to get each item in the array to be one vese. Line 9 combines the `full chapter` array into one long string.
10. And then line 10 re-splits that string at each number *(`.split(/\d+/` the `\d` represents a digit and the `+` tells the computer that, as long as they are next to each other, any number of digits should be considered together)* and sets it to the instance variable `@todays_chapter_array`
11. The `scrape_full_chapter` method returns `@todays_chapter_array` so the CLI can iterate through it and print it to the console.

12. `scrape_todays_passage` was the first method I defined, it is simpler than `scrape_full_chapter`
13. This scraper only ever needs to go to the biblegateway home page, but other than that it is almost the same as line 5 above. Even though I wrote this one first!
14. Sets `@todays_passage` to be a new Passage object. Though right now it still does not have any attributes.
15. `doc.css(".votd-box p").text` points to the text of the passage. This line sets the Passage's text attribute.
16. On biblegateway.com, the book, chapter, and verse are presented as a single string that looks like this: "Genesis 1:1". This line splits it at the space. Now `citation[0]` is the book name while `citation[1]` is the chapter and verse separated by a colon.
17. Uses `citation[0]` to set the Passage's book attribute.
18. This line splits up the chapter and verse into a new array using `.split(":")`.
19. Sets the Passage's chapter attribute.
20. Becasue a passage might end up being multiple verses, here I push the verse number into the Passage's verse array.
21. Sets the Passage's link_to_full attribute as the link to the full chapter. This is the attribute that gets passed to the `scrape_full_chapter` method when the user wants to read the rest of the chapter.
22. Finally, the method returns `@todays_passage` so the CLI can do something with it when asked :)

## biblus.rb

This file acts as my environment. I've seen other programs that use an environment file, and I guess I could rename this file to reflect that, but biblus.rb was generated for me by the bundler and it was alerady requiring some other files, so I used it for my environment. It looks like this:

```
require 'open-uri'
require 'nokogiri'
require 'pry'

require_relative "./biblus/version"
require_relative './biblus/cli'
require_relative './biblus/bible-scraper'
require_relative './biblus/passage'
```

## That's all folks!


That sums up the Biblus CLI Data Gem! If you think it's a cool gem, feel free to make use of it :)

https://github.com/oconnojb/biblus

"everything of mine is yours and everything of yours is mine" -John 17:10
# THE END
***
