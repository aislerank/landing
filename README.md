# AisleRank Landing Page

Static, zero-build, Vercel-ready. Everything lives in `index.html`.

## Deploy (10 min)
1. `npm i -g vercel` (or use vercel.com UI: New Project → drag this folder)
2. From this folder: `vercel --prod`
3. Point `aislerank.com` at Vercel (Domains tab → follow DNS instructions)

## Wire-up checklist (edit the CFG block at top of index.html)
- [ ] `CALENDLY_URL` — your 15-min event link
- [ ] `POSTHOG_KEY` — PostHog project API key (events no-op to console until set)
- [ ] `SUPABASE_URL` + `SUPABASE_ANON` — optional lead storage (run SQL below first)
- [ ] Footer: real mailing address (CAN-SPAM) + hello@ email
- [ ] `tos.html` / `privacy.html` — replace placeholders via Termly (Tuesday task)
- [ ] Week 3: swap Loom placeholder (`#loomSlot`) for real embed

## PostHog funnel (already wired)
`landing_visit` → `url_submitted` (store_url) → `pricing_click` (tier, price) → `founding_access_requested` (email, tier) → `demo_booked` (fires on Calendly confirmation postMessage). Extras: `fake_door_dismissed`, `faq_open`, `nav_book_click`.

## Supabase `leads` table (run in SQL editor before enabling lead store)
```sql
create table if not exists leads (
  id uuid primary key default gen_random_uuid(),
  created_at timestamptz default now(),
  type text not null,            -- audit_request | founding_access
  email text,
  store_url text,
  tier text,
  source text
);
alter table leads enable row level security;
create policy "anon can insert leads" on leads for insert to anon with check (true);
-- no select policy for anon: writes only from the browser, reads via service key only
```

## Stripe (test mode)
Fake-door pricing intentionally has no checkout. Stripe goes live on the **report CTA in Week 2** (per plan §7 / checklist Wk 2 Thursday), not the landing page.
