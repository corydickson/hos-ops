# [STAGING] Task 4: Test staging DAO policy

**Environment:** `STAGING`  
**Created by:** fastnear-hos.near

## Background

Proposal to call `get_config` on staging voting contract `vote.stagingdao.near`.
It's a NOOP, which will return a config in the transaction on success.

### For reference: DAO proposal creation process


Encode inner args:

```bash
export INNER_ARGS='{}'
export INNER_ARGS_B64=$(echo $INNER_ARGS | base64)
echo $INNER_ARGS_B64
# Expected: e30K
```

Proposal args:

```bash
export PROPOSAL_DESCRIPTION='STAGING: Call `get_config` on `vote.stagingdao.near`'
export CONTRACT_ID='vote.stagingdao.near'
export PROPOSAL_ARGS='{"proposal": {"description": "'$PROPOSAL_DESCRIPTION'","kind":{"FunctionCall":{"receiver_id":"'$CONTRACT_ID'","actions":[{"method_name":"get_config","args":"'$INNER_ARGS_B64'","deposit":"0","gas":"20000000000000"}]}}}}'
echo $PROPOSAL_ARGS
# Expected: {"proposal": {"description": "STAGING: Call `get_config` on `vote.stagingdao.near`","kind":{"FunctionCall":{"receiver_id":"vote.stagingdao.near","actions":[{"method_name":"get_config","args":"e30K","deposit":"0","gas":"20000000000000"}]}}}}
export PROPOSAL_ARGS_B64=$(echo $PROPOSAL_ARGS | base64)
echo $PROPOSAL_ARGS_B64
# Expected: eyJwcm9wb3NhbCI6IHsiZGVzY3JpcHRpb24iOiAiU1RBR0lORzogQ2FsbCBgZ2V0X2NvbmZpZ2Agb24gYHZvdGUuc3RhZ2luZ2Rhby5uZWFyYCIsImtpbmQiOnsiRnVuY3Rpb25DYWxsIjp7InJlY2VpdmVyX2lkIjoidm90ZS5zdGFnaW5nZGFvLm5lYXIiLCJhY3Rpb25zIjpbeyJtZXRob2RfbmFtZSI6ImdldF9jb25maWciLCJhcmdzIjoiZTMwSyIsImRlcG9zaXQiOiIwIiwiZ2FzIjoiMjAwMDAwMDAwMDAwMDAifV19fX19Cg==
```

CLI command to create the proposal:

```bash
export DAO_ACCOUNT="hos-root-staging.sputnik-dao.near"
export SIGNER_ACCOUNT_ID="fastnear-hos.near"
near contract call-function as-transaction $DAO_ACCOUNT add_proposal base64-args $PROPOSAL_ARGS_B64 prepaid-gas '100.0 Tgas' attached-deposit '0.1 NEAR' sign-as $SIGNER_ACCOUNT_ID network-config mainnet 
```

CLI command to create the proposal:

```bash
near contract call-function as-transaction $DAO_ACCOUNT add_proposal base64-args $PROPOSAL_ARGS_B64 prepaid-gas '100.0 Tgas' attached-deposit '0.1 NEAR' sign-as $SIGNER_ACCOUNT_ID network-config mainnet 
# Proposal ID returned: 1
# TX ID: https://nearblocks.io/txns/Gq8XCZXECbnNpJBnW6P46NMgqo3eWGKxNeZnVYZQhC1U
```

## Proposal Details

**Proposal ID:** `1`

**Description:** STAGING: Call `get_config` on `vote.stagingdao.near`

**Expected result:** NOOP. The transaction will contain the current config in the execution. This can be checked in the block explorer. We're interested in checking the multi-sig policy of the DAO.

## Verification Steps

> **⚠️ ENVIRONMENT CHECK**: This is a `STAGING` task. Verify all contract addresses and proposals match the STAGING environment.

### Step 1: Check the Proposal

Use the NEAR CLI to retrieve the proposal:

```bash
# STAGING environment on top of STAGING DAO `hos-root-staging.sputnik-dao.near`
near contract call-function as-read-only hos-root-staging.sputnik-dao.near get_proposal json-args '{"id": 1}' network-config mainnet now
```

### Step 2: Verify Target Contract and Parameters

- [ ] Confirm DAO (e.g. `hos-root-staging.sputnik-dao.near`) matches STAGING DAO
- [ ] Confirm target contract from the proposal (e.g. `vote.stagingdao.near`) matches STAGING environment
- [ ] Verify the proposal kind of `FunctionCall`
- [ ] Verify the function being called is correct (e.g. `get_config`)

### Step 3: Additional Checks

- [ ] Review the proposer account
- [ ] Verify the proposal status
- [ ] Check voting requirements
- [ ] Confirm no conflicting pending proposals

## Expected Results (you should double-check values here)

- The DAO account should be `hos-root-staging.sputnik-dao.near`
- The proposal should be in the `InProgress` status
- The proposal kind should be `FunctionCall`
- The proposal target account ID should be `vote.stagingdao.near`
- The method name should be `get_config`
- Arguments are: `e30K` which are base64 encoded args of `{}`

## Transaction Links

- Proposal creation transaction: https://nearblocks.io/txns/Gq8XCZXECbnNpJBnW6P46NMgqo3eWGKxNeZnVYZQhC1U

## Notes
