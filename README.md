# CENTERLINE Efficiency Calculator — Private Demo

A stakeholder-facing preview of the CENTERLINE Architecture Firm Efficiency Calculator, gated behind a client-side access code.

---

## Deployment to GitHub Pages

### 1. Create a new GitHub repository

Pick an obscure repo name so the URL is not easily guessable. Good example: `ce-preview-q3-2026` or `centerline-prev-a7k9`. Avoid obvious names like `centerline-demo` (still works, but marginally more discoverable).

- Go to github.com → New repository
- Repo can be Public (the access code gates content) or Private with Pages (requires GitHub Pro+)
- **Do not initialize with a README.** You'll push this folder as-is.

### 2. Push this folder to the repo

From this folder:

```bash
git init
git add .
git commit -m "Initial demo build"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git push -u origin main
```

### 3. Enable GitHub Pages

- In the repo on github.com, go to **Settings → Pages**
- Source: **Deploy from a branch**
- Branch: **main** / **/ (root)**
- Save
- Wait 1 to 2 minutes for the first deploy

The URL will be:

```
https://YOUR_USERNAME.github.io/YOUR_REPO_NAME/
```

### 4. Share with stakeholders

Send them:

- The URL
- The access code (`seo123`)

Their flow: open URL, enter code, use the tool.

---

## Changing the access code

The current access code is `seo123`. To rotate it:

### Option A: Use an online SHA-256 generator

Go to any SHA-256 generator (e.g., `https://emn178.github.io/online-tools/sha256.html`). Enter your new password. Copy the hash. Paste it into `index.html` replacing the value of `PASSWORD_HASH`.

### Option B: Use your terminal

```bash
echo -n "your-new-password" | shasum -a 256
```

The first token of the output is your hash. Paste it into `index.html`.

### Option C: Use the browser console

Open any webpage, press F12 for DevTools, paste:

```javascript
(async s => Array.from(new Uint8Array(await crypto.subtle.digest('SHA-256', new TextEncoder().encode(s)))).map(b => b.toString(16).padStart(2,'0')).join(''))('your-new-password')
```

The console prints the hash.

### Where to paste it

In `index.html`, find this line near the bottom (around line 940):

```javascript
const PASSWORD_HASH = '222824e39821eb3d68d35d557a8a3d657e462c6576b3822dfe931cc03eb7299c';
```

Replace with your new hash. Commit, push. GitHub Pages will redeploy in 1 to 2 minutes.

---

## What this is and isn't

### What it is

- Private preview for invited stakeholders
- Client-side gated using SHA-256 hash comparison
- Marked `noindex` so it will not appear in search results
- Deployable on GitHub Pages with zero configuration beyond repo setup

### What it isn't

- **Not truly secure.** The tool content is in the page source. A determined attacker who views the source can bypass the gate. This is intentional for a low-stakes demo; the gate deters casual access and prevents Google from indexing content.
- **Not an authentication system.** No user accounts, no tokens, no server. One shared code for all stakeholders.

### When you need more

If you need real auth (email-based login, per-user access, audit logs), use one of:

- **Cloudflare Pages + Cloudflare Access.** Free for up to 50 users. Requires a custom domain pointed at Cloudflare. Configure Access in the dashboard, pick email OTP or magic link. About 30 minutes of setup.
- **Netlify with password protection.** $19/month paid plan. Drop the file in, enable password protection in settings. Simplest paid option.
- **Vercel with password protection.** Similar to Netlify. Paid feature.

---

## Files in this folder

```
centerline-demo-github/
├── index.html        # the gated tool (what GitHub Pages serves)
└── README.md         # this file
```

That's it. One HTML file, one README. Nothing to build, no package.json, no dependencies.

---

## Troubleshooting

**The gate doesn't unlock with the password.**
Check that `PASSWORD_HASH` in `index.html` matches the hash of the password you're entering. The current password is `seo123` (all lowercase, no spaces).

**GitHub Pages shows a 404.**
Wait 2 minutes after enabling Pages. Check Settings → Pages confirms the site is deployed. The URL format is `https://USERNAME.github.io/REPO/`, not `/REPO/index.html`.

**The page loads but the tool is invisible.**
You probably entered the code correctly. Look for the "Private Demo" gate; if it's still showing, the JavaScript didn't run. Check browser console for errors.

**Stakeholders are asking for a shorter URL.**
Add a custom domain. In repo Settings → Pages → Custom domain, enter something like `demo.yourbrand.com`. Update your DNS to point a CNAME to `USERNAME.github.io`. GitHub Pages will issue SSL automatically.

**I need to share different codes with different stakeholders.**
This single-password setup doesn't support that. Switch to Cloudflare Access (recommended for per-user tracking) or issue fresh demo deployments with different codes per recipient.

---

## Rotating for a new demo cycle

When you want to lock out old stakeholders:

1. Generate a new password and hash (see above)
2. Paste into `index.html`
3. Commit and push
4. Old session storage will fail the new hash check; everyone re-enters

No cache-busting headers needed — the hash change is enough.
