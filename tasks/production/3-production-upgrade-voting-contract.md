# Task 3: PRODUCTION: Upgrade voting contract

**Environment:** `PRODUCTION`  
**Created by:** lane.near

## Background

Proposal to upgrade the voting contract to v1.0.2 for the **production** HoS contracts.

Links: 
- Commit: `ff25d7de8af1a778b0c1b0df2084e05cff1424b8`
- Github commit: https://github.com/houseofstake/house-of-stake-contracts/commit/ff25d7de8af1a778b0c1b0df2084e05cff1424b8
- The Github changes: https://github.com/houseofstake/house-of-stake-contracts/compare/4c9079df73020b9e35dc807146404f7415b0a0be...ff25d7de8af1a778b0c1b0df2084e05cff1424b8

This task uses `echo -n` to avoid adding a newline before base64 encoding.

### For reference: DAO proposal creation process

Rebuilding contracts

```bash
git checkout ff25d7de8af1a778b0c1b0df2084e05cff1424b8
./build_release.sh
```

We're interested in the voting binary located at `res/release/voting_contract.wasm`
```bash
export CONTRACT_HASH=$(cat res/release/voting_contract.wasm | sha256sum | awk '{ print $1 }' | xxd -r -p | base58)
echo $CONTRACT_HASH
# Expected: FNk94kmPkxdrDV7mTYBEiq1HCozsCY5Faqif9dMt4WHk
ls -l res/release/voting_contract.wasm
# Size: 221492
```

Uploading the contract binary to the DAO using `store_blob`, we'll need to attach `2.22 NEAR` to cover storage costs.

```bash
export DAO_ACCOUNT="hos-root.sputnik-dao.near"
export SIGNER_ACCOUNT_ID="fastnear-hos.near"
near contract call-function as-transaction $DAO_ACCOUNT store_blob file-args res/release/voting_contract.wasm prepaid-gas '300.0 Tgas' attached-deposit '2.22 NEAR' sign-as $SIGNER_ACCOUNT_ID network-config mainnet
# Returned result matches: "FNk94kmPkxdrDV7mTYBEiq1HCozsCY5Faqif9dMt4WHk"
# TX ID: https://nearblocks.io/txns/5iJhctZP72Vdnz3kzxWS6Suh8WH52Pp6z4uA9X6YwGqX
```

Proposal args:

```bash
export PROPOSAL_DESCRIPTION='PRODUCTION: Upgrade voting contract to `1.0.2`'
export CONTRACT_ID='vote.dao'
export PROPOSAL_ARGS='{"proposal": {"description": "'$PROPOSAL_DESCRIPTION'","kind":{"UpgradeRemote":{"receiver_id":"'$CONTRACT_ID'","method_name":"upgrade","hash":"'$CONTRACT_HASH'"}}}}'
echo $PROPOSAL_ARGS
# Expected: {"proposal": {"description": "PRODUCTION: Upgrade voting contract to `1.0.2`","kind":{"UpgradeRemote":{"receiver_id":"vote.dao","method_name":"upgrade","hash":"FNk94kmPkxdrDV7mTYBEiq1HCozsCY5Faqif9dMt4WHk"}}}}
export PROPOSAL_ARGS_B64=$(echo -n $PROPOSAL_ARGS | base64)
echo $PROPOSAL_ARGS_B64
# Expected: eyJwcm9wb3NhbCI6IHsiZGVzY3JpcHRpb24iOiAiUFJPRFVDVElPTjogVXBncmFkZSB2b3RpbmcgY29udHJhY3QgdG8gYDEuMC4yYCIsImtpbmQiOnsiVXBncmFkZVJlbW90ZSI6eyJyZWNlaXZlcl9pZCI6InZvdGUuZGFvIiwibWV0aG9kX25hbWUiOiJ1cGdyYWRlIiwiaGFzaCI6IkZOazk0a21Qa3hkckRWN21UWUJFaXExSENvenNDWTVGYXFpZjlkTXQ0V0hrIn19fX0=

```

CLI command to create the proposal:

```bash
near contract call-function as-transaction $DAO_ACCOUNT add_proposal base64-args $PROPOSAL_ARGS_B64 prepaid-gas '100.0 Tgas' attached-deposit '0.1 NEAR' sign-as $SIGNER_ACCOUNT_ID network-config mainnet 
# Proposal ID returned: 16
# TX ID: https://nearblocks.io/txns/AVwFnPPGyyfidj6eAcSrUZGtt4JDEhbmzBZjACYCyyKV
```

## Proposal Details

**Proposal ID:** #16

**Description:** PRODUCTION: Upgrade voting contract to `1.0.2`

**Expected result:** Once executed the proposal will call `upgrade` on the `vote.dao` contract with arguments of new contract binary with hash `FNk94kmPkxdrDV7mTYBEiq1HCozsCY5Faqif9dMt4WHk`. If the upgrade is successful, the voting contract will be running version `1.0.2`.

## Verification Steps

> **⚠️ ENVIRONMENT CHECK**: This is a `PRODUCTION` task. Verify all contract addresses and proposals match the PRODUCTION environment.

### Step 1: Check the Proposal

Use the NEAR CLI to retrieve the proposal:

```bash
# PRODUCTION environment
near contract call-function as-read-only hos-root.sputnik-dao.near get_proposal json-args '{"id": 16}' network-config mainnet now
```

### Step 2: Decode and Verify Arguments

Decode the inner arguments to verify the actual parameters:

```bash
near contract call-function as-read-only hos-root.sputnik-dao.near get_proposal json-args '{"id": 16}' network-config mainnet now | jq '.kind.FunctionCall.actions[0].args | @base64d | fromjson'
```

### Step 3: Verify Target Contract

- [ ] **CRITICAL**: Confirm target contract matches PRODUCTION environment
- [ ] Verify the function being called is correct
- [ ] Check all parameters are as specified in the proposal description
  - [ ] Build release artifacts (wasm) based on commit specified by the release
  - [ ] Check the voting contract hash from the arguments matches the expected hash
- [ ] If PRODUCTION, double-check proposal description for any STAGING indicators

### Step 4: Additional Checks

- [ ] Review the proposer account
- [ ] Verify the proposal status
- [ ] Check voting requirements
- [ ] Confirm no conflicting pending proposals

## Expected Results

- The DAO account should be `hos-root.sputnik-dao.near`
- The proposal should be in the `InProgress` status
- The proposal kind should be `UpgradeRemote`
- The proposal target account ID should be `vote.dao`
- The method name should be `upgrade`
- The lockup contract hash should be `FNk94kmPkxdrDV7mTYBEiq1HCozsCY5Faqif9dMt4WHk`

## Transaction Links

- Previous task: [Task 2: Update PROD DAO policy](./2-update-prod-dao-policy.md)
- Store blob transaction: https://nearblocks.io/txns/5iJhctZP72Vdnz3kzxWS6Suh8WH52Pp6z4uA9X6YwGqX
- Proposal creation transaction: https://nearblocks.io/txns/AVwFnPPGyyfidj6eAcSrUZGtt4JDEhbmzBZjACYCyyKV

## Notes

This upgrade was already tested in STAGING.

