# Deployment Guide for blog.abhiakl.dev

## Overview

This guide covers deploying your Astro blog to a subdomain (`blog.abhiakl.dev`) using popular hosting platforms.

---

## Prerequisites

- ✅ GitHub repository with your blog code
- ✅ Domain name (`abhiakl.dev`) with DNS access
- ✅ TinaCMS account and credentials (if using the CMS)

---

## Option 1: Deploy to Netlify (Recommended)

### Step 1: Connect Repository

1. Go to [Netlify](https://netlify.com) and sign in
2. Click "Add new site" → "Import an existing project"
3. Connect to your GitHub repository
4. Select the `abhiakldev` repository

### Step 2: Configure Build Settings

```
Build command: npm run build
Publish directory: dist
```

### Step 3: Add Environment Variables

Go to Site settings → Environment variables and add:

```
TINACLIENTID=<your-tina-client-id>
TINATOKEN=<your-tina-token>
TINASEARCH=<your-tina-search-key>
```

Get these from [tina.io](https://tina.io) dashboard.

### Step 4: Configure Custom Domain

1. In Netlify: Site settings → Domain management → Add custom domain
2. Enter: `blog.abhiakl.dev`
3. Netlify will provide DNS instructions

### Step 5: Update DNS Records

In your domain registrar (where you bought `abhiakl.dev`):

**Add a CNAME record:**
```
Type: CNAME
Name: blog
Value: <your-netlify-site>.netlify.app
TTL: 3600 (or Auto)
```

### Step 6: Enable HTTPS

Netlify automatically provisions SSL certificates. Wait 5-10 minutes for DNS propagation.

---

## Option 2: Deploy to Vercel

### Step 1: Import Project

1. Go to [Vercel](https://vercel.com)
2. Click "Add New" → "Project"
3. Import your GitHub repository

### Step 2: Configure Project

```
Framework Preset: Astro
Build Command: npm run build
Output Directory: dist
Install Command: npm install
```

### Step 3: Environment Variables

Add the same TinaCMS variables as above.

### Step 4: Custom Domain

1. Go to Project Settings → Domains
2. Add `blog.abhiakl.dev`
3. Follow DNS configuration instructions (similar to Netlify)

---

## Option 3: Deploy to Cloudflare Pages

### Step 1: Create Project

1. Go to [Cloudflare Pages](https://pages.cloudflare.com)
2. Connect your GitHub account
3. Select repository

### Step 2: Build Configuration

```
Build command: npm run build
Build output directory: dist
```

### Step 3: Environment Variables

Add TinaCMS credentials in Settings → Environment variables

### Step 4: Custom Domain

If your domain is already on Cloudflare:
1. Go to Custom domains
2. Add `blog.abhiakl.dev`
3. DNS records are configured automatically

---

## DNS Configuration Details

### If Using Netlify/Vercel/Other

Add this CNAME record to your DNS:

```
blog.abhiakl.dev → <your-hosting-url>
```

### If Using Cloudflare Pages

Cloudflare handles DNS automatically if your domain is managed there.

---

## TinaCMS Configuration

### Update Tina Config for Production

Edit `tina/config.js` and ensure the production URL is set:

```javascript
// Add your production domain
const branch = "main"; // or your default branch
const clientId = process.env.TINACLIENTID;
const token = process.env.TINATOKEN;

// ... rest of config
```

### Access TinaCMS in Production

Once deployed, access the CMS at:
```
https://blog.abhiakl.dev/admin/index.html
```

---

## Post-Deployment Checklist

- [ ] Site loads at `https://blog.abhiakl.dev`
- [ ] SSL certificate is active (HTTPS works)
- [ ] All blog posts display correctly
- [ ] RSS feed works: `https://blog.abhiakl.dev/rss.xml`
- [ ] Sitemap generates: `https://blog.abhiakl.dev/sitemap-index.xml`
- [ ] TinaCMS admin panel accessible
- [ ] Social share images work (Open Graph)
- [ ] Navigation links work correctly
- [ ] Footer links point to correct URLs

---

## Troubleshooting

### Build Fails

**Issue**: Build fails with TinaCMS errors

**Solution**: Ensure all three environment variables are set correctly:
- `TINACLIENTID`
- `TINATOKEN`
- `TINASEARCH`

### DNS Not Resolving

**Issue**: `blog.abhiakl.dev` doesn't load

**Solution**: 
- Wait 24-48 hours for DNS propagation
- Check CNAME record is correct
- Use `dig blog.abhiakl.dev` to verify DNS

### SSL Certificate Issues

**Issue**: HTTPS not working

**Solution**:
- Most platforms auto-provision SSL
- Wait 10-15 minutes after DNS propagation
- Check hosting platform's SSL settings

---

## Continuous Deployment

Once set up, your blog will auto-deploy when you:
1. Push changes to your GitHub repository
2. Edit content via TinaCMS admin panel

---

## Connecting to Main Site

To link from your main site (`abhiakl.dev` or `abhijithakl.xyz`):

Add a link in your main site's navigation:
```html
<a href="https://blog.abhiakl.dev">Blog</a>
```

Your blog already links back to the main site via the Header component.

---

## Local Development

Remember, for local development:

```bash
npm install
npm run dev
```

Access at: `http://localhost:4321`

---

## Support Resources

- **Netlify Docs**: https://docs.netlify.com
- **Vercel Docs**: https://vercel.com/docs
- **Cloudflare Pages**: https://developers.cloudflare.com/pages
- **Astro Docs**: https://docs.astro.build
- **TinaCMS Docs**: https://tina.io/docs

---

## Next Steps

1. Choose a hosting platform (Netlify recommended for beginners)
2. Set up environment variables
3. Configure custom domain
4. Deploy!
5. Test everything works
6. Start writing more blog posts! 🚀
