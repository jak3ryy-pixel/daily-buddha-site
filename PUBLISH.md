# Putting the Daily Buddha site online

This folder holds the three public pages the app links to:

| Address | File |
|---|---|
| https://dailybuddha.amos.live/ | `index.html` (Help & Support) |
| https://dailybuddha.amos.live/privacy/ | `privacy/index.html` |
| https://dailybuddha.amos.live/terms/ | `terms/index.html` |

It is set up exactly like the Daily Barakah site (`Website-DailyBarakah/`, which is the repo
`jak3ryy-pixel/daily-barakah-site`, live at https://dailybarakah.amos.live/): a public GitHub
repo, served free by GitHub Pages, with one DNS record in Cloudflare. Follow the six steps
below in order. The whole thing takes about 15 minutes, plus a wait for the certificate.

Nothing here touches the app's own code, which stays in the private repo.

## Before you start

Look at the pages once on your Mac. In Terminal, run:

```bash
python3 -m http.server 8767 --directory "/Users/jakeryan/Desktop/Daily Blessing/Website-DailyBuddha"
```

then open http://localhost:8767 in Safari. Press Control‑C in Terminal when you are done.
If you change any wording, also change the "Effective" date at the top of the Privacy
Policy and Terms of Use pages.

## Step 1. Create a new public repo on GitHub

1. Go to https://github.com/new (sign in as **jak3ryy-pixel** if asked).
2. Repository name: `daily-buddha-site`
3. Description: `Support, privacy and terms pages for the Daily Buddha iPhone app`
4. Choose **Public**. (GitHub Pages on a free account needs a public repo. The pages
   are public anyway; there is nothing private in this folder.)
5. Leave every "Initialize this repository" box **unticked**. No README, no .gitignore,
   no license. The folder already has everything.
6. Click **Create repository**. Leave the page open; you will not need to copy anything
   from it, but it shows the repo exists.

Shortcut: the `gh` command line tool on this Mac is signed in as jak3ryy-pixel, so Steps 1
and 2 can be done in one go from Terminal instead:

```bash
cd "/Users/jakeryan/Desktop/Daily Blessing/Website-DailyBuddha" && git init -b main && git add -A && git commit -m "Support, privacy and terms pages for Daily Buddha" && gh repo create jak3ryy-pixel/daily-buddha-site --public --source=. --remote=origin --push --description "Support, privacy and terms pages for the Daily Buddha iPhone app"
```

If that works, skip to Step 3.

## Step 2. Push this folder to that repo

Open Terminal and paste these commands one at a time, pressing Return after each.

```bash
cd "/Users/jakeryan/Desktop/Daily Blessing/Website-DailyBuddha"
```

```bash
git init -b main
```

```bash
git add -A
```

```bash
git commit -m "Support, privacy and terms pages for Daily Buddha"
```

```bash
git remote add origin https://github.com/jak3ryy-pixel/daily-buddha-site.git
```

```bash
git push -u origin main
```

If the last command asks you to sign in, use the same GitHub login that worked for the
Daily Barakah site. When it finishes, refresh the GitHub page from Step 1: you should see
`index.html`, `style.css`, `privacy`, `terms`, `CNAME` and the two icon files.

From now on, to change the site: edit the files in this folder, then in Terminal run
`git add -A`, `git commit -m "what you changed"`, `git push`. The site updates within a
minute or two.

## Step 3. Turn on GitHub Pages

1. On the repo page (https://github.com/jak3ryy-pixel/daily-buddha-site), click
   **Settings** (the tab on the right, with the gear).
2. In the left column, click **Pages**.
3. Under **Build and deployment**, set **Source** to **Deploy from a branch**.
4. Under **Branch**, choose **main** and leave the folder as **/ (root)**. Click **Save**.
5. Wait a minute, then refresh. A box at the top says "Your site is live at
   https://jak3ryy-pixel.github.io/daily-buddha-site/". Do not worry if the address
   looks odd; the next two steps give it the real name.

## Step 4. Add the DNS record in Cloudflare

Do this **before** adding the custom domain in GitHub. For Daily Barakah, GitHub only
started requesting the certificate once the DNS record already existed (see Step 6).

1. Sign in at https://dash.cloudflare.com and open the **amos.live** domain.
2. In the left column, click **DNS**, then **Records**.
3. Click **Add record** and fill it in exactly like this:

   | Type | Name | Target | Proxy status | TTL |
   |---|---|---|---|---|
   | CNAME | `dailybuddha` | `jak3ryy-pixel.github.io` | **DNS only** (grey cloud) | Auto |

   The proxy switch must be **off** (the cloud icon grey, not orange). If it is orange,
   GitHub cannot issue the HTTPS certificate and the site will show a security warning.
4. Click **Save**.

Just type `dailybuddha` in the Name box, not the full address; Cloudflare adds
`.amos.live` itself. It sits next to the existing `dailyblessing` and `dailybarakah`
records, which look the same.

## Step 5. Add the custom domain in GitHub

Back in GitHub, **Settings → Pages**:

1. Under **Custom domain**, type `dailybuddha.amos.live` and click **Save**.
2. GitHub runs a DNS check. Because the Cloudflare record from Step 4 already exists, it
   should turn green within a few minutes. If it shows "DNS check in progress" for longer,
   wait and refresh; new records can take up to an hour to be seen everywhere.

(The folder already contains a `CNAME` file with this name. GitHub may make a small
commit to the repo when you save the domain. If it does, run `git pull` in this folder
the next time you edit the site, so your copy matches GitHub's.)

## Step 6. Turn on HTTPS and check

1. Still on **Settings → Pages**, once the DNS check is green, tick **Enforce HTTPS**.
   If the box is greyed out, GitHub is still getting the certificate. Wait 10 to 30
   minutes and refresh.
2. If it still says "certificate: none" after an hour, **remove the custom domain, click
   Save, add it back and Save again**. That restarts the certificate request. This is what
   made it work for Daily Barakah: GitHub had been asked for the domain before the DNS
   record existed and never retried on its own. Then run `git pull` in this folder, because
   GitHub commits the CNAME removal and re‑add.
3. Open all three on your iPhone and check the padlock shows:
   - https://dailybuddha.amos.live/
   - https://dailybuddha.amos.live/privacy/
   - https://dailybuddha.amos.live/terms/

## Afterwards

- These three addresses are already in the app
  (`Apps/DailyBuddha/Sources/Theme/DailyBuddhaTheme.swift`), on the paywall and in
  Settings. Nothing to change there.
- In App Store Connect, use `https://dailybuddha.amos.live/privacy/` as the Privacy
  Policy URL and `https://dailybuddha.amos.live/` as the Support URL, and link the Terms
  page in the app description (already in `Apps/DailyBuddha/AppStore/listing.md`).
- Tick off "Web pages" under *Before you ship* in `Apps/DailyBuddha/README.md`.
- The certificate renews by itself. The pages are dated November 6, 2026; update the date
  whenever the wording changes.
- The support page and privacy policy say every teaching is read by a pre‑recorded AI
  voice, bundled in the app. If the app ever ships with the device voice instead, reword
  those two spots.
