# Whylog

Whylog keeps the reasons behind engineering decisions. It runs as three agents in one team chat room on Switch: a coding agent, a scribe that records each decision with its reasons, and a recall agent that answers "why did we do it this way?" only from what was recorded.

When someone leaves a team, the code stays and the reasons go with them. Whylog captures the reasons while the decision is being made, marks anything nobody said as "not stated", and says "This was not recorded" instead of guessing.

Hackathon: Flip the Switch, Track 01 (Best Business Workflow).

## How Switch is used

Everything below is a Switch feature working in one Mattermost room, `whylog-engineering`.

| Switch feature | What Whylog does with it |
|---|---|
| Agents (3, Claude Code, running on the local machine) | `whylog-coder`, `whylog-scribe`, `whylog-recall` |
| Room | `whylog-engineering`, private, mirrored to Mattermost. A person and all three agents talk in one place. |
| Roles | `scribe` and `recall`, both exclusive. Each agent takes its role in the room (`!roles` shows them). |
| Room documents | The scribe creates and updates one document per decision (`DR-1 ...`), following the room's "How we record decisions" document. `!list-documents` lists them. |
| References | A GitHub reference points at the demo repository, so records link to real PRs. `!list-references` shows it. |
| Room instructions | The briefing every agent in the room reads. |
| `!` commands | `!roles`, `!list-documents`, `!list-references` are used in the demo. |

The agents:
- **whylog-coder** works with an engineer on code. Before it picks an option it says the options and trade-offs out loud in the room.
- **whylog-scribe** takes the `scribe` role. It asks for what is missing, one question at a time (what was chosen, what was rejected and why, who decided, what would make us revisit it). It writes only what a person said and marks the rest "not stated". When someone is leaving it lists the decisions they own and interviews them about the gaps.
- **whylog-recall** takes the `recall` role. It answers only from the decision documents and the GitHub reference, quotes the record, names who said it, and links the PR. If nothing covers the question it says so.

## What we tested, and what happened

The demo subject is [ibnweb3/whylog-demo](https://github.com/ibnweb3/whylog-demo), a tiny webhook sender with two merged PRs: #1 adds a 5 second timeout, #2 adds up to 3 retries with backoff. Neither PR description says why. Everything about the team and the reasons is made up for the demo.

1. The engineer asked the coder for options on both PRs. It listed options with trade-offs and a pick for each, and said it had not read the PR diffs.
2. The scribe took its role, created `DR-1 5s timeout and 3 retries with backoff`, linked PRs #1 and #2, and marked the parts nobody had said as "not stated".
3. The engineer filled in the rejected options. The scribe updated DR-1.
4. Offboarding: the engineer said they were leaving. The scribe listed the one decision they own, named the three gaps, and interviewed them. Answers went into DR-1, marked as the engineer's.
5. The recall agent was asked why the team uses a 5 second timeout and 3 retries. It quoted DR-1, named who decided, linked both PRs, and listed what is not stated.
6. The recall agent was asked why the team did not use a message queue. Answer: "This was not recorded." It named DR-1 as the nearest record and did not give a reason.

## Run it

You need: Switch Console 0.40.1 (the Windows early-access build), Docker Desktop (for the bundled Mattermost), and Claude Code installed and signed in on the same machine.

1. Fork or clone [whylog-demo](https://github.com/ibnweb3/whylog-demo), or use your own repository. Make it public so the GitHub reference works.
2. In Switch Console, open **Templates**, then **Import template**, and paste `template/whylog.template.yaml`.
3. Fill in the three parameters: `team` (for example `whylog`), `repo_url` (your repository URL), `newhire_user` (a second Mattermost username that exists on your server).
4. Click **Create all** once. The Console can show a timeout while the server is still building. Do not retry: wait a few minutes and check **Agents** and **Rooms**, which fill in by themselves. Retrying creates duplicate rooms.
5. Open `<team>-engineering` and run `!roles` and `!list-references`.
6. Follow [docs/SCENARIO.md](docs/SCENARIO.md), the exact messages used in the demo.

## Limits, stated plainly

- **Sources.** The decision reasons come from the room (people and the coding agent talking). Switch has reference types for Jira, Confluence and Drive, but this project connects only the GitHub reference. It does not read Jira tickets, company chat history or research documents.
- **Not a retrieval system.** The recall agent reads the room's decision documents and the GitHub reference with its own access. There is no search index. It is a small, honest demo of the pattern, not a product.
- **The coder had no clone of the repo.** In our run the coder said it had not read the PR diffs, so it described the general option space. The scribe and recall agents rely on what people said and on the PR links, not on the diffs.
- **Roles are claimed by the agents.** Switch lists the roles as free until an agent takes one. The scribe and recall agents each did so in our run.
- **Mattermost and one machine.** We ran it on one Windows machine with the bundled Mattermost. One person played the engineer in the demo. We did not test other chat apps or remote hosts.
- **Created by template.** The room and agents are created by `template/whylog.template.yaml`, with no kickoff messages because they made the Console time out.

## Built with AI

This project was built with Claude Code, and the three agents run on Claude Code through Switch. The author can explain every part of it.
