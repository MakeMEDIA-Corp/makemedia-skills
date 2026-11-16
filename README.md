# makemedia skills for Claude

These skills connect Claude to **makemedia**, which stores marketing work
for each customer (company profiles, personas, interview briefs) and
creates content such as LinkedIn posts, including posts drawn from podcast
transcripts. The skills hold only public instructions. The content
workflows run on the makemedia server.

Setup takes about five minutes. You need a Claude Pro or Max plan and a
`kcilink.com` Google account.

| Environment | Connector URL |
|---|---|
| Preview (testing) | `https://mcp-preview.makemedia.ai/mcp` |
| Production | `https://mcp.makemedia.ai/mcp` (not live yet) |

Use the **Chat** tab of the Claude desktop app (or claude.ai) for all
makemedia work. Projects, project instructions, memory and the upload
button all live there.

## 1. Add the connector

1. In Claude, open **Settings → Connectors** and click **Add custom
   connector**.
2. Name it `makemedia` and paste the connector URL from the table above.
3. Click **Connect** and sign in with your `kcilink.com` Google account.
   Other accounts are refused.

The connector belongs to your Claude account, so it also works in Claude on
the web and on mobile.

## 2. Turn on what the skills need

In **Settings → Capabilities**:

- **Code execution and file creation**: turn it on. Skills need it, and it
  helps Claude convert uploaded transcripts exactly.
- **Memory**: optional. When it's on, Claude remembers which customer each
  project is for. The project instructions (step 4) do that job whether
  memory is on or off.

## 3. Install the skills

1. Open this repository's **[latest release](../../releases/latest)** and
   download the `.zip` files under **Assets**. Don't unzip them.
2. In Claude, open **Settings → Capabilities → Skills**, click **Upload
   skill**, and choose a zip. Repeat for each zip.

| Zip | What it does |
|---|---|
| `makemedia.zip` | Core rules: one customer per project, uploading transcripts, sharing. **Required.** |
| `company-profile.zip` | Researches a company's website and saves its profile |
| `customer-persona.zip` | Creates a customer persona |
| `discovery-interview.zip` | Runs an interview to set engagement goals |
| `linkedin-post.zip` | Writes a LinkedIn post, optionally from a transcript |

**Updating:** when a new release comes out, delete the old skills in
Settings → Capabilities → Skills and upload the new zips. Then disconnect
and reconnect the connector (Settings → Connectors → makemedia), so Claude
picks up any new tools.

The connector works without the skills too, because its tool descriptions
tell Claude what to do. The skills make the flow smoother and keep
customers separate.

## 4. One project per customer

1. In the Chat tab, create a **project** for the customer (e.g. "Acme").
2. Start a chat in it: "Use makemedia for Acme (acme.com)." Claude finds
   the company, or creates its profile, and gives you a line like:

   ```
   makemedia company_id: co_abc123 (Acme Corp). Use only this company for makemedia in this project.
   ```

3. Paste that line into the project's **Instructions**. From then on,
   every chat in the project uses only that customer's data.

Keep each customer in its own project. Don't mix customers in one project,
and don't do customer work in chats outside a project.

## 5. Check it works

In a chat, ask: **"What version of makemedia are you connected to?"**
Claude should report the version, the environment (`preview` or `prod`)
and your email address.

## Everyday use

- **Content from a podcast:** upload the transcript with the upload button
  (any format), then say "Write a LinkedIn post from this episode." Claude
  saves the transcript for the customer, so later posts can reuse it
  without another upload.
- **Revisions:** "Make it shorter" or "Use the second hook" creates a new
  version, and the original is kept.
- **Sharing a customer with a colleague:** "Give jane@kcilink.com access
  to this company." Only owners can do this. The colleague then sets up
  their own project for that customer (step 4).
- **Unfinished work:** interviews and other multi-step workflows carry
  over to new chats. Ask "Do I have any unfinished makemedia sessions?"

## Troubleshooting

| Problem | Fix |
|---|---|
| Claude doesn't use makemedia | In the chat, open the tools menu (the **+** button or the tools icon) and check that **makemedia** is turned on. |
| Skill upload is refused | Turn on **Code execution and file creation** (step 2), and upload the zip itself, not its contents. |
| New tools or workflows don't appear after an update | Settings → Connectors → makemedia: disconnect and reconnect. |
| Sign-in fails or loops | Sign in with your `kcilink.com` account. If Google picks another account, use a private window or switch accounts in the Google screen. |
| "Company not found" | You've lost access, or the ID is wrong. Ask the company's owner to add you again. |
| "A company for … already exists" | A colleague owns it. Ask the owner named in the message to add you. |
| A workflow stays on "working" | Ask Claude to check the session again. If it still hasn't finished after a few minutes, report it with the session ID. |
