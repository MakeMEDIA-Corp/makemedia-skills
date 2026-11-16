---
name: discovery-interview
description: Run makemedia's discovery interview with the user to establish the goal of a marketing engagement for a saved company, one question at a time, producing an interview brief. Use when the user wants to start a discovery interview, define goals for a campaign or engagement, or resume an unfinished interview. Requires the makemedia connector and a saved company profile.
---

# Discovery interview

A guided interview. The makemedia server decides each question based on the
answers so far; you relay questions and answers between it and the user.

## Steps

1. **Use the project's company.** Use the `makemedia company_id` pinned in
   the project instructions or memory (see the makemedia skill). If none is
   pinned, ask which customer this project is for and pin it. If the company
   has no saved profile, run the company-profile skill first.

2. **Resume or start.** If the user wants to continue an interview, call
   `list_sessions` with `status="needs_input"` and keep only sessions for
   this project's company_id. Confirm which one, call `get_session`, and
   continue at step 4. Otherwise go on.

3. **Start.** Optionally ask whether there is a particular topic to focus on.
   Call `start_workflow` with `workflow: "discovery_interview"`, the
   `company_id`, and `inputs: {"topic": "<topic>"}` (or `{"topic": null}`).
   Tell the user roughly how long it takes: up to `progress.max_questions`
   questions, usually fewer.

4. **Interview loop.** Every response has a `status` and
   `agent_instructions`. Always follow `agent_instructions`:
   - `needs_input`: ask `prompt_to_user` exactly as written. If
     `input.options` is non-empty, list the options; if `input.allow_other`
     is true, the user may also answer in their own words. Wait for the
     user's reply, then call `respond` with `session_id`, `turn`, and the
     reply **verbatim**. Don't summarise, improve or answer for them. If the
     reply is a question back to you or off-topic, help briefly, then ask the
     interview question again.
   - `working`: call `get_session` with the `session_id`.
   - `complete`: go to step 5.
   - `failed`: tell the user and offer to start again.

   If the user wants to stop, tell them their progress is saved and they can
   resume later. Only call `cancel_session` if they want to discard it.

5. **Present the brief.** Show the goal, summary, key findings and open
   questions from `result`. Mention that it's saved (`artifact_id`).
