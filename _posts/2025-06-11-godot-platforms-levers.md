---
layout: post
title: '[Godot Tutorial] Pressure plates, switches, levers and buttons.'
categories: [Godot, Gamedev, Coding, Programming, 2D]
---

[comment]: <> (My goals for people to learn)
[comment]: <> (* TOC)
[comment]: <> ({:toc} this is how you get table of contents)

In this tutorial we'll tackle basics of interactions. After finishing you should be able to create all kinds of 

**You'll learn how to:**
- Create gameplay events when player or objects step into them (we'll use pressure plates as example)
- Make interactive switches and buttons (objects with state)
- Give that little bit of polish to make thing looks good while simple
- Approach problems without resorting to external help (yes, looking at you ChatGPT)

**My assumptions are:**
- You have a character that can walk based on CharacterBody2D node.


## 1. Pressure Plate

I like when I can see what's going on so we'll start by creating sprites for pressed and unpressed platform.

Feel free to use mine if you're not into creating sprites for this tutorial:

| ![pressed](/images/tutorials/tutorial_platforms/platform_pressed.png){:height="256px" width="256px"} | ![smiley](/images/tutorials/tutorial_platforms/platform_unpressed.png){:height="256px" width="256px"}|
|:--:|
| *pressed.png* | *unpressed.png* |

### 1.1 Area and sprites
We need to know when player enters and exits our platform. Usually to tell if something interesects some other space we use colliders. But colliders will block our movement so what are our options instead?

The standard procedure would be to use some volume or shape that acts as collider but without doing actual collisions. They can be called various names: in Unity it's called a Trigger - in Godot - **Area** (or specifically **Area2D** in my case).

Create **Area2D** node in the scene and rename it to 'Platform' or any other fitting name.
Then, add two **Sprite2D** as children of the **Area2D** node. Finally - set 'Pressed' sprite to not visible. You can do it either by *'Eye' icon* in **Scene** tab or *CanvasItem -> Visibility* category in **Inspector**. Make sure position of sprites is set to 0 in all dimensions so they're centered under area node.

Final result should look as follows:
![pressed](/images/tutorials/tutorial_platforms/1_1-hierarchy.png)

### 1.3 Collision layers
In order to detect player entering platform we need to make sure collision will be properly detected. There's two important settings to check every time we're setting up new **Area2D**.

#### 1.3.1 Monitoring and Monitorable
These two settings of **Area2D** are responsible for deciding the direction of collision detection as follows:
- Monitoring - **Area2D** is checking if anything enters and exits
- Monitorable - other **Area2D**s can detect this **Area2D**

Double click 'Platform' node of type **Area2D** and check your **Inspector** tab.

We need to detect player entering the **Area** and don't care about anything else detecting it. Therefore - set **Monitoring** to **True** and **Monitorable** to **False** as below.

![monitorable_settings](/images/tutorials/tutorial_platforms/1_3_1_monitorable.png)

#### 1.3.2 Layers and Mask
Objects can be present on different layers - usually we want a seperate layers for player collision, hurtboxes etc.

Before doing any scripting, there's some necessary setup left to be done. need one more node addition. Godot seperates idea of Area and it's shape - therefore - we need to add CollisionShape2D as child of 'Platform' node. Set area to be 'monitoring' but not 'monitorable' as well as set Mask to match layer of your player and everything else we want to be able to interact with platform - in my case these are layers 1, 2 and 3 for Props, Player and Enemies.


### 1.4 Basic script

```gdscript
#pressure_plate.gd
extends Area2D

var is_pressed = false

@onready var pressed_sprite = $Pressed
@onready var unpressed_sprite = $Unpressed

func _ready() -> void:
	body_entered.connect(on_body_entered)
	body_exited.connect(on_body_exited)

func on_body_entered(body):
	if is_pressed == true:
		return
	
	pressed_sprite.visible = true
	unpressed_sprite.visible = false
	
func on_body_exited(body):
	if is_pressed == false:
		return
	
	pressed_sprite = false
	unpressed_sprite = true

```


Since we know we need to react on both entering and exiting we'll write functions to handle both situations.




![](/images/reverie-demo.png)

## 2. Features overview

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

## 3. Using Reverie on GitHub Pages

### 3.1. Step 1) Fork Reverie to your User Repository

Fork [this repository](https://github.com/amitmerchant1990/reverie), then rename the repository to `yourgithubusername.github.io`.

Alternatively, you can use [Use this template](https://github.com/amitmerchant1990/reverie/generate) button if you want to create a repository with a clean commit history which will use Reverie as a template.

Your Jekyll blog will often be viewable immediately at <https://yourgithubusername.github.io> (if it's not, you can often force it to build by completing step 2)

### 3.2. Step 2) Customize and view your site

Enter your site name, description, avatar and many other options by editing the `_config.yml` file. You can easily turn on Google Analytics tracking, Disqus commenting and social icons here.

Making a change to `_config.yml` (or any file in your repository) will force GitHub Pages to rebuild your site with jekyll. Your rebuilt site will be viewable a few seconds later at <https://yourgithubusername.github.io> - if not, give it ten minutes as GitHub suggests and it'll appear soon.

### 3.3. Step 3) Publish your first blog post

Create a new file called `/_posts/2019-2-13-Hello-World.md` to publish your first blog post. That's all you need to do to publish your first blog post! This [Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) might come in handy while writing the posts.

> You can add additional posts in the browser on GitHub.com too! Just hit the <kbd>Create new file</kbd> button in `/_posts/` to create new content. Just make sure to include the [front-matter](http://jekyllrb.com/docs/frontmatter/) block at the top of each new blog post and make sure the post's filename is in this format: year-month-day-title.md

## 4. Using Categories in Reverie

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

## 5. RSS

The generated [RSS feed](https://en.wikipedia.org/wiki/RSS) of your blog can be found at <https://yourgithubusername.github.io/feed>. You can see the example RSS feed over [here](https://www.amitmerchant.com/reverie/feed).

## 6. Sitemap

The generated sitemap of your blog can be found at <https://yourgithubusername.github.io/sitemap>. You can see the example sitemap feed over [here](https://www.amitmerchant.com/reverie/sitemap).

## 7. License

MIT


