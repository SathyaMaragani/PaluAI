# PaluAI: a free, self-hosted "Jules"

An async coding agent that costs $0. GitHub Actions is the cloud VM, a free model API (Groq by default) is the brain,
and [Aider](https://aider.chat) is the agent.

## How it works

```
issue + comment "@palu <extra instructions>" ──► palu-agent.yml ──► Aider edits code + runs tests ──► branch + PR
```

Only the repo OWNER, MEMBERs, and COLLABORATORs can trigger the agent. Strangers' comments are ignored.
There's no plan-and-approve step: you review the PR instead.

## One-time setup

1. Create a free key and save it as a repo secret:
   - **Groq** (default, ~1,000 requests/day, no card): https://console.groq.com/keys → `gh secret set GROQ_API_KEY`
   - **OpenRouter** (`:free` models, ~50 requests/day): https://openrouter.ai/keys → `gh secret set OPENROUTER_API_KEY`,
     then `gh variable set PALU_MODEL --body "openrouter/<model-id>:free"`
2. In the repo, go to **Settings → Actions → General → Workflow permissions** and choose **Read and write permissions**.
   Then tick **Allow GitHub Actions to create and approve pull requests**.

The repo variable `PALU_MODEL` picks the model (default `groq/openai/gpt-oss-120b`). The prefix picks the provider.

## Use it

1. Open an issue, for example: *"Add `multiply(a, b)` to calc.py with a test."*
2. Comment `@palu implement this`. A few minutes later a PR appears. Its description says whether the tests pass.
3. Review and merge it like any other PR.

## Limits

- Free-tier limits apply (Groq also caps tokens per minute). Private repos get 2000 Actions minutes a month,
  public repos are unlimited.
- The agent gets every tracked `.py` file, which suits small repos.
- PRs opened with the built-in token don't trigger other workflows, so the agent runs the tests itself.
