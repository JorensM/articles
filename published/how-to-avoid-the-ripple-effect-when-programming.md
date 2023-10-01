# How to avoid the ripple effect when programming

Image by [200 Degrees](https://pixabay.com/users/200degrees-2051452/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1745705) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1745705)

## Preface

I would like to start this article by saying that the methods shared here are not the only methods available and there are many other ways how one can avoid the ripple effect.

Also I would like to say that I am not a senior, but rather a mid-level programmer and as such the tips shared in this article may be lacking in some aspects. Feel free to correct me or suggest improvements. 

And finally I would like to thank you for taking your time to read this article, it means a lot to me!

## Table of contents

2. [Preface](#preface)
1. [How to avoid the ripple effect when programming](#how-to-avoid-the-ripple-effect-when-programming)
    * [The premise](#the-premise)
    * [Think ahead and think thoroughly](#think-ahead-and-think-thoroughly)
    * [Write abstract code](#write-abstract-code)
    * [Make your code backwards compatible](#make-your-code-backwards-compatible)
    * [Define good interfaces](#define-good-interfaces)
3. [Conclusion](#conclusion)

## How to avoid the ripple effect when programming

What is the ripple effect? You have most likely encountered it before when coding, whether you know about it or not. Simply put, in programming, the **ripple effect** is when you make changes to your existing code, and it causes your codebase to break, requiring you to make even more changes to your code. Let me demonstrate with a simple example:

Let's say you have a function `sum()` which takes two number arguments and returns the sum of these number. 

```
function sum(a, b) {
    return a + b
}
```

Now let's say that you start using this method extensively in your code:

```
//file a.js...
const variable = sum(1, 5)

//...file b.js...
const another_variable = sum(x1, x2)

//...file g.js...
const yet_another_varible = sum(y1, y2)

//...and so on...
```

Now let's say that you've decided to update the `sum()`function to be able to sum an indefinite amount of numbers, so you make it accept as an argument a single array and return the sum of the numbers in that array.

```
function sum(numbers) {
    let total = 0
    numbers.forEach( n => total += n)

    return total
}
```
*(In javascript this can be solved by using the spread operator to accept an indefinite amount of args, but for the sake of the example I'm using an array)*

Okay, so we've updated our function, great!

But now we have to go through our code and update each call to `sum()` to the new format.

```
//file a.js...
const variable = sum([1, 5])

//...file b.js...
const another_variable = sum([x1, x2])

//...file g.js...
const yet_another_varible = sum([y1, y2])

//...and so on...
```

This is the ripple effect in action.

The ripple effect is not as relevant in a small project where there is not much code (though it's still good to follow best practices), but it is quite relevant in larger  projects where a single change can require us to make many other changes across the codebase.

So now that we know what the ripple effect is, let's explore how we can prevent it.

### The premise
The premise upon which we will be solving the ripple effect problem is the following:

>the ripple effect is caused by changes in the outcome, and not the process. 

By this I mean that changing the code in a way thay doesn't change its API/interface/return values - but instead only changes only how the outcome is achieved - won't cause the ripple effect. So to prevent the ripple effect, we must minimize the changes in the outcome of our code. Below are some tips to achieve this:

### Think ahead and think thoroughly

Before you start writing any code, carefully think through where and how it will be used and how it may be used in the future. Consider all the possible use cases of the code that you are going to implement, both the current use cases and possible future use cases. This will ensure that your code will be long lasting and won't require many changes along the way, which will help you avoid the ripple effect.

### Write abstract code
It's not always the case, but oftentimes a function or a class can become more useful and less prone to causing a ripple effect if you make it abstract.

For example, let's say you have a function that takes a string and a name of a color and outputs the string in the console, in the specified color

```
function logWithColor(str, color) {
    switch(color) {
        case 'green':
            //Code for outputting green text
            break
        case 'red':
            //Code for outputting red text
            break
        case 'blue':
            //Code for outputting blue text
            break
    }
}
```

currently, you can only pass specific names of color strings for the colors, such as 'green', 'red', etc. This may become cumbersome if we decide to update the definition of 'green' to a different hex value, which will make each log using 'green' output a different color than before, which may not be what we want.

This can be solved by making our function more abstract. We can do this by changing the function to accept a hex color as an argument instead of a specific color name. 

```
function logWithColor(str, hexColor) {
    //Code to output text according to the hex color
}
```

Now when we can call it and use a specific hex color, and not worry that the definition of 'color' changes.

Do note that it is not always favorable to abstract code away, and sometimes it is better to keep it specific.

We've updated our function to be more abstract, but now we've met with a new problem - any code that used the old format of the function will break because now it accepts a hex string instead of a color name - see the next section for the solution.

### Make your code backwards compatible
Making code backwards compatible means changing code in such a way that it still supports the old format of accessing it.
Let's take the function from our previous example. After we updated the function to accept hex colors, any code that uses the old format will need to be updated to use the new format. This can be avoided by making the function backwards compatible. We can do this by making the function process both hex values and color names:

```
function logWithColor(str, color) {
    switch(color) {
        case 'green':
            //Code for outputting green text
            break
        case 'red':
            //Code for outputting red text
            break
        case 'blue':
            //Code for outputting blue text
            break
        default:
            //Code for outputting hex
            break
    }
}
```

This will ensure that any code that uses the old function's format won't break, and in turn will help us avoid the ripple effect.


### Define good interfaces
Another method to avoid the ripple effect is to write good interfaces.

What is an interface? An **interface** is a part of a system that is exposed to the user(also called the client), AKA the **public** part. The **user** can be anything that uses the interface - an end-user using an app, a program using a library or making requests to a REST API. In these examples, the interfaces respectively are the user-interface of the app, the methods and classes of the library that the program can access, and the endpoints that the program can call.

When speaking within the scope of a single program, an interface can be anything that another part of the program uses. For example, if you have a program that calls function A, function A can be said to be an interface that your program is using.

[In OOP, an interface](https://stackoverflow.com/questions/2866987/what-is-the-definition-of-interface-in-object-oriented-programming) is a more specific term that describes a specific type of class - a class with the purpose of describing the public parts of it (so the idea is the same). But in these examples an interface can be anything that is used by a client(or user)

So how do we define a good interface? In my opinion the first step to writing a good interface is writing it in such a way that will make it extensible. 
This builds on top of the first tip - think ahead.

Making an interface extensible is similar to making it backwards compatible. Making an interface extensible means *defining it in such a way that any future changes won't require changing the existing parts*.

Let's say we have the following class:

```
class Point {
    x = 0
    y = 0
    setPosition(x, y){
        this.x = x
        this.y = y
    }
    getPosition() {
        return {
            x: this.x,
            y: this.y
        }
    }
}
```

This class holds two variables and two methods - `x` and `y` for the position, `setPosition()` for setting the position variables, and `getPosition()` for retrieving them.

Now let's say we've decided to make this class 3D and support the Z axis.

class Point {
    x = 0
    y = 0
    z = 0

    setPosition(x, y, z) {
        this.x = x
        this.y = y
        this.z = z
    }

    getPosition() {
        return {
            x: this.x,
            y: this.y,
            z: this.z
        }
    }
}

With the way we had initially written the class, we needed to rewrite the `setPosition()` and `getPosition()` methods to accomodate the newly added `z` variable. This means that the class wasn't extensible, because when we made additions to it, we had to rewrite some of the existing code.

Let's try writing our class again from the beginning, but this time in such a way that we don't need to rewrite code when making additions: 

```
class Point {
    position = {
      x: 0
      y: 0
    }
    setPosition(position) {
        this.position = position
    }
    getPosition() {
        return this.position
    }
}
```

As you can see, this time we've written the class a bit differently - we store the position as a single object and accept an object in the `setPosition()` method, and directly return the position object in the `getPosition()` method. You'll see why we've done this in a second.

So now let's try extending our class with the Z position once again:

```
class Point {
    position = {
      x: 0
      y: 0
      z: 0
    }
    setPosition(position) {
        this.position = position
    }
    getPosition() {
        return this.position
    }
}
```

As you can see, this time all we had to do was add a `z` property to our position object - we didn't need to alter `getPosition()` and `setPosition()` at all.

Be mindful that it's better to write extensible code from the start, otherwise you will still have to face the ripple effect and rewrite the code that uses the old format of the interface (unless you make it backwards compatible)

## Conclusion

In this article we explored the root cause of the ripple effect in programming and explored some ways how we can prevent it when writing our code.

Even with all the tips available from this and other resources, it may not always be evident how you can prevent the ripple effect in your specific cases. Ultimatelly, it is something that comes with experience and as you code more and more, you will naturally learn to solve problems like this before they've occured. 

Thank you so much for reading this article and I hope you've learned something from it. If you have any feedback or questions, feel free to leave them in the comments or email me at [jorensmerenjanu@gmail.com](mailto:jorensmerenjanu@gmail.com) (I will definitely respond - I'm not a busy person)
