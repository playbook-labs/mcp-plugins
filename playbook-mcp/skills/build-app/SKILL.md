---
name: build-app
description: Build and host a small web app over one Playbook board - a gallery, portfolio, catalogue, lookbook or viewer - with deploy_app, and update, list or delete apps already hosted. Use when the user wants a web page, site or app that shows their Playbook assets, or a link to one they can share.
---

# Build an app on a Playbook board

Playbook hosts small static web apps that show one board and its sub-boards at their own address. `deploy_app` is the only way to host one. Never put an HTML file on a board and share it through a permalink or a share link: those serve files, not pages, and the page will not run.

## Before writing any code

1. Ask the user three things, unless they already said: **who opens the app and what they do there** (look, upload, approve), **which board** it shows, and **who may open it** (below). Whoever gets in sees the board and all its sub-boards, unapproved assets and uploader names included.
2. `read_documentation` with topic `hosted_apps`, then the recipe topic closest to the answer: `hosted_apps_recipe_gallery` to show a board, `hosted_apps_recipe_upload_brief` for one person sending files against a brief, `hosted_apps_recipe_review` for the board's people approving work; for visitors without a Playbook account (guests, below), `hosted_apps_recipe_client_approval` for a client approving work with a PIN, `hosted_apps_recipe_garage_sale` for claiming items first come first served, `hosted_apps_recipe_workshop` for RSVPs, votes and other answers per item. The guide is the reference for the files, the data calls and the fields of an asset; the recipe is a complete, designed app to copy and change. Build from them, not from memory of the REST API: an app reads through its own relative `playbook/api/...` address with no key.
3. `list_apps` to see the workspace's apps. Deploying to a slug the workspace already uses replaces that app's files, and the workspace's plan caps how many apps it may have. If the user's board already has an app, ask whether to replace it or add another.

## Build, look, fix, then show

1. **Start from the recipe's files** and change what the request needs. Keep what its "Keep" list names: relative URLs, retries, showing an answer instead of reading the board again, the theme tokens.
2. **Keep content that changes on the board.** Put a brief, captions, prices and names in the board's description, asset titles and fields, and have the page read them; tell the user where to edit them.
3. **Make it the user's own.** Give the app the user's name, words, colours and type. When reusing an app built for someone else, change all of them: only the code that reads the board carries over.
4. **Deploy, then read `warnings`** in the answer. Each names a likely mistake in the files; fix it and deploy again.
5. **Look at it before the user does.** Call `preview_app` for screenshots of the live app at 390 px and 1280 px; it opens password and members apps too, as a visitor who changes nothing. If you can open a browser, also check light and dark and each state: loading, empty, an error, done. Without either, go through the recipe's "Check it" list against your files. Fix what looks off and deploy again.
6. **Then give the user the link**, with what they can change on the board without you.

## Who may open the app

`deploy_app` takes `access`:

- `members`, the default for a new app: people who can view the board in Playbook, after they sign in. Let someone in with `invite_to_board`, putting the app's address in `message`: they get an email with a sign-in link and need no password.
- `password`: visitors who give the `password` you pass with it, at least 8 characters, on plans with share-link passwords.
- `public`: anyone with the address. Only when the user asks for it.

The board's own share settings do not protect the app; only its watermark and downloads apply to it (below). A redeploy without `access` keeps the app's access; the user can also change it under Developer in Playbook's settings.

## Uploads from visitors

An app can take files from its visitors into its own board, through `playbook/upload` as the `hosted_apps` topic shows. In an app that is not public, a signed-in visitor who may upload to the board can always upload. Anyone else can upload only while the board's share link allows uploads ("Allow uploads" or "Enable upload-only page" in the board's Share tab, a Pro feature). `share_board` with `enable_uploads: true` switches it on, but it also lets anyone with the board's share link upload, so ask the user first. Leave the link without a password, an email form or a past expiry: any of them refuses every upload from the app. `share_board` clears a password (`password: ""`) and an expiry (`expiration_date: ""`); an email form only the user can switch off, in the Share tab. Tell them that the app shows every visitor's files to every visitor.

## Watermark and downloads

An app can read what the PDFs and Illustrator files on its board say (`playbook/api/assets/{token}/text`): a dashboard of figures pulled from decks, a contract tracker, a search over documents. The `hosted_apps` topic has the call, what each `status` means and how to stay within the read budget. Every visitor can read that text, whatever the share link's download and watermark settings.

An app follows the board's share link. When the link has a watermark, every image the app reads carries it, and the answers hold no clean address. When the link is on and allows downloads, visitors can download the original files through `playbook/download`, typing the link's password if it has one, like a PIN; the `hosted_apps` topic shows the call. For a gallery that shows watermarked previews and hands out originals behind a PIN, set both with `share_board` (`watermark_text`, `disable_downloads: false`, `password`), and ask the user first: the same settings apply to the share page itself. Turning a watermark on in the Share tab switches downloads off; switching downloads back on there, or `share_board` with `disable_downloads: false`, keeps both. Video is not watermarked.

For a PIN per event, share each event's sub-board with its own `password` and `disable_downloads: false`, and keep the app board's link on with `disable_downloads: true`: a sub-board link with a password sets the PIN and download terms for the files under it, the app board's link covers the rest, and switching the app board's link off stops every download. With downloads on at the app board, its password would download every event from its share page. Ask for one event's files per `playbook/download` request: files that follow different links, the app board's included, answer `422 mixed_share_links`.

## Changes from signed-in visitors

