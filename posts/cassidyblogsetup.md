---
title: How to setup About Cassidy's blog template built with Astro and TinaCMS!
slug: cassidyblogsetup
description: >-
  To be honest it was a bit hard to setup this template even with cassidy's and
  other docs available, so as a first blog I'm writing how to setup this
  template even for a beginner who barely knows about web.
tags:
  - technical
  - learning
added: 2025-05-31T20:35:24.132Z
---

#### An I said before, as a person not that much familiar with web it was a bit hard to setup this template even with cassidy's and other docs available, so as a first blog I'm writing how to setup this template with a much easy to follow guide.

#### First of all head onto [cassidy's repo](https://github.com/cassidoo/blahg), now you have 2 options, either way is fine, the easy way is with Netlify, Ill explain just that, to proceed press the large blue button saying Deploy to Netlify,  now this will ask you to authorise to GitHub with Netlify and then deploy the template directly to Netlify, now this will not actually deploy the site, it will fail, that's because tinaCMS (if you don't want tinaCMS you don't need to do this, I think you can remove it and just edit the markdown files directly to write blog, i haven't tried it yet and i don't know how) is integrated in the code, for the successful build you need the TinaCMS credentials - client id, tina token and tina search, at this point you need to visit [tina.io](https://tina.io/)  and create a new account, its better if you authenticate with github, a menu will pop up asking your full name and privacy policy, enter your name and on the next menu you will be having a large blue button to create a new project, create a new project, select custom porject on the next menu, then authenticate with github again to select the template repository this repository will be automatically created at the moment you press the deploy to netlify button, select this repo and proceed, then it will ask you to enter a domain, enter [http://localhost:3000](http://localhost:3000) for now, then at the next page you'll get the TINACLIENTID![](/assets/image_2025-06-01_03-08-22.png) on the top bar you can see tokens option, the read only id is the TINATOKEN and just below that would be TINASEARCH, copy each value and go to netlify template deploy page then to project configuration and then to environment variables,  , if you're building it locally to generate SSG you should create a .env file in the root folder and then add these in the file :

TINACLIENTID=your-client-id

TINATOKEN=your-token

TINASEARCH=your-search-key
