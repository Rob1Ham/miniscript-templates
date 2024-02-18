# mint-003

## 3 Key Joint Custody

## Motivation

To provide a custody arrangement in which an owner of Bitcoin (principle) is able to secure bitcoin by working with an Agent. Unlike existing models up to this point, ***where bitcoin either by the principle as a custodian, or share keys within a multisig threshold,** The goal here is to have the default requirements of bitcoin held in this joint custody to require both a threshold of keys from the Agent & Principle for movement of funds.

This introduces a concept of "Negative Control" where, by default, funds are not able to be moved unless both the Principle and Agent sign the transaction.

In the event the principle loses access to all of their keys, a secondary agent is available to work with the primary agent such that funds can be recovered after a set period of time.

In the unlikely event the Primary agent has lost 2 of their 3 keys, a timelock enables only 1 of 3 keys from the primary agent to be signed with the secondary agent.

Finally, in the event the principle no longer wishes to work with the agent, say after a contract expires, the custody defers to a set of recovery keys, which can be held either by the principle, or their own delegated managers of the recovery keys.



### More on Timelock Values

- There are three timelocks used for this MinT, in increasing order:

1. `Contingency Timestamp` - This timestamp is the smallest timestamp, and expires first. It acts as a contingency to allow the Principle to sign for their quorum with only 1 of 3 possible keys. This is done in the event two principle keys are lost/destroyed/stolen. The passing of this timestamp enables "Layer 2" to be used.

2. `Recovery Timestamp` - In the event the pricniple has lost all of their keys, the agent and secnondary agent are able to recoery funds. The passing of this timestamp enables "Layer 3" also known as the "Recovery Layer".

3. `Catastrophe Timestamp` - In the unlikely event the primary agent loses two of their keys, the passing of the catastrophe timestamp enables the primary agent to use one key with the secondary agent to spend the bitcoin. The passing of this timestamp enables "Layer 4" also known as the "Cataostrophe Layer"

4. `Sovereign Timestamp` - The greatest valued timelock, signifying the expiration of the contract with the agent. The principle is able to unilaterally withdraw their bitcoin from the joint custody vault. The passing of this timestamp enables "Layer 5" also known as the "Soverig Layer"

### Keys

In total, there are 10 keys in use for the 3 Key Joint Custody Vault, they are as follows:

| Key Names| Description | Key Abbreviations|
|:--|:--:|:--:|
|Principle Keys 1,2,3 |These keys belong to the owner of the Bitcoin. They are used as the default keys the principle uses to transact bitcoin for the length of the relationship with the agent in the Joint Custody vault. | $PK_1$, $PK_2$, $PK_3$|
|Primary Agent Keys 1,2,3 |These keys belong to the agent, who the principle has engaged with to fascilitate the securing of Bitcoin for a determined set of time. | $PAK_1$, $PAK_2$, $PAK_3$|
|Secondary Agent Key |This key is held by a 3rd party unassoicated with the other keys, in the event the principle has lost a majority of their keys, can sign transactions with the primary agent to move funds after a designated "Recovery Period" has started. | $SAK$ |
|Recovery Keys 1,2,3 |These keys in practice belong to the principle, they may even be keys related to the Principle Keys with a different derivation path, but can also be delegated key holders. After the initial joint custody vault agreement has ended, and the Recovery Period has ended, the recovery keys can unilaterally be used to withdraw money from the vault. | $RK_1$, $RK_2$, $RK_3$ |


Below is a reference diagram on how the 3 Key Joint Custody operates across time:

---

### Joint Custody Summary Layers

Layer is used as an abstraction to segement the different eligible spending conditions, going in an ascending order of timelock values. At the start, only "Layer 1" is accessible for spending funds, over time, other spending conditions become available, but this does not restrict the ability to spend from a proceeding layer.

