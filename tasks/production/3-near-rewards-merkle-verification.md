# Task 3: NEAR Rewards Merkle Verification

**Environment:** `STAGING`
**Created by:** voteagora.near

## Background

This proposal will outline the process for deploying the claims contract as well as the details for publishing the first campaign. 
Once this proposal has passed Gauntlet will have the ability to publish new campaigns

## Proposal Details

Contract repo: https://github.com/voteagora/near-merkle-claim
Claims UI Repo: https://github.com/voteagora/near-claim

Rebuilding contracts

```bash
git checkout c7587d357be507744c1e961d42f6fdc56e8803aa
cargo near build
-> reproducible-wasm
```

Retrieve the contract binary at `target/near/near_merkle_claim.wasm`

```bash
export CONTRACT_HASH=$(cat target/near/near_merkle_claim.wasm | sha256sum | awk '{ print $1 }' | xxd -r -p | base58)
echo $CONTRACT_HASH
# Expected: FMgQ6iPcerbU6whoDpYrj9LbMRBEnPV3brvgmHWCMqMA

Deploying contract to account created by the NF with keys revoked i.e. $CLAIMS_ACCOUNT_ID:

```
export CLAIMS_ACCOUNT_ID=[TBD]
export GAUNTLET_ACCOUNT_ID=[TBD]
export STORAGE_DEPOSIT="1 NEAR"
# 0.1 NEAR (enough for 10000 bytes)
export MIN_STORAGE_DEPOSIT="100000000000000000000000"

near contract deploy $CLAIMS_ACCOUNT_ID use-file $TARGET with-init-call new json-args '{
  "config": {
    "owner_account_id": "'$GAUNTLET_ACCOUNT_ID'",
    "min_storage_deposit": "'$MIN_STORAGE_DEPOSIT'"
  }
}' prepaid-gas '10.0 Tgas' attached-deposit "$STORAGE_DEPOSIT" network-config mainnet sign-with-keychain send
```

## Action to be Taken

Once the contract is deployed Gauntlet will be able to create a campaign. Below you will find instructions for completing the end-to-end claims process:

$GAUNTLET_ACCOUNT_ID - Gauntlet account ID who is the owner of the claims contract.

Both users and memebers of the security council should use the Agora Merkle Tree (software)[https://near-claim.vercel.app/] to generate a proof with the 
trie artifacts provided by Gauntlet. The parameters you see below are just an example and should be populated with real data (TBD).

How to verify:

- User account Id
- Amount to be claimed
- Lockup contract

This CSV will be a three column, comma seperated file. With fields: `address,lockup,amount`

Ex.

```
address,lockup,amount
example.near,lockup-example.venear.dao,1134994235059497700000000
...
```

Gauntlet will then upload this file using the near-claims-processor UI at location https://near-claim.vercel.app/campaigns/new

The merkle tree will be generated and the root will be displayed example:

```
0x0c443eee8b546bae8046a69e4762db5c39198f50fac5f139d0a455fc45a76749
```

or in the raw format:

```
[
    158,
    236,
    219,
    170,
    25,
    1,
    253,
    172,
    46,
    71,
    82,
    30,
    201,
    181,
    15,
    59,
    58,
    254,
    170,
    207,
    59,
    87,
    184,
    46,
    81,
    28,
    122,
    202,
    227,
    92,
    92,
    128
]
```

As well as the entire trie data structure: 

```
{
  "format": "near-v1",
  "tree": [
    "0x0c443eee8b546bae8046a69e4762db5c39198f50fac5f139d0a455fc45a76749",
    "0xac1d19b7898d30ebd5df0d49bd3f8baeac613281ec6d2e933979fa790620aa04",
    "0xec08963f75e400c9db83ee1a7b449d209ddeae5697bc31b233ab73ad650c2b0f",
    "0x5e8fa1b8ba11df6ec59ca8299114c4c1e49f11ddb46c0d6407f7ebd406a2f502",
    "0x14e93176748df38d3a732bd54c6f0bfdd9d017be32d95989736a7b6dabf74522"
  ],
  "values": [
    {
      "value": {
        "account": "zaki.near",
        "lockup": "91eb7cf64735a35fa614c32207390ca74b622e5b.venear.dao",
        "amount": "200000000000000000000000"
      },
      "treeIndex": 4
    },
    {
      "value": {
        "account": "vinibarbosa.near",
        "lockup": "00215ad0d7d939b2a1adb3bfa675266d38cf30c8.venear.dao",
        "amount": "200000000000000000000000"
      },
      "treeIndex": 2
    },
    {
      "value": {
        "account": "klausbravegov.near",
        "lockup": "8b4555ab3d03dc94fbd410f0ff518710a0df8c65.venear.dao",
        "amount": "200000000000000000000000"
      },
      "treeIndex": 3
    }
  ],
  "leafEncoding": [
    "account",
    "lockup",
    "amount"
  ]
}
```

The near-claims-processor will also have a UI for users to retrieve their proof artifacts, by searching based on accountIds.

After this sensing proposal has passed. Gauntlet will update the claims contract with a new campaign running the following example command.

bash
```
near contract call-function as-transaction $CLAIMS_CONTRACT create_campaign json-args '{"merkle_root": [
    158,
    236,
    219,
    170,
    25,
    1,
    253,
    172,
    46,
    71,
    82,
    30,
    201,
    181,
    15,
    59,
    58,
    254,
    170,
    207,
    59,
    87,
    184,
    46,
    81,
    28,
    122,
    202,
    227,
    92,
    92,
    128
], "claim_end": "1789228321000000000"}' prepaid-gas '100.0 Tgas' attached-deposit '0 NEAR' sign-as $GAUNTLET_ACCOUNT_ID network-config mainnet sign-with-keychain send
```

A user then can interact with the claims contract and transfer their Rewards to their lockup using this command:

```bash
near contract call-function as-transaction $CLAIM_CONTRACT.near claim json-args '{"campaign_id": 1, "merkle_proof": [[..]], "lockup_contract": "lockup-example.near", "amount": "10000" }' prepaid-gas '100.0 Tgas' attached-deposit '0 NEAR' sign-as $YOUR_ACCOUNT.near network-config mainnet sign-with-keychain send
```

The SC/NF should determine a valid claim period from the proposed start date. The recommendation is 30 days from the timestamp of the campaign creation. 

## Verification Steps

> **⚠️ ENVIRONMENT CHECK**: This is a `PRODUCTION` task. Verify all contract addresses and proposals match the PRODUCTION environment.

### Step 1: Check the Proposal

Use the NEAR CLI to retrieve the proposal:

```bash
# PRODUCTION environment
near contract call-function as-read-only hos-root.sputnik-dao.near get_proposal json-args '{"id": 17}' network-config mainnet now
```

### Step 2: Decode and Verify Arguments

Decode the inner arguments to verify the actual parameters:

```bash
near contract call-function as-read-only hos-root.sputnik-dao.near get_proposal json-args '{"id": 17}' network-config mainnet now | jq '.kind.FunctionCall.actions[0].args | @base64d | fromjson'
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
