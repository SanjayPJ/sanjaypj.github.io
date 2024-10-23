---
title: "Hugo: A Simple Approach to Blogging"
draft: false
date: 2024-08-22T09:16:45.000Z
description: "A simple blog built with Hugo, offering a clean, fast, and distraction-free experience. Explore topics ranging from Tech, Business and Life, all powered by the simplicity of Markdown and the efficiency of Hugo."
categories:
  - Blogging
  - Tech
tags:
  - Hugo
  - Markdown
  - JAMstack
---

So, I’ve decided to use Hugo for my blog, and honestly, I think I got a bit inspired by Nithin Kamath’s site.

At first, I was all set on diving into something flashy with Next.js, Gatsby, or one of those other snazzy JavaScript frameworks. But then reality hit me. I thought, “Do I really want to spend forever developing systems or searching for the perfect plugins?” And my answer was a resounding NOPE!

Enter Hugo. This thing is like a speed demon! No JavaScript, no endless dependencies—just pure, fast goodness. Plus, it runs on Go, which makes it feel like I’m doing something smart for once.

Customization? Oh, it’s a breeze! I gave it a shot, and I was pleasantly surprised at how easy it is to tweak things to my liking.

I’m keeping it simple for my first foray into blogging. I don’t need a million features right off the bat; just the basics to get me started. Less is more, right?

And let’s not forget—it’s all JAMstack, baby!

Oh, and did I mention I love writing in Markdown? It feels like a cozy little secret language for writers.

So, let’s get this party started!

## Step 1: Install Hugo

```bash
choco install hugo-extended
```

## Step 2: Create a New Hugo Project

Once Hugo is installed, we can create a new project for our blog:

Open Terminal: Launch your terminal or command prompt.

Navigate: Use the cd command to navigate to the directory where you want to create the project.

Create Project: Execute the following command, replacing myblog with your desired project name:

```bash
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

```bash
hugo new posts/my-first-post.md
```

Write Content: Open the newly created my-first-post.md file and write your blog post content using Markdown syntax.

## Step 5: Serve Your Blog Locally

Start Server: Execute the following command:

```bash
hugo server
```

Access Blog: Open your web browser and visit http://localhost:1313/ to view your blog.

Simple right?

Happy blogging ;)
