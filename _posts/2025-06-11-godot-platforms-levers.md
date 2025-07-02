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

### 1.1. Area and sprites

We need to know when player enters and exits our platform. Usually to tell if something interesects some other space we use colliders. But colliders will block our movement so what are our options instead?

The standard procedure would be to use some volume or shape that acts as collider but without doing actual collisions. They can be called various names: in Unity it's called a Trigger - in Godot - **Area** (or specifically **Area2D** in my case).

Create **Area2D** node in the scene and rename it to 'Platform' or any other fitting name.
Then, add two **Sprite2D** as children of the **Area2D** node. Finally - set 'Pressed' sprite to not visible. You can do it either by *'Eye' icon* in **Scene** tab or *CanvasItem -> Visibility* category in **Inspector**. Make sure position of sprites is set to 0 in all dimensions so they're centered under area node.

Final result should look as follows:
![pressed](/images/tutorials/tutorial_platforms/1_1-hierarchy.png)

### 1.2. Collision

In order to detect player entering platform we need to make sure collision will be properly detected. There's two important settings to check every time we're setting up new **Area2D**.

#### 1.2.1. Monitoring and Monitorable

These two settings of **Area2D** are responsible for deciding the direction of collision detection as follows:

- Monitoring - **Area2D** is checking if anything enters and exits
- Monitorable - other **Area2D**s can detect this **Area2D**

Double click 'Platform' node of type **Area2D** and check your **Inspector** tab.

We need to detect player entering the **Area** and don't care about anything else detecting it. Therefore - set **Monitoring** to **True** and **Monitorable** to **False** as below.

![monitorable_settings](/images/tutorials/tutorial_platforms/1_3_1_monitorable.png)

#### 1.2.2. Layers and Mask

Objects can be present on different layers - usually we want to seperate layers for player collision, hurtboxes et cetera.
Similarly to **Monitoring** and **Monitorable** - **Mask** and **Layer** settings represent directional information in terms of which collision layers are being used. **Mask** tells us what **Area2D** detects. **Layer** accordingly - at what layers can this **Area2D** be detected.

Considering our goal - we want to leave **Layer** empty and set **Mask** to layer on which player is present. I additionally will set two more layers - for props and for enemies resulting in setup as below.

![layers](/images/tutorials/tutorial_platforms/1_3_2_layers.png)


### 1.3. Collision Shape
**Area2D** itself doesn't describe at what region it is detecting bodies entering. This is job of **CollisionShape** or more precisely in our case - **CollisionShape2D**. This is mandatory node that needs to be added as child of **Area2D** (if we wish to - we can add more than one). Then we'll need to set it up by assigning a new **RectangleShape** (or if you're not using my sprites - any other fitting shape).

You can then either drag the corners of the shape to cover the sprite describe it parameters.
Either way you should end up with setup resembling image below.

![collisionshape_setup](/images/tutorials/tutorial_platforms/1_3_3_collisionshape_setup.png)

With hierarchy looking like this.

![collision_shape_hierarchy](/images/tutorials/tutorial_platforms/1_3_3_hierarchy.png)

Setup is finished. Now let's move to code.

### 1.4. Script

**Area2D** natively provides us with some tools to be able to detect collisions so we'll be keen to use them.
As described in introduction - we assume our player is some kind of body (most likely **CharacterBody2D**). This means we're interested in body detection specifically. Luckily quick peek into **Area2D** documentation (I really recommend reading documentation!) shows us we have two signals to our disposal that do just that - "body_entered" and "body_exited".

In case you haven't encountered signals yet - think of them as someone giving you a phone call when a specified thing - like player entering the platform - happens. In order to use signals you need to 'connect' it to some function that will describe what will happen after signal has fired.

Enough of introduction let's have a quick look at basic code to make platform work.

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
	is_pressed = true
	
func on_body_exited(body):
<mark>	if is_pressed == false: </mark>
		return
	
	pressed_sprite = false
	unpressed_sprite = true
	is_pressed = false
```

Here we have few things that might need explanation.

First of all @onready var 'xxx' = $'yyy' means platform will look for child named 'yyy' and assign it to this variable. In our case we're assigning sprites so make sure your name in script matches with the hierarchy.

Then _ready() function is being called by engine at the start of our platform existance and inside of it - we connect to existing signals. Inside parenthesis we give name of our own functions to be executed - on_body_entered and on_body_exited accordingly. These functions take one additional argument - body.

This information is also present in **Area2D** documentation.

Functions themselves are pretty simple. We take variable - is_pressed - that tracks current platform state and check if state does need to be changed. For example if platform is already pushed we don't want to execute rest of the function so we return(exit) early. Then if state is actually changed we update visibility of our **Sprite2D** images switching between pressed and unpressed sprite.

If you precisely followed the tutorial then you should be able to already test the platform! Start the scene and try to walk inside of **Area2D**. If nothing happens - make sure your name match and that player is an actual body with proper layer being set.

### 1.5. Edge cases

If only player can press platforms and you have only one player in the game - you can skip this part.
In my case I allow enemies to press platforms too - and while it might not be completely obvious right away that there's anything wrong with the code so let me present a video example.

<video width="640" height="360" controls>
  <source src="../images/tutorials/tutorial_platforms/1_4_edge_case.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

As you can probably see even though we have a creature sitting on a platform - it gets unpressed whenever player leaves it.
It happens because we're not really checking for more than one body being present. There several ways to fix it. You can for example add each entering body to a list and remove them from list when theyre exiting. We'll use a built in **Area2D** method instead that we can find if we read through documentation(!!).

The method is called 

{% highlight gdscript mark_lines="21" %}
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
	
	is_pressed = true
	pressed_sprite.visible = true
	unpressed_sprite.visible = false
	
func on_body_exited(body):
	if is_pressed == false or get_overlapping_bodies():
		return
	
	is_pressed = false
	pressed_sprite.visible = false
	unpressed_sprite.visible = true

{% endhighlight %}