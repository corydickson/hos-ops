# Task 3: NEAR Rewards Merkle Verification

**Environment:** `PRODUCTION`  
**Created by:** voteagora.near

## Background

This proposal will be a temperature check on the merkle rewards root to be published for the first campaign. Once this proposal has passed it will be supplied to the Claims contract
for users to then recieve their rewards. 


## Proposal Details

**Proposal ID:** #15

## Action to be Taken

Both users and memebers of the security council should use the Agora Merkle Tree software [TODO: ] to generate a proof with the trie artifacts provided by Gauntlet

How to verify:

- User account Id
- Amount to be claimed
- Lockup contract

bash
```
TODO
```

Retrieving the merkle root:
```
TODO
```

The SC/NF should determine a valid claim period from the proposed start date. The recommendation is 30 days from the timestamp of the campaign creation. 

## Verification Steps

> **⚠️ ENVIRONMENT CHECK**: This is a `PRODUCTION` task. Verify all contract addresses and proposals match the PRODUCTION environment.

### Step 1: Check the Proposal

Use the NEAR CLI to retrieve the proposal:

```bash
# PRODUCTION environment
near contract call-function as-read-only hos-root.sputnik-dao.near get_proposal json-args '{"id": 15}' network-config mainnet now
```

### Step 2: Decode and Verify Arguments

Decode the inner arguments to verify the actual parameters:

```bash
near contract call-function as-read-only hos-root.sputnik-dao.near get_proposal json-args '{"id": 15}' network-config mainnet now | jq '.kind.FunctionCall.actions[0].args | @base64d | fromjson'
```

### Step 3: Verify Target Contract

- [ ] **CRITICAL**: Confirm target contract matches PRODUCTION environment
- [ ] Verify the function being called is correct
- [ ] Check all parameters are as specified in the proposal description
- [ ] If PRODUCTION, double-check proposal description for any STAGING indicators

### Step 4: Additional Checks

- [ ] Review the proposer account
- [ ] Verify the proposal status
- [ ] Check voting requirements
- [ ] Confirm no conflicting pending proposals

## Expected Results

Verification should show that the merkle root used should produce a successful merkle claim check.

## Transaction Links

- Proposal creation transaction: [TBD]

## Notes
