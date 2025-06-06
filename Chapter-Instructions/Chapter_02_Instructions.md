# Chapter 2, Workflow: basics


You now have some experience running R code. Before we go any further,
let’s ensure you’ve got a solid foundation in running R code and that
you know some of the most helpful RStudio features.

------------------------------------------------------------------------

## Coding Basics

Let’s review some basics we’ve omitted so far in the interest of getting
you plotting as quickly as possible. You can use R to do basic math
calculations.

``` r
1 / 200 * 30
(59 + 73 + 2) / 3
sin(pi / 2)
```

You can create new objects with the assignment operator \<-

``` r
x <- 3 * 4
```

Note that the value of x is not printed, it is just stored. If you want
to view the value, type x in the console. (**Hint**: you can also
highlight the object name by double clicking and pressing command +
enter. another simple way, type the object name and run the line)

You can combine multiple elements into a vector with c():

``` r
primes <- c(2, 3, 5, 7, 11, 13)
```

And basic arithmetic on vectors is applied to every element of of the
vector:

``` r
primes * 2
primes - 1
```

All R statements where you create objects, **assignment statements**,
have the same form:

object_name \<- value

When reading that code, say “object name gets value” in your head.

------------------------------------------------------------------------

## Comments

R will ignore any text after \# for that line. This allows you to write
**comments**, text that is ignored by R but read by other humans. We’ll
sometimes include comments in examples explaining what’s happening with
the code.

Comments can be helpful for briefly describing what the following code
does.

``` r
# create vector of primes
primes <- c(2, 3, 5, 7, 11, 13)

# multiply primes by 2
primes * 2
```

With short pieces of code like this, leaving a comment for every single
line of code might not be necessary. But as the code you’re writing gets
more complex, comments can save you (and your collaborators) a lot of
time figuring out what was done in the code…

ALWAYS BE DOCUMENTING (ABC!)

The what and how of your code are always possible to figure out, even if
it might be tedious, by carefully reading it. Figuring out why something
was done is much more difficult, if not impossible. For data analysis
code, use comments to explain your overall plan of attack and record
important insights as you encounter them. There’s no way to re-capture
this knowledge from the code itself.

------------------------------------------------------------------------

## What’s in a name?

Object names must start with a letter and can only contain letters,
numbers, “\_”, and “.”. You want your object names to be descriptive, so
you’ll need to adopt a convention for multiple words. We recommend
**snake_case**, where you separate lowercase words with “\_”.

i_use_snake_case

otherPeopleUseCamelCase

some.people.use.periods

And_aFew.People_RENOUNCEconvention

We will discuss more code style in Chapter 4.

------------------------------------------------------------------------

## Calling Functions

R has a large collection of built-in functions that are called like
this:

function_name(argument1 = value1, argument2 = value2, …)

Let’s try using seq(), which makes regular **sequences** of numbers, and
while we’re at it, learn more helpful features of RStudio. Type “se” and
hit TAB. A popup shows you possible completions. Specify seq() by typing
more (a q) to disambiguate or by using ↑/↓ arrows to select. Notice the
floating tooltip that pops up, reminding you of the function’s arguments
and purpose. If you want more help, press F1 to get all the details in
the help tab in the lower right pane.

When you’ve selected the function you want, press TAB again. RStudio
will add matching opening (() and closing ()) parentheses for you. Type
the name of the first argument, from, and set it equal to 1. Then, type
the name of the second argument, to, and set it equal to 10. Finally,
hit return. Your function should look like:

``` r
seq(from = 1, to = 10)
```

We often omit the names of the first several arguments in function
calls, so we can rewrite this as follows:

``` r
seq(1, 10)
```

Type the following code and notice that RStudio provides similar
assistance with the paired quotation marks:

``` r
x <- "hello world"
```

Quotation marks and parentheses must always come in a pair. RStudio does
its best to help you, but it’s still possible to mess up and end up with
a mismatch. If this happens, R will show you the continuation character
“+”.

The “+” tells you that R is waiting for more input; it doesn’t think
you’re done yet. Usually, this means you’ve forgotten either a ” or a ).
Either add the missing pair, or press ESCAPE to abort the expression and
try again.

Note that the environment tab in the upper right pane displays all of
the objects that you’ve created:

<img src="Images/Ch2_Environment_Tab_Example.png" class="border"
style="width:65.0%"
data-fig-alt="Zoomed in example of environment tab located in the upper right pane display of RStudio"
data-fig-align="center" alt="Chapter 2 Environment Tab Example" />

------------------------------------------------------------------------

## Exercises pt 1 of 1

1.  Why does this code not work?

``` r
my_variable <- 10
my_varıable
```

2.  Tweak each of the following R commands so that they run correctly:

``` r
libary(todyverse)

ggplot(dTA = mpg) + 
  geom_point(maping = aes(x = displ y = hwy)) +
  geom_smooth(method = "lm)
```

3.  Press Option + Shift + K / Alt + Shift + K. What happens? How can
    you get to the same place using the menus?

4.  Let’s revisit an exercise from Chapter 1. Run the following lines of
    code. Which of the two plots is saved as mpg-plot.png? Why?

my_bar_plot \<- ggplot(mpg, aes(x = class)) + geom_bar() my_scatter_plot
\<- ggplot(mpg, aes(x = cty, y = hwy)) + geom_point() ggsave(filename =
“mpg-plot.png”, plot = my_bar_plot)

------------------------------------------------------------------------

**NEXT UP:** [Chapter
3](https://github.com/UCSC-Treehouse/Essential-skills-for-Treehouse-computational-research/blob/main/Chapter-Instructions/Chapter_03_Instructions.md)
