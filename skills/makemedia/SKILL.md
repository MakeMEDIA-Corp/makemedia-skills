---
name: makemedia
description: Core rules for using the makemedia connector. Use whenever makemedia tools are involved (company profiles, personas, interviews, content such as LinkedIn posts, any makemedia workflow), when the user uploads a transcript or document for makemedia to use, when the user asks which company or customer this project is for, or when they want to share a company with a colleague or change who has access. Keeps each customer's data in its own Claude project.
---

# makemedia

makemedia stores marketing work per **company** (usually a consultant's
customer). Each company has an access list, so you only see companies the
user is a member of. Users keep **one customer per Claude project**, and
this skill keeps that separation.

## 1. Find this project's company (do this first)

Look in this project's instructions and in your memory for a line like:

```
makemedia company_id: co_abc123 (Acme Corp)
```

- **Found:** use that company_id for every makemedia tool call in this
  project. Don't call `list_companies` to choose another one. If a tool says
  the company isn't found, the user has lost access: tell them, and suggest
  they ask the company's owner to add them again.
- **Not found:** ask the user which customer this project is for. Offer the
  companies from `list_companies`, or creating a new one with the
  company-profile skill. Then pin it (step 2).
- **Not in a project:** suggest creating a Claude project for this customer
  so its chats and memory stay separate from other customers. You can
  continue in this chat if they prefer.

## 2. Pin the company to the project

As soon as the company_id is known (chosen, or returned by
`save_company`):

1. **Save it to memory**, if you can save memories here, with exactly:
   `makemedia company_id: <company_id> (<company name>)`
2. **Ask the user to add it to the project instructions.** This is the
   reliable way, because memory can be turned off. Give them the exact line:

   > Open this project's **Instructions** and add:
   > `makemedia company_id: co_abc123 (Acme Corp). Use only this company for makemedia in this project.`

Don't pin a second company in the same project. If the user wants to work
on a different customer, tell them to open (or create) that customer's
project.

## 3. Keep customers separate

- Only pass the pinned company_id to makemedia tools in this project.
- In `list_sessions` results, ignore sessions for other company_ids.
- Never bring another customer's profiles, personas or answers into this
  project, even if the user has access to them.

## 4. Uploaded files (transcripts and documents)

When the user uploads a file for makemedia content, such as a podcast
transcript, save it as a **source** so workflows can use it. Content is
only as accurate as this copy, so it must be complete and word for word.

1. **Check first.** Call `list_sources` for the pinned company. If a source
   with the same filename or title exists, ask whether to use it rather than
   uploading again.
2. **Convert to Markdown, verbatim.** Keep every word as it appears in the
   file. Don't summarize, shorten, correct grammar, remove filler words or
   reword anything.
   - Put each speaker's turn in its own paragraph, starting with the name
     in bold: `**Jane Doe:** ...`. Keep timestamps if the file has them.
   - Subtitle files (.srt, .vtt): drop the cue numbers, and join
     consecutive cues by the same speaker into one paragraph, keeping the
     first timestamp.
   - Remove only page furniture: repeated headers and footers, page
     numbers.
   - If you can run code, use it to read and convert the file, so nothing
     is dropped by accident.
   - If you can't see the whole file (for example it was cut off), tell the
     user instead of saving part of it.
3. **Upload it.** `kind` is `transcript`, `document` or `notes`; `title` is
   the episode or document name; `filename` is the uploaded file's name.
   - Up to 50,000 characters: one `save_source` call with `complete: true`.
   - Longer: split it **between paragraphs** into parts of about 40,000
     characters. Send part 1 with `save_source` and `complete: false`, then
     parts 2, 3, … with `append_source`, using `complete: true` on the last.
   - After each call, check that `ends_with` matches the end of the part
     you sent. If a call fails, send the same part number again; that's
     safe.
4. **Confirm** to the user: the title, the word count, and that it's saved
   for this company. Then pass its `source_id` to the workflow.

Sources follow the company's access list. Delete one (`delete_source`) only
when the user asks, after confirming.

## 5. Sharing and access

Roles: **owner** (can also manage members) and **member** (can use the
company, its artifacts and workflows). Whoever creates a company is its
first owner, and a company always keeps at least one owner.

- **Who has access:** `list_company_members`.
- **Give a colleague access:** confirm the email address and role with the
  user, then `add_company_member`. Only owners can do this, and only for
  addresses in the user's organization.
- **Change a role:** `add_company_member` with the new role.
- **Remove access:** confirm with the user first, then
  `remove_company_member`. Members can remove themselves.
- If `save_company` says the company exists but the user has no
  access, tell them which owner to ask. Don't create a second company for
  the same website.

## 6. Checking the connection

If the user asks whether makemedia is working, or what version is
connected, call `get_version` and report the version, environment and the
signed-in account.
