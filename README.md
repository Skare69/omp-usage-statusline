# omp usage statusline

Show your provider's coding-plan quota (5-hour / daily / weekly / monthly windows with reset
countdowns) directly in the [Oh My Pi](https://github.com/can1357/oh-my-pi) (omp) status line.

Rendered result (Z.ai GLM coding plan, lite tier):

```
⏱ lite · 5h 72% (3h 30m) · 7d 54% (2d 10h)
```

No extension, no polling script — omp ≥ 18 has a built-in `usage` status-line segment fed by the
same provider-usage system as the `omp usage` command. The catch: the `default` status-line preset
has no slot for it, so you switch to the `custom` preset and add the segment yourself.

## Install

Copy [`statusLine.yml`](./statusLine.yml) into `~/.omp/agent/config.yml` (merge with any existing
`statusLine:` key), then restart omp.

The segment list is the `default` preset's segments verbatim plus `usage` on the right side, so the
line looks exactly like before with the quota readout appended.

## Notes

- Shows the **active provider's** usage — switch models/providers and the readout follows
  (e.g. `zai/*` models all share one Z.ai coding-plan quota).
- Works for any provider with a usage integration: Z.ai, Anthropic, OpenAI Codex, Google, etc.
  Run `omp usage` to see everything omp can see, or `omp gallery --surface=segment --plain` to
  preview all status-line segments.
- Colors escalate automatically as a window fills (normal → warning → error).

## How it works under the hood (Z.ai example)

omp fetches provider usage itself. For Z.ai's GLM coding plan the endpoint is:

```
GET https://api.z.ai/api/monitor/usage/quota/limit
Authorization: Bearer <coding-plan API key>
```

returning `data.limits[]` entries (`number` + `unit`, where unit `3` = hours and `6` = weeks,
`percentage` used, `nextResetTime` epoch ms) and `data.level` (plan tier) — the same numbers the
[z.ai dashboard](https://z.ai/manage-apikey/coding-plan/personal/usage) renders.
