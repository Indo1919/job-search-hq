# Job Search HQ

A private dashboard for my UI/UX job search. It lives at **https://indo1919.github.io/job-search-hq/**.

It keeps new LinkedIn listings, applications, outreach drafts and networking follow-ups in one place. It's styled to match [matiasindacochea.com](https://matiasindacochea.com/).

## What it does

- **Discover** — new-grad and entry-level UI/UX roles pulled from LinkedIn every two hours (NYC and remote).
- **Approve** — one click queues outreach: Claude finds about five people for the role (recruiter, likely hiring manager, designers on the team, an alum) and writes a draft for each. Email drafts land in Gmail. Nothing is ever sent automatically.
- **Pipeline** — Approved → Applied → Interviewing → Offer, with application numbers and the latest email matched from Gmail.
- **Outreach** — every draft in one place, with copy, Gmail draft links, sent tracking and follow-up dates.
- **Network** — contacts with a one-week-then-two-week follow-up cadence.
- **Templates** — recruiter, hiring manager, peer, alum, follow-up, thank-you and catch-up messages that fill in the role, job number and portfolio link.
- **Overview** — pipeline funnel, weekly activity, open-role mix, a 7-day follow-up strip and a "needs you" list.

## How it's built

| Piece | Where |
| --- | --- |
| Website (this repo) | Static `index.html` on GitHub Pages. No personal data lives here. |
| Data | `data/jobs.json`, `data/network.json`, `data/settings.json` in the **private** repo `Indo1919/job-search-hq-data`. |
| Access | A fine-grained GitHub token (Contents read/write on the data repo only), entered once per device and stored in that browser's local storage. Without it the site shows a lock screen. |
| Background jobs | Two Claude scheduled tasks on my Mac: a LinkedIn + inbox scan every 2 hours (8 AM–10 PM ET) and an hourly outreach run for approved roles. They read and write data through `window.jobhq` on this page in my own Chrome. |

## Automation bridge

When the page is open and connected, it exposes `window.jobhq`:

```js
await jobhq.ready                    // true when connected
await jobhq.summary()                // compact list of every role
await jobhq.job(id)                  // one role, in full
await jobhq.update('jobs', d => { d[id].status = 'applied'; return d; }, 'commit message')
await jobhq.log('Scan: 4 new listings')
```

`update` re-reads the latest file from GitHub, applies the change and writes it back with the file's `sha`. If someone else saved in between, it retries, so the site and the background jobs never overwrite each other.

## Updating the site

Edit `index.html`, commit to `main`, and GitHub Pages redeploys within a minute.
