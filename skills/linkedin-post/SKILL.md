---
name: linkedin-post
description: Write a stand-alone LinkedIn post for a saved company with makemedia's LinkedIn workflow, optionally from an uploaded podcast transcript or other document and aimed at a saved persona. Use when the user asks for a LinkedIn post, or wants to turn a podcast episode, transcript or document into a LinkedIn post, or to revise one. Requires the makemedia connector and a saved company profile.
---

# LinkedIn post

Writes a stand-alone LinkedIn post. The writing runs on the makemedia
server; your job is to collect the inputs, relay any questions, and
present the post.

## Steps

1. **Use the project's company.** Use the `makemedia company_id` pinned in
   the project instructions or memory (see the makemedia skill). If none is
   pinned, ask which customer this project is for and pin it. If
   `get_company` returns no `profile`, run the
   company-profile skill first.

2. **Sources (optional).** If the user uploaded a transcript or document,
   save it as a source as the makemedia skill describes ("Uploaded files"),
   and use its `source_id`. If they refer to one they uploaded before,
   find it with `list_sources`. A post can also be written without a source.

3. **Collect the brief.** In **one** message, ask only what you don't
   already know:
   - What the post should be about: the topic, the angle, or which part of
     the episode to use. "You pick" is a fine answer: pass it on, and the
     workflow will suggest angles.
   - Who it's for, if the company has saved personas (`list_artifacts`
     with `type="persona"`): offer them by name, or no persona.
   - Who is posting (a person and their role, or the company page) and any
     call to action, such as a link to the episode. Both are optional.

4. **Start.** Call `start_workflow` with:
   - `workflow`: `"linkedin_post"`
   - `company_id`
   - `inputs`: `brief` (required), and if known `source_ids`, `persona_id`,
     `author`, `call_to_action`.

5. **Follow the response.** Every response has a `status` and
   `agent_instructions`. Always follow `agent_instructions`:
   - `needs_input`: ask the user `prompt_to_user` exactly as written
     (listing `input.options` if there are any), then call `respond` with
     the `session_id`, the `turn`, and the user's reply word for word.
     Never answer for the user.
   - `working`: tell the user it's being written and call `get_session`.
   - `complete`: go to step 6.
   - `failed`: tell the user and offer to try again.

6. **Present the post.**
   - Show `result.post` exactly as returned, inside a plain text block so
     the line breaks survive and it's easy to copy. Don't edit it yourself.
   - Then, briefly: the `alternative_hooks`; any `source_quotes`, so the
     user can check them against the recording; and the `notes`.
   - Mention that it's saved (`artifact_id`).

7. **Revisions.** If the user wants changes ("shorter", "more personal",
   "use the second hook"), call `start_workflow` again with
   `revision_of` set to the post's `artifact_id`, `brief` set to the
   requested changes, and the same `source_ids`, `persona_id`, `author`
   and `call_to_action` as before. Present the new version the same way.
