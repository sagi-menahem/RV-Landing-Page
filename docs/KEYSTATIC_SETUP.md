# Keystatic Cloud Setup Guide

This guide walks you through setting up Keystatic Cloud for production, enabling non-technical clients to edit content through a secure, authenticated admin panel.

---

## Overview

| Environment | Storage Mode | Authentication |
|-------------|--------------|----------------|
| Development (localhost) | `local` | None needed |
| Production (Vercel) | `cloud` | Keystatic Cloud (GitHub OAuth) |

---

## Step 1: Create a Keystatic Cloud Account

1. Go to **[https://keystatic.cloud](https://keystatic.cloud)**
2. Click **"Sign in with GitHub"**
3. Authorize the Keystatic Cloud app to access your GitHub account
4. You'll be redirected to your Keystatic Cloud dashboard

---

## Step 2: Create a New Project

1. In the Keystatic Cloud dashboard, click **"Create Project"**
2. Fill in the details:
   - **Project Name**: e.g., `client-landing-page`
   - **Team**: Select your team or create a new one
3. Click **"Create"**

After creation, you'll see your project with a slug like:
```
your-team/client-landing-page
```

**Save this value** - you'll need it for the `KEYSTATIC_STORAGE_PROJECT` environment variable.

---

## Step 3: Connect Your GitHub Repository

1. In your project settings, click **"Connect Repository"**
2. Select the GitHub repository containing your Astro site
3. Choose the branch to use (typically `main` or `production`)
4. Authorize Keystatic to read/write to the repository

> **How it works**: When clients edit content in Keystatic Cloud, changes are committed directly to your GitHub repository, which triggers a Vercel rebuild.

---

## Step 4: Get Your Environment Variables

From your Keystatic Cloud project dashboard, locate these values:

| Variable | Where to Find | Description |
|----------|---------------|-------------|
| `KEYSTATIC_STORAGE_PROJECT` | Project slug shown at top of dashboard | Format: `team/project` |
| `KEYSTATIC_SECRET` | Project Settings > API Keys > Generate | 32-character secret for session encryption |

### Generate the Secret Key

1. Go to **Project Settings** > **API Keys**
2. Click **"Generate New Secret"**
3. Copy the generated secret immediately (it won't be shown again)

---

## Step 5: Configure Vercel Environment Variables

1. Go to your **Vercel Dashboard** > Select your project
2. Navigate to **Settings** > **Environment Variables**
3. Add the following variables:

| Name | Value | Environment |
|------|-------|-------------|
| `KEYSTATIC_STORAGE_PROJECT` | `your-team/your-project` | Production |
| `KEYSTATIC_SECRET` | `your-32-char-secret-key` | Production |
| `PUBLIC_SITE_URL` | `https://your-domain.com` | Production |
| `PUBLIC_EMAILJS_SERVICE_ID` | Your EmailJS service ID | Production |
| `PUBLIC_EMAILJS_TEMPLATE_ID` | Your EmailJS template ID | Production |
| `PUBLIC_EMAILJS_PUBLIC_KEY` | Your EmailJS public key | Production |

> **Important**: Do NOT add `KEYSTATIC_STORAGE_PROJECT` or `KEYSTATIC_SECRET` to your local `.env` file. These are production-only variables. Local development uses `local` storage mode automatically.

---

## Step 6: Deploy and Test

1. Push your code to GitHub (triggers Vercel deployment)
2. Wait for the build to complete
3. Visit `https://your-domain.com/keystatic`
4. You should see the Keystatic Cloud login screen
5. Sign in with your GitHub account

---

## Inviting Clients (Team Members)

To give clients access to edit content:

1. Go to your **Keystatic Cloud Dashboard**
2. Navigate to **Team Settings** > **Members**
3. Click **"Invite Member"**
4. Enter the client's email address
5. They'll receive an invitation to create a Keystatic Cloud account

> **Note**: Clients will sign in with their own GitHub account. They don't need repository access - Keystatic Cloud handles permissions.

---

## How Content Editing Works

```
┌─────────────────────────────────────────────────────────────┐
│                    Client Workflow                          │
├─────────────────────────────────────────────────────────────┤
│  1. Client visits yoursite.com/keystatic                    │
│  2. Signs in via Keystatic Cloud (GitHub OAuth)             │
│  3. Edits content in the visual admin panel                 │
│  4. Clicks "Save" → Keystatic commits to GitHub             │
│  5. Vercel detects commit → Rebuilds site automatically     │
│  6. Changes are live in ~60 seconds                         │
└─────────────────────────────────────────────────────────────┘
```

---

## Troubleshooting

### "Storage project not configured" error

**Cause**: Missing `KEYSTATIC_STORAGE_PROJECT` environment variable in Vercel.

**Fix**: Add the variable in Vercel Dashboard > Settings > Environment Variables.

### "Unauthorized" error on /keystatic

**Cause**: Missing or invalid `KEYSTATIC_SECRET`.

**Fix**:
1. Generate a new secret in Keystatic Cloud dashboard
2. Update the `KEYSTATIC_SECRET` variable in Vercel
3. Redeploy

### Changes not appearing after save

**Cause**: Vercel rebuild not triggered.

**Fix**:
1. Check GitHub repository for new commits
2. Check Vercel dashboard for deployment status
3. Ensure the GitHub repository is correctly connected in Keystatic Cloud

### Local development shows cloud login

**Cause**: Environment detection issue.

**Fix**: Ensure you're running `npm run dev` (not `npm run build && npm run preview`).

---

## Security Notes

- **Keystatic Cloud** handles all authentication via GitHub OAuth
- **KEYSTATIC_SECRET** encrypts session data - never commit this to git
- **Clients don't need GitHub repo access** - they only need a Keystatic Cloud account
- **All content changes are git commits** - you have full version history

---

## Quick Reference

### Environment Variables Summary

```bash
# Production only (Vercel)
KEYSTATIC_STORAGE_PROJECT=your-team/your-project
KEYSTATIC_SECRET=your-32-character-secret-key

# Both environments
PUBLIC_SITE_URL=https://your-domain.com
PUBLIC_EMAILJS_SERVICE_ID=your_service_id
PUBLIC_EMAILJS_TEMPLATE_ID=your_template_id
PUBLIC_EMAILJS_PUBLIC_KEY=your_public_key
```

### URLs

| Resource | URL |
|----------|-----|
| Keystatic Cloud Dashboard | https://keystatic.cloud |
| Your Admin Panel (Production) | https://your-domain.com/keystatic |
| Your Admin Panel (Development) | http://localhost:4321/keystatic |

---

## Support

- Keystatic Documentation: https://keystatic.com/docs
- Keystatic Discord: https://discord.gg/keystatic
- Vercel Documentation: https://vercel.com/docs
