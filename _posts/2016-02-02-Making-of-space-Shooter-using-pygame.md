---
layout: post
title: "Making of space Shooter using pygame"
description: "Making of space Shooter"
tags: [linux, windows, pygame, gamedev, python]
comments: true
share: true
cover_image: '/content/images/2016/1/spaceshooter.jpg'
---

<link rel="stylesheet" href="http://tasdikrahman.me/public/css/milligram.min.css">

I procrastinated enough in writing this post so here it goes. `Pygame` treated me good. So good that it was able to create a decent enough `2-D` game in a day!

So here is my breakdown of it.

## Creating the basic rectangles

<center><img src="http://i.imgur.com/50qgY67.jpg"></center>

I used the [pygame-boilerplate](https://github.com/prodicus/pygame-boilerplate) which I made in the process of making this game.

It's nothing groundbreaking. Provides just a basic starting ground for you to base your pygame projects. Saves you some gruntwork.

## Adding the enemy sprites

<center><img src="http://i.imgur.com/HorSt1T.jpg"></center>

At this point, the collisions needed to be added. When the player collided with a mob sprite, the game needs to end.

## Adding the icons for all

<center><img src="http://i.imgur.com/QV57Zqb.jpg"></center>

Adding the icons for the sprites was not that hard. I got the icons from [opengameart.org/](http://opengameart.org/), more particulary from the [Space shooter content](http://opengameart.org/content/space-shooter-redux) pack from [@kenney](http://opengameart.org/users/kenney).

License for them is in `Public Domain`. This pack is a gem of a package. I mean you get all what you need in this pack!

The sound effects came next which included the explosions for the mob sprites as well as the player.

How about adding sound effects when shooting the missiles? Done deal!

## Finishing it up

<center><img src="http://i.imgur.com/1Zraayf.jpg"></center>

Done with the sound effects. The explosion animations. Was Left with adding things like high scores, player lives, health bar. 

Something which I had not planned were powerups like shields and power ups. Dealing with Github feature requests anybody?

So this is what is what the main menu looks like 

<center><img src="http://i.imgur.com/3MzfmbT.jpg"></center>

In the end. I loved making this game a lot and I hope you make something much cooler than this. Do share it when you do. 

Here's the git repo if you are interested in taking a look at the source

***

**Space Shooter** [prodicus/spaceShooter](https://github.com/prodicus/spaceShooter)

Wanna have a nostalgic trip back to your childhood playing it?

<a class="button" href="https://github.com/prodicus/spaceShooter/releases/download/v0.0.3/spaceShooter-v0.0.3_windows.zip" target="_blank">Download for windows</a>

<a class="button" href="https://github.com/prodicus/spaceShooter/releases/download/v0.0.3/spaceShooter-v0.0.3_linux.zip" target="_blank">Download for windows</a>

***

Watch me how I fared on it 

<iframe width="420" height="315" src="https://www.youtube.com/embed/o99zpLsM-ZI" frameborder="0" allowfullscreen></iframe>

Happy coding!