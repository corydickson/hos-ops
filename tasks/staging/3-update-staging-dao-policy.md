# [META] Task 3: Update Staging DAO policy

**Environment:** `STAGING`  
**Created by:** `fastnear-hos.near`

## Background

Proposal to update staging DAO policy config into simple 3/6 majority admins for all actions.

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
export DAO_ACCOUNT="hos-root-staging.sputnik-dao.near"
export SIGNER_ACCOUNT_ID="fastnear-hos.near"
near contract call-function as-transaction $DAO_ACCOUNT add_proposal base64-args $PROPOSAL_ARGS_B64 prepaid-gas '100.0 Tgas' attached-deposit '0.1 NEAR' sign-as $SIGNER_ACCOUNT_ID network-config mainnet 
# Proposal ID returned: 0
# TX ID: https://nearblocks.io/txns/Bpq5r2tkXj4Bxj1TkPKRJWNyEjeBZTZRK542SpWAmQ1f

# Execute this proposal (ONLY FOR STAGING)
near contract call-function as-transaction $DAO_ACCOUNT act_proposal json-args '{"action":"VoteApprove", "id": 0}' prepaid-gas '300.0 Tgas' attached-deposit '0 NEAR' sign-as $SIGNER_ACCOUNT_ID
# TX ID: https://nearblocks.io/txns/EJ4aVNeYU4W5qoYVUCBdLFGUmXphdStHTmF3VB1ALu4K

# Verify new policy
near contract call-function as-read-only $DAO_ACCOUNT get_policy json-args {} network-config mainnet now
```

## Transaction Links

- Proposal creation transaction: https://nearblocks.io/txns/Bpq5r2tkXj4Bxj1TkPKRJWNyEjeBZTZRK542SpWAmQ1f
- Proposal execution: https://nearblocks.io/txns/EJ4aVNeYU4W5qoYVUCBdLFGUmXphdStHTmF3VB1ALu4K

## Notes
