# Introduction to R for journalists 
(prepared for the European Journalism Centre in December 2020)

The training is intended to combine an introductory video with material the learner can follow along during the session and later.

The video is [here]([https://youtu.be/iUbxpQ9mjEg)
It lasts 73 minutes and includes explanations of why journalists would use R, and the basics of the RStudio IDE.

## Introduction
  [Intro](https://youtu.be/iUbxpQ9mjEg?t=0) is the general introduction explaining the usefulness of R

## Chapter 1 - RStudio 
  [Chapter 1](https://youtu.be/iUbxpQ9mjEg?t=91) introduces you to R and RStudio and explains how to get set up for the first time, including downloading and running packages.

## Chapter 2 - setting up (continued)
  [Chapter 2](https://youtu.be/iUbxpQ9mjEg?t=499) continues the setting up of R and RStudio and explains how to set up a project, and why it is best practice to do so.

If you plan to follow along, all the data you need for the session is [here](https://github.com/Stonepeople/-NISData-Training/blob/main/data.zip)

Download it, unzip it and save the three files it contains to your hard drive in a folder you can find later.

It's easiest if you follow the instructions for creating a project and then save the files to that project folder.

From here on we are writing and running code.
The code used in each session is set out below, and you are welcome to copy it into your scripts, but it's usually best to type it yourself, rather than pasting it, so you start to build up some muscle memory of writing the code you will use a lot in interrogating your data.

# Chapter 3 - importing data
  [Chapter 3](https://youtu.be/iUbxpQ9mjEg?t=1406) is where we start importing the data and exploring it.

First we run the tidyverse set of packages for this session. It's easy to forget this step - but you need to tell R what packages to run for every session. If you switch to another project without closing R, you will still need to tell it what packages to run for the new project. 

```{r}
library(tidyverse)
```
Now we need to read in the data, starting with the `facilities.csv` dataframe - which should be in the project folder you created for this lesson.

```{r}
fac <- read_csv("facilities.csv")
```
It should appear when it's loaded as an "object" in your Environment pane - top right of RStudio. 

You can give it any name you like - fac is nice and short, and is fairly clear what it refers to. In choosing the name the most important thing to think about is your future self, re-using the code in a few months' time and wondering what everything means!

If you click on the name `fac` in the Environment pane the dataframe will open in another pane, looking a bit like a spreadsheet. You can examined it, if you want, as you would a spreadsheet. But now that we're working in a scripted program, it's quite good to get some new habits, and make the most of what simple written commands can do for you. (When I first started using R I quickly realised that I was addicted to looking at my data in a spreadsheet, clicking and scrolling to see what was in it, using the filter menu to see what values were in my freshly opened dataset. So I had to force myself to look at my data in a new way.)

First question you might ask yourself - what's in my columns - ie, what are the variables called?

Type and run this command:
```{r}
names(fac)
```
A list of names appears in the console. Another way to look at your data is to ask R to summarise it - 

```{r}
summary(fac)
```
This quickly looks at every column, counts up the number of missing values, and does a basic statistical analysis of any column containing numbers - maximum, minimum, mean etc. It's quite hard to read it all in the console. So you might like this summary - which is part of the tidyverse suite: 

```{r}
glimpse(fac)
```
As you see, this "prints" the list of columns as a table, telling you what type of data each column contains, and showing a sample of the first few values in each column. 

What about seeing a summary of all the values in a particular column? How many different ones are there?  

```{r}
n_distinct(fac$countryCode)
```
n_distinct() counts the number of distinct values in a column. We tell R which one by calling the name of the data frame, and the name of the column, with the $ to separate the two

```{r}

unique(fac$countryCode)
```
unique() asks R to print a list of one of each value. But we can go a step further with this line of code:
```{r}
fac %>% count(countryCode, sort = TRUE)
```
In English this is saying to R - take the dataframe fac, THEN count how many times each country code is listed in it. sort = TRUE asks it to sort in descending order. 
The `%>%`, which you can insert with `CTRL + SHIFT + M` goes at the end of a line of code, and tells R there's a further instruction to come, which has to be performed on whatever you've already done.

Before you type this line - see if you can work out what it's going to do... 
```{r}
fac %>% count(parentCompanyName, sort = TRUE)
```


# Chapter 4 analysing your data
  [Chapter 4](https://youtu.be/iUbxpQ9mjEg?t=1798) is about making the equivalent of a pivot table, and using a filter to view only the data you want to analyse

```{r eval=FALSE, include = TRUE}

fac2 <- read_csv("~/facilities.csv",
                       locale = locale(encoding = "ISO-8859-1"))

E_PRTR_facilities <- readxl::read_excel("~E-PRTR facilities.xlsx")

```

# Chapter 5 filtering and analysing your data

  [Chapter 5](https://youtu.be/iUbxpQ9mjEg?t=2672) goes into a more complex, but very powerful, element of filtering which allows you to filter by parts of a word, or for lists of terms.
  
  Now let's read in another of the datasets we imported at the start - the pollutant release data. 

```{r eval=FALSE, include=TRUE}

rel <- read_csv("releases.csv")

```
Take a quick look at it, as we did with `fac`:

```{r}
names(rel)

n_distinct(rel$mainActivityName)

```

Let's have another quick summary of it - let's see how much pollution each industry (mainActivityName) is responsible for. We use the `count()` function as before, but instead of just counting how many times each industry is mentioned, we put `wt =` meaning we want a Weighted Total. In other words, it will add up all the mentions of each industrial activity multiplied by the number of kilograms of pollutant emitted. We sort in descending order - and we can use T instead of typing the whole word, TRUE. (`T` and `TRUE`, by the way, are "reserved" terms in R - you can't use them for anything else - so you can't name a data frame TRUE, for example. There is a shortish list of reserved terms which are there to avoid confusion and failed code...)

If we're looking at the data in search of patterns and simple analysis, we will probably run a series of `count()` instructions - 

```{r}
rel %>% count(mainActivityName, wt = totalPollutantQuantityKg, sort = T)

rel %>% count(pollutantName, wt = totalPollutantQuantityKg, sort = T)

rel %>% count(mainActivityName,  pollutantName, wt = totalPollutantQuantityKg, sort = T)

```
As you will have seen, the results of each of these counts is reported in the console - in other words, it disappears from view as we run more code, and it will disappear completely from our hard drive once we end our session - though the beauty of scripted languages is that everything we have done in this session can be rerun from the saved copy of the script when we next open it - we don't have to type it all again. 

Now we're going to do a bit more analysis, and this time we're going to get R to create a new object which will appear in the Global Environment list, top right: 

Let's see which country has been emitting the most CO2. This is going to be the R equivalent of making a pivot table in Excel

Here's what the next code block does - 
it creates a new object or mini dataframe, taking the `rel` dataframe we just read in. And then...
it filters it - taking only the rows containing "Carbon Dioxide" in the `pollutantName` column. And then...
groups the rows by countryName and parentCompanyName. And then
summarises - adds up the totalPollutantQuantity for the resulting groups. And then..
arranges them in descending order of total_release

Here is the R version of all that:

```{r}
CO2_country_list <- rel %>%
  filter(pollutantName == "Carbon dioxide") %>%
  group_by(countryCode, parentCompanyName) %>%
  summarise(total_release = sum(totalPollutantQuantityKg)) %>%
  arrange(desc(total_release))
```

Now try to work out what this block is going to do - then copy it into your script and run it: 

```{r}
CO2_sector_list <- rel %>%
  filter(pollutantName == "Carbon dioxide") %>%
  group_by(countryCode, mainActivityName) %>%
  summarise(total_release = sum(totalPollutantQuantityKg)) %>%
  arrange(desc(total_release))
```
This next block takes everything EXCEPT CO2 and does the same kind of grouped analysis:

```{r}
NON_CO2_sector_list <- rel %>%
  filter(!pollutantName == "Carbon dioxide") %>%
  group_by(countryCode, pollutantName, mainActivityName) %>%
  summarise(total_release = sum(totalPollutantQuantityKg)) %>%
  arrange(desc(total_release))
```

# Chapter 6 Enhance your data by joining two dataframes and adding a new calculation
In [Chapter 6](https://youtu.be/iUbxpQ9mjEg?t=3154) we join two dataframes together to allow us to do some more detailed analysis. 

Let's begin by creating a list of industrial sectors which are NOT emitting CO2. Here's the code - see if you can work out what each line is doing - 

```{r eval=FALSE, include=TRUE}
NON_CO2_sector_list <- rel %>%
  filter(!str_detect(pollutantName, "dioxide")) %>%
  group_by(countryCode, pollutantName, mainActivityName) %>%
  summarise(total_release = sum(totalPollutantQuantityKg)) %>%
  arrange(desc(total_release))
```

We gave our new dataframe a name - it's good practice to make the name something that makes sense to your future self or to colleagues you may be working on a story with. `NON_CO2_sector_list` makes sense to me - but feel free to create a name of your own. 

We filter the `pollutantName` column by detecting the string "dioxide" but use the `!` at the beginning of the instruction to tell R we DON'T want "dioxide", we want the rows which don't include it. 

We group by countryCode, pollutanName and mainActivityName and then create a new variable - "total_release" which is the sum of the totalPollutantQuantity for each group, and then sort the groups in descending order of "total_release". 

Now, before we do a new example - we need to cover something which we didn't have time to put in the video.  

# Chapter 6A Using lists and %in% to do more complex filters

By now you're probably getting into the swing of copying the code we're using, and so we didn't record this as a video demonstration. 

Supposing you want to filter for more than one value - EU members, say, using their two-letter code recorded in the CountryCode column. 

Let's run this bit of code - 

```{r}
EU_members <- c("BE", "NL", "LU", "FR", "DE", "IT", "IE", "DK", "SE", "SI", "HR", "PL", "HU", "AT", "GR", "FI","CZ",
"SK", "ES", "PT", "EE", "LT", "LV", "BG", "RO", "CY", "MT")

```

You will see EU_members appear in the Environment panel as an "object". What's the c doing before the open parenthesis? It stands for "concatenation" and tells R that there's more than one value inside the parentheses - it contains a list, in other words. 

Now that you've recorded the list with the label "EU_members" you can call that instead of typing or pasting the whole list every time you need it. Try this bit of code: 

```{r}
EU_releases <- rel %>%
  filter(countryCode %in% EU_members)
```

The `%in%` means, in effect, find any of the strings listed in the chosen column - a kind of multiple "either/or". 

So we can now create a new dataframe based on `EU_releases` - 

```{r}
EU_releases_by_country <- EU_releases %>%
  group_by(countryCode) %>%
  summarise(total_releases = sum(totalPollutantQuantityKg)) %>%
  arrange(desc(total_releases))
```

Wouldn't it be useful to compare the total amount of pollutant to the size of the country? To do that we need to combine data from the "areas.csv" dataframe which was included in the session data and should therefore be in the project folder you created - 

Let's read it in - 

```{r}
areas <- read_csv("areas.csv")

```

It is a list of most of the world's territories by size. Now we need to pull out the EU members. You may be able to work out now what code you need, but here is mine to compare yours with - 

```{r}
EU_areas <- areas %>%
  filter(countryCode %in% EU_members)
```

So now, in order to work out how much pollution there is in kg per square kilometer we are going to join the two dataframes - if we just wanted to keep the joined dataframes we could use code something like this - 

```{r}
joined <- left_join(EU_releases_by_country, EU_areas)
```

The `dplyr` package which is doing the joining needs to know which two frames to join (EU_releases_by_country and EU_areas) and, in theory, which variable to use to make the join. If you leave that blank, as I did here, `dplyr` will guess, and it is usually correct. But if you want to specify which variable is the common one, use `by = "variableName"`

To read more about dplyr's joins, type `?dplyr::join` into the Console. This brings up the relevant help page in the Help pane (bottom right of standard RStudio view).

Now we can work out the emission per sq km with the `mutate` command. We take the joined dataframe and ask R to add a new column, which will be the total_release divided by the tot_area_km_sq - and make a new dataframe with the helpful title `EU_releases_by_country_sq_km` while we're at it. 

```{r}
EU_releases_by_country_sq_km <- joined %>%
  mutate(release_per_sq_km = total_releases / tot_area_km_sq )
```

At this point in your R learning journey you will probably want to do things step by step, partly because if something goes wrong it's easy to see where. But as you get more experience (and more confident) you can write longer code blocks - linking the steps with the pipe ` %>% ` as you go: this is exactly what the pipe is good for. 

Here we can make the new dataframe from the joined frames and add the calculated column "release_per_sq_km" in one shortish block: 

```{r}
EU_releases_by_country_sq_km <- left_join(EU_releases_by_country, EU_areas) %>%
  mutate(release_per_sq_km = total_releases / tot_area_km_sq )

```



That works fine where there's a simple 2-letter code like the CountryCode. But we've already seen that there are some columns in our data which have whole phrases in them. We have already used `str_detect` to find strings such as "poultry" - if you want to filter for any row in the mainActivityName column containing "poultry" - 

```{r}
fac %>% 
filter(str_detect(mainActivityName, "poultry"))

```

But what if we want to find either "pigs" OR "poultry"? If those words were the only values in the rows we are looking for we could just use something like -
```{r}
pigs_poultry <- c("pigs", "poultry")
```

to set up a list of filter words, and then, as with EU_members, call `%in%` to ask R to find either word. 

The problem is that the word "poultry" occurs in long strings such as "Installations for the intensive rearing of poultry with 40,000 places for poultry". So, when you want to use `str_detect` to find strings in a list, you need to use a different instruction. Let's do it - 

First let's create the list of words to look for: 

```{r}
pigs_poultry <- c("pigs", "poultry")
```
Run that line to make it appear in your environment pane. 

Now use it to ask R to find pigs or poultry in the mainActivityName column in the `rel` dataframe: 

```{r}
poultry_or_pigs <- rel %>%
  filter(str_detect(mainActivityName, paste(pigs_poultry, collapse = "|")))

```

Instead of using `%in%` to find matches in a list, because we're using `str_detect` we tell R to look in mainActivityName as usual, but then we have to tell it to `paste` the values from our pigs_poultry object, and then find either value in any one row - and to make it look for EITHER value, we need to tell R to use the Boolean symbol for either - the pipe `|`: the command `collapse` tells it what symbol we are using for either/or (yes, even though the `|` doesn't appear in our data at all!)

This way of using `str_detect` to find any string in a list is a bit confusing at first - it took me a long time to find it. The code you need to write to do what seems quite a simple task is easy to forget if you don't use it every day - and that makes it a good example of how, the more you use R, the more snippets of useful code will end up in your own script library. So, if you vaguely remember using `collapse` at some point, you can find it again, maybe weeks later, by using `Edit > Find in Files` and setting RStudio to find `collapse` in any of your R scripts. Your saved scripts will normally only contain code that worked, so as you use R, you are building up a library of code which works - and can easily be found and used again - you just need to remember a key word that RStudio's search engine can find - so, although you might forget `collapse` you can still call up all the times you used `str_detect` and then find one use which jogs your memory. I use this all the time, often finding code I haven't used for many months, but which is sitting there waiting for me to look for it and use it again!


# Chapter 7 making elegant graphics with ggplot
[Chapter 7](https://youtu.be/iUbxpQ9mjEg?t=3442) starts to show you the power and flexibility of `ggplot` so you can begin to create graphs.

This chapter is self-contained, in that you don't have to re-run all the code we've used before. If you're starting here, you need to read in your two main dataframes:

```{r}
fac <- read_csv("facilities.csv")
rel <- read_csv("releases.csv")

```

Then, for the sake of an example graph, we will create a new dataframe based on "rel" which looks at the the 50 highest amounts of pollutant in each country in the year 2018. 

```{r}
rel2 <- rel %>%
  filter(reportingYear == 2018) %>%
  group_by(pollutantCode, countryCode) %>%
  summarise(totalKG = sum(totalPollutantQuantityKg)) %>%
  arrange(desc(totalKG)) %>%
  mutate()
  head(50)
```


Now we call our new dataframe using `ggplot`:

```{r}
ggplot(rel2) +
         aes(countryCode, totalKG) +
         geom_col() +
         coord_flip()

```

First important thing to note is that instead of using the now familiar pipe ` %>% ` to tell R there's another instruction to come, we have to use `+`. This is a historical quirk of `ggplot` and you just have to get used to it!

`ggplot` looks a bit complicated at first, especially for those of us (me included) who are used to dragging and dropping variables in pivot tables to make charts in Excel or Googlesheets. But it is worth sticking with it, because `ggplot` is much more powerful and flexible once you get the hang of it - and is brilliantly documented, with hundreds of tutorials and examples online. 

The `gg` of `ggplot` means "Grammar of Graphics", and what we have is a set of questions or instructions which should be at the back of your mind: 
What data are you going to visualise? `ggplot(data)`
Which variables are going in your `x` and `y` axis? `aes(countryCode, totalKG)` in this instance. (aes is short for aesthetic)
What type of graph are you going to plot? In this case a column chart `geom_col()` There are roughly 50 different geoms in ggplot, which you can read about by typing `?geom` in the console and scrolling through the list of all the options. 

When you first start using ggplot it is easy to make mistakes and spend ages trying to get your code to work. I confess, I cheated and used a neat little package called `esquisse` which allows you to drag and drop variables where you want them and get the graph to look the way you want it. Then you can copy the code which esquisse writes for you into your script. In theory only you will know you used esquisse to do the hard work! 
Because graphs were the least important part of my data analysis for a while, I used esquisse while I learned what code I needed to make the graphs I wanted

In the video we use this code

```{r}
esquisse::esquisser(rel2)

```

to start sketching our `rel2` dataframe into some shape. 

In the video, esquisse converts our dragging and dropping into this code block, which we copy into our script - 

```{r}
ggplot(rel2) +
  aes(x = countryCode, weight = totalKG) +
  geom_bar(fill = "#0c4c8a") +
  theme_minimal()
```

A further bit of dragging and dropping produces this - 

```{r}
ggplot(rel2) +
  aes(x = countryCode, weight = totalKG) +
  geom_bar(fill = "red") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

but in this case, we added a line `theme(axis.text.x = element_text(angle = 90, hjust = 1))` which turns the labels to an angle of 90 degrees so that they don't end up written over each other in a jumbled mess. To find that piece of code I did a google search which included the words - "ggplot axis label text angle"; there were several similar answers and I just had to copy and paste them into my script until one worked. Once that's in your saved scripts, as noted above, you can always find it using `Edit > Find in Files` with a search for `angle =`. 
This is how one learns new tricks in R - and unlike Excel, you don't need to watch an instructional video: a line of code will often be all you need. And when you have time, you stop to look at exactly what's going on in the code you're using - then you can try varying some of the instructions to see what happens. 

# Chapter 8 Getting Help
This brings us neatly to [Chapter8](https://youtu.be/iUbxpQ9mjEg?t=3818) which contains advice on how to get help, within R itself, and on the web in general.
