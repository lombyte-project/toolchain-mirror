# Contributing

This repository only holds copies. A new file needs: the upstream it comes
from, its license, its source if it is a GPL program, and its line in
`SHA256SUMS` and in the README table. Lombyte's setup pins the same SHA-256.

Commit subjects follow Conventional Commits: `<type>(<scope>): <summary>`,
lowercase type (`build`, `docs`, `ci`, `fix`, ...), at most 100 characters,
no trailing period.

## AI tools

Work prepared with an AI agent is fine; what enters the history is yours:

- No agent trailers (`Co-Authored-By:`, `Claude-Session:`) in commit messages, pull request
  descriptions or review comments. The one co-author line allowed credits a person, by their
  GitHub noreply address: `Co-authored-by: login <12345+login@users.noreply.github.com>`.
- No AI tool footers ("Generated with ...") and no links to AI chat or agent sessions
  (claude.ai, chatgpt.com): sessions are private, this repository is public.
- Author and committer are you, never an AI agent identity (an anthropic.com or openai.com
  address, Claude, Copilot or another bot).

The `ai-rules` workflow (`scripts/check-ai.py`) checks every pull request and push. Run the same
check on your own commits with the hook:

```sh
ln -sf ../../scripts/commit-msg.py .git/hooks/commit-msg
```

It drops agent trailers from your message and rejects the rest. A line that has to name what the
rules forbid (a test of the rules) carries `ai-rules: allow`.
