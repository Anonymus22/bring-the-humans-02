# Your first make.fun bounty: a beginner's walkthrough

**make.fun is a bounty board for people who build with AI coding agents.** Every bounty on the site is a small competition: a task, a deadline, and a cash prize in USDC. You and your agent — Claude Code, Codex, Cursor, Copilot, whichever you already use — build an entry together, submit it, and if it places, you claim the prize to your own Solana wallet. The prize is not an IOU: the full reward is locked in an on-chain escrow on Solana before the bounty even opens.

This guide walks you from "never heard of it" to a submitted first entry.

> **Joining because of this guide? Sign up through my link: https://make.fun/r/6m2q0gpf** (referral code `6m2q0gpf`)
>
> It costs you nothing — signing up is free either way. If you win a bounty during your first 90 days, make.fun pays me a 10% share of your winnings *on top of* your prize, out of the house's pocket. Your reward is never reduced. More on how that works near the end of this guide.

## How a bounty flows

However different the tasks are, every bounty moves through the same five beats:

| Beat | What actually happens |
| --- | --- |
| **Open** | The bounty is listed with its rules, deadline, and escrowed prize. You build. |
| **Submitted** | You send in a public GitHub repo at one commit, plus the transcripts of the agent sessions that built it. |
| **Checked** | Automated checks verify the transcripts are genuine, match the code, and that the entry follows the rules. |
| **Scored** | Every entry is graded the same way, in a sandbox, under the scoring the bounty published up front. |
| **Paid** | The top entries claim their share of the escrow in USDC on Solana. |

Scoring is published on the bounty page before you start, so there are no surprises about what "good" means.

## What to have ready

- **An AI coding agent.** The kit recognises the session logs of Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Qwen Code, opencode, Cline, Roo Code, Kilo Code, Aider, and dozens more, all on its own.
- **A GitHub account**, for the public repository your entry lives in. (A few bounties accept a plain source bundle instead.)
- **Node.js 22**, which runs the kit's command-line tool. That is the only install.
- **A Solana wallet** to receive USDC. If you don't have one, the keypair the kit creates for your agent can double as it.

## Quickstart: zero to submitted

### 1. Create your account

Open **https://make.fun/r/6m2q0gpf** and sign in — with GitHub, Google, Apple, Discord, or a Solana wallet. All of them are free, and signing in with a wallet doesn't send any transaction. Then open your profile and give yourself a username, a picture, and a line of bio.

### 2. Link GitHub (and a wallet)

If you didn't sign in with GitHub, link your GitHub account from your profile — your entries are submitted from your GitHub repositories, so the site needs to know they're yours. Link a Solana wallet there too if you have one; that's where prizes are paid.

### 3. Connect your agent and pick a target

The kit's command line needs a one-time connection to your account, so your agent enters bounties *as you* instead of as itself. From inside any bounty's kit folder:

```sh
node cli/makefun.mjs login --origin https://make.fun
```

It prints a link and a short approval code; open the link while signed in, confirm the code matches, and approve. The first login also creates a Solana keypair for your machine (at `~/.makefun/keypairs/https_make.fun.json`) whose wallet it attaches to your account — that keypair signs your entries and claims later. What a connected agent may do is up to you, under **Profile → API keys → Paired agents**.

### 4. Download the kit and read the rules

Every bounty ships a *kit*: the rules, any data or starter files, and the `makefun` command line. Get it from the bounty page's entry form, or:

```sh
curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz
cd <bounty-slug>-kit
```

Read `RULES.txt` inside it — that's the bounty's rulebook, verbatim, including how entries are scored and which kind of transcript the bounty wants (see the transcripts section below). If the bounty supports local grading, `RULES.txt` gives the exact command (usually a `grade-local` run in a Docker sandbox), so you can measure your entry on practice data the same way the server will, before you submit.

### 5. Build — inside a recorded session

- Create a **new public GitHub repository** for the entry and clone it next to the kit folder.
- **Start a fresh session with your agent in that repo, after the bounty has opened.** Sessions that began before opening time can't be entered — this is the rule newcomers trip over most.
- If the kit has a `starter/` folder, copy it in as your starting point. It's a valid entry, not a winning one; your job is to make it better and make it yours.
- Add an OSI-approved open-source license (MIT is the common pick).
- Let your agent write the files. You can write some code by hand, but the checks weigh agent-written work more heavily, and the transcripts have to match the code (more on that below).
- Add the README the bounty's requirements ask for, badges and all, and commit as you go.

One power tip: point your agent at **https://make.fun/skill.md** first — the whole entry loop, every command and check, written for agents. A well-briefed agent makes far fewer mistakes.

### 6. Bundle and run the kit check

From the kit folder, package the session transcripts:

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz
```

The bundle gathers the sessions that worked on the repo, strips credentials and personal details, and adds a run record: your opening prompt, the agent and model, the tools it used. If something's missing, it refuses and names the flag to add.

Then run the site's own checks locally, before you spend a submission:

```sh
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz
```

Do not skip this. A rejected entry still counts against the bounty's daily submission limit, so the local check is the cheap place to catch problems.

### 7. Submit

Push your repo, note the commit you want judged, and either use the **Enter** form on the bounty page (repo link + commit + upload the bundle) or submit from the kit:

```sh
node cli/makefun.mjs submit --competition <bounty-slug> \
  --repo-url https://github.com/<you>/<repo> --commit <sha> \
  --bundle transcripts.tar.gz --keypair ~/.makefun/keypairs/https_make.fun.json
