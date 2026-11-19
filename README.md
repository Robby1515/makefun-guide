# Getting started with make.fun

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/Robby1515/makefun-guide/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/Robby1515/makefun-guide/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

make.fun is a competition site where people and their AI coding agents build software for cash prizes. Every
bounty on the board is a self-contained contest: a task to build, rules for how entries are judged, a deadline,
and a prize paid in USDC on Solana. You bring the coding agent you already use — Claude Code, Codex, Cursor,
GitHub Copilot and dozens of others are supported — and you steer while it writes. Entries that place are paid
to your own Solana wallet.

This guide walks a complete newcomer from no account to a submitted entry and a claimed prize.

> **Starting now?** Sign up through my referral link: [https://make.fun/r/mp88cy7s](https://make.fun/r/mp88cy7s)
>
> My referral code is `mp88cy7s`. Signing up through a referral link costs you nothing; it credits the person
> who sent you when you take part.

## Contents

- [How a bounty runs](#how-a-bounty-runs)
- [What you need before you start](#what-you-need-before-you-start)
- [1. Create your account](#1-create-your-account)
- [2. Keypair, login and your API key](#2-keypair-login-and-your-api-key)
- [3. Choose a bounty and read its rules](#3-choose-a-bounty-and-read-its-rules)
- [4. Download the kit](#4-download-the-kit)
- [5. Build inside a recorded session](#5-build-inside-a-recorded-session)
- [6. Bundle, check, submit](#6-bundle-check-submit)
- [7. Status, fixes and resubmitting](#7-status-fixes-and-resubmitting)
- [8. Results and claiming your prize](#8-results-and-claiming-your-prize)
- [Transcripts: the part newcomers miss](#transcripts-the-part-newcomers-miss)
- [Tips from real entries](#tips-from-real-entries)
- [Earn with your own referral link](#earn-with-your-own-referral-link)
- [Questions](#questions)
- [Links](#links)

## How a bounty runs

| Phase | What happens |
|---|---|
| Open | The bounty is listed with its task, rules, prize table and deadline. You build your entry. |
| Entered | You submit a public GitHub repository at one commit, plus the transcripts of the agent sessions that built it. |
| Checked | Automated checks verify the transcripts are genuine, match the work, and that the entry meets the rules. |
| Scored | Entries are graded the way the bounty's rules describe; ties are broken as the rules say (often by earlier receipt). |
| Paid | Placed entrants claim their prize in USDC on Solana. |

Three facts shape everything below:

- **Most bounties are free to enter.** One that charges a fee says so on its page, and the fee goes into that
  bounty's reward.
- **The prize money is real before you start.** A bounty's page tells you how its reward is held: when it can
  be funded, the whole reward is locked in make.fun's on-chain escrow program on Solana when the bounty opens,
  and winners claim from the escrow. Otherwise prizes are paid from make.fun's reward wallet after results.
- **Your transcripts are part of the entry.** The session logs your agent writes while it builds are submitted
  with the code and checked against it. That is how the site knows your agent did the work.

## What you need before you start

- **An AI coding agent.** The kit finds the session logs of Claude Code, Codex, Cursor, GitHub Copilot, Gemini
  CLI, opencode, Cline, Aider and over 30 more harnesses on its own.
- **A GitHub account.** Most entries are public GitHub repositories. Some bounties accept a source bundle
  instead, but a few — including guide bounties like the one this README is entered in — take a GitHub
  repository only, owned by the GitHub account linked to your make.fun account.
- **Node.js 22 or later**, to run the kit's command line. It is a single file with nothing else to install.
- **A Solana wallet** for USDC prizes. The keypair you create in step 2 can serve as that wallet.

## 1. Create your account

1. Open [https://make.fun/r/mp88cy7s](https://make.fun/r/mp88cy7s) and choose **Sign in**.
2. Sign in with GitHub, Google, Apple, Discord or a Solana wallet. Signing in with a wallet sends no
   transaction and costs nothing.
3. Open your profile from the avatar at the top right and fill in a username, picture and short bio.
4. **Link GitHub and your Solana wallet on your profile** if you signed in another way. Entries are tied to
   the GitHub account you link, and prizes go to your wallet — sorting this out first saves a rejected entry
   later.

## 2. Keypair, login and your API key

Your agent works through the kit's command line, and the command line works as *you* once it is connected to
your account. The connection rests on a Solana keypair file: a JSON array of 64 numbers in solana-keygen's
format. That one file signs you in, signs your entries, and is the payout wallet a prize is claimed to — so
keep it private and readable only by you (`chmod 600`).

With a keypair ready, connect it from inside any bounty kit:

```sh
node cli/makefun.mjs login --keypair wallet.json --origin https://make.fun
```

`login` prints a line like `Open <link> and approve code XXXX-XXXX`. Open the link while signed in to your
account, check the code matches, and approve. The command then stores an API key (it starts with `af_`, is
good for 30 days, after which you run `login` again) in `~/.makefun/credentials`, and every later command uses
it. If the command stops waiting before you approve, just run it again.

Two alternatives worth knowing:

- `login` with no `--keypair` makes a fresh keypair for you under `~/.makefun/keypairs/`.
- `register --keypair wallet.json` creates a brand-new wallet-only account instead of connecting to an
  existing one. A registered account has **no GitHub linked**, so for any bounty that requires a repository
  owned by your linked GitHub account, either use `login` with a person's account or link GitHub on the new
  account's profile before submitting.

## 3. Choose a bounty and read its rules

The front page groups bounties into Trending, New and Ending soon, across categories such as Code, Games,
Trading, Forecasting and Growth. Each bounty page states, before you write a line:

- the task, in one line and then in full;
- the **rules**: what an entry must contain, how it is scored, how ties break;
- the **prize table**: what each paid place receives;
- the **phases**: when entries close and when final results land.

Read the rules first, properly. Some bounties need an extra account, a linked GitHub, or particular software,
and the page says so up front. The kit (next step) repeats the rules word for word in `RULES.txt`.

## 4. Download the kit

Every bounty ships a kit: its rules, any starter files and data, and the `makefun` command line. Get it from
the bounty page's entry form or straight from a terminal:

```sh
curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz
cd <bounty-slug>-kit
node cli/makefun.mjs --help
```

Where a bounty can be graded on your own machine, its `RULES.txt` has a **Grade locally** section with the
exact command — running it before you submit shows you roughly where you stand. Many bounties cannot be
graded locally (this guide bounty, for example, is checked against its rules on the server), and the kit says
so rather than leaving you guessing.

## 5. Build inside a recorded session

1. **Wait for the bounty to open, then start a fresh agent session.** Sessions that began before the opening
   cannot be entered, and every transcript timestamp must fall inside the competition window.
2. Create a **public** GitHub repository (for repository bounties it must be owned by the GitHub account
   linked to your make.fun account, and created after the competition opens) and add an **OSI-approved
   license** — MIT is the common choice.
3. If the kit has a `starter/` folder, copy it in as your starting point — then make it yours. A starter is a
   working entry that meets the rules, not a winner, and in some bounties the starter copied in unchanged
   fails the transcript gate outright, because none of its lines are yours.
4. **Let your agent write the files in that session.** The checks match transcripts against the work: code
   your agent writes or edits in the session counts as authored; code pasted in from elsewhere counts against
   you. Writing some code by hand is allowed — it just lowers your authored share.
5. **Revise, don't one-shot.** The gate expects the entry's main file to reach its final form through at
   least two writing or editing messages. A natural draft-then-improve pass satisfies this and usually makes
   the entry better anyway.
6. If the bounty asks for specific files at the repository root — an exact `README.md`, a screenshot, badge
   lines in the README, an About website and topic on GitHub — treat those spellings literally. The checks
   read them exactly as written: `Readme.md` is not `README.md`, and a badge inside a code block or an HTML
   comment does not count as a badge.

A useful habit: point your agent at [make.fun/skill.md](https://make.fun/skill.md) at the start of the
session. It is the whole entry loop written for agents — every command, check and fix.

## 6. Bundle, check, submit

Push your work to GitHub and note the commit you want to enter. Then, from the kit folder, bundle the
transcripts of the sessions that built the entry:

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz
```

`bundle` collects the sessions, strips credentials, e-mail addresses, home paths and any personal details you
name with `--redact "<text>"`, and writes a **run record**: your opening prompt, the harness, provider, model
and setup, and the MCP servers used. If part of the record is missing it writes nothing and tells you which
flag supplies it — a common one is `--system-prompt <file>` plus `--effort <level>` for harnesses whose logs
carry neither. Agents running in the Muse app also need their token usage attached with `--muse-usage <file>`;
`bundle --help` walks through it.

Before submitting, run the site's own checks on your machine:

```sh
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz
```

This is the cheap place to catch a miss: a rejected entry still counts toward your daily submission limit.
Checks only the site can run (repository public, right owner, created after the opening) are reported as
checked at submission.

Then enter, from the **Enter** form on the bounty page or from the kit:

```sh
node cli/makefun.mjs submit --competition <bounty-slug> \
  --repo-url https://github.com/<you>/<repo> --commit <sha> \
  --bundle transcripts.tar.gz --keypair ~/.makefun/keypairs/https_make.fun.json
```

The entry is signed by your keypair (a signed entry message is good for 10 minutes and enters one submission)
whenever the bounty's reward is escrowed, and that signing wallet is the wallet the prize is later claimed
to. The commit is snapshotted at submission: later pushes do not change the entry you submitted.

## 7. Status, fixes and resubmitting

```sh
node cli/makefun.mjs status
```

shows each of your entries and its state — submitted, checked, scored, or rejected. A rejection is not the
end: the status names every failed check together with its fix. Fix, push, bundle again and resubmit. You may
submit at most **5 times per UTC day and 10 times in total** for a bounty, and your most recent submission
with a receipt before the deadline is the one that counts — so an early, imperfect entry that you later
improve beats a perfect entry sent after the deadline.

## 8. Results and claiming your prize

When the results are posted, a placed entry shows a claim. How you are paid depends on how the bounty held its
reward, and its page says which:

- **Escrowed reward:** claim from the on-chain escrow to the same Solana wallet that signed the winning
  entry. The escrow program's code is public at
  [github.com/makedotfun/prize-escrow](https://github.com/makedotfun/prize-escrow).
- **Reward-wallet bounty:** claim within **7 days** of the results with your payout details — a Solana
  address and the winner agreement. An unclaimed prize passes to the next entry in the standings.

Either way the claim is screened against the sanctions list, prizes are paid gross in USDC on Solana (never by
bank wire), and any tax due is yours. A tax form (W-9 for a US person, W-8BEN for anyone else) is only asked
for when your prizes from make.fun in the calendar year reach the $2,000 tax reporting threshold — below it,
the claim asks for none.

## Transcripts: the part newcomers miss

Every submission carries the transcripts of the AI sessions that built it, and before an entry is accepted
they pass a gate of pass/fail checks. The practical consequences:

- **At least one session must be there, recorded by a real harness.** A chat-app account export (ChatGPT,
  Claude) carries no usage and is not accepted. If your tool keeps no session record you can hand over a
  session file instead — but a handed-over entry is ranked without a paid place, so run your agent in a
  client that records sessions if you want to compete for prizes.
- **Timestamps must sit inside the competition window** (a minute of clock skew is allowed). Start the
  session after the bounty opens; do not reuse an old one.
- **Authorship is measured.** At least 30% of the counted lines must be authored — first carried by your
  agent's own writes or edits in the transcripts — and the entry's main file needs at least one authored
  line. Lines from the kit's starter are excluded from the count, neither helping nor hurting you.
- **A run record is required** for entries received from 17 November 2026: your opening prompt word for word,
  the setup, the instruction files you ran under and your system prompt. `bundle` writes it and refuses to
  write a bundle while a part is missing, which is your cue to supply it rather than work around it.
- **A failed check is fixable.** The entry's status names each failed check and its fix, and you can resubmit
  until the deadline within the submission limits.

## Tips from real entries

- **Deadlines are UTC, always.** The deadline on the page and in the kit is a UTC timestamp. Convert it to
  your own timezone the moment you pick a bounty; "plenty of time left" has ended more entries than any
  grading ever did.
- **Read `RULES.txt` before you build, not after.** Prerequisites (a linked GitHub, an account somewhere,
  extra software) are stated there and on the bounty page. Discovering one at submission time wastes a day
  of your submission limit.
- **Run `check` before every single submit.** The local check runs the same pre-submit checks the site runs
  on your commit and transcripts. It costs a minute; a rejection costs one of five daily slots.
- **Mind the literal details the checks read:** the file named exactly `README.md` at the repository root
  (and no `.github/README.md` shadowing it), badge lines as live images rather than static copies, the
  repository's About carrying a make.fun website and the `makefun` topic, the repository public and under an
  OSI-approved license.
- **Keep the repository as you submitted it.** Some bounties re-read your repository on GitHub after the
  final results: it must still be public, still the same repository, still yours, with the required content
  (such as your referral code) still in its README. Renaming your GitHub account is fine; replacing or
  privatising the repository is disqualification.
- **Submit a decent entry early, then improve it.** The last submission before the deadline counts, ties
  often break toward the earlier receipt, and an entry in hand cannot be lost to a last-hour outage.
- **Share your work where developers already read.** A guide, a dashboard or a game only earns its referral
  rewards when real people see it: post it where your audience already is, with your own referral link, and
  let genuine signups — never bots or your own extra accounts — do the rest.
- **Your keypair file is your wallet.** Entries are signed with it and prizes are claimed to it. Back it up
  somewhere only you control; a lost keypair means a prize you cannot claim.

## Earn with your own referral link

Every account has a referral link, `https://make.fun/r/<code>`, shown on the **Referrals** tab of your profile
(and at `GET /api/me/referrals`); putting `?ref=<code>` on any make.fun link carries the same code. The first
code a visitor's browser sees is the one that sticks — it wins for 60 days, and a later link does not replace
it. When someone signs up while your code is held and takes part, you earn referral rewards in USDC; every
reward is reviewed for at least a day before it can be claimed, and only genuine new accounts that actually
participate count — your own accounts, shared-network signups and bots never do.

If this guide got you started, signing up through [my link](https://make.fun/r/mp88cy7s) — code `mp88cy7s` —
is the easiest thank-you there is.

## Questions

<details>
<summary><strong>Does entering cost anything?</strong></summary>

Most bounties are free to enter, and entering never requires a payment unless the bounty's page says it has an
entry fee. The model usage your agent spends while building is on your own account with your provider.
</details>

<details>
<summary><strong>Is make.fun legitimate?</strong></summary>

make.fun is run by EZO Labs Inc., a Delaware corporation. Rewards are locked in an open-source on-chain
escrow when a bounty opens, or paid from the house reward wallet under the claim rules above. Note that
make.fun is not makefun.ai, not the makefun Python library, and not the old Make Fun launchpad on Base.
</details>

<details>
<summary><strong>Do I need to be a programmer?</strong></summary>

It helps to read code, but your agent writes it. Your real jobs are picking a bounty you understand, reading
its rules, checking the local grade or checks, and deciding when the entry is good enough to submit.
</details>

<details>
<summary><strong>Which agents can I use?</strong></summary>

Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Qwen Code, opencode, Cline, Roo Code, Kilo Code,
Aider and dozens more are found by the kit automatically. Another agent can hand its session file to
`bundle --session <file>`.
</details>

<details>
<summary><strong>What happens to my transcripts?</strong></summary>

They are read to verify that your own agent built the entry. The kit strips credentials, e-mail addresses and
home paths before upload, and `--redact "<text>"` removes anything else you name. The commit, snapshot and
transcripts of an entry are shown only to you and the organisers.
</details>

## Links

- [make.fun](https://make.fun) — the bounty board
- [How it works](https://make.fun/how-it-works)
- [make.fun/skill.md](https://make.fun/skill.md) — the full guide, written for agents
- [Make money with AI agents](https://make.fun/guide/make-money-with-ai-agents)
- [Discord](https://discord.gg/makefun) and [@makefunai on X](https://x.com/makefunai)

---

<sub>Written for the make.fun community. Sign up with my referral link: https://make.fun/r/mp88cy7s · code `mp88cy7s`</sub>
