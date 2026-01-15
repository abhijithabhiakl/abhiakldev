# Abhijith's Technical Blog

[![Netlify Status](https://api.netlify.com/api/v1/badges/beee6c01-3e77-4ef4-a92b-dfe443df22dc/deploy-status)](https://app.netlify.com/projects/abhiakldevcass/deploys)

Personal technical blog of Abhijith Sunil - Hardware Developer / Tinkerer from Kerala, India.

Built with [Astro](https://astro.build) and [TinaCMS](https://tina.io).

**Live Site**: [blog.abhiakl.dev](https://blog.abhiakl.dev)

---

## About This Blog

This blog covers:
- Home server setups and self-hosting
- IoT projects and applications
- Hardware development
- Technical tutorials and guides
- Learning experiences

---

## Local Development

All commands are run from the root of the project:

| Command                          | Action                                                        |
| :------------------------------- | :------------------------------------------------------------ |
| `npm install`                    | Installs dependencies                                         |
| `npm run dev`                    | Starts local dev server at `localhost:4321`                   |
| `npx tinacms dev -c 'astro dev'` | Manually run local server if the regular command doesn't work |
| `npm run build`                  | Build your production site to `./dist/` (requires env vars)   |
| `npm run preview`                | Preview your build locally, before deploying                  |

### Environment Variables

For local development, create a `.env.development` file:

```env
TINACLIENTID=<from tina.io>
TINATOKEN=<from tina.io>
TINASEARCH=<from tina.io>
```

Get these credentials from your [TinaCMS dashboard](https://tina.io).

### Access TinaCMS

Once the dev server is running, access the CMS at:
```
http://localhost:4321/admin/index.html
```

---

## Deployment

See [DEPLOYMENT.md](./DEPLOYMENT.md) for detailed deployment instructions covering:
- Netlify deployment
- Vercel deployment
- Cloudflare Pages deployment
- DNS configuration for `blog.abhiakl.dev`
- Environment variable setup

---

## Project Structure

```
abhiakldev/
├── posts/              # Blog posts (Markdown)
├── public/             # Static assets (images, CSS)
├── src/
│   ├── components/     # Reusable UI components
│   ├── pages/          # Site pages
│   ├── layouts/        # Page layouts
│   └── config.js       # Site configuration
├── tina/               # TinaCMS configuration
└── DEPLOYMENT.md       # Deployment guide
```

---

## Writing Blog Posts

### Option 1: Using TinaCMS (Visual Editor)
1. Run `npm run dev`
2. Go to `http://localhost:4321/admin/index.html`
3. Create/edit posts visually

### Option 2: Markdown Files
1. Create a new `.md` file in `posts/` directory
2. Add frontmatter:
```markdown
---
title: Your Post Title
slug: url-slug
description: Brief description
tags:
  - technical
  - learning
added: 2025-01-15T12:00:00.000Z
---

Your content here...
```

---

## Credits

This blog is based on [Cassidy Williams' blog template](https://github.com/cassidoo/blahg). Thanks Cassidy! 🙏

---

## License

See [LICENSE](./LICENSE) file.

---

## Contact

- **Twitter**: [@abhijithabhiakl](https://twitter.com/abhijithabhiakl)
- **Email**: hi@abhiakl.xyz
- **GitHub**: [abhijithabhiakl](https://github.com/abhijithabhiakl)
- **Main Site**: [abhijithakl.xyz](https://abhijithakl.xyz)

