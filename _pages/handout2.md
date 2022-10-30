---
title: "Exercise 3"
permalink: /in-class-ex-3/
toc: true
mathjax: true
---

# Summarizing variables and making tables
One of the key starting points of analyzing data for research is examining the central tendancy and distribution (or spread) of the variables of immediate interest to the study. In this lab, we will walk through how to think about summarizing a variable and a relationship between two variables with a few simple graphs, summarizing several variables in a sample, and then creating tables we can use in our research reports. We will do this using data from the American Time Use Survey once again and we'll explore two broad outcomes: time spent alone and the effect of a young children on time use patterns.

## Installing Packages
Before we begin, however, we need to install a few user-created packages that will let us look at our data in a clear way and then create nice, clean tables after we run some analysis.

First, we will install `fre` which is short for "frequency." This command allows us to see tabulations of the values and labels of categorical variables in one place with a single command - a very useful and convenient need when we are first assessing variables in our data. To install it, simply run:

```
ssc install fre, replace
```

The `ssc install` command searches the STATA package repository, finds `fre` and installs it for use on your computer. You only need to do this once and you are good to go.

Next, we need to install the `estout` package, which includes a suite of commands that allow us to store estimates in STATA's temporary memory and then output them into formatted tables for use in Word, Excel, or a number of other formats. The process is the same:

```
ssc install estout, replace
```

Now we have the tools we need to get going on looking at variables and making tables!

## Some Simple Graphs
### Aging and Loneliness
Let's start with looking at the relationship between time spent alone and age. We might expect that as people age, they spend more time alone and given the effect of excess loneliness on mental health, this might be something we would be concerned about and could try to counteract with programs, volunteers, and other means. Both of these are continuous variables, so plotting the relationship between them using a scatter plot is sensible and appropiate.  We will add some wrinkles to this as we go, but let's start with the basics - simple scatter plot.

```
scatter trtalone teage
graph export "output\scatter1.png", replace
```

![Scatter Plot of Time Alone and Age](http://stevebholt.github.io/rpad399/assets/images/scatter1_ex3.png)

Note that the first line of the code generates the scatter plot and the second line tells STATA to save the graph as an image in my output folder. PNG files are useful and flexible - you can drag and drop them into work, text them as pictures, or add them to websites.

As we might have predicted, there seems to be a slightly positive, weak relationship between age and the minutes someone spends alone on a typical day. Let's see if it varies by gender.

```
scatter trtalone teage, by(tesex)
graph export "output\scatter2.png", replace
```

![Scatter Plot of Time Alone and Age](http://stevebholt.github.io/rpad399/assets/images/scatter2_ex3.png)

Hm. It seems like it might - there seems to be a slightly stronger relationship between the two in the female side of the graph, but it's too hard to tell from the plots alone. Let's add the best-fit line to each of these and see how that looks. The best-fit line will show us the slope of the relationship between age and minutes spent alone on a typical day. It will also give us a better visual cue - as steeper line will give us some indication that the relationship is stronger.

```
scatter trtalone teage, by(tesex) || lfit trtalone teage, by(tesex) lcolor(red)
graph export "output\scatter3.png", replace
```
![Scatter Plot of Time Alone and Age](http://stevebholt.github.io/rpad399/assets/images/scatter3_ex3.png)

Note that we added the two vertical lines (`||`). In STATA graphs, this can be used to overlay graphs. Everything after this part of the code is the instructions on what variables should define the line and how to mark the line to make it visible (in this case, changing the color to red). As we can see, there does seem to be a stronger relationship between time alone and age among women than men. Note though that there are more observations among women at the older end of the age distribution - likely driven in part by women living longer, on average, than men. At least some of the stronger relationship here is likely related to this difference.

Beyond gender, we might also consider whether income matters for time spent alone. Higher income corresponds to wide variety of other benefits - from mental and physical health to family stability and life satisfaction. Before we try to graph this, let's take a look at the categorical variable `income_cat` in our dataset with our handy new command `fre`:

```
fre income_cat

income_cat -- HH income
----------------------------------------------------------------------
                         |      Freq.    Percent      Valid       Cum.
-------------------------+--------------------------------------------
Valid   1 < $20K         |        371      24.73      24.73      24.73
        2 $20K to $40K   |        345      23.00      23.00      47.73
        3 $40K to 60K    |        240      16.00      16.00      63.73
        4 $60K to 75K    |        137       9.13       9.13      72.87
        5 $75K to $100K  |        176      11.73      11.73      84.60
        6 $100K to $150K |        121       8.07       8.07      92.67
        7 > $150K        |        110       7.33       7.33     100.00
        Total            |       1500     100.00     100.00           
----------------------------------------------------------------------
```
Here, we can see we have seven categories of income. We don't want to look at 7 different scatterplots - that's a lot to take in at once - so let's make things easy on ourselves and create a binary variable that identifies only the richest and poorest households in our data. For this, instead of using `gen` and `replace` like [we did in our previous excercise](https://stevebholt.github.io/rpad399/in-class-ex-2/#working-with-variables), we are going to learn a new command called `recode`. Recode is a useful command for collapsing a categorical variable with many categories into a smaller number of categories.

```
recode income_cat (1 = 1 "Low-income") (7 = 2 "High-income") (2/6 = .), gen(hilowinc)
label var hilowinc "High- and Low-income"
```

In the code above, each statement in parentheses is used for a new category. In the first, we tell STATA to use the first category in the original variable and to set it to be the first value in our new variable with a new label of "Low-income." The second, we take the 7th category of the original variable, the highest income bracket, and make it the second category in our new variable with a new label. Finally, demonstrating the flexibility of recode, we set all values from 2 to 6 in the original variable (the `/` tells STATA that this should be all numeric values between these two values) to be missing values in the new vairable. Finally, in our option section (the part of the code past the comma), we tell STATA to create a new variable called `hilowinc` with those new categories. The second line of code labels our new variable for a more intuitive reminder of what this variable is telling us.

Now we're ready to look at the relationship between age and time spent alone on a typical day separately by high and low income.

```
scatter trtalone teage, by(hilowinc) || lfit trtalone teage, by(hilowinc) lcolor(red)
graph export "output\scatter4.png", replace
```
![Scatter Plot of Time Alone and Age](http://stevebholt.github.io/rpad399/assets/images/scatter4_ex3.png)

Here, we can see that unlike with gender, the strength of the relationship between time alone and age is pretty similar across low- and high-income people. The slope is about the same - the line is just higher among low-income people, which tells us that low-income people spend more time alone in a typical day on average at each age.

### Children and Time Use Patterns
In addition to simple aging, having a child can alter how we spend our time dramatically. Importantly, looking at households that have young children and those that don't gives us a chance to look at some graphs that show the relationship between a continuous variable (time) and a categorical variable (whether someone has a child less than two years old or not). 


