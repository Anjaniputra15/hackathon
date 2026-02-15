# Netlify Deployment Guide

## Why Netlify? ✨

- **Native Next.js Support** - No adapters needed
- **Automatic HTTPS** - Free SSL certificates
- **Instant Rollbacks** - One-click previous version restore
- **Edge Functions** - Fast global performance
- **Generous Free Tier** - 100GB bandwidth/month

---

## Quick Deploy (3 Methods)

### Method 1: GitHub Auto-Deploy (Recommended) 🚀

**Step 1: Push to GitHub**
```bash
git push origin main
```

**Step 2: Connect to Netlify**

1. Go to [app.netlify.com](https://app.netlify.com)
2. Click "Add new site" → "Import an existing project"
3. Choose "Deploy with GitHub"
4. Select your repo: `parallel-you-` or `hackathon`
5. Configure settings:
   - **Branch:** `main`
   - **Build command:** `npm run build`
   - **Publish directory:** `.next`
6. Click "Show advanced" → "New variable" and add environment variables (see below)
7. Click "Deploy site"

**Done!** Every push to `main` will auto-deploy.

---

### Method 2: Netlify CLI (For Quick Testing) ⚡

**Step 1: Login to Netlify**
```bash
npx netlify login
```

**Step 2: Initialize Site**
```bash
npx netlify init
```

Follow prompts:
- Create & configure new site? **Yes**
- Team: Choose your team
- Site name: `parallel-you` (or custom)
- Build command: `npm run build`
- Publish directory: `.next`
- Netlify functions: `.netlify/functions`

**Step 3: Deploy**
```bash
npm run deploy
```

Or for draft preview:
```bash
npx netlify deploy
```

---

### Method 3: Drag & Drop (No Git Required) 📦

**Step 1: Build Locally**
```bash
npm run build
```

**Step 2: Deploy Folder**
1. Go to [app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag the `.next` folder to the upload zone
3. Wait for deployment

⚠️ **Note:** This method doesn't support environment variables - use for testing only.

---

## Environment Variables

Add these in **Netlify Dashboard → Site settings → Environment variables**:

### Required for Auth
```bash
AUTH_SECRET=<generate-with-openssl-rand-base64-32>
AUTH_GOOGLE_ID=<your-google-client-id>
AUTH_GOOGLE_SECRET=<your-google-client-secret>
AUTH_TRUST_HOST=true
NEXTAUTH_URL=https://your-site-name.netlify.app
```

### Required for Database (Optional - not used in current setup)
```bash
NEXT_PUBLIC_SUPABASE_URL=https://bigogflfmvdwytebmzmt.supabase.co
SUPABASE_SERVICE_ROLE_KEY=<your-service-role-key>
```

### Required for LLM (Choose One)

**Option A: Gemini (Free Tier)** ⭐
```bash
LLM_PROVIDER=gemini
GEMINI_API_KEY=<get-from-aistudio.google.com/app/apikey>
GEMINI_MODEL=gemini-2.0-flash
```

**Option B: OpenAI**
```bash
LLM_PROVIDER=openai
OPENAI_API_KEY=<get-from-platform.openai.com/api-keys>
OPENAI_MODEL=gpt-4o-mini
OPENAI_BASE_URL=https://api.openai.com/v1
```

---

## Update Google OAuth Redirect URI

1. Go to **Google Cloud Console → APIs & Credentials → OAuth 2.0 Client IDs**
2. Add authorized redirect URI:
   ```
   https://your-site-name.netlify.app/api/auth/callback/google
   ```
3. Replace `your-site-name` with your actual Netlify subdomain

---

## Custom Domain (Optional)

1. Go to **Site settings → Domain management**
2. Click "Add custom domain"
3. Enter your domain (e.g., `parallel-you.com`)
4. Follow DNS configuration instructions:
   - **Option A:** Point domain to Netlify (add A/AAAA records)
   - **Option B:** Use Netlify DNS (transfer nameservers)
5. Update `NEXTAUTH_URL` to your custom domain
6. Update Google OAuth redirect URI to custom domain

---

## Local Development

**Run Netlify Dev (simulates production environment):**
```bash
npm run preview
```

This runs your app with:
- Netlify Functions
- Environment variables from `.env` or Netlify
- Edge Functions
- Redirects/Headers from `netlify.toml`

**Regular Next.js Dev Server:**
```bash
npm run dev
```

---

## Deployment Status

**Check Build Logs:**
1. Netlify Dashboard → Site → Deploys
2. Click on a deploy to see logs
3. Green ✅ = Success
4. Red ❌ = Failed (click to see error)

**Live URL:**
```
https://your-site-name.netlify.app
```

**Deploy Previews:**
- Every PR gets a preview URL
- Format: `https://deploy-preview-123--your-site-name.netlify.app`

---

## Troubleshooting

### Build fails with "Module not found"
Run locally first:
```bash
npm install
npm run build
```

If it works locally but fails on Netlify, check:
- Node version matches (set in `netlify.toml`)
- All dependencies in `package.json`

### "Invalid redirect URI" error
- Verify Google OAuth redirect URI matches your Netlify URL **exactly**
- Don't forget `/api/auth/callback/google` at the end
- Check for `http` vs `https` mismatch

### Environment variables not working
- Go to Site settings → Environment variables
- Make sure they're added for the correct **context** (Production/Deploy previews)
- Redeploy after adding variables (they don't auto-apply)

### Auth session not persisting
- Check `AUTH_TRUST_HOST=true` is set
- Verify `NEXTAUTH_URL` matches your actual domain
- Clear browser cookies and try again
- Make sure cookies aren't blocked

### Function timeout errors
Netlify functions timeout after 10 seconds (free tier) or 26 seconds (Pro).

If LLM responses are slow:
- Upgrade to Pro plan ($19/mo for 26s timeout)
- Or optimize prompts to get faster responses
- Or switch to streaming responses

---

## Performance Optimization

**Enable Next.js Image Optimization:**
Netlify automatically handles `next/image` optimization.

**Enable ISR (Incremental Static Regeneration):**
Already configured in Next.js config - works out of the box.

**Edge Functions:**
For faster global performance, upgrade to Pro plan for Edge Functions.

---

## Cost

**Free Tier Includes:**
- 300 build minutes/month
- 100GB bandwidth/month
- 125,000 serverless function requests
- Unlimited sites
- HTTPS & CDN
- Deploy previews

**More than enough for a hackathon demo!**

---

## Files Created

- ✅ `netlify.toml` - Netlify configuration
- ✅ Updated `package.json` with Netlify scripts
- ✅ Removed Cloudflare-specific packages

---

## Compare: Netlify vs Cloudflare

| Feature | Netlify | Cloudflare |
|---------|---------|------------|
| Setup | ⭐⭐⭐⭐⭐ Easiest | ⭐⭐⭐ Requires adapter |
| Next.js Support | ✅ Native | ✅ Via OpenNext |
| Free Tier | 100GB bandwidth | 100k requests/day |
| Build Time | ~2-3 min | ~2-3 min |
| Global CDN | ✅ Yes | ✅ Yes |
| Serverless Functions | ✅ Yes (10s timeout) | ✅ Yes (30s timeout) |
| Deploy Previews | ✅ Auto PR previews | ✅ Manual only |
| Dashboard | ⭐⭐⭐⭐⭐ Very intuitive | ⭐⭐⭐⭐ Good |

**Verdict:** Netlify is easier for Next.js apps. Cloudflare is better for edge compute & unlimited requests.

---

**Ready to deploy!** Run `npm run deploy` or connect GitHub for auto-deploys. 🚀