| Layer Number | Layer Name                 | Required Signatures          | Timestamp Requirement | Description                                                                                                      |
|:------------:|----------------------------|:----------------------------:|:---------------------:|------------------------------------------------------------------------------------------------------------------|
| 1            | Default Path               | 2 of 3 $PK$ **and** 2 of 3 $PAK$ | None                  | Allows transactions under normal conditions without any timelock constraints.                                    |
| 2            | Contingency Path           | 1 of 3 $PK$ **and** 2 of 3 $PAK$ | Contingency Timestamp | Activated after the contingency timestamp is reached, allowing for fewer Principle Keys in the signing process.  |
| 3            | Recovery Path              | 2 of 3 $PAK$ **and** $SAK$    | Recovery Timestamp    | Allows for recovery procedure once the recovery timestamp has passed, requiring signatures from the Primary Agent Keys and the Secondary Agent Key. |
| 4A           | Catastrophe Path           | 1 of 3 $PAK$ **and** $SAK$ | Catastrophe Timestamp | In the event of a catastrophe, require only 1 Principle Agent Key and Secondary Agent Key.                        |
| 4B           | Enhanced Catastrophe Path  | 1 of 3 $PAK$ **and** 1 of 3 $PK$ | Contingency Timestamp **and** Catastrophe Timestamp | Requires satisfaction of both the Contingency and Catastrophe Timestamps, enabling transactions with reduced key requirements in enhanced catastrophic conditions. |
| 5            | Sovereign Path             | 2 of 3 $RK$                  | Sovereign Timestamp    | Marks the contract's expiration, allowing the Principle to unilaterally withdraw their bitcoin from the vault with Recovery Keys.



### Layer 1
| Default  Path | $PK_1$ | $PK_2$ | $PK_3$ | $PAK_1$ | $PAK_2$ | $PAK_3$ |
|:---------------------:|:---:|:---:|:---:|:----:|:----:|:----:|
| 2 of 3 PKs AND 2 of 3 PAKs | ![PK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) |



#### Valid Layer 1 Spend Conditions

| Spending Scenario | $PK_1$ | $PK_2$ | $PK_3$ | $PAK_1$ | $PAK_2$ | $PAK_3$ |
|-------------------|:------:|:------:|:------:|:-------:|:-------:|:-------:|
| Scenario 1        | ✅      | ✅      |        | ✅       | ✅       |         |
| Scenario 2        | ✅      | ✅      |        | ✅       |         | ✅       |
| Scenario 3        | ✅      | ✅      |        |         | ✅       | ✅       |
| Scenario 4        | ✅      |        | ✅      | ✅       | ✅       |         |
| Scenario 5        | ✅      |        | ✅      | ✅       |         | ✅       |
| Scenario 6        | ✅      |        | ✅      |         | ✅       | ✅       |
| Scenario 7        |        | ✅      | ✅      | ✅       | ✅       |         |
| Scenario 8        |        | ✅      | ✅      | ✅       |         | ✅       |
| Scenario 9        |        | ✅      | ✅      |         | ✅       | ✅       |




### Layer 2
| Contingency Spending Path | $PK_1$ | $PK_2$ | $PK_3$ | $PAK_1$ | $PAK_2$ | $PAK_3$ | BIP-113 greater than `Contingency Timestamp` |
|:-------------------------:|:------:|:------:|:------:|:-------:|:-------:|:-------:|:----------------------------:|
| 1 of 3 PKs AND 2 of 3 PAKs | ![PK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) |



#### Valid Layer 2 Spend Conditions

| Spending Scenario | $PK_1$ | $PK_2$ | $PK_3$ | $PAK_1$ | $PAK_2$ | $PAK_3$ | BIP-113 greater than `contingency timestamp` |
|-------------------|:------:|:------:|:------:|:-------:|:-------:|:-------:|:------------------------------------------:|
| Scenario 1        | ✅      |        |        | ✅       | ✅       |         | ✅                                         |
| Scenario 2        |        | ✅      |        | ✅       | ✅       |         | ✅                                         |
| Scenario 3        |        |        | ✅      | ✅       | ✅       |         | ✅                                         |
| Scenario 4        | ✅      |        |        |         | ✅       | ✅       | ✅                                         |
| Scenario 5        |        | ✅      |        |         | ✅       | ✅       | ✅                                         |
| Scenario 6        |        |        | ✅      |         | ✅       | ✅       | ✅                                         |
| Scenario 7        | ✅      |        |        | ✅       |         | ✅       | ✅                                         |
| Scenario 8        |        | ✅      |        | ✅       |         | ✅       | ✅                                         |
| Scenario 9        |        |        | ✅      | ✅       |         | ✅       | ✅                                         |




