---
layout: post
title:      "Trick-Tracker"
date:       2018-05-15 18:52:30 +0000
permalink:  trick-tracker
---

# A Sinatra Portfolio Project

For my final Sinatra project, I decided to build a web app that could help dog owners keep track of the tricks they have trained each of their dogs. The more dogs you have, the harder it is to keep track of which dogs know which tricks.

In this blog post, I'll dive into the in's and out's of [Trick-Tracker](https://github.com/oconnojb/trick-tracker)! Here we go!

## THE LAYOUT

For me, one of the most daunting parts of a project is starting it. That blank screen is more intimidating than any error message or roadblock in the building process. So, my first step was to get something in the project.

I had just finished the Fwitter lab (from Flatiron's curriculum), and that lab started with a good overview of the files and folders that would be required for a Sinatra app of this scale, so I built off of that (and even copy-pasted some of the files directly). 

With the exception of the app folder (where most of my programming work was done), the db folder (where all my migrations are held), and config.ru (where I mounted my extra controllers), many of the files that I copy-pasted from Fwitter did not need to be altered at all. This really saved me from the impending doom of a blank screen.

In this blog post, I won't really be talking much about these basic files, the db folder, or config.ru because there really isnt anything interesting happening there. But feel free to scroll through them in the github repo (linked above), just to prove for yourself how standard these files are. *With the one exception of my meandering migrations as I reconsidered column names and what information my database would need to hold, of course.*

The app folder is where all the interesting stuff happens. Let's look at it in some detail:

```
─── app
    ├── controllers
    │   ├── concerns
    │   │   └── helpers.rb
    │   ├── application_controller.rb
    │   ├── dogs_controller.rb
    │   └── tricks_controller.rb
    ├── models
    │   ├── dog.rb
    │   ├── dog_tricks.rb
    │   ├── trick.rb
    │   └── user.rb
    └── views
        ├── dogs
        │   ├── delete.erb
        │   ├── edit.erb
        │   ├── new.erb
		│   ├── show_all.erb
		│   ├── show_one.erb
		│   └── tricks_edit.erb
		├── tricks
        │   ├── breakdown.erb
		│   ├── new.erb
		│   └── show_one.erb
		├── users
        │   ├── delete.erb
		│   ├── edit.erb
		│   ├── home.erb
		│   ├── login.erb
		│   └── signup.erb
        ├── layout.erb
        ├── index.erb
        └── sample.erb
```

## MODELS FOLDER
I'll start with the models folder, because it's the most straightforward. 

These are my models and they are all interconnected:

### user.rb
The user class represents the users of the application.  It corresponds to the users table in the database. Users have a name, email, and password. Because they will need to be able to sign in to the application, I included the line `has_secure_password` to help facilitate that process. Because I am using the gem 'bcrypt', this method keeps the users' passwords away from my prying eyes. This gem is also the reason my users table has the column 'password_digest' instead of just using password. It's all to keep the password properly encrypted and safe. A user can have many dogs, and─becasue dogs can learn many tricks─a user can have many tricks associated with them.

```
class User < ActiveRecord::Base
  has_secure_password
  has_many :dogs
  has_many :tricks, through: :dogs
end
```

### dog.rb
The dog class represents the dogs that Trick-Tracker helps users keep track of.  It corresponds to the dogs table in the database. Dogs have a name, breed, and age. Dogs can only belong to one user, but can learn many tricks. The join table dog_tricks facilitates a dog learning many tricks.

```
class Dog < ActiveRecord::Base
  belongs_to :user
  has_many :dog_tricks
  has_many :tricks, through: :dog_tricks
end
```

### trick.rb
The trick class represents each of the tricks a dog might be able to learn. It corresponds to the tricks table in the database. Tricks have a name, degree of difficulty, and description. A trick can be learned by many dogs, which is facilitated by the dog_tricks join table. Just as users can have many tricks associated with them, a trick could have many users associated with it.

```
class Trick < ActiveRecord::Base
  has_many :dog_tricks
  has_many :dogs, through: :dog_tricks
  has_many :users, through: :dogs
end
```

### dog_tricks.erb

This class is built to represent the join table between dogs and tricks.  It corresponds to the dog_tricks join table in the database. It has a dog_id column and a trick_id column. And is designed purely to help the computer keep track of the many-to-many relationship that dogs and tricks have.

```
class DogTrick < ActiveRecord::Base
  belongs_to :dog
  belongs_to :trick
end
```

## CONTROLLERS FOLDER
All of the controllers as well as the helper methods these controllers rely on can be found in the controllers folder. These files control the URL routes that users will navigate through to interact with the application.

### concerns/helpers.rb
This was the last file to be added to my project. Originally the helper methods defined within helpers.rb were tagged onto the end of each of the controllers. But while I was going through and refactoring at the end, I pulled out the helper methods and put them in a module called ApplicationHelper. Each of my controllers reqires this file and uses the line `helpers ApplicationHelper` to bring the functionality of the helper methods into the controller. Let's take a look at the helper methods:

#### logged_in?
This method checks to see if the user is logged into the application. It only checks to see if the session includes a key called 'user_id'

```
  def logged_in?
    !!session[:user_id]
  end
```

#### current_user
This method identifies which user is logged in by searching for the user with the id that matches the user_id stored in the session.

```
  def current_user
    User.find(session[:user_id])
  end
```

#### place(integer)
I built this method to facilitate a functionality that I wanted my application to be able to include. When a user adopts a dog or a dog learns a new trick, the computer uses this method to formulate the sentence of congratulation. `Congratulations! You have adopted your #{place(5)} dog!`

*place(5) would return "fifth"*

Take a look:

```
  def place(integer)
    placement_array = [
      'zeroth', 'first', 'second', 'third',
      'fourth', 'fifth', 'sixth', 'seventh',
      'eighth', 'ninth', 'tenth', 'eleventh',
      'twelfth', 'thirteenth', 'fourteenth',
      'fifteenth', 'sixteenth', 'seventeenth',
      'eighteenth', 'nineteenth', 'twentieth']
    if integer.between?(1, 20)
      return placement_array[integer]
    else
      return integer.to_s
    end
  end
```

This method hardcodes the place names for 'first' through 'twentieth' into an array called placement array. Then, if the argument is between 1 and 20, it returns the place name. I included 'zeroth' at the start of the array so I could just use the integer passed to the argument directly and not have to worry about the fact that arrays are zero-indexed. If the argument is above 20 *(or negative or 0, which it really wouldn't be for this applications purposes)* it just gives them the number back as a string so it doesn't break the program when someone buys their twenty-first dog!

### application_controller.rb
I decided to make application_controller.rb the hub for the routes that revolve around users and the actions they might want to take while interacting with the application. Things like loggin in, signing up, editing your account details, and deleting your account are all handled by the application_controller.

Like the other controllers I built, this one starts with a configure block. I copied it from a previous lab. I don't really know what the first two lines do, but the program works with them in it so I'm keeping them! The second two lines enable sessions so users don't have to continually sign in to the application every time they navigate to a new page and keeps that session secret from people who want to use our session data for malignant purposes. Even though this same block is in all three controllers, I won't be talking about it again.

Let's take a look at our routes.

#### '/'
This route is designated as the first screen a user would see when they first come to Trick-Tracker. The index page (index.erb) welcomes a user to Trick-Tracker and give users the options to log in or sign up.

I extended this route to check if the user had already logged in, because the index page is really only applicable to users coming here for the first time or users who want to find the link to log in. If a user is already logged in, it will redirect them to the home page.

Initially, this route looked like this:

```
  get '/' do
    if logged_in? 
      redirect '/home'
    else
      erb :'index'
  end
```

But, why write something in four lines if you can do it in one? I found a pretty handy pattern that works if the if and else blocks are only one line each *(like in the above code)* that uses `?` and `:`. Basically, it works like this `condition ? (if_true) : (if_false)`. This pattern gets used a lot in my application. So, after some refactoring, my route looks like this:

```
  get '/' do
    logged_in? ? (redirect '/home') : (erb :'index')
  end
```

#### '/signup'
The signup route takes users (who aren't already logged in) to a sign up form:

```
  get '/signup' do
    logged_in? ? (redirect '/home') : (erb :'users/signup')
  end
```

This form posts to '/signup'. The post route, first checks to make sure none of the fields were left empty. If everything got filled out, it creates a new user from the params of the submitted post, adds the user's id to the session hash, and then redirects the user to the homepage. If any of the fields were left empty, it redirects to the signup page. You'll notice it redirects with a query string `?failed=yes`. This query string will be accessible in the params hash after the redirect as `params[:failed]`. The sign up form has a block at the top that checks to see if `params[:failed]` exists, and (if it does) presents a little message explaining that something wen't wrong and they should try again.

```
  post '/signup' do
    if !params[:user][:name].empty? && !params[:user][:email].empty? && !params[:user][:password].empty?
      @user = User.create(params[:user])
      session[:user_id] = @user.id
      redirect '/home'
    else
      redirect "/signup?failed=yes"
    end
  end
```

#### '/home'
The home route shows the homepage to the user, but only if they're logged in. Because the homepage greets the user by name, it needs to set `@user` by matching the user_id in the session hash to an instance of a User.

Initially my route looked like this:

```
  get '/home' do
    if logged_in?
      @user = User.find_by(id: session[:user_id])
      erb :'users/home'
    else
      redirect  "/login?failed=yes"
  end
```

I wantend to try and refactor this code like I did for the signup route, but there are multiple lines of code that should be executed if they are logged in. Because there is only one line of code *(the redirect)* that should be executed if they are **not** logged in, I was able to cut it down by first telling the computer to redirect if they're not logged in. Because users who aren't logged in will get redirected, all of the code following will only be excuted for users who are logged in. Also, I remembered I already have a helper method that finds my current user. Here is the final '/home' route:

```
  get '/home' do
    redirect "/login?failed=yes" if !logged_in?
    @user = current_user
    erb :'users/home'
  end
```

The homepage includes links to do the following:
1. See all your dogs - *links to a page found in the dogs controller: '/dogs'*
2. Adopt a new dog - *links to a page found in the dogs controller: '/dogs/new'*
3. See all the tricks you have taught - *links to a page found in the tricks controller: '/tricks'*
4. Edit your user information - *links to '/edit'*
5. Delete your account - *links to '/delete'*

#### '/login'
The login route shows a log in form to users who are logged in.

```
  get '/login' do
    logged_in? ? (redirect '/home') : (erb :'users/login')
  end
```

The post route looks a bit convoluted, but it works for my purposes. I needed to set the `@user` variable, but i needed to check two conditions for it *(making sure the fields hadn't been left blank)*. Initially, I was using an if statement *(like I did in the post '/signup' route)*, but theres another if later to make sure the password authenticates, and the post route was looking a bit jumbled.  So I went with technique that pares it down a bit. First I set a variable `none_empty` which gets set to `true` if both email and password are filled. Then, it sets the `@user` variable, but only if nothing was left blank.

After that, I use another variable, `user_password_match` which gets set to `true` if `@user` is'nt `nil` and the password authenticates. The route redirects if the passwords don't match. Finally, if the user hasn't been redirected away because of an error, it puts the user's id in the session hash and redirects to the homepage.

```
  post '/login' do
    none_empty = !params[:user][:email].empty? && !params[:user][:password].empty?
    @user = User.find_by(email: params[:user][:email]) if !!none_empty
    user_password_match = (@user!=nil) && (!!@user.authenticate(params[:user][:password]))
    redirect "/login?failed=yes" if !user_password_match
    session[:user_id] = @user.id
    redirect '/home'
  end
```

#### '/edit'
The edit route is used by users who wish to edit their profile information, if they're already logged in, of course.

```
  get '/edit' do
    redirect "/login?failed=yes" if !logged_in?
    @user = current_user
    erb :'users/edit'
  end
```

I could use a variable set to a truthiness value to remove this if statement and put the redirect at the top like I did in the login route, but because I'm only using one if statement here, and the route as a whole doesn't look to confusing, I'll leave it as is. Each line that sets a part of the user's information only gets run if that particular line is not empty. With this form, only the fields that the user fills in get edited. If the user leaves a field blank, the computer can assume it doesn't need to change that piece of data.

```
  post '/edit' do
    @user = current_user
    if !params[:password].empty? || !!@user.authenticate(params[:password])
      @user.name = params[:name] if !params[:name].empty?
      @user.email = params[:email] if !params[:email].empty?
      @user.password = params[:new_password] if !params[:new_password].empty?
      @user.save
      redirect '/home'
    else
      redirect "/edit?failed=yes"
    end
  end
```

#### '/logout'
Logout is a simple route. It clears the session hash and sends the user back to the index page.

```
  get '/logout' do
    session.clear
    redirect '/'
  end
```

#### '/delete'
This route is for users who wish to delete their account. While it is sad for me that they don't want to use Trick-Tracker anymore, I guess they have the right to do so, right? Of course, as with most of the actions on this page, they can only delete an account if they're logged in to an account first. The form on users/delete.erb will ask the user for their password again to verify that they have the authority to delete the account.

```
  get '/delete' do
    redirect "/login?failed=yes" if !logged_in?
    @user = current_user
    erb :'users/delete'
  end
```

As long as the password box hasn't been left blank and  the right password was entered, the computer will start the deletion process. The first thing it does is remove rows from my join table. I was running into a bug where, if I deleted a dog and then created a new dog, the new dog would have the same id as the old dog and therefore would still be associated with its tricks. So it goes through each row of the join table and deletes the rows that match the dog's id. Then it deletes all of the user's dogs from the database, deletes the user from the database, clears the session and sends the user to the index page. Deleting trick information is not necessary, users can create new tricks, but not edit or delete them. All users contribute to the over all trick list.

```
    @user = current_user
    if !params[:password].empty? && !!@user.authenticate(params[:password])
      @user.dogs.each do |dog|
        DogTrick.all.each do |row|
          row.delete if row.dog_id == dog.id
        end
        dog.delete
      end
      @user.delete
      session.clear
      redirect '/'
    else
      redirect "/delete?failed=yes"
    end
  end
```

### dogs_controller.rb
The dogs_controller is the controller for the routes involving the dogs of Trick-Tracker. While the application_controller gave users the opportunities to log in, sign up, edit their profile, and delete their profile; dogs_controller allows users to adopt a new dog, edit an existing dog, remove a dog from their profile, and edit the tricks associated with a dog's profile. Let's take a look:

#### '/dogs'
This route displays all of the dogs associated with a user's profile, but only if the user is logged in!

```
  get '/dogs' do
    redirect "/login?failed=yes" if !logged_in?
    @user = current_user
    @dogs = @user.dogs
    erb :'dogs/show_all'
  end
```

#### '/dogs/new'
This route is responsible for displaying a form that allows a user to adopt a new dog. Again, this functionality is only available to users who are logged in.

```
  get '/dogs/new' do
    logged_in? ? (erb :'dogs/new') : (redirect "/login?failed=yes")
  end
```

The post route creates a new dog based on the parameters submitted from the form. The dog can be built associated with `@user` through the build method, and is instantiated with a name, age and user_id. The breed needed to be set separately, because I am giving users the option to either select a breed from  a list of breeds, or create a new breed. At the moment, Trick-Tracker only allows dogs to have one breed at a time. The option to select a breed from the radio buttons returns as keys in a hash at `params[:dog][:breed]` in the following format: `{'Great Dane' => 'on'}` because these are radio buttons, only one option may be selected, so `params[:dog][:breed].keys.first` points to the name of the breed of the selected radio button. I wanted the option to create a new breed to override any selected breed, so that functionality comes second, and will rewrite `@dog.breed` if the field has some content in it. Afterwards, `@user` is saved, which also saves `@dog`. Finally, the page is redirected to display the individual dog that has been created *(the '/dogs/:id' route will be discussed next!)*. The redirect includes a query string that uses the place method from the helpers. This allows the program to congratulate a user for adopting thier next dog using correct language.

```
  post '/dogs/new' do
    @user = current_user
    @dog = @user.dogs.build(name: params[:dog][:name], age: params[:dog][:age], user_id: @user.id)
    @dog.breed = params[:dog][:breed].keys.first if !!params[:dog][:breed]
    @dog.breed = params[:new_breed] if !params[:new_breed].empty?
    @user.save
    redirect "/dogs/#{@dog.id}?new=#{place(@user.dogs.size)}"
  end
```

#### '/dogs/:id'
This route is responsible for displaying the information for one dog *(if the user is logged in)*. It sets `@dog` by finding the correct dog by the id in the params hash and `@user` with the current_user method.

```  
get '/dogs/:id' do
    redirect "/login?failed=yes" if !logged_in?
    @dog = Dog.find_by(id: params[:id])
    @user = current_user
    erb :'dogs/show_one'
  end
```

#### '/dogs/:id/edit'
It would dumb to think that users would never make mistakes when creating a new dog. This route begins by checking to see if a user is logged in, then sets `@dog`. In the last line, the computer verifies that the user owns the dog they are attempting to edit before displaying the edit page.

```
  get '/dogs/:id/edit' do
    redirect "/login?failed=yes" if !logged_in?
    @dog = Dog.find_by(id: params[:id])
    @dog.user == current_user ? (erb :'dogs/edit') : (redirect "/home?auth=no")
  end
```

As with the user edit route, in the dogs edit route only the field that a user decides to fill in will be changed. For this reason, each attribute is set on its own line, and the line is only run if the field is not empty. At the end, the dog's page is displayed.

```
  post '/dogs/:id/edit' do
    @dog = Dog.find_by(id: params[:id])
    @dog.name = params[:name] if !params[:name].empty?
    @dog.age = params[:age] if !params[:age].empty?
    @dog.breed = params[:breed].keys.first if !!params[:breed]
    @dog.breed = params[:new_breed] if !params[:new_breed].empty?
    @dog.save
    redirect "/dogs/#{@dog.id}"
  end
```

#### '/dogs/:id/tricks/edit'
I went back and forth with this functionality as to whether it should be in the dogs_controller or the tricks_controller. In the end, because a user is not editing the trick itself, but rather only editing which tricks are associated with a particular dog, I decided to place it in the dogs_controller. I am also thinking 'edit' may be a misnomer, because the purpose of this route is to remove unwanted tricks from a dogs profile. However, delete didn't seem like the right name, and because in the view I tell the user that they are being given the opportunity to edit the tricks associated with the account, I decided to keep it as 'edit'.

The route, like many other routes discussed here, checks first to make sure that the user is logged in. Then it sets `@dog` for the display. Finally it displays the tricks_edit view *(but only if the dog belongs to the current user)*.

```
  get '/dogs/:id/tricks/edit' do
    redirect "/login?failed=yes" if !logged_in?
    @dog = Dog.find_by(id: params[:id])
    @dog.user == current_user ? (erb :'dogs/tricks_edit') : (redirect "/home?auth=no")
  end
```

Honestly, I'm not sure if this is the best way to accomplish removing a selected set of tricks from a dogs overall repertoire. However, it works, so I'm keeping it!

Because `@dogs.tricks` is stored as an array, I was trying to find ways to remove specific items from arrays to accomplish this task. I really only knew how to take off the first or last item from an array, or find a way to remove an item at a specific index from an array. This was a bit more complex. However, I learned that arrays could be subtracted from eachother in this format: `[1, 2, 3, 4, 5] - [2, 4, 5]` would return an array `[1, 3]`; this is basically what I needed to accomplish only using trick objects instead of integers. So, I set `@tricks` to the array of the dogs tricks and then made a new array (`delete_array`) by collecting the tricks that the user had checked from the checkboxes in the form. Like the dog breeds, the tricks' ids were stored as keys to a hash in `params[:trick]`. I set `@dog.tricks` to the `@tricks` array minus the delete_array.

```
  post '/dogs/:id/tricks/edit' do
    @dog = Dog.find_by(id: params[:id])
    @tricks = @dog.tricks
    delete_array = params[:trick].keys.collect{|key| Trick.find_by(id: key)}
    @dog.tricks = @tricks - delete_array
    @dog.save
    redirect "/dogs/#{@dog.id}"
  end
```

#### '/dogs/:id/delete'
The delete functionality is the last route defined in the dogs_controller. After checking if the user is logged in, and that the dog belongs to the user, the delete form is rendered.

```
  get '/dogs/:id/delete' do
    redirect "/login?failed=yes" if !logged_in?
    @user = current_user
    @dog = Dog.find_by(id: params[:id])
    @dog.user == @user ? (erb :'dogs/delete') : (redirect "/home?auth=no")
  end
```

Unlike deleting a user account, removing a dog from the profile isn't a huge change, so this form does not authenticate the password. It just finds the dog, deletes the rows in the join table *(see '/delete')*, deletes the dog, saves the changes and redirects to '/dogs' as proof that the dog has been deleted.

```
  post '/dogs/:id/delete' do
    @user = current_user
    @dog = Dog.find_by(id: params[:id])
    DogTrick.all.each do |row|
      row.delete if row.dog_id == @dog.id
    end
    @dog.delete
    @user.save
    redirect '/dogs'
end
```

### tricks_controller.rb
The tricks_controller is the smallest of the controllers, becasue users interact the least with the trick objects. Tricks cannot be altered or deleted after they have been created because all users draw from the same bank of tricks. If one user teaches a new trick to a dog, all users have the ability to add that trick to their dog's repertoire. Let's look at the routes:

#### '/tricks'
This route displays all of the tricks that have been associated with a user's account through that users dogs. One special thing it does before rendering the breakdown page, is it only pulls unique tricks into `@tricks`. This means if a user has two dogs, and teaches sit to both of them, sit will not be listed twice. Rather, it will say the user has taught sit two times. But we'll talk about that functionality more when we are discussing the views.

```
  get '/tricks' do
    redirect "/login?failed=yes" if !logged_in?
    @user = current_user
    @tricks = @user.tricks.uniq
    erb :'tricks/breakdown'
  end
```

#### '/tricks/new'
The biggest responsibility of the tricks_controller is teaching a dog a new trick. Of course, this is only possible if the user is logged in.

```
  get '/tricks/new' do
    redirect "/login?failed=yes" if !logged_in?
    @user = current_user
    @dog = Dog.find_by(id: params[:id])
    erb :'tricks/new'
  end
```

The form at 'tricks/new.erb' pulls all the tricks from the whole database *(with a few exceptions which we'll get into later)* as checkboxes. The form also allows the user to create a new trick that isn't found in the database. 

The first block in the post route sets some variables that I'll need to make use of. 

Then, it collects the tricks from the checkboxes and adds them to the dog's collection of tricks. 

The nested if statements are responsible for the portion of the form that allows a user to make a new trick. First, it only runs this block if the field is not empty. Then, if the computer can find a trick with the same name as the one the user is trying to create, it just uses that one instead of making a whole new trick. `shorten` is a variable whose only use is to keep the next line from being incredibly long. The next block pushes the trick onto the existing `@dogs.tricks` array. If the computer can't find a matching trick, it builds a new one with the parameters from `params[:new_trick]`.

The final block saves the dog and user and redirects to show the dog's page. A query string utelizing the place method lets the computer congratulate a user for teaching the dog another trick.

```
  post '/tricks/new' do
    @user = current_user
    @dog = Dog.find_by(id: params[:dog_id])

    @dog.tricks += params[:tricks].keys.collect{|trick_name| Trick.find_by(name: trick_name)} if !!params[:tricks]

    if !params[:new_trick][:name].empty?
      if !!Trick.find_by(name: params[:new_trick][:name])
        shorten = Trick.find_by(name: params[:new_trick][:name])
        @dog.tricks << shorten if !@dog.tricks.include?(shorten)
      else
        @dog.tricks.build(params[:new_trick])
      end
    end

    @dog.save
    @user.save
    redirect "/dogs/#{@dog.id}?trick=#{place(@dog.tricks.size)}"
  end
```

#### '/tricks/:id'
This route displays the information about a specific trick. Like every other route on this application, a user needs to be signed in to see this page.

```
  get '/tricks/:id' do
    redirect "/login?failed=yes" if !logged_in?
    @user = current_user
    @trick = Trick.find_by(id: params[:id])
    erb :'tricks/show_one'
  end
```

## VIEWS FOLDER
This project has a lot of views, and a lot of the views are pretty standard and boring. Instead, I'm going to use this section to pull out specific blocks of code that I think are interesting or unusual *(in the sense that they don't contain things that Flatiron has taught directly)* and discuss them in a bit more detail.

### views/dogs
We will start with the dogs folder because it is first alphabetically.

#### dogs/show_one.erb
This view contains two interesting blocks, both of which have a similar function. They are responsible for congratulating the user when they adopt a new dog or teach a dog a new trick. The blocks are here:

```
<% if !!params[:new] %>
  <h1>Congratulations!</h1>
  <h3>You have adopted your <%= params[:new] %> dog!</h3>
  <p>------------------------------------------</p>
<% end %>

<% if !!params[:trick] %>
  <h1>Congratulations!</h1>
  <h3><%= @dog.name %> learned his/her <%= params[:trick] %> trick!</h3>
  <p>------------------------------------------</p>
<% end %>
```

Both of these blocks are set up within if statements that check for the existance of a key in the params hash. This key is only present when it has been redirected with a query string. As we discussed in `post '/dogs/new'`, when a new dog is adopted, params[:new] points to a value obtained by running an interger through the place method. This means, after adopting dog number seven, the page would redirect like this -> `'/dogs/<dog id here>?new=seventh'`.  And the user would see a message saying, `Congratulations! You have adopted your seventh dog!`. The second block works much in the same way, but shows the number of tricks a dog has learned.

### views/tricks
This folder comes next alphabetically!

#### tricks/breakdown.erb
This view is responsible for showing the breakdown of all the tricks a user has taught their dogs. Coming into this view, `@tricks` is set to only unique tricks, so there won't be duplicates if a user has two dogs who know the same trick. However, I wanted to include functionality that would let a user know how many dogs know a listed trick. Here's teh block that accomplishes this:

```
<% if @tricks.size > 0 %>
  <% @tricks.each do |trick| %>
    <% number = @user.tricks.select{|t| t.name == trick.name}.size %>
    <ul>
      <li><a href="/tricks/<%= trick.id %>"><%= trick.name %></a> (<%= number %> times)</li>
    </ul>
  <% end %>
<% end %>
```

Specifically, the line `<% number = @user.tricks.select{|t| t.name == trick.name}.size %>` sets a variable `number` which the `<li>` tag uses to display the number of times the trick has been taught. It goes through the full collection of a user's tricks *(which has not pulled out duplicates)* and selects only the tricks that match the name of the trick being referenced in the iteration through `@tricks`. The variable `number` gets set to the size of the selected array. So the user would see a list of links to specifics about each of the tricks associated with their account, followed by a parenthetical addition telling them how many times they have taught that trick to one of their dogs.

#### tricks/new.erb
This is the form responsible for teaching tricks to a dog. At first, I was thinking that it would just create checkboxes for every trick in the database, but I wanted to get a little more specific. Take a look at the if statements that are run in each of the iterations through `Trick.all`:

```
  <% Trick.all.each do |trick| %>
    <% if trick.name.empty? %>
      <% trick.delete %>
    <% elsif @dog.tricks.include?(trick) %>
      <% next %>
    <% else %>
      <p><input type="checkbox" name="tricks[<%= trick.name %>]"> <%= trick.name %> </p>
    <% end %>
  <% end %>
```

I was running into a bug where blank tricks were being created and kept showing up on this form. So the first thing I did was delete them if the trick didn't have a name. I have since solved this bug, but I'm keeping the condition in, just in case. The `elsif` condition tells the computer to skip to the next iteration if a dog already knows that trick. This way, the computer will only make checkboxes that are 1) not blank and 2) the dog doesn't already know.

#### tricks/show_one.erb
This view has a nifty little blocks in it, that I think is kind of cool. It is for displaying the difficulty of the trick. Take a look:

```
<h2>Degree of difficulty:</h2>
<% @trick.difficulty.times do %>
  x
<% end %>
<% (10 - @trick.difficulty).times do %>
  -
<% end %>
```

I intended for the degree of difficulty to be out of 10, but I did not simply want to display a number on the screen. I decided to go with a graphic representation of the difficulty. First, it types out `x` once for each degree of difficulty. Then, it fills up the rest of the 10 spots with `-`. So if a tricks difficulty is 7, the user would see the following: xxxxxxx--- 

### layout.erb
This file is present in every view, as it yeilds to any other view within it. I decided to include my home and log out links in this file, so users would always have those options available to them, no matter where they are in the applicaiton. This would prevent users from navigating themselves into corners and getting lost in the inner recesses of the application.

## Wrap-up
And there you have it, Trick-Tracker!

# THE END

