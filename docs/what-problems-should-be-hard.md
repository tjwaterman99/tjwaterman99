---
date: 2020-12-27
---

# What problems should be hard?

I remember at my job a few years ago there was a programming book lying on the coffee table called "[_practical object oriented design in Ruby_](https://www.poodr.com/)," by Sandi Metz. 

The author had an amazing sentence that summarized the problem the book was solving for:

> You know how to write the code, but not where to put it.

That's probably a surprising problem for most people to hear. Just getting the code to do the thing you hope it does can often be incredibly challenging, unless you've written code to do that thing many times before. Most of us just feel relieved when the thing finally does what we need it to do, and then we sign off for the day and happily look forward to doing something else tomorrow.

But if you've ever worked with someone else's code, where your goal isn't to write the thing and get it working, but to change it slightly _without breaking it_, then you've undoubtably experienced the problem that Sandi is writing about. It's usually far more difficult to change something than it is to get it working the first time, especially when you're working in a team.

So "putting the code in the right place" is the difference between productive systems where teams can collaborate together, and impossible to understand systems that are extremely challenging to build on. 

But how do you know your design is actually good? What do you look for to predict whether a system will be easy to adapt? 

It's hard to actually know the answer unless you've worked with different systems, and you've seen how the exact same problem can be much harder on one system than the other. Unfortunately, the ability to evaluate design is much more about tacit knowledge, rather than explicit principles.

Still, I want to say there's rule of thumb that anyone can apply, and that is "easy problems should be easy." You can usually get a good sense of whether a system is productive by trying to do the simplest possible addition (and remember additions are easier than changes). Was it hard? Then the system probably has a design problem.

That's the difference between good code bases and code bases that are a struggle to work with. I've noticed that the good code bases will often have a tutorial that proudly demos just how easy it is to get started, because the designers care about it and want to make it as easy as possible for others to collaborate.

Of course, a well-designed system has its own trade-offs as well. You probably don't need a good design unless multiple people are working on the project, since it's enough to "keep it all in your head." You might also see systems that are "over-designed," in the sense that they're trying to solve problems that might exist in the future, but don't exist today.

But it's worth revisiting the question about design on every change that gets made to the system you're building. _Does this change make it harder to solve the easy problems?_ If the answer is yes, then the change had better be doing something remarkable.