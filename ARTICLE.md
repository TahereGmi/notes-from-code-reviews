# What Code Reviews Taught Me Over Time
> 📖 Also published on [Medium](https://medium.com/@tgholami/what-code-reviews-taught-me-over-time-05984aa9fb08)

> Original article from the [Notes from Code Reviews](./README.md) collection.
> Want to add your own notes? See [how to contribute](./CONTRIBUTING.md).

I keep a small note on my laptop. Every time I leave a comment on a pull request and feel like *I've said this before*, I write it down.

I keep a small note on my laptop. Every time I leave a comment on a pull request and feel like I've said this before, I write it down.
Six months later, I had reviewed about a hundred PRs. I read the note. Some patterns showed up again and again. Most of them were not really about code.
Here is what I learned. Not the easy stuff like "use better variable names." The things that actually changed how I review.
1. A good description saves everyone time
In my experience, the fastest reviews were not the ones with the cleanest code. They were the ones with a clear description.
When a PR just said "fix bug," I had to guess what the author was trying to do. So our team created a small template for every pull request, and everyone follows it. The template asks three simple things: what changed, why, and what to look at first. With those three sentences, I can start reviewing right away.
Now, if a description doesn't follow the template, I ask the author to fix it before I look at the code. It feels strict at first, but it's actually kinder. The author thinks once. Every reviewer doesn't have to think again.
2. Big PRs don't get a real review
A small PR with 50 lines? I read every line.
A medium PR with 500 lines? I read most of it.
A huge PR with 2,000 lines? Honestly, there's nothing I can do. I ask the author to split it.
I used to think this was about discipline but it's not. Our attention has limits. When a PR is too big, my brain just gives up before the end. The author thinks they're saving time by sending everything at once. They're not. They're moving the work to the reviewer, and to the person who will debug it later.
My rule now: if a PR is more than around 200 lines, and it's not a simple rename, I ask the author to split it.
3. Reviewing AI-generated code: what to look for
This part did not exist a year ago. Now it is half of my job.
Most PRs I review today have some AI in them. Sometimes one function. Sometimes the whole file. And here is the tricky part: the code usually works. That's what makes it dangerous. Tests pass, the page loads, but problems are hiding.
Here is what I check, because the AI tools won't:
Imports that look right but don't exist
This is the most common problem I see in AI-generated code. The AI sometimes invents functions, or imports a real function from the wrong place.
Other times, the function is real but lives somewhere else. Or the library version is different from what the AI expects. The code reads nicely. It just doesn't work.
"Can't ESLint catch this?" 
ESLint and TypeScript catch the obvious cases, like a totally fake function. They don't catch the harder ones, for instance: a real function from the wrong place, or a real package used the wrong way. For those, a human still has to read carefully.
Imports are small. But they are where AI lies most often.
Old patterns
Many AI models learned from years of older code. So they sometimes write class components instead of functional ones, or use useEffect to fetch data when the team uses TanStack Query, or chain promises with .then() when the codebase uses async/await. The code works, but it looks like it's from 2019. If your team has a style, the AI doesn't know it.
Hidden errors
In AI-Generated codes you can see empty catch blocks, or errors that get logged and then ignored. The function finishes. The test passes. The bug ships to production.
Every empty catch is either a real choice or a hidden bug. If it's a real choice, the code should say so with a comment. If it's a hidden bug, the reviewer should ask about it. Either way, it shouldn't pass quietly.
Wrong edge cases
The AI doesn't know your app and handles cases that can never happen in your app, and forgets the ones that do happen. It's copying patterns from other code, not thinking about your code.
for example:
```jsx
function PostList({ posts }: { posts: Post[] }) {
  if (!posts) return <p>Loading...</p>
  if (posts.length === 0) return <p>No posts yet</p>
  return <ul>{posts.map(p => <li>{p.title}</li>)}</ul>
}
```
The !posts check can never be true - TypeScript already says posts is an array. So when the data is actually still loading, posts is [], and the user sees "No posts yet" instead of a loading message. The AI defended against an impossible bug. The real one "loading should look different from empty" was missed.
It's copying patterns from other code, not thinking about your code.
Fake types
Watch for comments that tell TypeScript to be quiet. Things like as any, or // @ts-expect-error. The AI wants the red lines in the editor to go away. That shortcut is often the bug.
Tests that test nothing
AI-generated tests sometimes test a fake version of the function. They pass. But they don't prove anything.
Why this is especially common in frontend tests?
Frontend tests mock a lot by necessity: APIs, routers, contexts, hooks, timers. The more you mock, the more you risk testing the mocks instead of the code. AI tends to mock aggressively, anything that might be hard, it just mocks. So frontend AI tests have an even higher risk of becoming decoration.
A good rule for frontend tests: mock the boundary, not the behavior. Mock the network. Don't mock the function you're trying to test.
When I review a test now, I ask one simple question: "if I deleted the real code, would this test still pass?" This is called the "break it on purpose" test, and it's the single best way to spot fake tests.
Accessibility problems
Accessibility problems are usually invisible in three places: design (the mockup looked fine), QA (the tester used a mouse), and production (no complaints because affected users just left). 
Code review is often the only stage where someone could have caught it. If you don't catch it in review, nobody will.
And the good news is most accessibility problems have a specific shape in the code. You don't need to be an expert. You just need to recognize the patterns.
The most common things I find: a <div> with onClick that should be a <button>, an <input> with no label, an <img> with no alt, or outline: none with no replacement. The page looks fine. Keyboard and screen-reader users find the problems later.
The "this doesn't feel like our code" smell
Variables called data, result, item. Functions that are too generic. The code works, but it doesn't fit. This is hard to explain in a review, but it's real. If you ignore it, slowly your codebase stops feeling like yours.
The simple way to think about it: think of AI code like code from a new freelancer. Not bad. Just new. They don't know your team, your rules, or your real bugs yet. 
"The code works" is the start of the review, not the end.
4. Small style comments are about consistency
I used to feel bad leaving small style comments. They felt picky.
Then I noticed something. The codebases I enjoyed working were the ones where every file looked similar. You open file number ten, and you already know how it works, because file one through nine taught you.
So small style comments are not picky. They protect this feeling.
But I follow one rule: if I leave the same comment twice, it should become a linter rule. A human should not do a tool's job. Things like "remove this console.log," "don't use any," or "missing key prop". These should be caught by a tool, not by a tired human reading code at 5 PM! :)
5. Ask, don't tell
"This should be a useMemo" is a worse comment than "what happens if this runs on every render?"
The first one ends the conversation. The second one teaches. And sometimes the author has a good reason I didn't see, so I learn something too.
I also started writing "non-blocking:" before small comments. It tells the author: "this is small, you don't have to fix it before we merge." Without that little word, even a tiny comment can feel like a complaint. With it, the same comment feels like a friendly suggestion.
For example, these two comments mean exactly the same thing:
rename data to userList
non-blocking: rename data to userList?
The second one feels lighter. The author reads it and thinks, "okay, just a small idea, I can take it or leave it." The first one can feel like an order.
One small word, but it changed the tone of my reviews a lot.
6. Bugs hide where I don't look
I did a small experiment. I looked at six months of production bugs and checked the original PRs. Where did the bugs come from?
Almost never came from the code I commented on. They came from the parts I skipped, a wrong API base URL in the config, a missing loading state, a useEffect with the wrong dependencies, the boring fifth file in a big PR.
This was uncomfortable to see. I was reviewing the interesting code, not the risky code. Now I start with the boring files first. That's where real problems live.
7. Approve more, comment less
In my first fifty reviews, I left about eight comments per PR. In my last ones, I left three.
Part of that is because the code really did get better. The team learned each other's style. We added linter rules. Tools improved. Many of the small comments I used to leave are now caught before the PR even opens.
But the bigger reason is me. I got better at telling the difference between "this is wrong" and "I would have done it differently." The second one is not a real review comment. It's just my ego.
When I stopped leaving those comments, reviews got faster and kinder. And this surprised me, I started catching more real bugs. The important comments were no longer hidden among the small ones.


## What I would tell myself at PR number one

Read the description before the code.
Ask for smaller PRs.
Treat AI code like a new teammate's code — friendly, but careful.
Start with the boring files.
Ask questions instead of giving orders.
Skip the ego comments.

And keep a note. You won't see the patterns until you write them down.

---

## Your turn

These are *my* notes from *my* reviews. But every developer has their own. If you've learned something from reviewing code — a small habit, a bug you keep finding, a comment you keep leaving — there's a place for it here.

Add your own notes: [How to contribute](./CONTRIBUTING.md).

The best lessons in this article came from real reviews. The next ones can come from yours.