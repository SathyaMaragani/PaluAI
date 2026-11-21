# PaluAI: a free, self-hosted "Jules"

An async coding agent that costs $0. GitHub Actions is the cloud VM, the Gemini API free tier is the brain,
and Google's official [`run-gemini-cli`](https://github.com/google-github-actions/run-gemini-cli) action is the agent.

## How it works

```
issue + "@gemini-cli <task>" ──► gemini-dispatch ──► gemini-invoke ──► posts a PLAN comment
"@gemini-cli /approve <plan-comment-id>" ──► gemini-plan-execute ──► branch + commits + PR
new PR ──► gemini-review (AI review)  +  tests.yml (runs the tests)
new issue ──► gemini-triage (auto labels)
```

Only the repo OWNER, MEMBERs, and COLLABORATORs can trigger the agent. Strangers' comments are ignored.

## One-time setup (do it yourself)

1. Get a free API key at https://aistudio.google.com/apikey and save it as a repo secret:
   `gh secret set GEMINI_API_KEY` (paste the key when prompted).
2. In the repo, go to **Settings → Actions → General → Workflow permissions** and choose **Read and write permissions**.
   Then tick **Allow GitHub Actions to create and approve pull requests**.
3. Optional: set the repo variable `GEMINI_MODEL` (for example `gemini-2.5-flash`) for more free-tier headroom.

## Use it

1. Open an issue, for example: *"Add `multiply(a, b)` to calc.py with a test."*
2. Comment `@gemini-cli implement this`. A few minutes later it posts a plan.
3. Read the plan. If it looks right, comment `@gemini-cli /approve <link-or-id-of-the-plan-comment>`.
4. It opens a PR. Tests and an AI review run on that PR. Review and merge it like any other PR.

Other commands: `@gemini-cli /review` on a PR, `@gemini-cli /triage` on an issue, and `@gemini-cli <question>` for questions.

## Option B: Palu agent (Aider + free GitHub Models, no API key)

Comment `@palu` on an issue (optionally followed by extra instructions). [Aider](https://aider.chat) edits the code
using a free [GitHub Models](https://github.com/marketplace/models) model, runs `python -m unittest`, and opens a PR.
There's no plan-and-approve step: you review the PR instead. Change the model with the repo variable `PALU_MODEL`
(default `openai/gpt-4.1`, e.g. `deepseek/DeepSeek-V3-0324` or `meta/Llama-4-Maverick-17B-128E-Instruct-FP8`).

## Limits

- The agent edits files through the GitHub API. It can't run shell commands, so `tests.yml` checks its work on the PR.
- Free tier: Gemini rate limits apply. Private repos get 2000 Actions minutes a month, public repos are unlimited.
