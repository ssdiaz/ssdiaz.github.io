---
layout: post
title:      "CLI Project for Curly-Haired Shoppers"
date:       2021-09-22 03:21:40 +0000
permalink:  cli_project_for_curly-haired_shoppers
---


This post is an overview of my GitHub repository **Curly Product Check**, my first CLI portfolio project. 

I remember a friend once said she didn’t use hair products with alcohol listed in the ingredients because it dried out her long, beautiful, curly hair. So I started to look at the ingredients in hair products and noticed nearly all contained some sort of alcohol. After researching, I found that certain types of alcohol cause frizz for curly hair because it’s meant to clarify and strip the hair of moisture, mostly benefiting straight hair. But to my surprise, there are actually some alcohols that can be good for curly hair because they provide moisture. 

My CLI project helps with this confusion. When running the program, the user selects a product based on a hair category and brand. It scrapes the best-selling hair products from Ulta Beauty Online, and iterates through the ingredients to see which type of alcohol, if any, are found in the product. This is to help curly-headed customers, like myself, identify products safe for curly hair instead of relying on pretty packaging. 

To start the build, I found creating a flow diagram as a rough draft helped me visualize the classes and their roles, which kept the project in line with object-oriented programming. 

I first created the CLI class, as this interacted with the user and mapped the overall flow of my program. I used many iteration methods to print data the user selected based on arrays scraped from the other classes. One area of improvement in my CLI class is to utilize more helper methods.

I then started to build the Scraper class to pull in data from the Ulta Beauty website. This was built in tangent of the other classes: Category, Brand, Product, and Ingredient. While most of these classes have similar methods, it was best to keep them separate when calling them in the CLI file. 
 
The hardest part of this project was starting. It took me too long to come up with an idea, but I’m really excited about what I created because it’s something I use in my personal life, and has already influenced my next shopping trip to Ulta. I really enjoyed designing the flow and thinking about what my user wanted out of the experience. 

I encourage you to check it out!

[ssdiaz/curly_product_check](https://github.com/ssdiaz/curlyproductcheck)


