# Prop AMM on Canton — liquidity slot pattern (sketch)

Reference sketch for a whitelisted prop AMM where Trader swap against
admin owned liquidity from their own wallets, without seeing the admin's
reserves, pricing config, or each other, and without an admin application in
the trade path.

## Layout

```
prop-amm-on-Canton/
├── daml.yaml
└── daml/PropAmm.daml
```

## Build and test

```bash
dpm build
dpm test
```

## Off-ledger plumbing

### 1. Admin: capture the disclosure blobs

Subscribe to the update stream (or query the ACS) as the admin party with
`includeCreatedEventBlob: true`. The blob is what a third party needs in order
to submit a command against a contract they are not a stakeholder of.

### 2. Trader: submit

Use a deterministic `commandId` so retries dedupe rather than double swap.

## Operational prerequisites

- The Trader's participant node must have `dar` uploaded and vetted, or their submission is rejected before it reaches the synchronizer.
- All inputs must live on the same synchronizer. If the payment token is on the Global Synchronizer and slots are on a private one, reassign first.
- The admin party is a signatory on every slot, so the admin's participant node confirms every swap. That node needs real HA. The admin *application* does not.
- Traffic fees for the swap are paid by the submitting (Trader's) participant.
