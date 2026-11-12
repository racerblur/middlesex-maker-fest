# Middlesex County Maker Fest — Stakeholder Network

A deployable, responsive GitHub Pages web application backed by Supabase. Includes 39 starting contacts, 39 relationships, interactive network, search, filters, public read-only access, and authenticated organizer editing.

## 1. Create the database

1. Create a free project at https://supabase.com/dashboard.
2. Open **SQL Editor** and run `schema.sql`, then `seed.sql`.
3. In **Authentication → Providers**, enable email/password authentication. Enable email confirmation for security.
4. In **Authentication → URL Configuration**, set your published GitHub Pages URL as the site URL and add it as an allowed redirect URL.
5. In **Project Settings → API**, copy the **Project URL** and **publishable/anon key**. **Never put a service_role or secret key in frontend code.**

## 2. Configure the website

1. Copy `config.example.js` to `config.js`.
2. Replace the two placeholders with your project URL and publishable/anon key. This key is intentionally public; security is enforced through Supabase Row Level Security (RLS).
3. Keep `config.js` in the published site. All other files except `seed.json`, `schema.sql`, `seed.sql`, and `README.md` are required for the site. (These extra files are not used by the browser.)

## 3. Deploy to GitHub Pages

1. Create a GitHub repository (e.g., `middlesex-maker-fest`).
2. Upload `index.html`, `style.css`, `app.js`, and `config.js` to the repository root.
3. Open **Settings → Pages**. Under **Build and deployment**, choose **Deploy from a branch**, `main`, `/ (root)`, and Save.
4. Visit `https://YOUR-USERNAME.github.io/middlesex-maker-fest/` once deployment completes.

**Privacy warning:** Standard GitHub Pages sites and the bundled data are public. The `people` and `relationships` tables are publicly readable. Only publish names, relationships, status and contributions that you're comfortable sharing. Keep private discussions in `private_notes`, which is protected by RLS. Avoid putting phone numbers, personal emails, or sensitive notes in public fields.

## 4. Approve organizer accounts

1. On the published website, click **Organizer sign in → Create account**. Confirm your email if prompted.
2. In Supabase Dashboard → Authentication → Users, find the newly created user's **UUID**.
3. In SQL Editor run:

```sql
insert into public.organizers (user_id, role)
values ('PASTE-YOUR-USER-UUID', 'admin');
```

4. Sign out and back in (or refresh). The **Add person** and **Add relationship** buttons appear for approved organizers.
5. For Rick or another organizer, have them sign up and confirm their email. Add their UUID using `role = 'editor'`.
6. To revoke editing rights, delete their row from `public.organizers`. User accounts remain but become read-only.

**Important:** There is deliberately no public self-service promotion to editor. Only a database administrator can approve editors. The browser never receives admin secrets.

## What it does

- Visitors can explore the chart and browse/search/filter contacts without an account.
- Approved editors can add/edit/delete contacts and add/remove relationships.
- Organizers can add private notes; only approved organizers can read them.
- Supabase is the source of truth, so changes are visible to all users on refresh.
- Established relationships are shown with solid lines; proposed connections use dashed lines.
- Donna Anonsen is identified as the first Middlesex College connection.

## Limitations and future improvements

- The app requires an internet connection to load the Supabase client from a CDN.
- The network layout is recomputed after edits, and node dragging is not persisted.
- There is no edit-history audit log, email invitation workflow, automatic realtime synchronization, or row-level per-person ownership. Add these before relying on the app for complex workflows.
- Any approved editor can delete public contacts and relationships; grant access only to trusted collaborators.
- Private notes are visible to **all** approved organizers, not just their authors. Notes can be appended but not edited in the UI.
- The initial 39-contact dataset contains research prospects and unverified relationships; don't interpret these as confirmed commitments.
- Supabase free projects have limits and may pause after inactivity. Check current plan terms.

## Test checklist

- [ ] Unauthenticated visitor can see people and network but not edit controls.
- [ ] Unapproved signed-in user remains read-only.
- [ ] Approved editor can add a contact and a relationship.
- [ ] Visitor can see the new public contact after refresh.
- [ ] Visitor cannot read `private_notes` via Supabase API.
- [ ] Revoked editor loses edit privileges.
