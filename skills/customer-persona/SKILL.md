---
name: customer-persona
description: Create a detailed buyer persona (customer persona) for a saved company with makemedia's persona workflow, with a condensed version for a presentation slide. Use when the user asks for a buyer persona, customer persona or ideal customer description, wants ideas for which buyers to target, or wants to see or reuse the company's saved personas. Requires the makemedia connector and a company whose services are set up.
---

# Customer persona

A persona describes one buyer: the job role being targeted, the problem
they're trying to solve, and which of the company's services they'd buy.
makemedia drafts the full persona on its server. Once the user accepts it,
the persona is saved together with a condensed version for a presentation
slide. Later workflows, such as LinkedIn posts, use the two versions together.

## Steps

1. **Use the project's company.** Use the `makemedia company_id` pinned in
   the project instructions or memory (see the makemedia skill). If none is
   pinned, ask which customer this project is for and pin it. Call
   `get_company`. If its `profile` has no `services`, run the
   company-profile skill first.

2. **Fill in the form.** Ask for these three things, showing each one's
   help text. Use the user's own words.
   - **Target job role for this buyer** (`job_title`). What role within
     your ideal client profile are you targeting? Examples: CEO of a
     mid-sized pharmaceutical company • doctor • CIO of a large security
     company • managing partner of a tax law firm • HR manager
   - **What problem is this buyer trying to solve?** (`problem`). A short
     phrase describing the problem they're trying to solve. Examples:
     employee retention • HIPAA compliance • lead generation
   - **Products and services for this buyer** (`services`). Which of your
     products or services would this persona buy? Show
     `profile.services.items` as a numbered list and ask the user to pick
     the ones this buyer would actually buy, at most five. When the company
     has more than five, one buyer doesn't buy everything, so ask for the
     handful that fit. Don't pick them yourself. Pass them exactly as
     saved.

   **If the user isn't sure what to put**, offer ideas: call
   `start_workflow` with `workflow: "persona_ideas"`, the `company_id` and
   `inputs: {}`. Show the ideas as `agent_instructions` say. When the user
   picks one, use its job title and organization type as the job role (for
   example "HR Director, regional bank"), its problem and its services. If
   the idea has no services, or more than five, ask the user to pick the
   ones this buyer would buy. The user can change any of them first. Ideas need the company's customer
   characteristics; if they aren't set up, run the company-profile skill
   or ask the user to fill in the form themselves.

3. **Draft the persona.** Call `start_workflow` with
   `workflow: "customer_persona"`, the `company_id` and
   `inputs: {"job_title": "...", "problem": "...", "services": [...]}`.
   If it's `working`, call `get_session` until it completes. If it
   `failed`, tell the user and offer to try again.

4. **Review it with the user.** The user can't see tool results, so paste
   the full text of `result.persona` into your reply: every section, word
   for word, as Markdown. Never summarize, shorten, excerpt or reword it,
   even though it's long; the user needs to read all of it to decide.
   Then ask the user to accept it, to ask for changes, or to get a
   different one. After any edits, show the full edited text again.
   - **Changes:** edit the text yourself as the user asks, keeping its
     format, and show it again. Keep the edited name and job title in mind
     if they change.
   - **A different one:** run step 3 again, with changed inputs if the user
     wants. Each run gives a new persona.

   Don't save anything until the user accepts.

5. **Accept it.** Call `start_workflow` with `workflow: "persona_slide"`,
   the `company_id` and `inputs: {"draft_id": "<the draft's artifact_id>"}`.
   If the user changed the persona, also pass the full edited text as
   `persona`, and `name` or `job_title` if those changed. This writes the
   slide version and saves both as one persona.

6. **Present the result.** Paste the full text of `result.slide` into your
   reply: every section and bullet, word for word, as Markdown. Don't
   summarize or shorten it. Then tell the
   user the persona is saved (`artifact_id`), with its full and slide
   versions, and that later work such as LinkedIn posts can use it. Offer
   to create another persona for a different buyer.

## Saved personas

To see the company's personas, call `list_artifacts` with
`type: "persona"`. Each one's `title` is its name and job title. Use
`get_artifact` for one persona's full (`persona`) and slide (`slide`)
versions. New personas never reuse a saved persona's name, and the ideas
skip job titles already covered.

**Changing a saved persona.** The user changes the full version; the slide
version is always rewritten from it, never edited by itself. Show the
user the full version, edit it as they ask, and show the full edited text
again. When they're happy, call `start_workflow` with
`workflow: "persona_slide"`, the `company_id` and
`inputs: {"persona_id": "<the persona's artifact_id>", "persona": "<the full edited text>"}`,
plus `name` or `job_title` if those changed. This overwrites both versions
of that persona, keeping its `artifact_id`. Present the new slide as in
step 6.

**Names are unique.** Two personas can't share a name. If saving is refused
because a saved persona already has the name, ask the user for a different
name, or, if they meant to change that persona, use its `persona_id` as
above. Don't accept the same draft twice.
