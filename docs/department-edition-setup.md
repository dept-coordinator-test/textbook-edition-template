# Setting up your department's edition

This guide takes you from zero to a live, free website for your department's
version of the textbook. No coding is involved — you will copy a template,
paste in your chapters, change four settings, and click through one hosting
setup. Budget about an hour the first time.

**What you need before starting:**

- A GitHub account (free — github.com/signup). GitHub is where your edition's
  files live.
- A Cloudflare account (free — dash.cloudflare.com/sign-up). Cloudflare is
  what puts the site on the internet.
- Your edition's chapters as markdown files (usually your fork of the
  canonical textbook — see `for-course-coordinators.md` for how to make one).

---

## Step 1 — Create your own copy of the template

1. Open the template repository:
   `https://github.com/textbookproject2026-alt/textbook-edition-template`
2. Click the green **Use this template** button (top right), then
   **Create a new repository**. [SCREENSHOT: Use this template button]
3. Give it a name like `textbook-biology-edition`. Leave it set to **Public**
   (the licence requires the content to stay open, and public repos get free
   hosting). Click **Create repository**.

You now have your own copy. Note its address — it will look like
`github.com/YOUR-NAME/textbook-biology-edition`. You'll need the
`YOUR-NAME/textbook-biology-edition` part in Step 3.

## Step 2 — Add your edition's pages

All the readable content lives in the folder called `content`. Anything you
put there becomes a page on your site; `content/index.md` is the front page.

The simplest way to add files in the browser:

1. In your repository, click the `content` folder.
2. Click **Add file → Upload files**. [SCREENSHOT: Upload files menu]
3. Drag in your chapters (markdown files and any images), then click
   **Commit changes** at the bottom.

If you're comfortable with GitHub Desktop, that works too — anything that gets
files into `content/` is fine. Chapter links, images, footnotes and math all
work the same as in the canonical textbook.

## Step 3 — Set your edition's four values

There is exactly one settings file: `quartz.config.yaml`, at the top level of
your repository. Open it, click the pencil icon (✏️) to edit, and change the
four lines marked `← EDIT`: [SCREENSHOT: editing quartz.config.yaml]

- **3a. `pageTitle`** — the name shown at the top of every page, e.g.
  `"Biology Edition — <Textbook Title>"`.
- **3b. `baseUrl`** — your site's web address once it's live, without
  `https://` and without a trailing slash. If you don't have a custom address
  yet, you can come back to this after Step 4 and paste in the
  `something.pages.dev` address Cloudflare gives you.
- **3c. `plausibleScriptSrc`** — your edition's own analytics script address.
  Someone with access to the project's Plausible account adds your site there
  (Sites → Add website), then copies the script address from
  **Site settings → Site installation**. It looks like
  `https://plausible.io/js/pa-XXXXXXXXXX.js`. Paste it between the quotes.
  If you skip this, everything still works — you just won't have visitor
  statistics until you add it.
- **3d. `repo`** (in the *edit-on-github* section near the bottom) — the
  `YOUR-NAME/repository-name` part of your repository's address from Step 1.
  This makes the "Edit on GitHub" link on each page point at *your* files.

Click **Commit changes** when done.

**About `hypothesisGroupId`:** leave it as `""`. Readers can highlight and
annotate pages from day one using the sidebar on the right edge. Right now
those annotations are on Hypothes.is's public layer; department-private
annotation groups are switched on centrally later — you'll receive a group ID
and a one-line instruction when that happens. Nothing for you to do today.

## Step 4 — Put it online (Cloudflare Pages)

1. Log in at `dash.cloudflare.com`.
2. Go to **Compute (Workers) → Workers & Pages → Create application →
   Pages → Connect to Git**. [SCREENSHOT: Create application page]
3. Authorise Cloudflare to see your GitHub account and pick the repository
   you created in Step 1.
4. In **Set up builds and deployments**, enter exactly:

   | Setting                | Value                                                                       |
   | ---------------------- | --------------------------------------------------------------------------- |
   | Production branch      | `main`                                                                       |
   | Framework preset       | `None`                                                                       |
   | Build command          | `git fetch --unshallow && npx quartz plugin install && npx quartz build`     |
   | Build output directory | `public`                                                                     |

5. Open **Environment variables** on the same screen and add one:
   name `NODE_VERSION`, value `22`. [SCREENSHOT: build settings filled in]
6. Click **Save and deploy**. The first build takes a few minutes; when it
   finishes you get an address like `textbook-biology-edition.pages.dev`.
   That's your live site.

From now on, every time you change a file on GitHub, the site rebuilds and
updates itself within a couple of minutes — you never repeat this step.

The `git fetch --unshallow` part of the build command looks odd but matters:
it lets the site show correct "last updated" dates on pages. Don't trim it.

If you later get a proper address (e.g. `biology.university.edu`), add it
under the project's **Custom domains** tab in Cloudflare, and update
`baseUrl` (Step 3b) to match.

## Step 5 — Check five things

Open your live site and confirm:

1. The front page renders, links are **purple**, and the overall look matches
   the canonical textbook (same fonts, same airy spacing). If the reading
   column looks noticeably wider than the canonical site's, tell the
   maintainer — it's a one-line style fix on their side.
2. Clicking between chapters works, and the left sidebar shows your chapters.
3. **Edit on GitHub** under a page title opens that exact file in *your*
   repository.
4. The annotation sidebar tab appears at the right edge; highlighting a
   sentence offers "Annotate".
5. If you set up analytics (3c): visit a few pages, then check the Plausible
   dashboard — your visit should appear within a minute or two.

That's it. Your edition is live.

---

## Why pages fully reload

You may notice that clicking from one chapter to another reloads the whole
page, rather than swapping the text in instantly. That is deliberate, and it
is set centrally in `quartz.config.yaml` (`enableSPA: false`).

The instant-navigation mode is incompatible with the annotation sidebar: it
tears the Hypothes.is panel out of the page on every click, and the panel
cannot be revived afterwards. Editions accept slightly slower navigation in
exchange for annotation that reliably works — which is also how the canonical
Obsidian Publish site behaves. Please don't switch it back on.

---

## Keeping your edition up to date

When the canonical textbook is revised (once a year), you pull those changes
into your copy — see `for-course-coordinators.md` and `sync-upstream.sh`.
Updates to how the site *works* (as opposed to what it says) are shipped
centrally through the template's building blocks; your site picks them up on
its next rebuild without you doing anything.

## If something goes wrong

The most common issue is a typo in `quartz.config.yaml` — the build log in
Cloudflare (Deployments → View build) will say which line. Compare against
the template's original file, or contact the project maintainer with a link
to your repository and a copy of the build log.
