# FDE - AI Automation Agency Website

The official website of **FDE - AI Automation Agency**. FDE builds AI voice agents, chat agents, n8n and WhatsApp automation, and websites for small and medium businesses.

**Live site:** [www.fdeautomation.com](https://www.fdeautomation.com)

![Next.js](https://img.shields.io/badge/Next.js-16-black)
![React](https://img.shields.io/badge/React-19-61DAFB)
![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4-38BDF8)

---

## Table of Contents

1. [Overview](#overview)
2. [Features](#features)
3. [Tech Stack](#tech-stack)
4. [Pages](#pages)
5. [Project Structure](#project-structure)
6. [Getting Started](#getting-started)
7. [Environment Variables](#environment-variables)
8. [Supabase Setup (Login and Comments)](#supabase-setup-login-and-comments)
9. [AI Assistant](#ai-assistant)
10. [Security](#security)
11. [Editing Content](#editing-content)
12. [Deployment](#deployment)
13. [Scripts](#scripts)
14. [Contact](#contact)

---

## Overview

This is the marketing website for FDE - AI Automation Agency. It explains the agency's services and process, shows example solutions, and helps visitors start a conversation.

The site includes an AI chat assistant that answers questions about the agency. It also includes Google login and a moderated comments box.

"FDE" stands for Forward Deployed Engineer. The agency learns how a client's business runs, builds a system that fits it, and supports it after launch.

## Features

- **Modern, fast pages.** Built with the Next.js App Router, React 19, and Tailwind CSS 4.
- **AI chat assistant.** Streams answers from approved website content only. Available on the home page and on a full-page assistant.
- **Moderated comments.** Visitors sign in with Google. New comments stay hidden until the owner approves them.
- **Contact form.** Opens a prefilled email or WhatsApp message. No server-side form storage.
- **ROI calculator.** Helps visitors estimate time saved by automation.
- **Projects browser.** Filterable list of example solutions and a demo project.
- **Animations.** Lottie animations load only when they scroll into view.
- **Team section.** Edited from one small file (`team.js`).
- **SEO basics.** Sitemap, robots file, Open Graph image, and page metadata.
- **Security headers.** Strict Content Security Policy and other protective headers.

## Tech Stack

| Area | Technology |
|------|------------|
| Framework | Next.js 16 (App Router) |
| UI | React 19, Tailwind CSS 4, Motion |
| Language | TypeScript 5 |
| AI | Vercel AI SDK (`ai`) with the Vercel AI Gateway |
| Auth and database | Supabase (Google login, Postgres with Row Level Security) |
| Animation | Lottie (`lottie-web`) |
| Hosting | Vercel |

Node.js **20.9 or newer** is required.

## Pages

| Route | Description |
|-------|-------------|
| `/` | Home |
| `/ceo-message` | Message from the founder |
| `/projects` | Example solutions and demo project |
| `/about` | About the founder |
| `/services` | Services in detail |
| `/ai-assistant` | Full-page AI assistant |
| `/contact` | Contact options |
| `/privacy` | Privacy policy |

## Project Structure

```
.
├── app/                  # Routes, layout, global styles, sitemap, robots
│   ├── api/chat/         # AI assistant API route
│   └── ...               # One folder per page
├── components/           # UI components (chat, comments, header, forms, etc.)
├── lib/
│   ├── content.ts        # All page text
│   ├── site.ts           # Contact details, navigation, links
│   ├── assistant.ts      # AI assistant instructions (built from the content)
│   ├── rateLimit.ts      # Chat message limits per visitor
│   ├── auth.ts           # Supabase Google login helpers
│   └── logos.ts          # Tool logos
├── public/               # Images and Lottie animations
├── supabase/
│   └── comments.sql      # Database table, rules, and triggers for comments
├── team.js               # Leadership team (names, titles, photos)
├── next.config.ts        # Security headers and caching
└── .env.example          # Example environment variables
```

## Getting Started

**Requirements:** Node.js 20.9 or newer and npm.

```bash
# 1. Install dependencies
npm install

# 2. Create your local environment file
cp .env.example .env.local

# 3. Start the development server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

The site runs without any keys. The AI assistant and Google login stay off until you add the variables below.

## Environment Variables

Add these to `.env.local` for local work, and to your Vercel project settings for production. Never commit real keys to GitHub.

| Variable | Required | Description |
|----------|----------|-------------|
| `AI_GATEWAY_API_KEY` | For local AI chat | Vercel AI Gateway key. On Vercel, the project can authenticate automatically. |
| `AI_MODEL` | Optional | Chat model in `provider/model` format. Default: `openai/gpt-4o-mini`. |
| `NEXT_PUBLIC_SUPABASE_URL` | For login and comments | Your Supabase project URL. |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | For login and comments | Your Supabase **anon (public)** key. Never use the service role key here. |

> **Note:** `.env.example` currently lists only the two AI variables. Add the two Supabase variables to it so new contributors can find them.

## Supabase Setup (Login and Comments)

1. Create a project at [supabase.com](https://supabase.com).
2. Open **SQL Editor**, paste the full contents of `supabase/comments.sql`, and run it. It is safe to run again.
3. Open **Authentication → Providers**, enable **Google**, and add your Google OAuth client ID and secret.
4. Add your site URL (for example `https://www.fdeautomation.com` and `http://localhost:3000`) to the allowed redirect URLs.
5. Copy the project URL and the anon key into your environment variables.

**How comments work:**

- Anyone can read comments that are approved.
- Only signed-in users can post. Name and photo come from the Google account, so no one can post under another name.
- Every new comment starts hidden (`approved = false`).
- The owner approves a comment in **Table Editor → comments** by setting `approved` to `true`.
- A user can delete only their own comments. No one can edit or approve comments from the website.
- Limits: 1,000 characters per comment and 5 comments per user per hour.

## AI Assistant

The assistant uses the Vercel AI SDK with the Vercel AI Gateway.

- It answers **only** from the facts in `lib/content.ts` and `lib/site.ts`. These facts are placed into the instructions in `lib/assistant.ts`.
- It does not invent prices, timelines, clients, or reviews.
- Two styles are available: a concise home-page style and a more formal full-page style.
- If the gateway is not available, the chat shows a polite message with the contact details.

To change what the assistant knows, edit the content files. Do not edit the instructions for facts.

## Security

- **Security headers.** `next.config.ts` sets a Content Security Policy, HSTS, `X-Frame-Options`, `X-Content-Type-Options`, a strict `Referrer-Policy`, and a `Permissions-Policy`. The microphone is allowed only on `/ai-assistant`.
- **Same-site chat API.** `/api/chat` refuses requests that do not come from the site itself.
- **Input limits.** Requests are limited to a small body size, a few recent messages, and a maximum message length.
- **Rate limiting.** Each visitor is limited to 6 messages per minute, 30 per hour, and 60 per day. These counters are stored in server memory, so they apply per server instance. For stronger protection on Vercel, also add a Vercel Firewall rate-limit rule for `/api/chat` and set a monthly spending limit on the AI Gateway.
- **Database rules.** Row Level Security is enabled on the `comments` table. A database trigger sets the user, name, avatar, and approval state, so the browser cannot fake them.
- **Public keys only.** The site uses only the Supabase anon key in the browser.
- **Content Security Policy note.** Scripts and styles currently allow `'unsafe-inline'`. Tightening this with nonces is a possible future improvement.

## Editing Content

| What to change | Where |
|----------------|-------|
| Page text, services, FAQ, projects | `lib/content.ts` |
| Contact details, navigation, links | `lib/site.ts` |
| Leadership team | `team.js` |
| Client reviews | `TESTIMONIALS` in `lib/content.ts` (nothing shows while the list is empty) |
| Example solutions | `PROJECTS.examples` in `lib/content.ts` |
| Animations | `public/lottie/` |

Replace the example solutions with real client projects when they are available.

## Deployment

The site is built for Vercel.

1. Push this project to a GitHub repository.
2. In Vercel, click **Add New → Project** and import the repository. Keep the default settings.
3. Add the environment variables from the table above.
4. Turn on **AI Gateway** for your Vercel team.
5. Click **Deploy**.
6. In **Settings → Domains**, add your domain and update the DNS records at your registrar as Vercel shows.

## Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start the development server |
| `npm run build` | Create a production build |
| `npm run start` | Run the production build |
| `npm run typecheck` | Check TypeScript types |

## Contact

**FDE - AI Automation Agency**
Founder and CEO: Kamran Aziz

- Website: [www.fdeautomation.com](https://www.fdeautomation.com)
- Email: contact@fdeautomation.com
- LinkedIn: [Kamran Aziz](https://www.linkedin.com/in/kamran-aziz-69b9651a0)

---

*All rights reserved. Add a license file before sharing the code publicly.*
