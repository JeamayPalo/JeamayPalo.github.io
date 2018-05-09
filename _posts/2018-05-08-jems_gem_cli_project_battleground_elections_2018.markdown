---
layout: post
title:      "Jem's Gem: CLI Project,  Battleground Elections 2018"
date:       2018-05-09 03:05:52 +0000
permalink:  jems_gem_cli_project_battleground_elections_2018
---


After 10 brutal days of wrestling with this beast of a project, it's finally finished! For review at least. It seemed impossible when I started, but I can't believe it came together. Before this project, it never felt like everything I’d learned was solidly consolidated in my brain. It all seemed fragmented. I guess that’s the downside of learning too much too fast. This project is truly where I felt like everything came together. Finally! This blog piece is an attempt to retrace that messy process. 

**Developing the Concept**
I decided to do something related to the midterm elections this coming November 2018. I think we live in interesting political times, and the election system in the U.S. really fascinates me. I am from the Philippines, which has a direct democracy, so every vote literally counts. It was interesting to me that here, someone could win the popular vote, but could still lose because of the electoral votes. 

I decided to scrape from the website: https://ballotpedia.org/U.S.Senatebattlegrounds,2018, because it has all the pertinent information that I needed: the list of the battleground states, and a comprehensive list of each state's candidates. I would later find out that this website was very difficult to scrape, because all its elements are encompassed in one ```***div*** ``` and there were no class identifiers. A future note to self on scraping: pick a well-coded website.

**Moving to Atom**
My first major hurdle wasn't the code. It was IDE. I couldn't get binding.pry to work in the IDE, and I was working out of the sandbox. It took me 3 days to get binding.pry to work, and even the project coaches were challenged by IDE limitations. Finally I attended an in-person study session in Washington DC, and the instructors were really helpful. It was, as they said, time to take the training wheels off, and move to my own local environment. I have since moved to Atom, and never looked back! 

**Faking the Interface**
After the environment hiccup, the first step was then to code my interface from the user's perspective. I first created a CLI class, and hard-coded the data that I intended to scrape. If I was sketching, this would be when I would outline the shapes of my drawings. First, I wanted the users to see a 'menu' of the 9 battleground states, and from there, be able to choose the state whose candidates they want to know. My initial CLI class looked like this: 
``` class CLI 
def self.call
      puts "Hello"
      list_states
      list_state_candidates
      goodbye
    end

    def self.list_states
       puts "Here are the Battleground States for Midterms 2018"
       1.) Arizona
       2.) Florida 
       3.) Indiana
       4.) Missouri #etc.
    end

  def self.list_state_candidates
   input = nil
    while input != 'exit'
     puts "To find out your State's candidates, please enter the State number (1-9):"
     input = gets.strip
     case input
       when '1'
         #display list of Arizona candidates
       when '2'
         #display list of Florida candidates 
       when '3'
         #display list of Indiana candidates
       ....#etc. the same mechanism for the rest of the states
       else
         puts "Invalid Input"
      end
    end
  end
	
	def goodbye
	puts "Vote Wisely!"
	end 

end

``` 
From here, I knew that I needed a scraper method for my states and candidates which I can easily call. This step in building from the user in mind, really helps, because it gave me an idea on how to build my other classes. 

**Scraping States and Candidates**

From the CLI, I then started scraping the data I needed. Now I'm filling in my drawings (let me just keep up the sketching metaphor ;) ). As I said, scraping proved to be challenging, because the elements in the website weren't specifically labeled. Almost all the elements were also pretty much in one parent body, so it was challenging to look for the parent selector. This is where I really struggled and asked help from project coaches. As much as possible, I like figuring things out on my own first. But it really helps when you talk through it with someone. This is one thing that I really appreciate about the Flatiron School: the help that's available to online students like me. For someone new to programming, some things can be challenging to piece together on your own. Even though I've developed the ability to independently problem-solve and debug, sometimes, there are things that just really need to be put in context by a coach. So a big shout out and thank you Flatiron school's project coaches! 

After several attempts, and with help from the coaches, I successfully scraped the 9 battleground states, and the candidates. 

```def self.scrape_states
    doc = Nokogiri::HTML(open("https://ballotpedia.org/U.S._Senate_battlegrounds,_2018"))
    battleground_states = [
      doc.css("span#Arizona").text,
      doc.css("span#Florida").text,
      doc.css("span#Indiana").text,
      doc.css("span#Missouri").text,
      doc.css("span#Montana").text,
      doc.css("span#Nevada").text,
      doc.css("span#North_Dakota").text,
      doc.css("span#Ohio").text,
      doc.css("span#West_Virginia").text
    ]
    battleground_states.each.with_index(1) do |state, index|
      puts "#{index}.) #{state}"
      State.create(state)
    end
  end
	
	def self.scrape_candidates(index)
    doc = Nokogiri::HTML(open("https://ballotpedia.org/U.S._Senate_battlegrounds,_2018"))
    candidates_array = [doc.css("#mw-content-text").css("ul")[index].text]
    candidates_array
  end
	
	```

