# 💳 Facecard

A TikTok-inspired social voting site. Upload your **facecard** (profile picture + optional X handle), let people upvote/downvote, and compete for a spot in the **Hall of Fame (Top 10)**.

Hosted on **GitHub Pages**, powered by **Supabase** for database, storage, and edge functions.

---

## ✨ Features
- Submit a profile picture (upload or URL)
- Link your X (Twitter) handle and optional verification tweet
- Browse and vote (▲ up / ▼ down)
- **One vote per person per submission** (via salted IP+UA hash)
- Hall of Fame leaderboard (Top 10 all-time by score)
- Search by display name or handle
- Responsive, modern UI built with vanilla HTML/CSS + Supabase JS

---

## 🚀 Setup

### 1. Supabase Project
1. Create a free Supabase project.
2. Copy your **Project URL** and **Anon Key**.

### 2. Storage Bucket
- Create a public bucket named `facecard` for profile images.

### 3. Database Schema
Run the provided SQL (`schema.sql`) in the Supabase SQL editor:
```sql
-- create tables: submissions, votes
-- create view: v_submissions_with_score
-- enable pg_trgm extension for search
```

### 4. Edge Function
Deploy the `cast-vote` Edge Function. This enforces one-vote-per-person and basic rate-limiting.

Secrets required:
- `HASH_SALT` → long random string (used to hash IPs)

Example (CLI):
```bash
supabase secrets set HASH_SALT="<your-random-string>" --project-ref <project-ref>
```

### 5. Configure index.html
Update these constants near the top of `index.html`:
```js
const SUPABASE_URL = "https://YOUR-PROJECT.supabase.co";
const SUPABASE_ANON_KEY = "YOUR-ANON-KEY";
const EDGE_FUNCTION_URL = "https://YOUR-PROJECT.functions.supabase.co/cast-vote";
```

### 6. Deploy to GitHub Pages
1. Create a new public repo.
2. Place `index.html` in the repo root.
3. Push and enable GitHub Pages.

Your Facecard site is live! 🎉

---

## 🔒 Security Notes
- Votes are written only via the edge function (using service-role key). Clients cannot directly insert into the `votes` table.
- `HASH_SALT` ensures hashed IPs cannot be reversed.
- RLS (Row Level Security) policies restrict client access to read-only on votes.
- Moderation: submissions have an `is_approved` flag (set to true/false in DB).

---

## 🖌️ Customization
- **Branding:** Adjust colors in CSS `:root {}` section.
- **Assets:** Swap background/hero images. (Use MidJourney prompts to generate stylish assets.)
- **Moderation:** Add an admin panel or manually flip `is_approved` to hide inappropriate submissions.

---

## 📸 Screenshots
_Add your own screenshots of the live site here._

---

## 📄 License
MIT License. Free to remix, fork, and build upon.
