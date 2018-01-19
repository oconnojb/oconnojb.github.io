---
layout: post
title:      "a RUBY in the rough"
date:       2017-12-04 13:13:53 -0500
permalink:  a_ruby_in_the_rough
---

Want to learn some Ruby? Great! Let's do it!

## VARIABLES
Sometimes, when we say one thing we actually mean something else. It's not lying, *per se*, but a matter of efficiency. Let's say we collected a user's date of birth on a website and we want to write some code that discusses their age. It's easy to figure out the age of a person if you know the date of birth, just take the present year and subtract the birth year *(ok, its a bit more complicated than that, but for simplicities sake...)*. That calculation is easy to make, but it's not fun to write out every time you want to discuss the person's age. Wouldn't it be easier to just tell the computer "Hey, when I say 'age', I'm talking about the current year minus the year the person was born!"

Variables allow us to do this. How? With the assignment operater `=` *(It looks like an equals sign)*, that's how. Maybe I want to make a new variable called 'number' which is assigned to the number 7. It would look like this:

```
number = 7
```

Now, when I write 'number', the computer knows I'm talking about 7! This lets us do some crazy stuff! This code declares a new variable, `another_number`. What do you think its value is?

```
number = 7
another_number = number + 2
```

*If you guessed 9, you're right! You must be a pro!*

In order to count as a variable in Ruby, the variable name needs to fit a few qualifications:
1. The variable name can't start with a number. This means `7_variable` would be a bad variable name.
2. The variable can't include punctuation or spaces, so don't go naming a variable `'.! ;'.`
3. The variable can't be named using a word that Ruby uses for something else, like `if` or `elsif`.
4. The variable must start with a lowercase letter. Our example above would'nt've worked if I named the variable `Number`.

It is also important to note *(especially for anyone familiar with Javascript variables)* that Ruby prefers working with variables using underscores to separate words as opposed to "camelCase". In short, `this_is_a_good_variable` while `thisIsABadVariable`.


## DATA TYPES
Ruby is built to work with six different types of data. 

1. Numbers
2. Strings
3. Booleans
4. Symbols
5. Arrays
6. Hashes