### Layer 3

| Recovery Path | $PAK_1$ | $PAK_2$ | $PAK_3$ | $SAK$ | BIP-113 greater than `Recovery Timestamp` |
|:-----------------------:|:-------:|:-------:|:-------:|:-------:|:--------:|
| 2 of 3 PAKs AND SAK1 after Timelock | ![PAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![SAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) |


##### Valid Layer 3 Spend Conditions

| Spending Scenario | $_PAK_1$ | $_PAK_2$ | $_PAK_3$ | $_SAK_1$ | BIP-113 greater than `Recovery Timestamp` |
|-------------------|:--------:|:--------:|:--------:|:--------:|:------------:|
| Scenario 1        | ✅        | ✅        |         | ✅        | ✅          | 
| Scenario 2        | ✅        |          | ✅        | ✅        | ✅          |
| Scenario 3        |          | ✅        | ✅        | ✅        | ✅          |


### Layer 4A
| Catastrophe Path | $PK_1$ | $PK_2$ | $PK_3$ | $PAK_1$ | $PAK_2$ | $PAK_3$ | $SAK$ | BIP-113 greater than `Catastrophe Timestamp` |
|:----------------:|:------:|:------:|:------:|:-------:|:-------:|:-------:|:----:|:----------------------------:|
| 1 of 3 PAKs AND SAK |        |        |        | ![PAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![SAK](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) |




##### Valid Layer 4A Spend Conditions

| Spending Scenario | $_PAK_1$ | $_PAK_2$ | $_PAK_3$ | $_SAK_1$ | BIP-113 greater than `Catastrophe Timestamp` |
|-------------------|:--------:|:--------:|:--------:|:--------:|:----------------------------------------------:|
| Scenario 1        | ✅        |          |          | ✅        | ✅                                            | 
| Scenario 2        |          | ✅        |          | ✅        | ✅                                            |
| Scenario 3        |          |          | ✅        | ✅        | ✅                                            |




### Layer 4B

### Layer 4B
| Enhanced Catastrophe Path | $PK_1$ | $PK_2$ | $PK_3$ | $PAK_1$ | $PAK_2$ | $PAK_3$ | Catastrophe Timestamp Unlock | Concierge Timestamp Unlock |
|:-------------------------:|:------:|:------:|:------:|:-------:|:-------:|:-------:|:----------------------------:|:-------------------------:|
| 1 of 3 PAKs AND 1 of 3 PKs | ![PK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) |


##### Valid Layer 4B Spend Conditions

| Spending Scenario | $PK_1$ | $PK_2$ | $PK_3$ | $PAK_1$ | $PAK_2$ | $PAK_3$ | Contingency Timestamp Satisfied | Catastrophe Timestamp Satisfied |
|-------------------|:------:|:------:|:------:|:-------:|:-------:|:-------:|:-------------------------------:|:--------------------------------:|
| Scenario 1        | ✅      |        |        | ✅       |         |         | ✅                               | ✅                                |
| Scenario 2        |        | ✅      |        | ✅       |         |         | ✅                               | ✅                                |
| Scenario 3        |        |        | ✅      | ✅       |         |         | ✅                               | ✅                                |
| Scenario 4        | ✅      |        |        |         | ✅       |         | ✅                               | ✅                                |
| Scenario 5        |        | ✅      |        |         | ✅       |         | ✅                               | ✅                                |
| Scenario 6        |        |        | ✅      |         | ✅       |         | ✅                               | ✅                                |
| Scenario 7        | ✅      |        |        |         |         | ✅       | ✅                               | ✅                                |
| Scenario 8        |        | ✅      |        |         |         | ✅       | ✅                               | ✅                                |
| Scenario 9        |        |        | ✅      |         |         | ✅       | ✅                               | ✅                                |


#### Layer 5:
| Sovereign Recovery Path | $RK_1$ | $RK_2$ | $RK_3$ | Network BIP-113 time greater than `Sovereign Timestamp` |
|:-----------------------:|:------:|:------:|:------:|:-------:|
| 2 of 3 RKs AND Timelock | ![RK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![RK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![RK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) |

##### Valid Layer 5 Spend Conditions

| Spending Scenario | $RK_1$ | $RK_2$ | $RK_3$ | Network BIP-113 time greater than `Sovereign Timestamp` |
|-------------------|:------:|:------:|:------:|:------------:|
| Scenario 1        | ✅      | ✅      |        | ✅          |
| Scenario 2        | ✅      |        | ✅      | ✅          |
| Scenario 3        |        | ✅      | ✅      | ✅          |


---

# Example Miniscript Output Descriptor

For this example, here is a table for reference timestamps:

| Timestamp Name     | Epoch Timestamp | Calendar Date/Time |
|--------------------|-----------------|--------------------|
| Contingency Timestmap | 1671062400      | Dec 15, 2022 12:00am GMT     |
| Recovery Timestamp     | 1672531200      | Jan 1, 2023  12:00am GMT      |
| Catastrophe Timestamp      | 1673740800      | Jan 15, 2023 12:00am GMT      |
| Sovereign Timestamp    | 1675209600      | Feb 1, 2023  12:00am GMT      |


- MINT-003 Output Descriptor: 
<code>wsh(or_i(and_v(v:thresh(2,pkh($RK_1),a:pkh($RK_2),a:pkh($RK_3)),after(`Sovereign Timestamp`)),and_v(v:thresh(2,pk($PAK_1),s:pk($PAK_2),s:pk($PAK_3),snl:after(`Recovery Timestamp`)),or_i(and_v(v:pkh($SAK),after(`third_epoch_timestamp`)),thresh(2,pk($PK_1),s:pk($PK_2),s:pk($PK_3),snl:after(`Contingency Timestamp`))))))</code>


- Source Policy (FOR REFERENCE PURPOSES ONLY):
<code>or(99@and(thresh(2,pk($PAK_1),pk($PAK_2),pk($PAK_3),after(`Catastrophe Timestamp`)),or(99@thresh(2,pk($PK_1),pk($PK_2),pk($PK_3),after(`Contingency Timestamp`)),and(pk($SAK),after(`Recovery Timestamp`)))),and(thresh(2,pk($RK_1),pk($RK_3),pk($RK_3)),after(`Soverign Timestamp`)))</code>




## Layer 1 Example Spend

Signed by: $PK_1$, $PK_2$, $PAK_1$, $PAK_2$

[Reference Testnet
Transaction](https://mempool.space/testnet/tx/10d5b57266be3f47eb739f7d2c91ff637584bf6d726084b0d5c98825058e8eec)

## Layer 2 Example Spend 

Signed by: $PK_1$, $PAK_1$, $PAK_2$,

[Reference Testnet
Transaction](https://mempool.space/testnet/tx/ffef9035eefd28c510da61b238826324bf4df1cc4a6142131e1bcbf03325c268)

## Layer 3 Example Spend

Signed By: $PAK_1$, $PAK_2$, $SAK$

[Reference Testnet
Transaction](https://mempool.space/testnet/tx/3be529a997c387f8549f3345e391b56f1b3a8533109d496c1373bc7dbdf779c1)

## Layer 4A Example Spend

Signed by: $PAK_1$, $SAK$

[Reference Testnet
Transaction](https://mempool.space/testnet/tx/4defec9e5680c30e3239ebe038c2a51392fe6090debdb563dff1c444ba68099d)

## Layer 4B Example Spend

Signed by: $PAK_1$, $PK_1$

[Reference Testnet
Transaction](https://mempool.space/testnet/tx/cec7c2781c6cc4122ae2445a34766e4e0120b7c17509d4d72c29c133d439069a)

## Layer 5 Example Spend

Signed by: $RK_1$, $RK_2$

[Reference Testnet
Transaction](https://mempool.space/testnet/tx/68cd577ea5c33092ef36bfb84a3e31f7bcffc5d11a7131e451f1e5921ed1e14e)