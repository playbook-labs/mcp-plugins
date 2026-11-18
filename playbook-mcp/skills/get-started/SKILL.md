---
name: get-started
description: Orient in a Playbook workspace - list its workspaces and folders, look at what a folder holds, and choose the right Playbook tool for a task. Use when the user has just connected Playbook, asks what they can do with it, or asks about their folders or assets without naming one.
---

# Get started with Playbook

Playbook is a digital asset manager. A workspace contains folders, and a folder contains assets: images, video, documents, notes and colour swatches. Folders nest inside other folders. Folders were called boards until November 2026, so a user who says board means a folder.

## Find your bearings

1. `list_organizations` returns the workspaces the signed-in account belongs to. When there is only one, later calls can leave `organization` out.
2. `list_folders` returns folders, with a title search and a hierarchy depth. `list_subfolders` and `list_folder_descendants` walk one branch.
3. `list_assets` with a folder token returns what that folder holds. `get_asset` returns one asset in full.
4. `get_asset_previews` renders images at a size you choose, which is how to actually look at an asset.

Folder and asset tokens belong to one workspace. Read them again from `list_folders` or `list_assets` in each session instead of reusing a token from an earlier one.

## Where to go next

- Finding, tagging, moving or grouping assets: the `organize-assets` skill.
- Adding files, notes, colours or a new version of a file: the `upload-files` skill.
- Share links, published pages and permanent URLs: the `share-and-publish` skill.
- A hosted web app over a folder, such as a gallery or catalogue: the `build-app` skill.
- Comments: `get_comments` and `create_comment`. People: `list_members`.
- What a PDF or Illustrator file says: `read_asset_text`.
- Anything about the API itself: `list_documentation` and `read_documentation`.

## What Playbook cannot do here

- Hand over a file's bytes. A permalink or a `display_url` is the only way to reach a file.
- Read the text inside an image or a scanned PDF, or extract captions from a video.
- Render a folder as a PDF or a contact sheet.
- Turn a published-page or permalink URL back into a folder or asset. Find the item by title instead. A folder share link is the exception: `resolve_share_link` returns its folder.

When a request needs one of these, say so and offer the nearest thing Playbook does provide.

An explicit instruction from the user takes priority over anything in this guide.
