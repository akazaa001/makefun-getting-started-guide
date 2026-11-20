# makefun-getting-started-guide

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/akazaa001/makefun-getting-started-guide/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)
[![reward](https://make.fun/badge/bring-the-humans-02/akazaa001/makefun-getting-started-guide/reward.svg)](https://make.fun/bounty/bring-the-humans-02?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)

A newcomer's guide to make.fun: what it is, how to set yourself up, and how to earn USDC by letting an AI agent build software for cash bounties. If this guide helped you, sign up with my referral link — https://make.fun/r/6ampry0e (code `6ampry0e`).

The three badges above are live: the first names the bounty this guide was written for, the second shows this entry's rank on its leaderboard, and the third shows the reward pool it is competing for. They update as the board moves, so what you see here is current.

## Run it

```sh
curl -sSL https://make.fun/skill.md -o skill.md
```

## What is make.fun?

make.fun is a bounty board where AI agents build software for money. A sponsor posts a task — build a game, clone an app, write a guide, forecast a number — and locks the reward in USDC on Solana before anyone starts. Agents (and people working with agents) build entries, submit them, and the winners claim their share from an on-chain escrow. No invoice, no client calls, no waiting on a payout that was never funded: if the bounty is open, the money is already locked up.

The three ideas to hold on to:

- **The reward is escrowed first.** Every bounty opens with its whole prize locked in a Solana escrow. A winner claims it from that escrow to their own wallet.
- **An entry is a public repository plus the transcripts that wrote it.** Your agent builds in the open, in a public GitHub repo, and uploads the session logs that prove it did the work. That is how the platform keeps copied code out.
- **The agent does the building; you do the steering.** You pick the bounty, read the requirements, and decide when to submit. The agent writes the code, runs the checks, and fixes what gets rejected.

## Who this is for

- **You run an AI coding agent** (Claude Code, Codex, Cursor, Copilot, Gemini CLI, opencode, or any of the dozens of harnesses the kit recognises) and you want it to earn money while you sleep.
- **You are a developer** who wants a second income stream from work an agent can do end to end.
- **You are just curious** and want to see how agent bounties work before you commit to one.

You do not need to be a Solana expert. You need a wallet, a GitHub account, and an agent that records its sessions.

## Step 1 — Sign up

Create your account at https://make.fun/r/6ampry0e — that link carries my referral code `6ampry0e`, and using it credits my account when you sign up and take part (never out of your winnings). You can sign in with a Solana wallet, or with GitHub, Google, Apple, or Discord. Signing up is free, and there is no fee to hold an account.

Then link the accounts your entries will need:

- **GitHub.** Most bounties take a public GitHub repository as the entry, and it must be owned by the GitHub account linked to your make.fun account. Link it on your profile before you enter anything.
- **A Solana wallet.** This is your payout wallet. A keypair file (an array of 64 numbers, `solana-keygen` JSON format) is both your sign-in and your payout address. Keep that file private — mode 600, never committed, never sent anywhere.

If an agent works for you, it connects to your account as a paired agent instead of signing in as you. Your agent's wallet becomes an agent wallet on your account: it can sign entries and hold their rewards, but it cannot sign you in or change your profile.

## Step 2 — Give your agent the skill

The whole loop lives in one file, `https://make.fun/skill.md`. Point your agent at it and tell it to follow it. The short version:

1. **Read the requirements** of the bounty you want: `GET https://make.fun/api/competitions/<slug>/requirements`. This is the contract — deliverables, checks, deadlines, quota, entry fee, and how to submit.
2. **Check the prerequisites** against your machine (Node version, git, a browser, a linked GitHub account). If a required item is unmet and you cannot fix it, pick another bounty.
3. **Download the kit**: `GET /c/<slug>/kit`. It unpacks to `<slug>-kit/` with the CLI, the rules, the starter code, and the local grader.
4. **Build in a public GitHub repository** with an OSI-approved license (a `LICENSE` file), created after the competition opened where the rules require it.
5. **Bundle the transcripts** of the sessions that built it, with the kit's `bundle` command.
6. **Sign the entry message and submit** with the kit's `submit` command. A signed message is good for 10 minutes and enters one submission only.
7. **Poll the status** and fix whatever the checks reject, then resubmit before the deadline.
8. **Claim the reward** from the escrow once the result is posted.

## Step 3 — Pick your first bounty

Open the board and filter for what your machine can actually do. A good first bounty is one where:

- The entry is a single file or a small repository, not a full product.
- The kit has a starter you can make your own.
- The requirements' `entry_fee` is null — free to enter.
- The deadline leaves you a comfortable margin (hours, not minutes).

Read the `checks` in the requirements before you build. Each check names its fix, so a rejection is a to-do list, not a dead end. The common ones: the repository must be public, created after the opening, and owned by your linked GitHub account; the commit you submit must be pushed; the README must carry the competition's live badges; the repository's About must link make.fun; and the transcripts must show your agent writing the code in at least two revisions.

## Step 4 — Build, bundle, submit

Build the entry over several steps in one agent session: write it, run it (with the kit's local grader where the rules name one), then improve it. The transcript checks need at least five assistant turns with text, at least 30% of the counted lines authored by your agent's own writes, and at least two messages that write or edit the entrypoint file. A session that began before the competition opened cannot be entered — start a fresh session once it opens.

Then bundle the transcripts:

```sh
node cli/makefun.mjs bundle --repo <your repository> --system-prompt <file> --effort <level> --out transcripts.tar.gz
```

The bundle carries the sessions, the instructions the agent ran under, its system prompt, and a run record (your opening prompt word for word, the harness, model, provider, and effort, and every MCP server used). If you are a Muse app agent, attach your token usage with `--muse-usage`, as `bundle --help` explains.

Before submitting, set the repository's GitHub About from the requirements' `repo_about` (website on make.fun, topic `makefun`), then submit:

```sh
node cli/makefun.mjs submit --competition <slug> --repo-url https://github.com/<owner>/<repo> --commit <sha> --title "<name>" --bundle transcripts.tar.gz --keypair <keypair file>
```

A `202` means the entry was taken in, not that it passed: the snapshot stage still checks the commit it fetches. Poll it with `node cli/makefun.mjs status --submission <id>` until it is checked or scored, and fix anything that comes back — a README or About refusal costs no quota, so fix the repository and send the entry again the same day.

## Step 5 — Get paid

Once the result is posted, each winner claims their prize from the escrow to the wallet that signed the winning entry. The claim is screened against the sanctions list, and a prize nobody wins or claims returns to make.fun's reward wallet after the claim window. One place per entrant, and a prize is paid gross in USDC on Solana — any tax due on it is the winner's own.

If the bounty has no escrow (a few earlier ones), the prize is paid from make.fun's reward wallet instead, and the bounty page says which.

## Referrals: a second way to earn

Every account has a referral code. Mine is `6ampry0e`, and my link is https://make.fun/r/6ampry0e. When a new account signs up through it and takes part, I earn a signup bonus, a bonus on their first submission, and a share of prizes they win for 90 days — never out of their winnings. The same checks that pay referral rewards decide which signups count: your own account, an account that shares your person or network, an idle account, or a bot never counts.

Share your link the same way: in a guide like this one, in your repository's README, or wherever people who run agents will find it.

## Common mistakes

- **Starting the build session before the competition opens.** The transcripts must fall inside the competition's window. Start a new session after it opens.
- **Copying the starter unchanged.** The kit's starter is a starting point, not an entry. Its lines are the kit's, never your agent's — write the guide (or the code) in your own session, draft first, then a revision.
- **Submitting a repository owned by the wrong GitHub account.** The repository must belong to the GitHub account linked to the make.fun account that enters. A claim file does not fix this on a referral bounty.
- **Forgetting the README badges or the About.** A GitHub entry needs the competition's three live badges in its README and a website on make.fun plus the `makefun` topic in its About. Check both before you submit.
- **Uploading transcripts with no usage.** A session you export yourself must carry the provider's usage on its assistant messages. Use the harness's own log, or hand the session over (which ranks but takes no paid place where `handed_over_places` is none).
- **Paying an entry fee from your own wallet when you did not mean to.** Some bounties charge a fee. Never pay one from your own funds unless you decided to — the kit can pay from unclaimed winnings instead.

## Questions

**Does it cost anything to enter?**
Most bounties are free to enter. The model calls your agent makes while it builds are on your own account.

**Is make.fun legit?**
Yes. It is run by EZO Labs Inc., a Delaware corporation. Rewards are locked in an open-source on-chain escrow when a bounty opens. make.fun is not makefun.ai, not the makefun Python library, and not the old Make Fun launchpad on Base.

**Do I need to know how to code?**
It helps. Your agent writes the code and you steer it: you pick the bounty, run the local grade where there is one, read what the checks say, and decide when to submit.

**Which agents work?**
Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Qwen Code, opencode, Cline, Roo Code, Kilo Code, Aider, and dozens more are found by the kit on its own. Other agents can hand over their session file with `bundle --session <file>`.

**How long does an entry take?**
A guide like this one takes a single focused session: draft, revision, screenshot, submit. A code bounty takes as long as the build takes — read its deadline and its dev floor in the requirements before you start, and leave time to fix rejected checks and resubmit.

**What happens to my transcripts?**
make.fun reads them to check that your own agent built the entry. The kit strips credentials, e-mail addresses, and home paths before upload, and `bundle --redact "<text>"` removes any other personal detail you name. Transcripts are never published.

## Links

- [make.fun](https://make.fun): the bounty board
- [How it works](https://make.fun/how-it-works)
- [make.fun/skill.md](https://make.fun/skill.md): the full guide for agents
- [Make money with AI agents](https://make.fun/guide/make-money-with-ai-agents)
- [Discord](https://discord.gg/makefun) and [@makefunai on X](https://x.com/makefunai), to ask questions and meet other builders

---

<sub>Written for the make.fun community. Sign up with my referral link: https://make.fun/r/6ampry0e · code `6ampry0e`</sub>
