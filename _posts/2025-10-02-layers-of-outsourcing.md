---
title: The Layers Of Outsourcing Code
date: 2025-10-02 18:00:00 +1000

categories: [Misc]
tags: [copilot,programming,llm,chat-gpt,shower-thought]
image:
    path: https://media.licdn.com/dms/image/v2/D4D12AQFzefGcGXYsMw/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1712330807336?e=2147483647&v=beta&t=6VKh8fKTrcSH8nrDuyo3BGQAZbZ7H66lYNS8GnlJi6s
---

So there's layers to outsourcing code right.

Programmers have been stealing code since there was code to steal. Why reinvent the wheel when a more general version of the problem probably has a better solution than anything you could come up with published on the internet? With the rise of gen AI and tools like copilot that integrate gen AI into IDEs, conversations about the general utility of gen AI, its sustainability, and the long term effect it will have on the software engineering industry are happening every day in varying capacities.

Here is my opinion as a junior developer that learnt how to program before gen AI and started working with code professionally alongside the gen AI boom. There are layers to outsourcing your code and the higher you go the more productive you are in the short term and the more debt you gather in the long term.

![Pyramid Representing Hierarchy](https://nemtss.unl.edu/wp-content/uploads/2022/05/Pyramid_3-D_T2.png)

## The Ground

Beneath the pyramid there is ground. Writing code yourself involves conceptualizing the design, typing it out, and making further edits without consulting any external sources. In a sense this is the "purest" your code can ever be although there are all kinds of implicit biases and inspirations for every thought and idea that goes into code.

## The Bottom Layer

![Stack Overflow Answer](https://i.sstatic.net/fqNYt.png)

At the bottom layer of the outsourcing pyramid is pulling prewritten bespoke code snippets from stack overflow, or other Q&A sites/forums. Working at this tier is more or less completely benign, in that it counts as coding independently and you are completely in control. In virtually any serious application development scenario (in fact any coding scenario outside competitive programming) using these sites is allowed. It is basically a slightly faster way to read the documentation directly. A large portion of the time [documentation will include similar code snippets as illustrations](https://learn.microsoft.com/en-us/dotnet/api/system.idisposable?view=net-9.0). 

At this level, you are not *losing* anything by avoiding having to write code yourself (which you will see is not the case for the higher tiers). If there is significant logic in the snippet, you might have understood the containing module better by implementing that logic yourself. But chances are you just didn't know some syntax or a library/function name, which is not something you can produce using critical thinking.

Additionally, a consequence of these snippets often being answers to specific questions is that you will have to do some adapting to make it fit your use case and at the same time will have to critically evaluate its suitability, both of which increase the quality of your code and the amount of human review it undergoes.

## Manually Typing Out Gen AI's Ideas

The next level up is having gen AI generate code via prompt and you typing it in your codebase. Now you are throwing away a containment barrier of thinking because you are not producing the logic or design yourself. Working at this tier it is difficult to "mindlessly" end up with code that you can't be accountable for because you have to actively type it all and review it while you type, and only if you zone out properly and transcribe the gpt response unconsciously will you end up with something you can't explain.

As a result of working within this tier, you might not be able to explain why you chose one implementation over another but you should know how your implementation works since you are interacting with the generated response at the line-of-code level.

## Danger Zone: Pasting Generated Code

![copy-paste image](https://physicians.dukehealth.org/sites/default/files/article/copying-and-pasting-ehrs-300x188.jpg)

The next level is copy-pasting generated code. This is an unpopular opinion but I believe there is still some degree of technical thinking at this level because you can selectively copy and you have to think about where to put it. But this is dangerous territory and where I draw the line, out of principle. At this stage the granularity of control is reduced from line-by-line to chunk-by-chunk.

Chunks can be small or large. Using a prompt to generate a logically simple, pure function (without side effects) that is only a couple lines, like a linq query is on the safest extreme of this tier. Copy-pasting an entire file from the imports all the way down is the other extreme.

If you are primarily operating at this tier, there is a very real chance you don't know how the product works or if it works. Since the only things you have left are being able to choose what to copy and where to paste it, you are probably still on top of the flow of logic and high level design (especially if you copy-paste multiple times one at a time and test as you go).

Nobody wants to operate at this tier. I have made a conscious effort to steer clear of it. My understanding is that people who work in it are forced into it due to time constraints. The nature of most of the projects that are built in this layer is such that they will be abandoned soon anyway, so technical debt is not an issue (such as uni assignments).

## The Pinnacle

![Image of copilot vs code](https://learn.microsoft.com/en-us/visualstudio/ide/media/vs-2022/copilot-agent-mode/copilot-agent-dropdown.png?view=vs-2017&viewFallbackFrom=vs-2022)

At the very apex of the pyramid is a technique I tried for the first time this morning, which is the VS Code copilot extension. This extension is an LLM widget that sits on the side of the IDE. But unlike chat-gpt, copilot reads directly from your workspace and actively replaces the code for you. The changes it makes can be huge, in my small social media app implementing a feature such as being able to upload images to posts is implementable in a single prompt and changes 10 files and tests. I have seen instances of copilot changing 1000s of files. Reviewing copilot's changes is sometimes more effort than implementing the changes yourself[^footnote].

In all the above tiers there is a real risk of falling into traps of "mindlessness" in different ways, ranging from not having designed the code at the bottom tier to completely not knowing what is going on at the top. When we choose a tier we work within the freedom/constraints of that tier and for the price of convenience surrender certain 'rights', slightly taking away ownership of our code. Some of these 'rights' include: being able to defend the design, understanding the design, understanding the implementation, and understanding the flow of logic.

This is what makes copilot the most dangerous- copilot's tier offers the least freedom in term of control because it 'allows' you to code as such a high level. If your prompt makes a change that works, the tier 'lets' you accept it without a thorough review. This is similar to static vs dynamic typing, more convenience at a cost. If copilot makes a change that partially works, you may accept it and try to fix the defect in the next prompt (again because the tier 'lets' you). And so you begin to lose track of what is going on, errors become impossible to diagnose and you, now completely dependent, can only ask copilot to "fix things please" which is what we see in the memes.

At this point the developer has lost control and has become nothing more than a user. The dev, as a user, only sees that the product does not work, and describes the desired state as "this but fixed". If gen AI has been unable to make the change until now- maybe the problem is technical and it hasn't been trained on data that has solved it, or maybe copilot can't see what it needs to diagnose the problem- all copilot can do is basically rearrange code. And you understand less and less of what the rearrangements/gradual refactors are doing, and your existing understanding becomes less correct as things are moved around.

[^footnote]: This made me think of how some [unsubscribe pages deliberately load slowly to discourage users from unsubscribing](https://www.sitepoint.com/annoying-web-dark-patterns/). Or how I find C# code easy to reason about when there is more boilerplate/setting up classes with distinct responsibilities as opposed to working in a fast imperative language (e.g. python scripting). It becomes clearer specifically due to the time it takes to write more code. In that time you get familiar with the code and have the chance to review it. With gen AI, code is no longer valuable, it is easier to describe declaratively the end state from a product perspective (e.g. "the images for each post load in the frontend") than follow the code and figure it out. I believe this idea is what caused the phrase "AI slop" to become popular, the massive amount of content that is easily generated brings to mind the image of copiously produced literal slop.

## Conclusion

The tiers laid out represent a very natural progression- each tier is a higher level of abstraction of logic culminating in copilot, to which you give instructions at the feature level. Recognizing the tiers is important for juniors to be aware of what you're trading away for the luxury of convenience.