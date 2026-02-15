# Cloudflare Deployment Guide

## Quick Deploy (Hackathon Fast Track)

### 1. Set Environment Variables in Cloudflare Dashboard

Go to: **Cloudflare Dashboard → Workers & Pages → Your Worker → Settings → Variables**

#### Environment Variables (non-secret):
```
AUTH_TRUST_HOST=true
NEXTAUTH_URL=https://parallel-you.<your-account>.workers.dev
NEXT_PUBLIC_SUPABASE_URL=https://bigogflfmvdwytebmzmt.supabase.co
```

#### Secrets (encrypt these):
```
AUTH_SECRET=<your-auth-secret>
AUTH_GOOGLE_ID=<your-google-client-id>
AUTH_GOOGLE_SECRET=<your-google-client-secret>
SUPABASE_SERVICE_ROLE_KEY=<your-service-role-key>
LLM_PROVIDER=gemini
GEMINI_API_KEY=<your-gemini-key>
GEMINI_MODEL=gemini-2.0-flash
```

**Or if using OpenAI:**
```
LLM_PROVIDER=openai
OPENAI_API_KEY=<your-openai-key>
OPENAI_MODEL=gpt-4o-mini
OPENAI_BASE_URL=https://api.openai.com/v1
```

### 2. Update Google OAuth Redirect URI

In **Google Cloud Console → APIs & Credentials → OAuth 2.0 Client IDs**:

Add authorized redirect URI:
```
https://parallel-you.<your-account>.workers.dev/api/auth/callback/google
```

(Or use your custom domain if you set one up)

### 3. Deploy from CLI

```bash
npm run deploy
```

That's it! 🚀

---

## Alternative: GitHub Auto-Deploy

### 1. Connect GitHub Repo

1. Go to Cloudflare Dashboard → Workers & Pages
2. Click "Create Application" → "Pages" → "Connect to Git"
3. Select your GitHub repo: `hackathon` or `parallel-you-`
4. Configure build:
   - **Framework preset:** Next.js
   - **Build command:** `npm run build`
   - **Build output directory:** `.next`
5. Click "Save and Deploy"

### 2. Add Environment Variables

In the Pages settings, add all the variables listed above.

### 3. Update Build Command

In Pages settings → Builds & deployments:
- **Build command:** `npm install && opennextjs-cloudflare build`

### 4. Push to GitHub

Every push to `main` will auto-deploy!

---

## Custom Domain (Optional)

1. Go to Worker/Pages → Custom Domains
2. Click "Add Custom Domain"
3. Enter your domain (e.g., `parallel-you.com`)
4. Follow DNS setup instructions
5. Update `NEXTAUTH_URL` to your custom domain
6. Update Google OAuth redirect URI to your custom domain

---

## Environment Variables Reference

### Required for Auth
| Variable | Type | Description |
|----------|------|-------------|
| `AUTH_SECRET` | Secret | Generate with: `openssl rand -base64 32` |
| `AUTH_GOOGLE_ID` | Secret | Google OAuth Client ID |
| `AUTH_GOOGLE_SECRET` | Secret | Google OAuth Client Secret |
| `AUTH_TRUST_HOST` | Var | Set to `true` for Cloudflare |
| `NEXTAUTH_URL` | Var | Your worker/custom domain URL |

### Required for Database (Optional - not used in current setup)
| Variable | Type | Description |
|----------|------|-------------|
| `NEXT_PUBLIC_SUPABASE_URL` | Var | Supabase project URL |
| `SUPABASE_SERVICE_ROLE_KEY` | Secret | Supabase service role key |

### Required for LLM (Choose One)
**Gemini (Recommended - Free Tier):**
| Variable | Type | Description |
|----------|------|-------------|
| `LLM_PROVIDER` | Var | Set to `gemini` |
| `GEMINI_API_KEY` | Secret | Get from https://aistudio.google.com/app/apikey |
| `GEMINI_MODEL` | Var | `gemini-2.0-flash` |

**OpenAI:**
| Variable | Type | Description |
|----------|------|-------------|
| `LLM_PROVIDER` | Var | Set to `openai` |
| `OPENAI_API_KEY` | Secret | Get from https://platform.openai.com/api-keys |
| `OPENAI_MODEL` | Var | `gpt-4o-mini` |
| `OPENAI_BASE_URL` | Var | `https://api.openai.com/v1` |

---

## Troubleshooting

### Build fails with "Module not found"
Run: `npm install` and try again

### "Invalid redirect URI" error
Make sure you added the exact Cloudflare Workers URL to Google OAuth settings

### Environment variables not working
- Check they're set in Cloudflare Dashboard (not just local .env)
- Secrets must be added as "Encrypt" type
- Redeploy after changing variables

### Auth session not persisting
- Verify `AUTH_TRUST_HOST=true` is set
- Check `NEXTAUTH_URL` matches your actual domain
- Make sure cookies aren't blocked in browser

---

## Local Preview (Test Before Deploy)

```bash
npm run preview
```

This runs your app in a local Cloudflare Workers environment.

---

## Cost

**Free tier includes:**
- 100,000 requests/day
- More than enough for a hackathon demo!

---

## Files Created

- ✅ `wrangler.jsonc` - Cloudflare Workers config
- ✅ `open-next.config.ts` - OpenNext adapter config
- ✅ `.dev.vars` - Local development variables (gitignored)
- ✅ Updated `package.json` with deploy scripts
- ✅ Updated `.gitignore` to exclude build artifacts

---

**Ready to deploy!** Run `npm run deploy` when you're ready to go live.
