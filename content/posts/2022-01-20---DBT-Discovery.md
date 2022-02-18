---
title: "DBT: The analytics engineer tool"
date: "2022-01-23T15:00:37.121Z"
template: "post"
draft: false
slug: "dbt-analytics-engineer"
category: "Data Analytics"
tags:
    - "Data"
    - "Analytics"
    - "Engineering"
    - "DBT"
description: "In this post we discover what is DBT, how it works and why it's useful"
socialImage: "/media/article_images/dbt/dbt-logo.png"
---

As a Data Analyst, I'm very interested in Analytics Engineering, analytics and visualization.

And to do that I like [Looker](https://looker.com/) a lot. It enables me to perform very quick analysis thanks to its explore, I easily visualize my data and make it available to my stakeholders and last but not least I can modify my data and how it's rendered straight from the tool, thanks to the LookML language.
It is Looker's internal language enabling you to make your data available in the UI of Looker but also and more importantly to transform your data.

This makes Looker very powerful and a very compelling offer to me.
However, Looker has a flaw: it's proprietary. It would be awesome to have an Open Source tool to do what Looker does, the main part here being the transformation part made possible thanks to LookML.
And when you look to open source tool equivalent to LookML, the entire internet points you to [DBT](https://www.getdbt.com/).

## What is DBT

DBT stands for "Data Build Tool". It's a tool enabling you to transform your data, really inscribed in an ELT vision and focused on the T part.
Its goal is to enable faster development for analytics engineers by staying as close to SQL as possible while enabling some neat functionalities:
1. Being able to test the models (tables or views) you build with DBT.
2. Being able to document all your tables straight in the code, columns and get a website to explore this documentation.
3. The code is hosted on a git controlled repository, so it's fully versioned.

It exists in 2 version:
- DBT Core: Open Source and free to use. It gives you all the functionalities mentioned above.
- DBT Cloud: The paid version, with an online IDE, where everything is integrated.

## Using DBT Cloud

You can try out DBT on their website by setting up a database with example data in Snowflake or Bigquery for instance. I recommend the [DBT fundamentals](https://courses.getdbt.com/courses/fundamentals) tool to have guided instructions about that and discover the tool easily.

If you do that, you'll be able to access DBT Cloud's IDE.

![DBT Cloud interface](/media/article_images/dbt/dbt_ui.png)

It's a fairly classic interface with your file structure on the left and your code at the center.

### Models

DBT is all about models, a model matches roughly a view or a table. To create a new model you essentially just have to create a new `.sql` file in the models directory and write your query.
Voilà, you have created a new model.

Now what is interesting about DBT is that by referencing the source table of your query, you can get a DAG representing your pipeline.
To do that, you have two functions available to you:
- `ref()`: You use it when you reference a model already created in DBT
- `source()`: You use it when you want to reference a source table, stored in your database.

By doing so you get the nice dag as shown in the capture above.
This enables you to see very quickly all the dependencies within your pipeline, and go upstream if you need to understand your data.

Now this is for creating the model, but in DBT you also have `.yml` files. They serve a referencing and documentation purpose.

### Documentation and testing
Thanks to `.yml` files you can:
- Declare you data sources so that you can use them with the `source()` function.
- Document your models ! You created models, you can add descriptions (text or markdown) to them and to each column. It will be used to generate documentation afterwards.
- Test your models ! You can test each column of your model with generic tests:
  - `unique`: Each value of your column should be unique
  - `not_null`: Each value of the column should be not null
  - `relationship`: Each value of the column should match a value of a reference table
  - `acceptable_values`: Each value of the column should match a defined list.
  If that's not enough for you though, you can still declare any test you want using a custom SQL query and putting it in the test folder.
  And what's even better is that tests are all SQL in the end, so you can always see the SQL that was run and test it yourself in case something went wrong for debugging.

### Commands

So far we have developed models, we documented them and tested them. This is cool but what we want actually is the data in our database.
To do that, you just have to execute the command `dbt run` (in the UI if you use dbt cloud).
Upon that command DBT will run your code and generate all your data in a **development** environment associated to **you** and only you (typically a schema on Snowflake).
You can therefore now see your data in your database, play with it and check that it matches your expectations.

There are two other useful commands:
- `dbt test`: It enables you to run the test you have declared. You can narrow two the scope by passing arguments if you don't want to test everything each time.
- `dbt docs generate`: This will generate the files for your documentation website where you will be able to graphically explore your data and its definitions.


### Documentation

So you have generated documentation, how can you use it ? 
Well via the UI ! You have a button just aside the commit/PR button written `view docs` taking you straight to the documentation website.
And it looks something like this:
![DBT Cloud Documentation interface](/media/article_images/dbt/dbt_docs_ui.png)

You get the models you have defined on the left, along with your sources.
At the center, your table with some details, the description of the table you have inputted in the `.yml` file and also description for columns with type data imported from your warehouse.
You also have what your model depends on and what it is referenced by.

You'll notice a button at the bottom right, this button enables you to see a DAG graph of all the dependencies in your model, or when on a specific table, only the dependencies of this table.
It looks like this:
![DBT Cloud Documentation DAG](/media/article_images/dbt/dbt_lineage_graph.png)


It's pretty useful to see your pipeline that way easily.

### Production

Now that you have developed your models and are confident that everything works as intended you can make a pull request for your code.
It will be reviewed by your peers and when approved you'll be able to merge it to the production base.

Now the production base will have its own deployment environment and schedule that you can set up via DBT Cloud fairly easily.
It could be once every day, or more, or less, as your needs require.
You could also have several schedules building different part of your pipeline.

## Conclusion

DBT is a neat tool to transform your data. It's easy to use and I feel like it's a very efficient way of transforming your data.
It's very powerful and in some aspect I believe it may be more advanced than LookML for transformation purposes.
But Looker and LookML still have one thing DBT does not. 
When you modify your LookML you get your new transformations directly into looker in your development interface, enabling you to iterate very quickly and efficiently on your analytics workflow.
DBT being just the transformation part it can not do that. There is an alternative to Looker called [Lightdash](https://www.lightdash.com/) based on DBT, but the integration is not as well-made as Looker is. For instance, you need to wait for your code to be in DBT to see modifications in Lightdash.

So as much as I liked the tool, I believe Looker still as the advantage here in terms of functionalities and ease of use. You could use DBT and Looker together, but I'm concerned you might have to generate a lot of similar code to have your data in Looker. I'm pretty confident it's not ideal, although it's possible.
But if you're not using Looker, it's definitely worth considering and likely to be a good investment.
