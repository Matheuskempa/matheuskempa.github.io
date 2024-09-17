---
layout: post
author: matheus
name_displayed: Matheus Kempa
image: pdf_photo.jpg
subtitle: A PDF generator engine for a invoice template.
language_tag: EN 
---

#### Table of Contents

1. [Introduction](#introduction)
2. [Project](#project)
    - [Build your html file](#build-your-html-file)
    - [Jinja](#jinja)
        - [Code Example](#code-example)
    - [Wkhtmltopdf](#wkhtmltopdf)
3. [Results and Conclusions](#results-and-conclusions)
4. [References](#references)


<br>
---

## Introduction

Welcome back! This project is a bit outside the realm of Data Engineering, but it’s always good to explore other areas. They strengthen you as a professional, and in the end, you’ll have more tools to tackle your challenges. This particular problem was proposed by the business area in two of my previous jobs, and my approach was similar to what’s described in this post.

I firmly believe that the best way to learn is by doing. So focus on the concepts behind this post—understanding why and how each tool is used is crucial. Let me recap the project steps so that you can replicate and start projects from scratch, without simply copying and pasting. Smartly navigating through the process will ensure you emerge on the other side without major concerns

## Project

Imagine you run a company—any service you like—and now you need to export customer invoice bills as PDFs. But here’s the catch: you can’t rely on canvas because you have a multitude of customers. How do you tackle this challenge? Well, fear not! We’re about to solve it using Python and the Jinja module. Don’t worry—it won’t be overly complex, and even if you have basic Python skills, you’ll find it manageable.

Now, let’s address the elephant in the room: some problems may seem noobie, but they mirror the reality faced by many companies. Not every challenge is mind-bendingly intricate; sometimes it’s about finding the right tools and putting in the hard work.

So, here’s our mission:

* Create a PDF generator engine.

Let’s roll up our sleeves and dive into the solution! For those who like spoilers [here is the complete solution](https://github.com/Matheuskempa/bills_repo).


### Build your html file

First, you need to create an HTML file to use. For mine, I found a template online and modified it to suit my needs. However, you can choose any template you prefer.

[My template is right here!](https://github.com/Matheuskempa/bills_repo/blob/master/bills/template_bill.html).

Pay attention to the body of this HTML—there are many \{\{\}\} and \{\{  %% \}\}. tags. This is crucial, and I’ll explain why, just don't panic.


### Jinja

Remember when I emphasized the importance of those brackets? Well, now it’s time to unveil their magic! Enter Jinja—a versatile, lightning-fast templating engine¹. In our scenario, Jinja takes center stage as the renderer for the templates we’ve crafted. But how does it work? Through those very brackets and special placeholders we strategically placed in our files! These placeholders allow us to inject dynamic content into our templates, almost like writing Python code within HTML.

In a nutshell, Jinja excels at templating and generating dynamic content. Let’s explore some real-world use cases. If you’re on the software development path, you’ll encounter Jinja in Flax and Django. But if you’re leaning toward the data engineering side (like me), Jinja pops up in DBT and Airflow. Now, here’s the twist: In Airflow and DBT, you won’t witness Jinja’s inner workings directly; you’ll only see the results it produces.

In Airflow, Jinja templates shine when defining dynamic tasks and handling templating within Directed Acyclic Graphs (DAGs). Meanwhile, in DBT, Jinja flexes its muscles for variables, loops, macros, functions, and even environment variables.

Now, let’s circle back to our original problem: building a PDF engine. Here’s the catch—Jinja doesn’t fully satisfy our needs here. It excels at templating, but for PDF generation, we require more. Jinja handles the HTML templating part beautifully, but we’ll need additional tools to convert that templated HTML into polished PDFs.

#### Code example

To enable Jinja to process the code, we must pass data to it. In the case of our DataFrame, I converted it to a dictionary just because of convinience, but there wouldn't be problem on passing a dataframe directly. In my case specifically, I designed a mechanism in my Python code that breaks down the DataFrame into manageable chunks—each representing a page—and delivers only the relevant dictionary data to Jinja, (annoying logic haha).  So make it simple, how do we deliver this information to Jinja? Just like this:

```python
from jinja2 import Template

template = Template("<h1>Hello my friend, My name is {{ nome }}!</h1>")
output = template.render(nome="Bob")

print(output)
```
***Output***
``` python
<h1>Olá, Mundo!</h1>
```

Simple as that, but in our case, we’ve created numerous variables within the HTML. Now the task at hand is to pass the values for all those variables, and once that’s done, we’re good to go! 

For passing data through a pandas DataFrame, you should follow the example below exactly:

<style>
pre code {
    background-color: #1e1e1e;
    color: #dcdcdc;
    padding: 10px;
    border-radius: 5px;
    display: block;
    overflow-x: auto;
}

pre code .keyword {
    color: #569cd6;
}

pre code .string {
    color: #ce9178;
}

pre code .comment {
    color: #6a9955;
}

pre code .function {
    color: #dcdcaa;
}
</style>

```python
from jinja2 import Template
import pandas as pd

data = {'Coluna': ['Linha 1', 'Linha 2', 'Linha 3']}
df = pd.DataFrame(data)

template_str = """
{% raw %}
{% for linha in dataframe['Coluna'] %}
  <h1> {{ linha }} </h1>
{% endfor %}
{% endraw %}
"""

template = Template(template_str)
rendered = template.render(dataframe=df)
print(rendered)
```
***Output***
```python
<h1> Linha 1 </h1>
<h1> Linha 2 </h1>
<h1> Linha 3 </h1>
```




The full repo to run this code is [here](https://github.com/Matheuskempa/bills_repo).


### Wkhtmltopdf 

Almost done! With Jinja handling our rendered HTML, the next step is to transform it into a PDF file. For this task, we’ll turn to wkhtmltopdf.

Wkhtmltopdf and wkhtmltoimage are open-source (LGPLv3) command-line tools that render HTML into PDFs and various image formats using the Qt WebKit rendering engine. The beauty of these tools lies in their “headless” operation—they don’t require a display or display service.²

In our code, we utilize a library called PDFkit, which internally leverages wkhtmltopdf. I’ve already included the executable in the folder, but if you’re on a Linux platform, you’ll need to install Wkhtmltopdf separately. Windows users, make sure to choose the correct version for your system.


## Results and Conclusions

Here is our final PDF, already rendered! Take a look:

<style>
  .responsive-object {
    width: 100%;
    height: 100vh; /* Full viewport height */
  }

  @media (max-width: 768px) {
    .responsive-object {
      height: 80vh; /* Reduce height for smaller screens */
    }
  }
</style>

<object data="{{ site.url }}{{ site.baseurl }}/assets/documents/historico_fatura_123456.pdf" 
        class="responsive-object" 
        type="application/pdf">
</object>

Jinja is widely used, so understanding how it works can be incredibly valuable! It opens up many possibilities for various tasks. I hope this project provided valuable insights, and I genuinely hope you enjoyed the post! For further exploration, I recommend diving into the documentation and references mentioned below. You’ll find all the code in my GitHub repository. Looking forward to seeing you in the next post!

---

<br>

## References

Here are the full ducomentations sites:

1. JINJA. Documentation. Available on: https://jinja.palletsprojects.com/en/3.1.x/.
2. WKHTMLTOPDF. Documentation. Available on: https://wkhtmltopdf.org/.


