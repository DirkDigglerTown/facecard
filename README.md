# 💳 Facecard - Never Declines

A TikTok-inspired social voting platform with a luxury gold aesthetic. Upload your **facecard** (profile picture + optional X handle), let people upvote/downvote, and compete for a spot in the **Hall of Fame (Top 10)**.

Hosted on **GitHub Pages**, powered by **Supabase** for database, storage, and edge functions.

---

## ✨ Features

- **Submit Your Facecard** - Upload image file or paste URL
- **Link Social Profiles** - Add X (Twitter) handle and verification tweet
- **Community Voting** - Upvote/downvote system
- **Vote Limiting** - One vote per person per submission (via edge function IP tracking)
- **Hall of Fame** - Top 10 leaderboard with gold/silver/bronze badges
- **Live Search** - Find users by display name or handle
- **Scrolling Marquee** - Current top 10 displayed on submit page
- **Luxury Design** - Gold-themed prestige aesthetic

---

## 🚀 Setup

### 1. Supabase Project
1. Create a free [Supabase](https://supabase.com) project
2. Copy your **Project URL** and **Anon Key** from Settings → API

### 2. Storage Bucket
1. Go to Storage in Supabase dashboard
2. Create a new bucket named `facecard`
3. Set it as **Public**
4. Add RLS policies:
   - **Allow public uploads**: INSERT permission for `anon` role
   - **Allow public viewing**: SELECT permission for `anon` role

### 3. Database Schema
Run this SQL in Supabase SQL editor:

```sql
-- Tables
CREATE TABLE submissions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  display_name TEXT NOT NULL,
  image_url TEXT NOT NULL,
  x_handle TEXT,
  verify_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE votes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  submission_id UUID REFERENCES submissions(id),
  direction INT NOT NULL CHECK (direction IN (1, -1)),
  voter_hash TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(submission_id, voter_hash)
);

-- View for scores
CREATE VIEW v_submissions_with_score AS
SELECT 
  s.*,
  COALESCE(SUM(v.direction), 0) as score
FROM submissions s
LEFT JOIN votes v ON s.id = v.submission_id
GROUP BY s.id;
```

### 4. Edge Function
Deploy the `cast-vote` edge function for vote limiting:

```bash
supabase functions deploy cast-vote --project-ref <your-project-ref>
```

### 5. Configure index.html
Update these constants in your `index.html`:

```javascript
const SUPABASE_URL = "https://YOUR-PROJECT.supabase.co";
const SUPABASE_ANON_KEY = "YOUR-ANON-KEY";
const EDGE_FUNCTION_URL = "https://YOUR-PROJECT.supabase.co/functions/v1/cast-vote";
```

### 6. Add Assets
Place these files in your repo root:
- `index.html` - The main application file
- `logo.jpg` - Your golden facecard logo image

### 7. Deploy to GitHub Pages
1. Create a new public GitHub repository
2. Upload `index.html` and `logo.jpg` to repo root
3. Go to Settings → Pages
4. Select source: "Deploy from a branch" → main branch
5. Your site will be live at `https://USERNAME.github.io/REPO-NAME`

---

## 🎨 Design Features

- **Gold Theme** - Luxury prestige aesthetic with gold accents
- **Animated Backgrounds** - Subtle particle effects and gradients
- **Card Rankings** - Gold/silver/bronze badges for top 3
- **Hover Effects** - Cards lift and glow on interaction
- **Responsive Layout** - Works on mobile and desktop

---

## 🔒 Security

- **Vote Protection** - Edge function prevents duplicate voting
- **Storage Policies** - RLS policies control file upload permissions
- **No Direct Writes** - Votes only through edge function
- **IP Hashing** - User privacy protected

---

## 📱 Social Preview

The golden facecard logo appears when sharing on:
- Twitter/X
- Telegram
- WhatsApp
- Facebook/LinkedIn

Update meta tags in `index.html` with your repo's logo URL.

---

## 📄 License

MIT License - Free to fork and customize.
