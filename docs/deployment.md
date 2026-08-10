# Deployment — SafeClaim

## Prerequisites

- `py-genlayer` installed (`pip install py-genlayer`)
- `genlayer` CLI installed (`npm install -g genlayer`)
- Wallet with funds on target network

## Deploy

```bash
genlayer deploy --contract contracts/safe_claim.py
```

The constructor takes no arguments. The deployer becomes the contract owner.

## Network

- **StudioNet**: Chain ID 61999 (0xF22F), RPC: `https://studio.genlayer.com/api`
