# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) Lab Lesson: Hubot (3:00)

| Timing | Type | Topic |
| --- | --- | --- |
| 10 min | [Opening](#opening) | Introduction |
| 75 min | [Lab](#lab1) | Hubot: Independent Practice |
| 135 min | [Lab](#lab2) | Hubot: Exercises |
| 5 min | [Conclusion](#conclusion) | Final Questions & Exit Tickets |


### Objectives

*After this lesson, students will be able to:*

- Install and configure all utilities needed to run a Hubot
- Write scripts that allow their Hubot to interact with users of the class Slack room

### Preparation
*Before this lesson, students should be able to:*

- Describe how parameters and arguments relate to functions
- Create and call a function that accepts parameters to solve a problem
- Define and call functions defined in terms of other functions
- Return a value from a function using the return keyword
- Define and call functions with argument-dependent return values
- Determine the scope of local and global variables
- Create a program that hoists variables

---
<a name="opening"></a>
## Introduction (10 min)

Tonight we'll be building our own chat bot in Slack. To help you become familiar with bots, we're going to use something called Hubot. Hubot is a framework for creating chat bots that you can use to create new bots for use in a variety of chat environments, including Slack. Using Hubot, you create bots (also often confusingly called Hubots) which follow scripts written in JavaScript to perform various tasks, from configuring new servers to updating code on GitHub. We will mostly be using Hubot in this project to interact with real users in Slack.

### What Are We About to Do?

First, let's define some terms we'll be using during this lab.

**[Slack bot user](https://api.slack.com/bot-users):** You have probably seen automated "bots" on platforms like Twitter. Essentially, a bot is a script that is programmed to interact with users as if it's a person. In Slack, these are called "bot users," or "bots" for short. There is one bot user that is built into Slack that you've probably heard from already when you joined, called Slackbot. In this lab, we'll begin creating our own bots for use in the class Slack team.

**Hubot:** [Hubot](https://hubot.github.com/) is a framework built by GitHub to speed up the process of developing bot users on a variety of platforms, including Slack. It's built using Node.js and CoffeeScript (see below), and it includes built-in functionality for performing common bot tasks (posting images, etc.). We'll be using Hubot to create the bot users we build.

---
<a name="lab1"></a>
## Hubot: Independent Practice (135 mins)

**Fork and Clone this Repo**

- Fork and Clone this repo into your `~` folder or your Desktop
- `cd` into the `hubot-starter` folder
- Run `npm install` on the command line
- Open the folder in your text editor

All of the starter code you need is inside `scripts/example.js`.

**NOTE:** You will need to restart your server each time you make a change to the file. On a Mac, `CTRL + C` to stop your server.

**Create a New Slack Workspace and generate a Hubot token**

We will walk through this together. Remember your `HUBOT_SLACK_TOKEN`.

- Create a new Slack workspace
- Add the Hubot app
- Remember your token.

**Test your bot locally to make sure it's working:**

From the `hubot-starter-code` folder, run:

`HUBOT_SLACK_TOKEN=TOKEN-GOES-HERE ./bin/hubot --adapter slack`

Now we have our very own Hubot that's willing and able to do our bidding. What can it do? Let’s take it for a test drive.

<br>

## Interacting with Your Hubot

To create instructions for your Hubot, you need to add a JavaScript file to the `scripts` folder. You can add multiple scripts files to that folder and all will execute when the Hubot is run. Let's look at a few commands that will help us build our Hubot scripts.

#### Listening

**.hear**

 The `.hear` command listens for a specific phrase anywhere in the Slack room. You don't have to mention your Hubot in order to get a response.

 In the example below, when the bot hears "Hello!", it will respond, "Hi there!":

 ```js
 bot.hear(/Hello!/, function(res) {
   return res.send("Hi there!");
 });
 ```

 ```
 tim: Hello!
 hubot: Hi there!
 ```

**.respond**

 `.respond` is similar to `.hear`, except it will only be triggered when someone specifically mentions the Hubot using `@`, or sends a direct message.

 ```javascript
 bot.respond(/What's your favorite food?/, function(res) {
   return res.send("I'm a robot--I don't eat food!");
 });
 ```

 ```
 tim: @hubot What's your favorite food?
 hubot: I'm a robot--I don't eat food!
 jon: What's your favorite food?
 ... [no response because Jon didn't tag Hubot in his message]
 ```

#### Speaking Responding to User Input

**.send and .reply**

The "send" method allows your Hubot to send a message to the channel, and the "reply" method allows it to respond directly to a user with an @ reply. In order to understand the difference, we’ll need to accept some user input, so let's take a look at the example below.

**The Wildcard Selector**

 Would you like to accept user input in your script? Take a look at the example below:

 ```js
 bot.respond(/Hi Hubot! My name is (.*)/i, function(msg) {
   var name;
   name = msg.match[1];
   if (name == "Hubot"){
     return msg.send("You're not Hubot--I'm Hubot!");
   } else {
     return msg.reply("Nice to meet you, " + name + "!");
   }

 });
 ```
 ```
 tim: @hubot Hi Hubot! My name is Tim
 hubot: @tim Nice to meet you, Tim!  
 tim: @hubot Hi Hubot! My name is Hubot
 hubot: You're not Hubot--I'm Hubot!
 ```

Did you notice the difference between `.send` and `.reply`?

Now, let's dig into how the user input works. As you can see, we're using `.respond` to set the bot up to listen for its @ handle. In this case, it's listening for the phrase `@hubot Hi Hubot! My name is _____`. The `(.*)` syntax is a wildcard value that effectively represents the "blank" in that sentence. When the bot is triggered with the phrase, `@hubot Hi Hubot! My name is (.*)`, it stores the contents of `(.*)` in an array called `match`, which lives inside an object called `msg`. When we want to retrieve that value, we use `fav = msg.match[0]` to grab the first value in that array, which, in this case, is "Tim".

 <details>
 <summary>
 **Wait, What Does the `[1]` Mean?** (click the arrow for the answer)
 </summary>
`msg.match[1]` will grab the value corresponding to the second group `(.*)` in the expression. Just like most collections in JavaScript, this is a zero-based index. However, the first group is the entire expression. So in the example above, `msg.match[0]` will return the entire expression: `Hi Hubot! My name is Tim`. If you use multiple `(.*)`s within one RegEx statement, you can assign each of the values to different variables, such as: `var foo = msg.match[1]`, `var bar = msg.match[2]`.
 </details>

 Here's an example that uses two wildcards:

 ```js
 bot.respond(/add (.*) and (.*)/i, function(msg) {
   var a;
   var b;
   a = parseInt(msg.match[1]);
   b = parseInt(msg.match[2]);
   c = a + b

   return msg.reply(a + " plus " + b + " = " + c);
 });

 ```
 ```
 tim: @hubot add 3 and 4
 hubot: 3 plus 4 = 7
 ```

 Here's another example that uses a switch statement to handle different cases:
 ```js
 bot.respond(/what is your favorite (.*)/, function(msg) {
   var fav;
   fav = msg.match[1];
   console.log(fav);
   switch (fav) {
     case "food":
       return msg.reply("I'm a robot--I don't eat food!");
       break;
     case "band":
       return msg.reply("It's gotta be Daft Punk!");
       break;
     case "programming language":
       return msg.reply("Javascript, of course!");
       break;
     default:
       return msg.reply("I don't have a favorite " + fav + ". What's yours?");
   }
 });
 ```
 ```
 tim: @hubot what is your favorite food?
 hubot: @tim I'm a robot--I don't eat food!
 tim @hubot what is your favorite color?
 hubot: @tim I don't have a favorite color. What's yours?
 ```

#### Getting Fancy

 **Regular Expressions**
   You might have noticed that we haven't used quotes around the phrases Hubot is listening for; instead, we’re using `/` (e.g. `/What's your favorite food/`). Why do we do this?

  Most programming languages allow you to search for text within text using something called **Regular Expressions**. Regular expressions, or "RegEx," enable you to listen for very specific patterns of text within a document, or, in this case, a chat room.

  For example, if you want to find the word "what" using a RegEx, you can use the following code: `\bwhat\b`. Here, "b" represents the boundaries of the word (i.e., its beginning and end) and the "what" represents the word we are searching for.

  This is an advanced example, though. **For most of your bots, you can simply enclose phrases between `/` marks**. If you would like to learn more about RegEx, check out the resources below:
  - [RegexOne](http://regexone.com/) walks you through basic to advanced RegEx syntax. Check it out!
  - [RegExr](http://regexr.com/) lets you test out your RegEx without the need to constantly start and stop your bot.


To wrap things up, here's one final example of an advanced Hubot that uses a RegEx, and that can respond to a number of different inputs:

```js
module.exports = function(robot) {
    robot.respond(/is it a (weekend|holiday)\s?\?/i, function(msg){
        var today = new Date();

        msg.reply(today.getDay() === 0 || today.getDay() === 6 ? "YES" : "NO");
    });

    robot.hear(/i did it/i, function(msg){
        msg.send("Congratulations! Good job!");
    });

    robot.respond(/are you there?/i, function(msg){
        msg.reply('Yes, usually here, and listening.');
    });

    robot.respond(/convert \$(.*) to btc/i, function(res){
        var usd = res.match[1];
        res.reply('That is about ' + usd * 0.0024 + ' in BTC');
    });

}
```

This should give you a sense of what a Hubot can do—and perhaps give you some ideas for building your own Hubot...

<br>

## Exercises

### Deliverables

Pick one of the features below to implement in your Slackbot. After that you're free to build something of your own or pick another challenge below.

At the end of class, please git add, commit and git push your code to your repo. We will walk through how to submit a pull request.

#### 1. Reverse

Create a command that will return the message reversed.



#### 2. What should I eat for lunch?

When I message `lunch` I should get a random food.

![](https://i.imgur.com/Ik7kI5J.png)

#### 3. Return a list of available Hubot commands

When I message `list` I should get a list of available Hubot commands.

![](https://i.imgur.com/FT6y58V.png)

#### 4. Todo List

When I message `todo buy food` Hubot adds the todo to my list and prints it out.

![](https://i.imgur.com/WYRfVK4.png)



#### 5. Currency Converter

Convert USD to SAR and SAR to USD.

![](https://i.imgur.com/gMy66fi.png)

#### 6. Calculator

Create one command that will add, subtract, divide or multiply two numbers.


#### 7. Random Picture

Create a command that will return a random picture URL from an array. 

HINT: Create an array and call `res.random` on it.

```js
bot.hear(/rock/i, function(res) {
  res.send(res.random(rock))
})
```

![](https://i.imgur.com/r6NFMpB.png)


<br />

---
<a name="conclusion"></a>

## Conclusion (5 min)

Make sure the lesson objectives have been met.

* What did we install and why?
* How does Hubot work?


