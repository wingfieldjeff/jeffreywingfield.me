---
title: Learning just enough Hugo to Start Blogging
date: 2023-11-15T20:48:51-08:00
draft: true
---

# The Why

I have wanted to set up a blog for some time. It seems the trend in IT, Cybersecurity, and especially software development to use a blog as a sort of live resume (as well as a place to put a regular resume). It's really good evidence that you, as a professional, enjoy this kind of work and are active in it. It says "Hey look! I am a real person. I actually do this stuff. Check out what I'm up to. Hire me!" In a way that a resume and a coding challenge just can't. There are a variety of ways to set this up. [Squarespace](https://www.squarespace.com/websites/create-a-blog) for example, can get you going in an hour.

That all makes sense to me and is a perfectly legitimate reason to build a blog. However, I don't think I would be able to to keep up with if for professional reasons alone. I think I would get bored trying to fit buzz words about current CVEs into posts. I think I would get annoyed with logging into a web app and clicking through menus to paste text in a box. I think I would write 3 or 4 posts, upload a copy of my resume, and forget that the blog exists for months at a time.

I think that what I need is simpler, faster, and less focused. I want my chest of treasures that I can dump all my loot in and marvel at the pile of goodies I have amassed. **I need something that:**
1. I**s nearly instantaneous to access.** It should be already on my computer and ready to go right when I have something to put in it. Minimal friction means I am more likely to document my projects.
2. I** can easily move between hosting, platforms, etc.** I do not want to be locked into working with one company or another's software or service. Markdown docs and static site generators are ideal for this as I am already an [Obsidian](https://obsidian.md/) user for my notes.
3. **Is cheap to operate** 🤷🏼‍♂️

# The How

Luckily, I am not the only one out there with these needs and a very popular option for this is [Hugo](https://gohugo.io/) . Hugo's literal tagline is *"Hugo is one of the most popular open-source static site generators. With its amazing speed and flexibility, Hugo makes building websites fun again."* and that sounds about perfect to me. It is also pretty popular to host Hugo sites on [Github Pages](https://docs.github.com/en/pages) and I have recently spent a lot of time with [Github Actions](https://docs.github.com/en/actions). Once I have a grip on how Hugo works, I can run it with an Action and host the html in Pages. Also, since the whole idea is to be public, I can do all of this in a free repo. I can open the `\content` folder as an Obsidian Vault to do my editing(I'll write more on this below). Finally, I can use Google Domains(Now Squarespace) to get a domain and point it at Github

|Step|Solution|
|-|-|
|Editing|[Obsidian](obsidian.md)|
|Site Generation| [Hugo](https://gohugo.io/) running in  [Github Actions](https://docs.github.com/en/actions) |
|Hosting| [Github Pages](https://docs.github.com/en/pages)|



| URL | Note |
|---|---|
| [Quick start Hugo](https://gohugo.io/getting-started/quick-start/)| Official Docs. Light on Context|
| [Hugo Actually Explained (Websites, Themes, Layouts, and Intro to Scripting) - YouTube](https://www.youtube.com/watch?v=ZFL09qhKi5I) | Thorough and Fast |
| [GitHub - lukeorth/poison: Professional Hugo theme for dev bloggers. Based on Mdo's classic Hyde theme.](https://github.com/lukeorth/poison) | Nice theme that I found |