Ruby interacts with these data types using methods. We'll talk more aobut methods later. Until we get into more detail, know that, when writing about methods, programmers sometimes precede their name with a # to help clue readers in. It is just easier to type #greeting than 'the greeting method', I guess! **Note: don't put that hashtag(#) in the code itself, it's only to be used when referencing methods outside of the code!**

### 1. NUMBERS

Ruby knows numbers just like people do. We don't even have to do anything special to let Ruby know it's dealing with a number. Just type the number, the program will know what to do.

Ruby splits numbers into two categories. Fixnums are whole numbers, Floats are numbers that include a "float" a.k.a. a decimal point.

Here are some examples of Fixnums:
* 1
* 25
* 1000000

Here are some examples of Floats:
* 1.8
* 35.678
* 0.12

Ruby has some useful built-in methods for working with numbers. Two example methods for operating on Floats are #floor and #ceil. #floor rounds down a float while #ceil *(short for ceiling)* rounds up a float. 

In this example, I am setting a variable equal to a float an then operating on that float using a period `.` *(a dot operator)*. The information to the left of the dot operator is what the method will act upon, and the information to the right of the dot operator is the method that will do the operating. Here, I am storing the answers as new variables. *(Plus a bonus)* Check it out, what do you think each variable will be stored as after we run this code?

```
original_float = 6.25
floored = original_float.floor
ceiling = original_float.ceil
bonus = floored + ceiling
```

* *original_float is still set to 6.25*
* *floored is set to 6*
* *ceiling is set to 7*
* *bonus is set to 13*

### 2. STRINGS

A string is a set of characters strung together. The characters in a string can be any characters you can think of, but be warned! Computers can't actually read. The computer just keeps those characters together so you can access them again later. Tell a computer you are giving it a string by wrapping it in quotations. Here are a few variables set to strings *(and a challenging one)* What are they set to?

```
num_1 = "The string"
num_2 = "and"
num_3 = "the other string."
bonus = num_1 + num_2 + num_3
```

*num_1 is set to "The string"
num_2 is set to "and"
num_3 is set to "the other string."
bonus is set to "The stringandthe other string."*

Why no spaces? Becasue we didn't tell the computer to put one in! Here are two different ways of getting the bonus to be a real, readable sentence:

**1**
```
num_1 = "The string "
num_2 = "and "
num_3 = "the other string."
bonus = num_1 + num_2 + num_3
```
*I added a space to the end of the first two strings so they combine well together!*

**2**
```
num_1 = "The string"
num_2 = "and"
num_3 = "the other string."
bonus = num_1 + " " + num_2 + " " + num_3
```
*I added the spaces manually as new strings being added in*

Ruby has some built in methods for operating on strings. #size counts the characters in the string, #upcase puts the whole string in capital letters, #capitalize capitalizes the first letter of the string, and #reverse will reverse the order of the letters. There are more too! Do a google search to see all the cool things Ruby expects you to do with a string. Here are my examples in action! What do the results look like?

```
example = "i am a string"

ex_1 = example.size
ex_2 = example.upcase
ex_3 = example.capitalize
ex_4 = example.reverse
ex_5 = example.capitalize.reverse
ex_6 = example.reverse.capitalize
```

1. 13 *(Don't forget: spaces are characters too!)*
2. "I AM A STRING"
3. "I am a string"
4. "gnirts a ma i"
5. "gnirts a ma I"
6. "Gnirts a ma i"

### 3. BOOLEANS

If you've spent any time learning about libraries, you might be familiar with the word boolean. Quite simply, it means true or false. Equally simply, Ruby recognizes the words `true` and `false` as booleans, so no special notation is needed to let the computer know what we mean!

When we're talking booleans, it's useful to know the comparison operator `==` *(double equal sign)*. It works by comparing the two things on either side of the operator. If they are the same, it returns true. If they are different, it returns false. See if you can figure out the boolean value of the next examples:

```
Ex 1
1 == 1

Ex 2
1 == 2

Ex 3
"Hello" == "Hello"

Ex 4
"Hello" == "hello"

Ex 5
1 == "1"
```

1. true
2. false
3. true
4. false
5. false

Any of the examples stump you? Remember, if they aren't exactly the same, the cumputer processes it as false. In Ex 4, one string has a capital H and the other doesn't so they are different! In Ex 5, 1 is a number and "1" is a string so the computer says they're different *(Even if we might know otherwise)*.

### 4. SYMBOLS

Symbols are like strings in the sense that they keep track of characters, but each instance of a string needs to be stored separately, even if they look the same (which takes up more memory). Symbols will take up less memory becasue each instance calls the original symbol. But, unlike strings which are fully editable, symbols cannot be operated on, so you had better like it when you make it! *If that doesn't make sense to you right now, that's ok!*

Just know that symbols represent a piece of data.

Symbols are declared using a colon `:` and look like this: `:this_is_a_symbol`.

Symbols are often used as keys for hashes, but we'll worry aobut that when we get to number 6 *(below)*. Keep reading to find out more!

### 5. ARRAYS

Arrays are collections of Ruby objects that can be accessed by their spot in the array. Oh look! A wild array appeared! 

```
wild_array = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

This array's name is 'wild_array'. The contents of the array are the numbers one through ten. The computer knows where an array starts and stops because of the square brackets `[]` that surround it.

Arrays can be operated on just like strings and numbers. Here's a new array and some methods, can you figure out what the output would be?

```
jumble = [2, 5, 7, 9, 1, 4, 6, 10, 3, 8]

ex_1 = jumble.sort
ex_2 = jumble.reverse
```

1. [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] *#sort arranges the array in numeric order!*
2. [8, 3, 10, 6, 4, 1, 9, 7, 5, 2] *#reverse reverses the order!*

Arrays can store more than just numbers. Arrays can store strings or even other arrays. Arrays can *(gasp!)* even store a variety of types of data! Here are some legitimate, three item arrays:

```
array_1 = [1, 2, 3]
array_2 = ["one", "two", "three"]
array_3 = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
array_4 = [1, "two", [3, "four", 5]]
```

If we want to get some info out of an array, we need to know the information's index. The index is simply the data's position in the array. Trickily, arrays are 0 indexed, so the first slot in the array is index 0, the second slot is index 1, and so on. We access this information by putting the name of the array followed by the index we want to access in square brackets. In this example, I will access the number 5 from our wild_array from earlier!

```
wild_array = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
accessed_number = wild_array[4]
```

Here, the `accessed_number` variable is now set to 5!!

### 6. HASHES

Hashes exist as key - value pairs. They are kind of similar to arrays in that they can store a variety of information. Arrays are defined with square brackets `[]` but hashes are defined with curly brackets `{}`. Key - value pairs are linked with a hash rocket `=>`. Here is an example hash:

```
my_new_hash = {
  "a key" => "a value",
	"a different key" => "a different value"
}
```

It is very common to see symbols used as keys in hashes. Above, we talked about symbols being preceded by a colon, but when being used as a key, the colon goes at the end and replaces the hash rocket. Like this:

```
hash_with_symbols = {
  a_symbol_key: "a value",
	another_symbol: "values don't need to be strings",
	more_symbols: 12,
	"not all of them need to be symbols" => ["an array", "inside a hash?", "wild"]
}
```

However, unlike arrays *(which are accessed by an index number)*, Hashes are accessed by their keys. For example, `hash_with_symbols[:more_symbols]` will point to the number `12`.

## METHODS
Ruby executes methods to perform tasks. If you know anything about Javascript, Ruby's methods are similar to Javascript's functions.

A method is defined using the `def` keyword *(Def is short for define)* and ends with the `end` keyword. Pretty straightforward, right? Right. Let's do an example:

Here, I'll define a method called 'say_hi' which will do somthing pretty expected.

```
def say_hi
  puts "Hi!"
end
```

*#say_hi's purpose is to output the string "Hi!".*

Ruby lets us pass outside information to a method using arguments. Arguments are defined in parentheses after the method name and can be referenced like variables within a method's body. Here, I'll define a method whose purpose is to add 2 to any number it's given, and return the answer to the user.

```
def add_two(number)
  answer = number + 2
	return answer
end
```

To make use of this method, I call it with the number I want to pass it in the parentheses. `add_two(6)` will return `8`.

Back to #say_hi: I am realizing it's much more polite to refer to people by name. I can code a new method that will add the name to the greeting by passing the name to the method as an argument. But, in order to utizize that argument in my solution, I need to use something called "string interpolation". Inside a string, Ruby allows us to access variables by wrapping them in `#{variable_here}` Here, I'll make a more polite greeting method. What will this method output if I call it with `greeting("Mary")`?

```
def greeting(name)
  puts "Hello, #{name}. It is a pleasure to meet you."
end
```

*`greeting("Mary")` will output the string "Hello, Mary. It is a pleasure to meet you."*

**It is important to note that string interpolation only works inside double quotation marks `"` and not single quotation marks `'`.**

## Challenge yourself
What will the following code output?

```
1. array = [2, 5, 7, 9, 1, 4, 6, 10, 3, 8]
2. re_array = array.sort
3.
4. def challenge(argument)
5.   twist = 2
6.   puts "I am #{argument[twist]} years old!"
7. end
8.
9. challenge(re_array)
```

The call `challenge(re_array)` will output `I am 3 years old!`. Why? Let's break it down, line by line:
1. sets the variable `array` equal to a jumble of numbers
2. sets a new variable `re_array` equal to `array` sorted: *[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]*
3. A blank line :)
4. defines a new method `challenge` with an argument 
5. defines a variable `twist` equal to the number `2`
6. sets the output. We use string interpolation to tell the computer we want to access `argument` array at index `twist` a.k.a. index 2
7. ends the method's definition
8. Another blank line :)
9. Calls #challenge and passes it the argument of `re_array`

*Our challenge method is given the array `re_array` as its argument and accesses index 2. Remember, array's are 0 indexed, so `re_array[2]` is `3`. The method interpolates the number 3 into the string being output.*

## Is that it?
Well, that's it for me at this moment. Is there more to learning Ruby? OF COURSE!! But with these basic skills, you're well on your way! What else can you learn about Ruby? The internet is your friend!

*Until next time!*



