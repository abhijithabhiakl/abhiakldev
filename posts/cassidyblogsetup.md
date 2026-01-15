---
title: Complete Guide to Setting Up Cassidy's Astro Blog Template with TinaCMS
slug: cassidyblogsetup
description: A step-by-step guide to deploying and configuring the Cassidy blog template, including TinaCMS setup and local development
tags:
  - technical
  - learning
  - blogging
added: 2025-05-31T20:35:24.132Z
updated: 2026-01-16T00:00:00.000Z
---

## Introduction

Setting up Cassidy's Astro blog template can be tricky for beginners, especially when integrating TinaCMS. This guide walks you through each step in plain language, so you don't get stuck like I did.

**What you'll be building:** A modern, fast blog with a visual content editor (TinaCMS) and automated deployment.

---

## Prerequisites

- A GitHub account
- A Netlify account (free)
- A TinaCMS account (free)
- ~30 minutes of setup time

---

## Step 1: Deploy to Netlify

1. Head to [Cassidy's blog repo](https://github.com/cassidoo/blahg)
2. Click the blue **"Deploy to Netlify"** button
3. Authorize Netlify to access your GitHub account
4. Name your repository (e.g., `my-personal-blog`)

**What happens next:** Netlify creates a copy of the template in your GitHub account and starts a deployment. **This will fail** — that's expected. We need TinaCMS credentials first.

---

## Step 2: Set Up TinaCMS

### Create a TinaCMS Account

1. Visit [tina.io](https://tina.io) and sign up
2. **Pro tip:** Use GitHub authentication to make it easier
3. Fill in your full name and accept the privacy policy
4. You'll see a dashboard with a blue **"Create Project"** button

### Create a TinaCMS Project

1. Click **"Create Project"** 
2. Select **"Custom Project"** on the next screen
3. Authenticate with GitHub again (if prompted)
4. **Select the repository** that Netlify just created (it has your blog name)
5. For the domain, enter `http://localhost:3000` (we'll update this later)
6. Click create

### Get Your TinaCMS Credentials

Once the project is created, you'll see a dashboard with your credentials:

1. **TINACLIENTID** — displayed at the top of the dashboard
2. **TINATOKEN** — go to the "Tokens" section and copy the "read-only token"
3. **TINASEARCH** — in the Tokens section, find the search token

**Keep these values safe.** You'll need them in the next step.

---

## Step 3: Configure Environment Variables

### Option A: Deploy to Netlify (Recommended for Beginners)

1. Go back to your Netlify project
2. Navigate to **Settings → Environment Variables**
3. Add three new variables:
   ```
   TINACLIENTID = [paste your client ID]
   TINATOKEN = [paste your token]
   TINASEARCH = [paste your search key]
   ```
4. Click **"Deployments"** and **redeploy** the latest commit
5. Wait for the build to complete — it should succeed now!

### Option B: Local Development Setup

If you want to work on your blog locally:

1. Clone your repository to your computer
2. Create a `.env.development` file in the root folder
3. Add these lines:
   ```
   TINACLIENTID=your-client-id
   TINATOKEN=your-token
   TINASEARCH=your-search-key
   ```
4. Run `npm install` to install dependencies
5. Run `npm run dev` to start the local server at `localhost:4321`

---

## Step 4: Access Your Blog

- **Live site:** Visit your Netlify domain (e.g., `my-blog.netlify.app`)
- **TinaCMS editor:** Go to `[your-domain]/admin` to edit posts visually
- **Local editor:** After `npm run dev`, visit `http://localhost:4321/admin`

---

## Troubleshooting

**"Build failed" error:**
- Check that all three environment variables are set correctly
- Make sure you're using the read-only token (not the full token)
- Redeploy from Netlify

**"Can't connect to TinaCMS":**
- Verify your `TINACLIENTID` is correct
- Make sure you're accessing `/admin` at the end of your URL

**Local development not working:**
- Clear node_modules: `rm -rf node_modules && npm install`
- Check that `.env.development` has the exact variable names (no typos)

---

## Next Steps

- Write your first post in TinaCMS or by editing markdown files directly
- Customize your site colors and fonts in the Astro config
- Set a custom domain in Netlify settings

Happy blogging! 
