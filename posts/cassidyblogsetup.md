---
title: How to setup Cassidy's blog template built with Astro and TinaCMS!
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

#### Setup Guide for Cassidy's Blog Repo with TinaCMS

First, head over to [Cassidy's repo](https://github.com/cassidoo/blahg). You have two options to proceed—either is fine. The easier method is via **Netlify**, which I’ll explain here.

---

### 🚀 Deploy via Netlify

1. Click the large blue **Deploy to Netlify** button.
2. Authorize GitHub access when prompted.
3. Netlify will attempt to deploy the template directly from the GitHub repo.

> ⚠️ **Note:** This deployment will fail initially. That's because the project uses **TinaCMS**, which requires authentication credentials to work.

---

### 🛠️ TinaCMS Setup (Skip if you don't need TinaCMS)

If you plan to use TinaCMS (the in-browser content editor), you'll need three credentials:

- `TINA_CLIENT_ID`
- `TINA_TOKEN`
- `TINA_SEARCH`

#### Steps to Get TinaCMS Credentials:

1. Go to [tina.io](https://tina.io/) and sign up (preferably with GitHub).
2. Enter your full name and accept the privacy policy.
3. Click the large **Create a New Project** button.
4. Choose **Custom Project** when prompted.
5. Authenticate with GitHub again to select the template repository.
    - This is the repo that was auto-created when you clicked **Deploy to Netlify** earlier.
6. When asked for a domain, enter: `http://localhost:3000`
7. On the next page, you’ll see your **TINA_CLIENT_ID** at the top.
8. Click the **Tokens** tab:
    - The **Read-only ID** is your `TINA_TOKEN`
    - Below that is your `TINA_SEARCH`

---

### 🔧 Add Credentials to Your Project

#### On Netlify:

- Go to your deployed site’s settings
- Navigate to **Site settings → Build & deploy → Environment variables**
- Add the following:

```env
TINA_CLIENT_ID=your-client-id
TINA_TOKEN=your-token
TINA_SEARCH=your-search


TINACLIENTID=your-client-id

TINATOKEN=your-token

TINASEARCH=your-search-key
