---
layout: post
title: '[Godot Tutorial] Pressure plates, switches, levers and buttons.'
categories: [Godot, Gamedev, Coding, Programming, 2D]
---

[comment]: <> (My goals for people to learn)

## Hi! 

In this tutorial we'll try to tackle the fundamental interactions to give you a taste of how to make stuff interact with player in easiest way possible while keeping things to look good.

### You'll learn how to:
- Create gameplay events when player or objects step into them (we'll use pressure plates as example)
- Make interactive switches and buttons (objects with state)
- Give that little bit of polish to make thing looks good while simple
- Train reading documentation

### My assumptions are:
- You have a character that can walk based on CharacterBody2D node.


## Pressure Plate

I like when I can see what's going on so we'll start by creating sprites for pressed and unpressed platform.

Feel free to use mine if you're not into creating sprites for this tutorial.

![](/images/platform_pressed.png)
![](/images/platform_unpressed.png)

Since we want to know when player enters and exits our platform we need a way to detect that. Usually to tell if something interesects some other area we use colliders. But colliders will block our movement so what are our options instead?

When it comes to just detecting presence the standard procedure is to use some volume or shape that acts as collider but without doing actual collisions. They can be called various names in Unity it's called in trigger and in Godot they're called Areas.

That's why we're going to create Area2D as our root node in the scene, this is also the node that we will attach script to later on. First though, let's add our sprites as children of the Area2D node which we'll rename to 'Platform' and set sprite reflecting pressed platform to not visible. Before we'll jump into coding we'll need to do one more node addition. Since godot seperates idea of Area and it's shape we need to add CollisionShape2D and create the shape that matches our visible platform. Last addition - let's set our area to be 'monitoring' but not ;'monitorable' as well as set layers of mask to ones of our player and everything else we want to be able to interact with platform - in my case these are layers 1, 2 and 3 for Props, Player and Enemies.



```gdscript
extends Area2D

signal on_triggered
signal on_released

var is_pushed = false

func _ready():
	body_entered.connect(on_body_entered)
	body_exited.connect(on_body_exited)

func on_body_entered(_body):
	if is_pushed == true:
		return
	
	is_pushed = true
	on_triggered.emit()

func on_body_exited(_body):
	if is_pushed == false or has_overlapping_bodies():
		return

	is_pushed = false
    on_released.emit()



```


Since we know we need to react on both entering and exiting we'll write functions to handle both situations.




![](/images/reverie-demo.png)

## Features overview

- Command-line free fork-first workflow, using GitHub.com to create, customize and post to your blog
- Fully responsive and mobile optimized base theme
- Sass/Coffeescript support using Jekyll 2.0
- Free hosting on your GitHub Pages user site
- All the SEO goodies comes in-built
- Markdown blogging
- Syntax highlighting using Pygments
    - [Dracula syntax theme](https://draculatheme.com/) included
- Disqus commenting
- Google Analytics integration
- Fuzzy search across blog posts
- Pagination of posts works out-of-the-box.
- Categorize posts out-of-the box
- RSS Feed
- In-built sitemap

<div style="text-align: center;">
 <script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CE7D6KJY&placement=wwwamitmerchantcom" id="_carbonads_js"></script>
</div>

## Using Reverie on GitHub Pages

### Step 1) Fork Reverie to your User Repository

Fork [this repository](https://github.com/amitmerchant1990/reverie), then rename the repository to `yourgithubusername.github.io`.

Alternatively, you can use [Use this template](https://github.com/amitmerchant1990/reverie/generate) button if you want to create a repository with a clean commit history which will use Reverie as a template.

Your Jekyll blog will often be viewable immediately at <https://yourgithubusername.github.io> (if it's not, you can often force it to build by completing step 2)

### Step 2) Customize and view your site

Enter your site name, description, avatar and many other options by editing the `_config.yml` file. You can easily turn on Google Analytics tracking, Disqus commenting and social icons here.

Making a change to `_config.yml` (or any file in your repository) will force GitHub Pages to rebuild your site with jekyll. Your rebuilt site will be viewable a few seconds later at <https://yourgithubusername.github.io> - if not, give it ten minutes as GitHub suggests and it'll appear soon.

### Step 3) Publish your first blog post

Create a new file called `/_posts/2019-2-13-Hello-World.md` to publish your first blog post. That's all you need to do to publish your first blog post! This [Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) might come in handy while writing the posts.

> You can add additional posts in the browser on GitHub.com too! Just hit the <kbd>Create new file</kbd> button in `/_posts/` to create new content. Just make sure to include the [front-matter](http://jekyllrb.com/docs/frontmatter/) block at the top of each new blog post and make sure the post's filename is in this format: year-month-day-title.md

## Using Categories in Reverie

You can categorize your content based on `categories` in Reverie. For this, you just need to add `categories` in front matter like below:

For adding single category:

```md
categories: JavaScript
```

For adding multiple categories:

```md
categories: [PHP, Laravel]
```

The contegorized content can be shown over this URL: <https://yourgithubusername.github.io/categories/>

## RSS

The generated [RSS feed](https://en.wikipedia.org/wiki/RSS) of your blog can be found at <https://yourgithubusername.github.io/feed>. You can see the example RSS feed over [here](https://www.amitmerchant.com/reverie/feed).

## Sitemap

The generated sitemap of your blog can be found at <https://yourgithubusername.github.io/sitemap>. You can see the example sitemap feed over [here](https://www.amitmerchant.com/reverie/sitemap).

## License

MIT


