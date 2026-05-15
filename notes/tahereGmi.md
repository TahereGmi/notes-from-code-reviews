# Notes from @tahereGmi

A frontend developer who reviews pull requests every day. Mostly React, Next.js, and TypeScript.

These are extra notes I didn't fit into the main article — small lessons, ongoing experiments, and patterns I'm still figuring out.

---

## The "is this a real test, or just a green checkmark?" habit

After I noticed how many AI-generated tests were just decoration, I started doing a small thing in every review: before approving, I look at the test file and ask one question — *"if I deleted the function this is testing, would this test still pass?"*

If yes, the test isn't real. It's just a green checkmark.

This takes about ten seconds and has caught more fake tests than any tool I've used.

---

## Reading PRs from the bottom up

I used to read PRs top-to-bottom. Recently I started reading from the **bottom** — starting with config files, tests, and the last file in the changeset.

Two reasons:
1. The "boring" files at the end are where production bugs hide
2. By the time I get to the main code, I already know the *context* the author was working in

It feels strange at first. Try it for a week — you'll catch things you used to miss.

---

## A small comment I keep using

For PRs where I see two changes mixed together (a feature + a refactor, or a bug fix + a cleanup), I leave this exact comment:

> *"non-blocking: this PR is doing two things — the [feature] and a small refactor. They're both fine, but separating them in future PRs would make the history easier to read when we're debugging."*

I don't ask the author to split this one. I just plant the idea for next time. About half the time, the next PR comes in already split. Slow change, but it sticks.

---

## What I'm still figuring out

- How to give useful feedback on AI-generated code *without* sounding negative about AI. The author often used the tool in good faith. The bug is real, but the tone matters.
- When to add a linter rule vs. when to just leave one human comment. Some patterns aren't common enough to lint, but I keep seeing them.
- How to review code from teammates who are much more senior than me without feeling like I'm "checking their work." Still working on this one.

---

If you've thought about any of these, I'd love to read your notes too. Open a pull request and add your own file to `/notes`.