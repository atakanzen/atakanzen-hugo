---
title: "Terminolator"
date: 2022-11-11T23:11:57+01:00
draft: true
author: "Atakan Zengin"
summary: "A web applicaiton that was born from laziness."
description: "A web applicaiton that was born from laziness."
tags: 
    - projects
---

## A little background

It was a quarantine weekend. A busy week with all the work and school stuff was just off. While I was strolling around the web and trying to kill some depressive time, I've come up with an interesting subject. It was about "[Natural Language Processing](https://www.ibm.com/cloud/learn/natural-language-processing)". Of course, as a translation student and a person who loves natural languages, it got my attention quickly. The rabbit hole was there for me, and besides, I hadn't had to think about how I was supposed to spend my quarantine for the weekend as well. 

After some reading and research, I've also come up with the good old **[NLTK](https://www.nltk.org/)**. So two of the best things that I'm capable of were merged in one context, programming, and natural languages. While I was turning NLTK upside down; I had an idea.

> I should make a toy project with NLTK — maybe parse and extract meaningful words from a text?
> 

Then another one hit right away.

> Wait a sec, I have homework about making a terminology on an Excel file — manually — What If I...
> 

Aaand we're off.

## What does this do?

So, since you have a little background about the project's idea, let's check what it does actually. The main goal is to create terminologies in a really fast way. I mean, who likes to create Excel files with so many rows and columns by hand? I don't like it. So, sue me. 

At first, I just wrote a Python script that gets the `.txt` file from the command as an argument and then does the required stuff to it. Let's open this up, step by step.

- Get the `.txt` file from the command's argument and read it.

```python
text = open(sys.argv[1], 'r').read()
```

- Initialize the `translator` (This is a Python API that helps us to use Google's translator engine. More information [here](https://github.com/lushan88a/google_trans_new))

```python
translator = google_translator()
```

- Then set up the **stop words** for the required language.

```python
stop_words = set(stopwords.words('english'))
```

- Tokenize the words from the given file.

```python
tokenized_text = word_tokenize(text)
words = [word.lower() for word in tokenized_text if word.isalpha()]
```

- Define an empty `set` to avoid having the same words in the result, and a `dictionary` to add the **source word** as a `key`, and the **target word** as a `value`.

```python
ordered_words = set()
result = {}
```

So, what is `set` and `dictionary`? I heard you say. Here is a little data-structure course for you,

### Set

```python
# Array (Or List as in Python) is a data structure, that can contain same elements.
# Example of an Array.
my_array = [1, 1, "foo", "bar", "foo", 2, False, False]

# Set is a data structure, that contains only unique elements.
# Now let's convert our Array that we defined above to a Set,
my_set = set(my_array)

# The output will be,
{False, 1, 2, 'foo', 'bar'}
```

### Dictionary

```python
# Dictionary is a data structure, that is very similar to, well, dictionaries.
# It contains items, that are basically key-value pairs. 
# Example of a Dictionary
my_dictionary = {
	"name": "Michael",
	"surname": "Scott",
	"birth_year": 1962
}

# Example of getting a value from a specific key,
my_dictionary["name"] 
# The output will be 
"Michael"
```

Since you have a better grasp on the concepts, let's continue with our script.

- Loop inside the tokenized words, translate them and add them to the dictionary.

```python
for src_word in words:
    if src_word not in stop_words and src_word not in ordered_words:
        ordered_words.add(src_word)
        tgt_word = translator.translate(src_word, lang_src='en', lang_tgt='tr')
        result[src_word] = tgt_word
```

- Set up the `xlsxwriter`, and create the desired column names.

```python
workbook = xlsxwriter.Workbook(f'{sys.argv[1].lower()}_Terminology.xlsx')
worksheet = workbook.add_worksheet()

worksheet.write(0, 0, 'source_language')
worksheet.write(0, 1, 'target_language')
```

- Finally, loop inside the result `dictionary` and write them to an Excel file.

```python
row = 1
col = 0
for src, trg in result.items():
    worksheet.write(row, col, src)
    worksheet.write(row, col + 1, trg)
    row += 1

workbook.close()
```

There you go, now you have a better understanding of what laziness can cause. 

## A trouble shared is a trouble halved

Since this script helped me a lot to get through the cumbersome process of creating terminology on Excel, I wanted to share it with my friends; who are also fellow translator students. However, you might imagine how hard it might be for them to benefit from this script, regarding the possibility of their unfamiliarity with the technical sides of technologies. It turned out, they were unfamiliar. So I've spent some more time and developed a web application for the sake of user experience.

## The web witchery

For the web application, I've used one of my favorite frameworks; **React.js**. The backend logic was already present, the only thing I needed to do was to create a **Single Page Application** (SPA) with decent **User Interface** (UI) and **User Experience** (UX). With the help of **TailwindCSS**, I created my components swiftly. After the user interface was ready, I still needed to transform my script's logic to a server. Because the script was in Python, I decided to go with **Flask**. It was the right tool for this case since it was straightforward, and it doesn't require complex configurations unlike **Django**; no need for overengineering. 

I made two endpoints: one for the application's index route, which renders the static HTML file from the SPA. 

```python
@app.route('/')
def index():
    return app.send_static_file('index.html')
```

And one for the application's API, which provides required logic to the client-side. 

```python
@app.route('/api/', methods=["POST"])
def create_terminology():
	# Application logic occurs here,
	# You can check the source-code from GitHub Repo.
	# https://github.com/ataknz/terminolator.web
```

For the deployment of the application, I went with **Heroku** since it provides a runtime for Python, and again very simple and robust. You can visit the application from [here](https://terminolator.herokuapp.com/).

## What this project taught me?

I think at first, my goal with this project was to get a better understanding of the **NLTK** and **Natural Language Processing** and to run away from time-consuming boring things with the help of automation. However, I've come up with some other challenges on the way as well. Such as, React and Flask being a niche stack. This cost me some time to deploy the application to Heroku, but these kinds of things are the best things to improve oneself. So, appreciate the challenges that you face. 

After the deployment, I shared the application with my friends and had an insight into something very important. The will to create for other people; sharing the benefits. I believe this is the main focus for the open-source community as well.

We should not forget to share. Thanks for reading, have a good one!