---
title: 'Fourth Post'
date: 2021-01-21T18:01:09-08:00
draft: false
author: Andrew Lloyd
year: '2021'
month: '2021/01'
categories:
  - Personal
  - Thoughts

tags:
  - software
  - html
---

![img](https://miro.medium.com/max/700/1*mpj36aaGse8i4wavnyZwhA.jpeg#center)

As a recent graduate from Lighthouse Labs’ web development bootcamp I am currently working on challenging myself by entering Mintbean.io’s Hackathons. You sign up for a weekly challenge and each Friday a new challenge is revealed. There are limitations, user stories and guidelines to submit your project. This week the challenge was to create a clone of the classic game Pong!

<!--more-->

## Choosing an engine

The first decision to make was to choose a suitable engine to code our game in. The challenge calls for a front end only, no backend and no game engines like Unity3D or UnrealEngine. Here were some of the candidates I identified:

- **React and Redux** — https://www.youtube.com/watch?v=QZcNGfcn-oo
  Whilst the above tutorial proves it is possible to build a game in React and Redux it may not be the most suitable candidate for the physics needed for pong-graphics.

- **JavaScript and HTML5** — https://www.youtube.com/watch?v=nl0KXCa5pJk
  The above tutorial shows a pong game being made using vanillaJS and Html5, there are no set defined methods and you would have to manually code a lot of the mechanics.

- **Pixi.js** –https://danielohn.me/blog/pong-in-pixijs/ — a 2D webGL render that uses Canvas2D as a backup. It’s performant and mainly used for 2d browser games and adding more sophisticated graphics to websites.

- **Phaser** — https://www.youtube.com/watch?v=4dHlbXigtss Possibly one of the more widely adapted technologies for games on the web, has a lot of preset methods for camera, physics, collision, sprites, groups, sound and more. The documentation is pretty detailed also and there are plenty of question and answers to look through on stackoverflow and other websites.

I decided to go with Phaser purely as it was the recommendation on the orientation of the mintbean.io challenge as well as it ticking a lot of the boxes of being as close to a game engine as you can get for web. The tutorial I found from WiLD above was also pretty convincing with the demonstrable simplicity of using Phaser in general.

## Getting Started with Phaser.JS and Mintbean CLI

Mintbean.io provides a CLI at https://github.com/Mintbean/mintbean-cli to get you started to easily build a project up. They even kindly put in an example for phaser.js with a simple game where you collect stars! It contains some of the mechanics needed for moving a player controller around, interaction and the general canvas and state management.

![img](https://miro.medium.com/max/700/1*EsgyzbKuQOANBYbzRSH36w.png#center)

Use Git clone to copy the mintbean CLI to a new directory in your terminal

![img](https://miro.medium.com/max/700/1*pwr7jDvoMw0YrZ24tS4MtA.png#center)

![img](https://miro.medium.com/max/700/1*OiIGmNfaH5mlCi0_rhLKdg.png#center)

This is what the directory will look like if you run ls and you should then run mint n phaser-js-gh-pages to start the wizard to create a project. If you don’t have mintbean-cli installed yet you should run `npm install -g mintbean-cli`

![img](https://miro.medium.com/max/700/1*9Vs8iILrt4O5_UPT0hN6Rw.png#center)

As pictured above you can choose phaser as your project template.

![img](https://miro.medium.com/max/700/1*Xg0L9S8lSl4fbF4eJG5YEQ.png#center)

You will also have to choose your package manager. Git init runs automatically so you should need to only set your remote repository using git remote set-url origin git@github.com:<YourName>/<YourRepo> (this is if you don’t clone the repo first, it’s advisable to clone the repo to your github and then pull from there!

Once that is done open the whole folder in VScode and you should see your new phaser-js-gh-pages project

![img](https://miro.medium.com/max/700/1*auG0CIrBVtXy_8-guB_MdQ.png#center)

Kindly provided is the skeleton phaser project which you can run with npm/yarn start from inside the phaser-js-gh-pages directory in your terminal with cd phaser-js-gh-pages/. You should run this in your /mintbean-cli/phaser-js-gh-pages directory. The first time you run npm start it will build the package to run the server and you will get a message that the server is running at: http://localhost:1234 by visiting http://localhost:1234 you should see the following:

![img](https://miro.medium.com/max/700/1*fCW9e4q0vH6QMs2Jpif7wA.gif#center)

## Making Your First Modification

The assets provided give you some idea how the game can work however we will have to find some more Pong like assets. My first port of call was https://opengameart.org/content/pong-graphics kindly provided for free by user Deathsbreed. I downloaded the spritesheet which looked a bit like this:

![img](https://miro.medium.com/max/250/1*0iYYEzY01ssaeNaJ6caPaA.png#center)

As you can see it provides different coloured paddles and a ball object.

I did a little editing using [Pinta](https://www.pinta-project.com/) on Ubuntu to strip the red and blue paddle away so I can import those into phaser as Images rather than a sprite sheet so we can have more control over our physics and look of our paddles.
The background also came as part of the package from opengameart so for now I will use that but I will probably make adjustments to that later.
The assets have to be imported into the phaser-js-gh-pages/src/assets folder
The next step is to go into phaser-js-gh-pages/src/scenes/Game.js and import and declare some new variables for our assets at the top of our file

```javascript
import ball from “../assets/ball.png”;
import paddle1 from “../assets/paddle1.png”;
import paddle2 from “../assets/paddle2.png”;
```

Next we want to change any reference of our player character from the default “box” in our game.js and put something more appropriate in there. For now select the word box and hit ctrl/cmd + F2 to change all instances to paddleP1

```javascript
let paddleP1;
```

The next thing we need to do is tell phaser to load our paddle image in the preload: function. We will be making a key reference to our image in this step, it makes sense to refer to our art asset as paddle1 and we will be referring to our imported paddle1 object from earlier.

```javascript
preload: function preload() {
this.load.image(“background”, background);
this.load.image(“paddle1”, paddle1);
this.load.spritesheet(“tiles”, tiles, {
frameWidth: 32,
frameHeight: 32,
});
this.load.image(“star”, star);
},
```

The next thing to change would be the background image so let’s define that at the top of the file as well:

```javascript
import bg from “../assets/bg.jpg”;
```

We will need to tell Phaser in the preload() function to use bg instead of background we can change this the following way:

```javascript
this.load.image(“bg”, bg);
Then to render the background we have to make a reference to “bg” in the create() function as follows:
this.add.image(400, 300, “bg”);
this code: paddleP1 = this.physics.add.image(700, 300, “paddle1”);
```

The above code will instantiate the paddle into our gameworld at the position X 700 and Y 300 we may tweak this later.
The below code adds a physics collider and checks for collision between currently the star object and the paddle

```javascript
this.physics.add.collider(stars, paddleP1, processCollision, null, this);
paddleP1.setBounce(1, 1);
paddleP1.setCollideWorldBounds(true);
},
update: function () {
const { velocity } = paddleP1.body;
```

This next block of code checks to see if the space key is held down and if so acts as a break for the player controller (slowing down player’s velocity)

```javascript
if (cursors.space.isDown) {
  const x = decelerate(velocity.x);
  const y = decelerate(velocity.y);
  paddleP1.setVelocity(x, y);
}
```

This final block of code checks for input from the player using the up down left and right arrows and calculates and adds velocity based on that input.

```javascript
if (cursors.up.isDown) paddleP1.setVelocityY(accelerate(velocity.y, -1));
if (cursors.right.isDown) paddleP1.setVelocityX(accelerate(velocity.x, 1));
if (cursors.down.isDown) paddleP1.setVelocityY(accelerate(velocity.y, 1));
if (cursors.left.isDown) paddleP1.setVelocityX(accelerate(velocity.x, -1));
```

You also have to ensure that you add a reference to the image in the create() function otherwise Phaser
will just load a blank canvas, make sure you have a reference such as the following code:

```javascript
create: function create() {
this.add.image(400, 300, “bg”);
this.add.image(“paddle1”, paddle1);
```

## Success?

Now let’s take a look at the modified game:

Image for post

![img](https://miro.medium.com/max/700/1*g04FzMzT3jTri0dvMkByrg.gif#center)

Now there are some of the basic elements of pong in place!

This first post covers the basic steps and getting started with the Mintbean CLI and phaser. The next post will dive into the addition of a second paddle and writing some code for an AI, replacing the star sprite with a ball and adding a couple of sound effects.

[You can find the progress on the repo for the project here](https://github.com/AndrewRLloyd88/pong-hackathon)