In a `members` app, the page can also change the board's assets as the signed-in visitor, with that visitor's permissions in Playbook: title, description, status, custom fields and tags, approval, and comments. The `hosted_apps` topic lists the calls. A tracker, a review queue or an approval board fits here. Approval itself is for workspace owners and admins only: for a client's decision, use a status or a custom field, which a visitor who may edit the board can set. Invite such a client with `invite_to_board` and `role: "edit"`; edit is full control of the board in Playbook (deleting assets, inviting others, publishing), so tell the user before granting it and keep the app on a board that holds only the work under review. Statuses and option fields take existing options only, and a multi-select field takes a list of them. The page can read the fields and their options itself (`playbook/api/custom_fields`, see the topic), or you can build them in from `list_custom_fields`. It can also show an asset's comments as the visitor sees them, with who wrote each one and whether it is internal (`playbook/api/assets/{token}/comments`), so a client review page can show feedback next to the work. Mark internal comments, as Playbook does. Read them when a visitor opens an asset, and use `comment_count` from the listing for badges: reading every asset's comments at once runs into the visitor's rate limit. Public and password apps cannot read comments; if the user wants feedback on the page, the app has to be `members`.

## Guests: people without a Playbook account

A public or password app can let visitors without a Playbook account act on the board as **guests**: a buyer reserving an item at a garage sale, a client approving a design or asking for changes, a workshop attendee saying they'll come. A `playbook.json` file in the app declares how visitors become guests and the only things they may do; the `hosted_apps` topic has the format, the guest calls and what it cannot express. Read it before writing one.

- **Access.** Deploy with `access: "public"` or `"password"`. A new app without `access` is `members`, and its `playbook.json` is refused; an app with guest actions can't be switched to `members` either.

- **How guests join.** `"signup": "open"`: a visitor gives their name (and an email or phone, if the actions need it). `"signup": "pin"`: the user adds each guest with `create_app_guest` and gets a personal PIN to hand them, shown once; the guest enters it on the app's password page or in the app. Use `pin` for a client approval in a password app, `open` for a public sale or an event.
- **What guests do.** A state action writes the values it declares into one item's Status, select or typed fields, under a condition: of two guests claiming one item, one wins. With `"signup": "open"`, each state action that undoes nothing needs `"limit": { "active_per_guest": 1 to 50 }`. A record action keeps an answer instead (a choice from a list and/or a note): an RSVP, a vote, "I'm interested", with an optional cap per item. `undoes` lets a guest take back their own action.
- **Contacts.** A guest's email and phone may only go into fields made with `create_custom_field` and `members_only: true`: such values never reach the app, a share link or other guests, only people signed in to Playbook who can see the item, workspace members and anyone invited to the board. Create those fields before deploying, and don't invite one client to a board holding other clients' contacts. Fields are shared by the whole workspace: check `list_custom_fields` first, since `create_custom_field` on an existing select replaces its options. Ask for every contact the actions need on the sign-up form: sign-up doesn't check them, and signing up again makes a new guest. Inputs go into text fields, which not every workspace has yet: where `create_custom_field` refuses `field_type: "text"`, build with `set` and record actions only. Show guest text in the page with `textContent`, never as HTML.
- **For the user.** `list_app_guests`, `list_app_answers` and `remove_app_guest` (with `undo`, which withdraws the guest's answers and runs the undo actions for their changes; a change no action undoes stays) are the owner's view; the web app has the same under Developer → Custom apps → Guests, and can email the user when an action has `"notify": true`. Open sign-up has no captcha: tell the user that someone could sign up many times, and that they can remove guests.

A `members` app takes no guests: its visitors are the board's own people and change the board as themselves (above).

## What an app cannot do

An app cannot delete, move, copy or group assets, create asset versions, change boards or sharing, create custom fields or their options, or reach any board but its own. Guests change only what the app's `playbook.json` declares, one item at a time; otherwise public and password apps change nothing but uploads. When the user wants an app that does any of that, say that hosted apps cannot do this yet and ask how to proceed. Do not host it some other way.

## Deploy

`deploy_app` takes the board token, a `slug`, a `name`, `access` when it is not `members`, and `files`: every file's text keyed by path, `index.html` required. Each deploy replaces the whole file set and is kept as a numbered version.

Give the user the `url` from the response. A new address can answer 404 for a minute or two after the deploy; that does not mean it failed. Open it again after a short wait before reporting a problem.

## Change an app that is already live

Playbook keeps the files of an app's last 50 versions, so work from them rather than from memory:

1. `get_app_source` lists the live version's files; with `path`, it returns a file's text (follow `next_offset` for a long one).
2. `edit_app_source` with the app's live `head_version_number` (from `list_apps` or `list_app_versions`) as `expected_version`, the files to replace in `files` and any to remove in `delete_files`, with paths exactly as `get_app_source` lists them: a path the app does not have is ignored silently. The rest stays as it was. If the answer is `stale_app`, someone changed the app since you read it: read it again and redo the change.
3. When a change goes wrong, `list_app_versions` shows the history and `revert_app` puts an earlier version back live.

Each deploy, edit or rollback adds a version, and past 50 the oldest is dropped. After it, the app reads the board as you; if someone else deployed it last, signed-in visitors have to reload before their next change.

An app not deployed since Playbook began keeping versions has no stored files (`source_unavailable`): rebuild it and deploy it once with `deploy_app`.

`delete_app` takes an app offline, frees its slug and deletes its versions.

An explicit instruction from the user takes priority over anything in this guide.