**Arranging Classes**

When I started, I had a State class, Candidate class and CLI class. However, after talking to a coach, I realized that essentially my project's main object is the State. My goal was to create State objects, that had unique candidates. So I scrapped the Candidate class and made a Scraper class instead, which I patterned after Avi's second video. Again, it was very helpful to see different ways of doing this project. Finally, I had a Scraper class, a State class and a CLI class. 

**Making CLI Work**

Now that my classes were more defined, I began to make my CLI 'real' so to speak. I added in my scrape methods from the Scraper class. This is where I really appreciated the technique of beginning with the user in mind, because my inital hard-coded CLI class, looked very similar to my final one that returned actual scraped data. 

```class CLI

    def self.call
      puts "Hello"
      list_states
      list_state_candidates
      goodbye
    end

    def self.list_states
       puts "Here are the Battleground States for Midterms 2018"
       Scraper.scrape_states
    end

  def self.list_state_candidates
   input = nil
    while input != 'exit'
     puts "To find out your State's candidates, please enter the State number (1-9):"
     input = gets.strip
     case input
       when '1'
         Scraper.scrape_arizona
       when '2'
         Scraper.scrape_florida
       when '3'
         Scraper.scrape_indiana
       when '4'
         Scraper.scrape_missouri
       when '5'
         Scraper.scrape_montana
       when '6'
         Scraper.scrape_nevada
       when '7'
         Scraper.scrape_northdakota
       when '8'
         Scraper.scrape_ohio
       when '9'
         Scraper.scrape_westvirginia
       else
         puts "Invalid Input"
      end
    end
  end
	```
	
I then set up a new file in my bin where I would call that CLI method. After several attempts, I was able to get my CLI to work with the command ruby bin/battlegrounds. It was like magic! I started playing around with the CLI, and that's when I found out that several of my candidates would show up in other states. My theory is that something had been changed in the website (a candidate might have been added or deleted), that all the arrays started to adjust. I guess this is the challenge of scraping from a dynamic website, unlike the Student scraper lab that we did a couple of lessons back. Ah the woes of scraping!

**Refactoring Attempt**

In one of my 1 on 1 sessions, a coach pointed out that my Scraper class has a big chunk of code. I was so caught up with scraping everything I needed that I didn't pay attention to how my code looked. Of course, I've yet to have the eye for well-written code, but I imagine it's a lot like effective writing. Less is more. Indeed, I realized that 9 state candidate scraper methods, each looking like this: 

``` 
def self.scrape_arizona
		arizona = Nokogiri::HTML(open("https://ballotpedia.org/U.S._Senate_battlegrounds,_2018"))
		arizona_candidates = {
				"Incumbent" => [arizona.css("#mw-content-text").css("ul")[8].text], #Incumbent
				"Democrats" => [arizona.css("#mw-content-text").css("ul")[5].text], #Democrats
				"Republicans" => [arizona.css("#mw-content-text").css("ul")[6].text], #Republicans
				"Independent" => [arizona.css("#mw-content-text").css("ul")[7].text], #Independent
				"Libertarian" => [arizona.css("#mw-content-text").css("ul")[9].text] #Libertarian
		}
		arizona_candidates.each do |key, array|
				puts "#{key}"
				 array.each do |candidate|	
							puts "#{candidate}"	
	        end	
	      end	
   end
	``` 
	
...would get my code more likely to break later on. This might also make it difficult to debug. So I attempted to refactor. I started with the iteration. Each self.scrape_nameofstate method I have will iterate the hash of candidates. So instead of having the iteration for every method, I made a separate method for iterating. 
	
```def self.enumerate(state_candidates)
    state_candidates.each do |key, array|
      puts "#{key}"
      array.each do |candidate|
        puts "#{candidate}"
      end
    end
  end
	```
	 
	By putting my iteration in a separate method, my new scrape_state candidate method now looks like this: 
	
	```
	def self.scrape_arizona
    arizona_candidates = {
      "Incumbent" => Scraper.scrape_candidates(8), #Incumbent
      "Democrats" => Scraper.scrape_candidates(5), #Democrats
      "Republicans" => Scraper.scrape_candidates(6), #Republicans
      "Independent" => Scraper.scrape_candidates(7), #Independent
      "Libertarian" => Scraper.scrape_candidates(9) #Libertarian
    }
    Scraper.enumerate(arizona_candidates)
  end
	```
	
	With this initial refactoring process, I am beginning to understand what 'cleaner-looking' code means. I know I have a long way to go, but I am deeply glad that I learned a lot from creating this project from scratch. I plan to submit everything by the end of the week. Here's to hoping I pass! 
	
Github link for my project: https://github.com/JeamayPalo/battlegrounds_2018.git 
