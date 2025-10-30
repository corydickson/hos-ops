# WORK IN PROGRESS. NOT READY BELOW. TO BE DONE.

# [META] Task 2: Update PROD DAO policy

**Environment:** `PRODUCTION`  
**Created by:** `fastnear-hos.near`

## Background

Proposal to update DAO policy config into simple 3/6 majority admins for all actions.

### For reference: DAO proposal creation process

Drafting policy.

```bash
export PROPOSAL_ARGS='{
  "proposal": {
    "description": "* Title: Update Policy - Only Admin group with 3/6 for each action",
    "kind": {
      "ChangePolicy": {
        "policy": {
          "roles": [
            {
              "name": "Admin",
              "kind": {
                "Group": [
                  "as.near",
                  "e953bb69d1129e4da87b99739373884a0b57d5e64a65fdc868478f22e6c31eac",
                  "c65255255d689f74ae46b0a89f04bbaab94d3a51ab9dc4b79b1e9b61e7cf6816",
                  "fastnear-hos.near",
                  "lane.near",
                  "root.near"
                ]
              },
              "permissions": [
                "*:*"
              ],
              "vote_policy": {}
            }
          ],
          "default_vote_policy": {
            "weight_kind": "RoleWeight",
            "quorum": "0",
            "threshold": "3"
          },
          "proposal_bond": "100000000000000000000000",
          "proposal_period": "604800000000000",
          "bounty_bond": "100000000000000000000000",
          "bounty_forgiveness_period": "604800000000000"
        }
      }
    }
  }
}'
export PROPOSAL_ARGS_B64=$(echo $PROPOSAL_ARGS | base64)
echo $PROPOSAL_ARGS_B64
# Expected: eyAicHJvcG9zYWwiOiB7ICJkZXNjcmlwdGlvbiI6ICIqIFRpdGxlOiBVcGRhdGUgUG9saWN5IC0gT25seSBBZG1pbiBncm91cCB3aXRoIDMvNiBmb3IgZWFjaCBhY3Rpb24iLCAia2luZCI6IHsgIkNoYW5nZVBvbGljeSI6IHsgInBvbGljeSI6IHsgInJvbGVzIjogWyB7ICJuYW1lIjogIkFkbWluIiwgImtpbmQiOiB7ICJHcm91cCI6IFsgImFzLm5lYXIiLCAiZTk1M2JiNjlkMTEyOWU0ZGE4N2I5OTczOTM3Mzg4NGEwYjU3ZDVlNjRhNjVmZGM4Njg0NzhmMjJlNmMzMWVhYyIsICJjNjUyNTUyNTVkNjg5Zjc0YWU0NmIwYTg5ZjA0YmJhYWI5NGQzYTUxYWI5ZGM0Yjc5YjFlOWI2MWU3Y2Y2ODE2IiwgImZhc3RuZWFyLWhvcy5uZWFyIiwgImxhbmUubmVhciIsICJyb290Lm5lYXIiIF0gfSwgInBlcm1pc3Npb25zIjogWyAiKjoqIiBdLCAidm90ZV9wb2xpY3kiOiB7fSB9IF0sICJkZWZhdWx0X3ZvdGVfcG9saWN5IjogeyAid2VpZ2h0X2tpbmQiOiAiUm9sZVdlaWdodCIsICJxdW9ydW0iOiAiMCIsICJ0aHJlc2hvbGQiOiAiMyIgfSwgInByb3Bvc2FsX2JvbmQiOiAiMTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwIiwgInByb3Bvc2FsX3BlcmlvZCI6ICI2MDQ4MDAwMDAwMDAwMDAiLCAiYm91bnR5X2JvbmQiOiAiMTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwIiwgImJvdW50eV9mb3JnaXZlbmVzc19wZXJpb2QiOiAiNjA0ODAwMDAwMDAwMDAwIiB9IH0gfSB9IH0K
```

CLI command to create the proposal:

```bash
export DAO_ACCOUNT="hos-root.sputnik-dao.near"
near contract call-function as-transaction $DAO_ACCOUNT add_proposal base64-args $PROPOSAL_ARGS_B64 prepaid-gas '100.0 Tgas' attached-deposit '0.1 NEAR' sign-as $SIGNER_ACCOUNT_ID network-config mainnet 
# Proposal ID returned: TBD
```

## Proposal Details

**Proposal ID:** `TBD`

**Description:** Title: Update Policy - Only Admin group with 3/6 for each action

**Expected result:** Once executed the proposal change the DAO policy into simple 3/6 multisig.

## Verification Steps

> **⚠️ ENVIRONMENT CHECK**: This is a `PRODUCTION` task. Verify all contract addresses and proposals match the PRODUCTION environment.

WORK IN PROGRESS. NOT READY BELOW. TO BE DONE.

<!--

### Step 1: Check the Proposal

Use the NEAR CLI to retrieve the proposal:

```bash
# STAGING environment
near contract call-function as-read-only hos-root.sputnik-dao.near get_proposal json-args '{"id": 13}' network-config mainnet now
```

### Step 2: Verify Target Contract and Parameters

- [ ] **CRITICAL**: Confirm target contract from the proposal (e.g. `vote.stagingdao.near`) matches STAGING environment
- [ ] Verify the proposal kind of `UpgradeRemote`
- [ ] Verify the function being called is `upgrade`
- [ ] Check all parameters are as specified in the proposal description
  - [ ] Build release artifacts (wasm) based on commit specified by the release
  - [ ] Check the voting contract hash from the arguments matches the expected hash

### Step 3: Additional Checks

- [ ] Review the proposer account
- [ ] Verify the proposal status
- [ ] Check voting requirements
- [ ] Confirm no conflicting pending proposals

## Expected Results (you should double-check values here)

- The DAO account should be `hos-root.sputnik-dao.near`
- The proposal should be in the `InProgress` status
- The proposal kind should be `UpgradeRemote`
- The proposal target account ID should be `vote.stagingdao.near`
- The method name should be `upgrade`
- The lockup contract hash should be `FNk94kmPkxdrDV7mTYBEiq1HCozsCY5Faqif9dMt4WHk`

## Transaction Links

- Store blob transaction: https://nearblocks.io/txns/5iJhctZP72Vdnz3kzxWS6Suh8WH52Pp6z4uA9X6YwGqX
- Proposal creation transaction: https://nearblocks.io/txns/9Hr1bh8eogkRMybP7brFUR7d2puAxJKk9UgLdgk6xgZd

## Notes

-->
