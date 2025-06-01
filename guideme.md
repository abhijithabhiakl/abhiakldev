# Project Overview

Welcome to this project! This repository contains a blog template built with Astro and TinaCMS.


## Getting Started

1. Clone the repository.
2. Install dependencies: `npm install`
3. Start the development server: `npm run dev`
    ( you might need to edit the package.json file if an error shows up while running `npm run dev` : 
    `"dev": "npx tinacms dev -c \"astro dev\"",`)


## Project File Structure

```
abhiakldev/
├── .astro/
│   ├── content-assets.mjs
│   ├── content-modules.mjs
│   ├── content.d.ts
│   ├── data-store.json
│   ├── settings.json
│   ├── types.d.ts
│   └── collections/
├── .github/
│   ├── FUNDING.yml
│   └── commands/
├── .vscode/
│   ├── extensions.json
│   └── launch.json
├── posts/
│   ├── cassidyblogsetup.md
│   ├── hello-world.md
│   ├── idkwhatslugis.md
│   └── markdown-example.md
├── public/
│   ├── favicon.ico
│   ├── home-blog-card.png
│   ├── robots.txt
│   ├── script.js
│   ├── style.css
│   ├── admin/
│   ├── assets/
│   └── img/
├── src/
│   ├── config.js
│   ├── content.config.js
│   ├── components/
│   ├── layouts/
│   ├── pages/
│   ├── scripts/
│   └── style/
├── tina/
├── .gitignore
├── .npmrc
├── astro.config.mjs
├── guideme.md
├── LICENSE
├── package.json
└── README.md
```

## Significant Project File Structure (with `src/components` and `src/pages` expanded)

```
abhiakldev/
├── posts/                     -->   all blog markdowns
│   ├── cassidyblogsetup.md
│   ├── hello-world.md
│   ├── idkwhatslugis.md
│   └── markdown-example.md
├── public/                    -->   all static files here
│   ├── robots.txt
│   ├── style.css              -->   site css
│   ├── admin/
│   ├── assets/
│   └── img/                   -->   images
├── src/
│   ├── config.js
│   ├── content.config.js
│   ├── components/
│   │   ├── Footer.astro       -->   all page footer
│   │   ├── Header.astro       -->   all page navbar and heading
│   │   ├── basehead.astro     -->   all page head
│   │   ├── colorscript.astro
│   │   ├── headerlink.astro
│   │   ├── homeposts.astro
│   │   ├── post.astro
│   │   └── tags.astro
│   │
│   ├── layouts/
│   │   └── ... (layout files)
│   ├── pages/
│   │   ├── about.md
│   │   ├── index.astro         -->   # main page
│   │   ├── posts.astro         -->   # blog post page
│   │   │── future pages
│   │   │── ...
│   │   └── rss.xml.js
│   ├── scripts/
│   │  
│   └── style/
│      
├── tina/
│   └── ... (TinaCMS config files)
├── astro.config.mjs
├── package.json
├── README.md
└── guideme.md
```
## More Information

See the main [README.md](README.md) for detailed setup and usage instructions.