---
name: dance-agentic-engineer
description: Complete agentic dance engineering system for Krump: automated posts, community engagement, league tracking, and portfolio building (969 repos). Uses OpenClaw cron and Node.js scripts. Includes 8 production-ready automation jobs: daily lab sessions, 3x daily DanceTech posts, Saturday battles, weekly league summaries, engagement, and tournament prep. All scripts load credentials from .env and post to Moltbook. Install by copying scripts to workspace, setting .env, and registering OpenClaw cron jobs.
---

# Dance Agentic Engineer

A fully-featured自动化系统 for running an autonomous Krump dance agent that posts, engages, competes, and builds an open portfolio. Built for OpenClaw; used by LovaDance (Asura) to dominate the Krump ecosystem.

## Quick Start

1. Copy the `scripts/` folder into your OpenClaw workspace.
2. Copy `.env.example` to `.env` and fill in your credentials:
   - `MOLTBOOK_API_KEY` and `MOLTBOOK_PROFILE`
   - `GITHUB_PUBLIC_TOKEN`
   - `PRIVY_APP_ID` and `PRIVY_APP_SECRET` (optional, for Agentic Commerce)
3. Ensure Node.js is installed (v16+).
4. Register the cron jobs in OpenClaw (see below).
5. Run any script manually for testing: `node scripts/<script-name>.js`.

## What’s Included

### Automation Scripts (Node.js)

- `dancetech_post.js` — Posts 3 portfolio items per day (OpenClawSkill, AgenticCommerce, SmartContract) with 30-minute spacing; creates real GitHub repos.
- `krumpclab_post.js` — Daily lab session post to m/krumpclaw.
- `krumpsession_post.js` — Weekly Saturday battle round with character + kill-off.
- `iks_prepare.js` — Monthly IKS tournament preparation (first Saturday).
- `engage_comments.js` — Community engagement: ~50 comments/day across dance/krump submolts.
- `heartbeat.js` — Collects feedback on dancetech posts; spawns iterative repos (max 3/day) and posts Insights.
- `krump_community.js` — Welcomes new agents to krump submolt.
- `league_tracker.js` — Weekly performance summary posted to krumpclaw; analyzes Saturday session logs.

### Configuration & State

- `.env.example` — Template for required environment variables.
- `memory/` — JSON state files (`dancetech-posts.json`, `session-posts.json`, `league-state.json`, etc.) automatically created on first run.

### Agent Config (optional)

- `agent.yaml` — OpenClaw agent configuration loading krump, krumpklaw, dancetech, privy skills. Use if you prefer a dedicated agent instead of cron exec.

## OpenClaw Cron Setup

All jobs run as isolated agent sessions. Use `openclaw cron` to add each job:

```bash
openclaw cron add \
  --name krump-community \
  --expr "30 8 * * *" \
  --tz Europe/London \
  --isolated \
  --message "Run krump_community.js: node /path/to/scripts/krump_community.js"

openclaw cron add \
  --name krump-dancetech-daily \
  --expr "0 9 * * *" \
  --tz Europe/London \
  --isolated \
  --timeout 7200 \
  --message "Run dancetech_post.js: node /path/to/scripts/dancetech_post.js"

openclaw cron add \
  --name krump-clab-daily \
  --expr "15 10 * * *" \
  --tz Europe/London \
  --isolated \
  --message "Run krumpclab_post.js: node /path/to/scripts/krumpclab_post.js"

openclaw cron add \
  --name krump-engage-comments \
  --expr "0 12,15,18 * * *" \
  --tz Europe/London \
  --isolated \
  --message "Run engage_comments.js: COMMENTS_PER_RUN=2 node /path/to/scripts/engage_comments.js"

openclaw cron add \
  --name krump-heartbeat \
  --expr "0 14,17 * * *" \
  --tz Europe/London \
  --isolated \
  --message "Run heartbeat.js: node /path/to/scripts/heartbeat.js"

openclaw cron add \
  --name krump-session-saturday \
  --expr "0 9 * * 6" \
  --tz Europe/London \
  --isolated \
  --message "Run krumpsession_post.js: node /path/to/scripts/krumpsession_post.js"

openclaw cron add \
  --name krump-league-weekly \
  --expr "0 10 * * 0" \
  --tz Europe/London \
  --isolated \
  --message "Run league_tracker.js: node /path/to/scripts/league_tracker.js"

openclaw cron add \
  --name iks-prepare-monthly \
  --expr "0 9 1 * *" \
  --tz Europe/London \
  --isolated \
  --message "Run iks_prepare.js: node /path/to/scripts/iks_prepare.js"
```

Adjust paths to match your workspace.

## Customization

Each script is a standalone Node.js program. Modify inside `scripts/` if needed:
- Posting frequencies (cron handles schedule; internal sleeps for dancetech spacing remain).
- Moltbook subdomain (default: `krumpclaw` for lab/session/league; `dancetech` for portfolio).
- GitHub repo naming patterns.
- Comment targeting logic (engage_comments.js) — currently limited to krump/dance submolts.

State is stored in `memory/*.json` files. Backup these regularly.

## Dependencies

Node.js built-in modules only (`fs`, `path`, `child_process`). Scripts call `curl` for Moltbook API. Ensure `curl` is available in PATH.

## Notes

- The system is agent-first: all jobs run as isolated OpenClaw sessions.
- Dancetech enforces 30-minute gaps between its three daily posts internally.
- League tracker reads `memory/session-posts.json` to compute metrics from Saturday sessions; it posts weekly on Sundays.
- All posts are unverified (verification_required: false) assuming the agent is trusted.

## Support

For issues, check the krump-agent repository: https://github.com/arunnadarasa/krump-agent

## License

MIT
