# VibeCheck

Real-time crowd sentiment with Supabase (database + Realtime + Edge Function) and Claude.

## Structure

- `index.html` — single-file frontend (Tailwind + supabase-js via CDN, no build step)
- `supabase/schema.sql` — `feedback` table, RLS policies, Realtime
- `supabase/functions/summarize/index.ts` — Edge Function that calls Claude

## Setup

1. **Create a Supabase project** at https://supabase.com.
2. **Run `supabase/schema.sql`** in Dashboard > SQL Editor. It creates the table, row-level security, and enables Realtime.
3. **Deploy the Edge Function** (needs the [Supabase CLI](https://supabase.com/docs/guides/cli)):
   ```bash
   supabase login
   supabase link --project-ref <your-project-ref>
   supabase secrets set ANTHROPIC_API_KEY=sk-ant-...
   supabase functions deploy summarize
   ```
4. **Fill in `CONFIG`** at the top of the script in `index.html` with your Project URL and anon key (Project Settings > API).
5. **Open `index.html`** in two tabs and post vibes. Or deploy to Vercel/Netlify/GitHub Pages.

## Changes from the original guide

- The Anthropic key lives in an Edge Function secret instead of the browser, where anyone could copy it.
- RLS is enabled (the original table had none) with read + insert only.
- Fixed `const supabase = supabase.createClient(...)`, which throws a ReferenceError.
- Feed is rendered with `textContent` rather than `innerHTML`, preventing XSS from user messages.
- Summaries are debounced so a burst of messages makes one Claude call.
- Updated the model to `claude-sonnet-5-5`; the summary now includes an action item.
