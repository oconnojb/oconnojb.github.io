---
layout: post
title:      "A Dynamic Web App, Made With Sinatra"
date:       2018-04-03 16:22:11 +0000
permalink:  a_dynamic_web_app_made_with_sinatra
---


I was recently asked to write some code that would facilitate a business' need to share finalized schedules for thier factory machines with the employees responsible for running those machines. As the machines' schedules are constantly being updated and re-released when edits are made to orders, it is important that the process be as easy and intuitive as possible for the employees utilizing it.

Now, I am not hooked into any company databases or servers, and I'm writing the code in Ruby and Sinatra which this company does not utilize; however, I am treating this as an excercise in my own abilities to create the interface in the best *(and only)* way I know how.

## Let's dive in, shall we?

Take a look at the full project [here](https://github.com/oconnojb/release-to-web-pages) to follow along!

### config.ru

config.ru is the file that runs the whole app.

All it does is require the environment (so the program has access to all the correct files) and run the App!

The web app can be started in the terminal with the command `rackup config.ru` or, becasue I have included the gem 'shotgun', the program can be started with the command `shotgun`.

As this app is still in 'development', running `shotgun` starts a local web server.

### app.rb

When config.ru is executed, it is ordered to run the application, housed in app.rb.

app.rb defines a Ruby class 'App' which inherits a lot of functionality from the Sinatra library.

This class defines two paths for the webserver to recognize.
1. `'/'`, the default page, calls for the program to read the file index.erb
2. `'/released'`, the page the form is submitted to, calls for the program to read the file released.erb

When the program is run, by default, we see the HTML (and Ruby) content of index.erb

### machine.rb

Before I can dive into index.erb, I need to clarify some of the behind-the-scenes work I've done in machine.rb.

machine.rb defines a new class 'Machine'. Each of the company's machines is represented by an instance of the Machine class. Becasue I don't have all of the information about these machines, my Machine class is quite bland and does not currently reflect the true nature of the real machines.

The Machine class has a few attributes
1. name
2. location
3. type
4. id

Currently, all of the data is being hard-coded in, as I had to make it up myself. However, ideally, the Machine class would pull this information (and more) directly from the company's database.

I have written a few class methods that the Machine class can respond to:
1. **self.all**: *This method returns the entire array `@@all` where instances of the class are persisted to. Eventually, to become functionally integrated with the company, this information would have to come from a permanent database, and not an array that lives in memory.*
2. **self.create**: *This method takes in machine attributes as arguments, instantiates a new machine, uses those arguments to set the machine's instance variables, and then persists the machine by pushing it to the array `@@all`.*
3. **self.populate**: *This method takes the place of a seed file. I define a few arrays of attributes for machines and then iterate through them, creating 8 new machines by zipping together the arrays. It is not a permanent solution, but it was a quick way of getting some data into the `@@all` array without spending too much time on it. This method would become obselete once the program was hooked into the database and it could create machines from database rows.*

### index.erb

The interface the user interacts with can be found in *views/index.erb*

ERB (Enhanced RuBy) allows for the intermixing of HTML and Ruby within one file. By default, the file treats each line as HTML, however a programmer can open lines of Ruby code with the tags `<%` *(or `<%=` for using Ruby to substitute into HTML)* and close them with `%>`.

The first two lines of index.erb -
```
<% Machine.populate %>
<% machines = Machine.all %>
```
-create the sample data by calling `Machines.populate` and set a variable 'machines' which points to the array `@@all`. Without these lines, there would be no machines to fill up the form, and the array would be trickier to access *(though still possible)*.

Then, I open a `<form>` tag. The `action = "released"` designates the path that the form will submit to. Here, the form knows that submitting the form will redirect to the path `'/released'` *(see app.rb)*.

Then I start making checkboxes.

This company has 2 locations *(abbreviated SH and SB)* and 2 types of machines *(cutters and folders)*. As each location has both cutters and folders, I laid out the site as follows:

```
SB location
  SB cutting machines
	  machine 1
	  machine 2
	  etc.
  SB folding machines
	  machine 3
	  etc.
SH location
  SH cutting machines
	  machine 4
	  etc.
  SH folding machines
	  machine 5
	  machine 6
```

Each line, including the locations and types, is a checkbox. The idea is that by checking the SB location, all of the machines at that location will be checked as well. Or, by checking the SH folding machines header, all of the machines below it get checked automatically. *(Or will uncheck accordingly as well)*

To separate the different levels of the tree, I did the following:
- The locations are separated  by putting thier checkbox within an `<h2>` tag.
- The machine types are placed as a regular, old checkbox.
- The individual machines exist within a `<ul>` tag as `<li>` items.

To get the HTML to render the different machines properly, I had to do some tricky ERB footwork. Here's the block where I display the SB cutting machines, each as its own `<li>` item:

```
<% machines.each do |machine| %>
  <% if machine.location == "SB" %>
    <% if machine.type == "cutter" %>
      <li><%= "<input type='checkbox' name='#{machine.name}' id='#{machine.id}'><label for='#{machine.name}'> #{machine.name} </label></input>" %></li>
    <% end %>
  <% end %>
<% end %>
```

By using Ruby, I was able to iterate through the array of machines *`(machines.each do |machine|)`*.

There are two layers of `if` statements which weed out the machines in the array that arent SB cutting machines.

Then, within an `<li>` tag, I create the checkbox for a specific machine. Because I am iterating through all the machines, this will create as many new `<li>`'s as needed. I use string interpolation to set the checkbox `name` and `id` attributes, the label `for` attribute, and the text of the label. 


Once the structure of the checkboxes is complete, there is a submit button. Becasue the `action` attribute for the form is set, the submit button submits to that path.

So, at this point, the webpage looks like this:

![](https://i.imgur.com/HisRS9t.png)

After the submit button, I use Javascript *(within a `<script>` tag)* to facilitate the checking and unchecking of boxes. 

I start by declaring some variables that give me access to the checkboxes. Because the locations and types are hard-coded, declaring the variables was quite straightforward. However, because the machines can change, I had to use the power of Ruby to iterate through the machines and create new JS variables pointing to each of the machines by id.

Then, the JS functions responsible for doing the actual checking and unchecking. *(I think, eventually, the functions facilitating the checking and unchecking of individual machines will need to be abstracted, as the machine id's could change.*

Finally, I have a series of event listeners that tell the program when to actually run the JS functions.


### released.erb

When the form is submitted, I told it to call up the path that leads to the file released.rb. If this web app were to be integrated into the company's system, this file would be totally different. The purpose of the program is to release the schedules for these machines to other parts of the company website. Because I don't have access to the schedules for any machines or these other parts of the company's infrastructure, I was not able to do this. However, as a proof of concept, I have shown that the data from the form in index.erb is passed to the location I told it to go as a part of a hash called 'params'.

released.erb simply iterates through the params hash and lists out the machine names that were checked when the form was submitted. Because the form submits the information from ALL the checked checkboxes, I included this line: `<% if !machine_name.include?('_') %>`  to weed out the boxes representing the larger headings. This wording was convenient for me becasue none of my machine names included an underscore, but all of my other headings *(like sb_cutters, sh_folders, or all_sb_machines)* do include an underscore. 

## In Conclusion...

Of course, this web app doesn't actually do anything useful with the information from the form, becasue I don't know exactly what it should be doing with the information. With actual machine names from an actual database, and a real path for the form to submit to, this project might look very different and be useful to employees. Right now, it's still proof of concept!
