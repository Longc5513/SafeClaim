<p align="center">
  <img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExajBqZ3JqNnF5eGJ6aGZ6eGZ6eGZ6eGZ6eGZ6eGZ6eGZ6eGZ6eA/agentbot.gif" width="120" alt="AgentBot" />
</p>

<h1 align="center">SafeClaim</h1>

<p align="center">
  <strong>Product-Safety Recall Compensation — GenLayer Intelligent Contract</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/GenLayer-Intelligent%20Contract-7c3aed?style=flat-square&logo=data:image/svg+xml;base64,..." alt="GenLayer" />
  <img src="https://img.shields.io/badge/Python-3.11+-3776ab?style=flat-square&logo=python&logoColor=white" alt="Python" />
  <img src="https://img.shields.io/badge/Status-Active-10b981?style=flat-square" alt="Status" />
  <img src="https://img.shields.io/badge/License-MIT-ffd700?style=flat-square" alt="License" />
</p>

---

## What It Does

SafeClaim is a GenLayer Intelligent Contract that escrows per-unit compensation for product-safety recalls. A manufacturer (or insurer) deposits funds into a recall pool; affected consumers file claims by submitting evidence URLs (receipt pages, product registration portals, recall-notice databases). Validators fetch those URLs live inside consensus, cross-reference them against the recall criteria, and render a verdict: **APPROVED**, **NEEDS_EVIDENCE**, or **DENIED**.

Approved claims release escrowed funds to the consumer. Denied claims allow retry with new evidence. Either party can raise a dispute that routes to a designated arbiter.

## Why GenLayer

No other platform lets a smart contract **fetch a live web page inside validator consensus** and have multiple independent validators agree on whether the page's content supports a claim. Traditional oracles trust one operator; multisigs add delay; deterministic chains can't read the web at all. SafeClaim uses GenLayer's `gl.nondet.web.render()` + `gl.eq_principle.prompt_comparative()` to make the evidence judgment itself a consensus-verified fact.

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    SafeClaim Contract                    │
├──────────────┬──────────────┬───────────────────────────┤
│  RecallPool  │    Claim     │  Consensus Judge          │
│  (escrow)    │  (evidence)  │  (web.render + exec_prompt)│
├──────────────┼──────────────┼───────────────────────────┤
│  manufacturer│  consumer    │  APPROVED → pay consumer   │
│  deposits    │  submits     │  DENIED   → reject         │
│  per-unit $  │  evidence    │  NEEDS    → allow retry    │
│  + criteria  │  URLs        │                           │
└──────────────┴──────────────┴───────────────────────────┘
```

## Key Methods

| Method | Who | What |
|--------|-----|------|
| `create_pool` | Manufacturer | Deposit escrow, set recall criteria + per-unit amount |
| `file_claim` | Consumer | Submit evidence URLs for a recall pool |
| `resolve_claim` | Anyone | Trigger AI consensus verification |
| `retry_claim` | Consumer | Resubmit with new evidence after NEEDS_EVIDENCE |
| `raise_dispute` | Either party | Pause claim, route to arbiter |
| `resolve_dispute` | Arbiter | Final APPROVE/REJECT |
| `close_pool` | Manufacturer | Close pool, refund unclaimed balance |
| `expire_stale_claims` | Anyone | Expire claims past stale threshold |

## Consensus Design

Two non-deterministic operations, nothing more:

1. **`gl.nondet.web.render()`** — fetch each evidence URL as live text
2. **`gl.nondet.exec_prompt()`** — judge whether fetched text demonstrates recall-affected status

Everything else — escrow accounting, state transitions, dispute routing, refund logic — is deterministic Python. Safe-failure direction: any unparseable or ambiguous model output defaults to `NEEDS_EVIDENCE`, never a fabricated `APPROVED`/`DENIED`.

## Deploy

```bash
genlayer deploy --contract contracts/safe_claim.py
```

## License

MIT

## Deployment

| Network | Contract Address | Deploy TX |
|---------|-----------------|-----------|
| Bradbury (testnet) | `0x5cfdc943dDE197eCc587f5C67a62600E65aBd591` | `0x5122bdbd...` |

- Explorer: https://explorer-bradbury.genlayer.com/contract/0x5cfdc943dDE197eCc587f5C67a62600E65aBd591
- Deploy TX: https://explorer-bradbury.genlayer.com/tx/0x5122bdbd4c9f52cdeb1e7dad069238c01105bb0401d73d65042a4ce8e5f5f9f9
