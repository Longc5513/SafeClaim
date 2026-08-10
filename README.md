<p align="center">
  <img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExajBqZ3JqNnF5eGJ6aGZ6eGZ6eGZ6eGZ6eGZ6eGZ6eGZ6eGZ6eA/agentbot.gif" width="120" alt="AgentBot" />
</p>

<h1 align="center">SafeClaim</h1>

<p align="center">
  <strong>Product-Safety Recall Compensation — GenLayer Intelligent Contract</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/GenLayer-Intelligent%20Contract-7c3aed?style=flat-square" alt="GenLayer" />
  <img src="https://img.shields.io/badge/Python-3.11+-3776ab?style=flat-square&logo=python&logoColor=white" alt="Python" />
  <img src="https://img.shields.io/badge/Network-Bradbury%20Testnet-10b981?style=flat-square" alt="Network" />
  <img src="https://img.shields.io/badge/License-MIT-ffd700?style=flat-square" alt="License" />
</p>

---

## What It Does

SafeClaim is a push-based product-safety recall compensation service on GenLayer. A manufacturer deposits per-unit compensation into a recall pool. Affected consumers file claims by submitting evidence URLs (receipts, product registration, recall databases). Validators fetch those URLs live inside consensus and judge: **APPROVED** (consumer is affected, pay them), **NEEDS_EVIDENCE** (ambiguous, allow retry), or **DENIED** (not affected).

The verdict is **pushed** to the consumer's callback contract via `emit(on="finalized")` — consumers never poll.

## Why GenLayer

No other platform lets a smart contract fetch live web pages inside validator consensus and have multiple independent validators agree on whether the content supports a recall claim. SafeClaim uses `gl.nondet.web.render()` + `gl.eq_principle.prompt_comparative()` for trustless evidence verification.

## Key Methods

| Method | Who | Type | What |
|--------|-----|------|------|
| `create_recall` | Manufacturer | payable | Deposit escrow, set criteria + per-unit amount |
| `file_claim` | Consumer | write | Submit evidence URLs for a recall |
| `resolve_claim` | Anyone | write | Trigger AI consensus + push verdict to callback |
| `retry_claim` | Consumer | write | Resubmit evidence after NEEDS_EVIDENCE |
| `reclaim_stale_claim` | Consumer | write | Reclaim stale claims |
| `raise_dispute` | Either party | write | Route to arbiter |
| `resolve_dispute` | Arbiter | write | Final APPROVE/REJECT |
| `spawn_instance` | Anyone | write | Factory: deploy child SafeClaim instance |
| `close_recall` | Manufacturer | write | Close pool, refund unclaimed balance |

## Architecture

```
Consumer ──file_claim──> SafeClaim ──resolve_claim──> Validators
                              │                            │
                              │  gl.nondet.web.render()    │
                              │  gl.nondet.exec_prompt()   │
                              │                            │
                              ◄──push verdict──────────────┘
                              │
                         emit_transfer → Consumer
```

## Deployment

| Network | Contract Address | Deploy TX |
|---------|-----------------|-----------|
| Bradbury (testnet) | `0xc55D56f0EceFe6F03F86774141B41051e4FBE046` | `0x5b57bcb8...` |

- Explorer: https://explorer-bradbury.genlayer.com/address/0xc55D56f0EceFe6F03F86774141B41051e4FBE046
- Deploy TX: https://explorer-bradbury.genlayer.com/tx/0x5b57bcb8ab58dcd0684f669d7c0ec9075adb84544a4fcb5d937104915fe9e64a

## Deploy

```bash
genlayer deploy --contract contracts/safe_claim.py
```

## License

MIT