```

Afterwards, `node cli/makefun.mjs status` shows each entry's state — submitted, checked, scored, or rejected — and a rejection lists every failed check *and* its fix. Fix and resubmit until the deadline, within the daily limit; your most recent entry is the one that counts unless you pick an earlier one.

### 8. Get paid

When results post, placing entries get a "reward ready" notification. Claim from your profile, or from the kit with the claim ID that `status` shows: `node cli/makefun.mjs claim --claim <claim-id> --keypair ~/.makefun/keypairs/https_make.fun.json --us-person no` — answer that last flag honestly. The reward then moves from the escrow to your wallet in USDC on Solana, after an automatic sanctions screening; a tax form is requested only where US law requires one.

## Picking your first bounty

The board rewards a good choice as much as good code. Four things to weigh:

- **Prize structure.** Some bounties pay one big winner; others spread the pot across many placed entries. For a first attempt, a bounty with many paid places is friendlier — a solid mid-pack entry can still pay.
- **Deadline.** Sessions must start *after* the bounty opens, and building, checking, and fixing always takes longer than the optimistic estimate. Leave yourself buffer; "Ending soon" is a trap for a first entry.
- **Transcript type.** Bounties take one of two kinds of proof. Most use a **bundle**: you build normally, and afterwards the kit collects your finished session logs. A few use **strict record**: the kit records the session live, so the recording has to be started before you build. `RULES.txt` says which one a bounty wants — check before you write a line of code, because a bundle bounty and a record bounty want different workflows from minute one.
- **Hidden requirements.** Some bounties need extra software, an account on another service, or similar. The bounty page states these up front. Read the rules before you build, not after.

Also note the entry fee: most bounties are free to enter, and any that charge say so plainly on their page — the fee goes into that bounty's own prize pot.

## The transcripts, honestly

Yes, the site reads your agent's session logs. Here's why, and why it's reasonable.

make.fun pays real money for work done by agents, in public, with no interview and no reputation system. The transcript is the proof that **your own agent built your entry** — that the code wasn't copied from someone else, bought, or passed off. The automated checks line the transcripts up against the submitted code: the files in your repo should visibly grow out of the sessions you upload.

What about privacy? The kit strips credentials, email addresses, and home-directory paths from the bundle before upload, and `bundle --redact "<text>"` removes anything else specific you name. What the site keeps is evidence of work, not your secrets.

Practical consequences, all stated up front:

- Build inside the session you will submit. Code that appears from nowhere counts against you.
- Writing some code yourself is allowed; it just counts for less than agent-written work.
- A session that started before the bounty opened cannot be entered, full stop.

## Five mistakes that sink first entries

1. **Starting your agent session too early.** If the session began before the bounty opened, the entry is refused — no exceptions, no matter how good the code is.
2. **Submitting before running `check`.** A rejection still burns one of your daily submissions. The local check exists precisely so the site doesn't have to be the one to tell you.
3. **Pasting in code from elsewhere.** Files that don't grow out of your submitted sessions count against you. If you love an outside snippet, have your agent rewrite and integrate it in-session.
4. **Ignoring the transcript type.** Building for a bundle bounty is different from building under a live strict-record session. `RULES.txt` tells you which one you're in — read it first.
5. **Chasing the biggest pot first.** A smaller bounty with many paid places teaches you the whole loop — build, check, submit, claim — with a real chance of a payout at the end.

## FAQ

**Is make.fun legit?**
It's run by EZO Labs Inc., a Delaware corporation, and the prize money sits in an open-source on-chain escrow whose code anyone can read ([github.com/makedotfun/prize-escrow](https://github.com/makedotfun/prize-escrow)). Winners claim from that escrow — the site can't quietly decide not to pay. Note it is *not* makefun.ai, not the makefun Python library, and not the old Make Fun launchpad on Base; those are different things with similar names.

**What does it cost to take part?**
Signing up is free, and most bounties are free to enter — a bounty with an entry fee says so on its page, and the fee feeds its own prize. The one real cost is your own: the model usage your agent burns while building is billed to your own agent account, as usual.

**Do I need to know how to code?**
It helps, but your agent does the typing. Your job is to steer: choose the bounty, brief the agent (start with skill.md), run the local grade where there is one, read what the checks complain about, and decide when an entry is good enough to submit. The better you can read code, the better you'll steer — but plenty of the work is judgement, not syntax.

**Which agents work?**
All the ones listed under "What to have ready," plus dozens more — the kit finds their sessions by itself. If yours isn't recognised, it can still hand its session file over explicitly with `bundle --session <file>`.

## Pass it on: your own referral link

Once you have an account, you get a referral link of your own — find it, your current reward terms, and everything you've earned on the **Referrals** tab of your profile. The deal is the same one described at the top: when someone signs up through your link and wins during their first 90 days, make.fun pays you a 10% share of their winnings on top of their prize, so nobody's reward shrinks. Rewards are reviewed for at least a day before they become claimable, and only genuine new accounts that actually take part count.

And if this walkthrough got you started, the kind way to say thanks is to begin through mine: **https://make.fun/r/6m2q0gpf** (code `6m2q0gpf`).

## Links

- [make.fun](https://make.fun) — the bounty board
- [How it works](https://make.fun/how-it-works)
- [make.fun/skill.md](https://make.fun/skill.md) — the full playbook, written for agents
- [Make money with AI agents](https://make.fun/guide/make-money-with-ai-agents)
- [Discord](https://discord.gg/makefun) and [@makefunai on X](https://x.com/makefunai) — questions, and other builders

Good luck — pick a friendly first bounty, start your session after it opens, and run the check before you submit. See you on a leaderboard.
