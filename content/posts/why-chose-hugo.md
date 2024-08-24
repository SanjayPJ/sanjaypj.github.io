---
title: "Hugo: A Simple Approach to Blogging"
draft: false
date: 2024-08-22T09:16:45.000Z
description: "A simple blog built with Hugo, offering a clean, fast, and distraction-free experience. Explore topics ranging from Tech, Business and Life, all powered by the simplicity of Markdown and the efficiency of Hugo."
categories:
  - Tech
tags:
  - Hugo
---

I use Hugo for my blog, I think the inspiration is https://nithinkamath.me/.

This is the reasoning behind choosing hugo.

My first choice was always do something with nextjs+markdown or gastby or any other flashy javascript framework. then i thought about the time I have to develop the systems or finding a perfect fork or plugins. so NOOOO...

Hugo, Its fast. Not using javascript and 100 million dependencies a good thing, It uses Go, and it seems to be fast.

Customization. I tried customizing it, and its pretty simple.

Simple, I dont need a lot of functionality for the first time, and need to know where its going. so bare minimum functionalities are enough.

It is JAMStack.

And I love writing Markdown.

so lets start

## Step 1: Install Hugo

```
choco install hugo-extended
```

## Step 2: Create a New Hugo Project

Once Hugo is installed, we can create a new project for our blog:

Open Terminal: Launch your terminal or command prompt.

Navigate: Use the cd command to navigate to the directory where you want to create the project.

Create Project: Execute the following command, replacing myblog with your desired project name:

```
hugo new site myblog
```

## Step 3: Choose a Theme

Hugo offers a vast collection of themes that can be used to customize the appearance of your blog. To select a theme:

Browse Themes: Explore the available themes on platforms like Themes.gohugo.io.

Download Theme: Choose a theme you like and download its ZIP file.

Extract Theme: Extract the downloaded theme to the themes directory within your project.

also check the instructions in the theme to set up the blog properly.

## Step 4: Create Content

Now that you have a basic structure in place, it's time to create content for your blog. Hugo uses Markdown files to store content. To create a new post:

Create Post: Use the following command:

```
hugo new posts/my-first-post.md
```

Write Content: Open the newly created my-first-post.md file and write your blog post content using Markdown syntax.

### Step 5: Serve Your Blog Locally

Start Server: Execute the following command:

```
hugo server
```

Access Blog: Open your web browser and visit http://localhost:1313/ to view your blog.

Simple right?

Happy blogging ;)
