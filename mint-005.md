# mint-005

## 3 Key Joint Custody

## Motivation

To provide a custody arrangement in which an owner of Bitcoin (principle) is able to secure bitcoin by working with an Agent. Unlike existing collaborative custody models up to this point, **where bitcoin keys WITHIN a multisig threshold are shared between principles and agents**, a joint custody model by default requires a threshold of keys by both the Agent & Principle for movement of funds.

This introduces a concept of "Negative Control" where, by default, funds are not able to be moved unless both the Principle and Agent sign the transaction.

In the event the principle loses access to all of their keys, a secondary agent is available to work with the primary agent such that funds can be recovered after a set period of time.

In the unlikely event the Primary agent has lost 2 of their 3 keys, a timelock enabled threshold allows  only 1 of 3 keys from the primary agent to be signed with the secondary agent.

Finally, in the event the principle no longer wishes to work with the agent, say after a contract expires, the custody defers to a set of recovery keys, which can be held either by the principle, or their own delegated managers of the recovery keys. As a result, when enough time has passed, the Principle is able to move Bitcoin unilaterally without having the Agent sign key material.



### More on Timelock Values

There are three timelocks used for this MinT:

1. `smallest_epoch_timestamp` - The smallest epoch timestamp timelock enables a "Asset Recovery" period such that only one of the principle keys is required to sign.

2. `between_epoch_timestamp` - The epoch timestamp value in between the smallest and largest epoch timestamp enables a "Emergency Recover Path". In the event the Principle has lost all of their keys, the Primary agent and Secondary Agent can work together to recover the Bitcoin in the Joint Custody vault.

3. `largest_epoch_timestamp` - The largest epoch timestamp, signifying the expiration of the contract, where the principle is able to unilaterally withdraw their bitcoin from the joint custody vault.

### Keys

In total, there are 10 keys in use for the 3 Key Joint Custody Vault, they are as follows:

| Key Names| Description | Key Abbreviations|
|:--|:--:|:--:|
|Principle Keys 1,2,3 |These keys belong to the owner of the Bitcoin. They are used as the default keys the principle uses to transact bitcoin for the length of the relationship with the agent in the Joint Custody vault. | $PK_1$, $PK_2$, $PK_3$|
|Primary Agent Keys 1,2,3 |These keys belong to the agent, who the principle has engaged with to fascilitate the securing of Bitcoin for a determined set of time. | $PAK_1$, $PAK_2$, $PAK_3$|
|Secondary Agent Key |This key is held by a 3rd party unassoicated with the other keys, in the event the principle has lost a majority of their keys, can sign transactions with the primary agent to move funds after a designated "Recovery Period" has started. | $SAK$ |
|Recovery Keys 1,2,3 |These keys in practice belong to the principle, they may even be keys related to the Principle Keys with a different derivation path, but can also be delegated key holders. After the initial Joint Custody vault agreement has ended, the recovery keys can unilaterally be used to withdraw money from the vault. | $RK_1$, $RK_2$, $RK_3$ |


Below are reference diagrams on how the 3 Key Joint Custody operates across time:

---

### Joint Custody Summary Layers

Layer is used as an abstraction to segement the different eligible spending conditions, going in an ascending order of timelock values. At the start, only "Layer 1" is accessible for spending funds, over time, other spending conditions become available, but this does not restrict the ability to spend from a proceeding layer.

| Layer | Layer Name                | Key Set 1                      | Condition Between Sets | Key Set 2          | Timelock Condition | Timelock                        |
|:-----:|:-------------------------:|:------------------------------:|:----------------------:|:------------------:|:------------------:|:-------------------------------:|
| 1     | Default Spending Path     | $PK_1$, $PK_2$, $PK_3$ (2 of 3)| AND                    | $PAK_1$, $PAK_2$, $PAK_3$ (2 of 3)| N/A            | None                            |
| 2     | Asset Recovery Path   | $PK_1$, $PK_2$, $PK_3$ (1 of 3)     | AND                    | $PAK_1$, $PAK_2$, $PAK_3$  (2 of 3)         | AND               | After (`smallest_epoch_timestamp`) |
| 3     | Emergency Recovery Path    | $PAK_1$, $PAK_2$, $PAK_3$ (2 of 3)| AND                 | $SAK$           | N/A               | After (`between_epoch_timestamp`)                            |
| 4     | Sovereign Recovery Path   | $RK_1$, $RK_2$, $RK_3$ (2 of 3)| None | None              | AND               | After (`largest_epoch_timestamp`)  |



### Layer 1
| Default Spending Path | $PK_1$ | $PK_2$ | $PK_3$ | $PAK_1$ | $PAK_2$ | $PAK_3$ |
|:---------------------:|:---:|:---:|:---:|:----:|:----:|:----:|
| 2 of 3 PKs AND 2 of 3 PAKs | ![PK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) |



#### Valid Layer 1 Spend Conditions

| Spending Scenario | $PK_1$ | $PK_2$ | $PK_3$ | $PAK_1$ | $PAK_2$ | $PAK_3$ |
|-------------------|:------:|:------:|:------:|:-------:|:-------:|:-------:|
| Scenario 1        | ✅      | ✅      |        | ✅       | ✅       |         |
| Scenario 2        | ✅      |        | ✅      | ✅       | ✅       |         |
| Scenario 3        | ✅      | ✅      |        |         | ✅       | ✅       |
| Scenario 4        |        | ✅      | ✅      | ✅       | ✅       |         |
| Scenario 5        | ✅      |        | ✅      |         | ✅       | ✅       |
| Scenario 6        |        | ✅      | ✅      |         | ✅       | ✅       |
| Scenario 7        | ✅      | ✅      |        | ✅       |         | ✅       |
| Scenario 8        | ✅      |        | ✅      | ✅       |         | ✅       |
| Scenario 9        |        | ✅      | ✅      | ✅       |         | ✅       |


#### Layer 2
| Default Spending Path | $PK_1$ | $PK_2$ | $PK_3$ | $PAK_1$ | $PAK_2$ | $PAK_3$ | BIP-113 time > `smallest_epoch_timestamp` |
|:---------------------:|:------:|:------:|:------:|:-------:|:-------:|:-------:|:----------------------------------------:|
| 1 of 3 PKs AND 2 of 3 PAKs AND Time Condition | ![PK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) |



##### Valid Layer 2 Spend Conditions

| Spending Scenario | PK_1 | PK_2 | PK_3 | PAK_1 | PAK_2 | PAK_3 | BIP-113 greater than (smallest_epoch_timestamp) |
|-------------------|:----:|:----:|:----:|:-----:|:-----:|:-----:|:-----------------------------------:|
| Scenario 10       | ✅    |      |      | ✅     | ✅     |       | ✅                                   |
| Scenario 11       | ✅    |      |      | ✅     |       | ✅     | ✅                                   |
| Scenario 12       | ✅    |      |      |       | ✅     | ✅     | ✅                                   |
| Scenario 13       |      | ✅    |      | ✅     | ✅     |       | ✅                                   |
| Scenario 14       |      | ✅    |      | ✅     |       | ✅     | ✅                                   |
| Scenario 15       |      | ✅    |      |       | ✅     | ✅     | ✅                                   |
| Scenario 16       |      |      | ✅    | ✅     | ✅     |       | ✅                                   |
| Scenario 17       |      |      | ✅    | ✅     |       | ✅     | ✅                                   |
| Scenario 18       |      |      | ✅    |       | ✅     | ✅     | ✅                                   |

#### Layer 3:

| Emergency Recovery Path | $PAK_1$ | $PAK_2$ | $PAK_3$ | $SAK$ | BIP-113 greater than `between_epoch_timestamp` |
|:-----------------------:|:-------:|:-------:|:-------:|:-------:|:-----------------------------------------------:|
| 2 of 3 PAKs AND SAK AND after BIP-113 time is greater than `between_epoch_timestamp`  | ![PAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![SAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) |


##### Valid Layer 3 Spend Conditions

| Spending Scenario | PAK_1 | PAK_2 | PAK_3 | SAK | BIP-113 time greater than  (between_epoch_timestamp) |
|-------------------|:-----:|:-----:|:-----:|:---:|:----------------------------------:|
| Scenario 19       | ✅     | ✅     |       | ✅  | ✅                                  |
| Scenario 20       | ✅     |       | ✅     | ✅  | ✅                                  |
| Scenario 21       |       | ✅     | ✅     | ✅  | ✅                                  |                                |


#### Layer 4:
| Sovereign Recovery Path | $RK_1$ | $RK_2$ | $RK_3$ | Network BIP-113 time greater than `largest_epoch_timestamp` |
|:-----------------------:|:------:|:------:|:------:|:-------:|
| 2 of 3 RKs AND after BIP-113 time is greater than `largest_epoch_timestamp` | ![RK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![RK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![RK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) |

##### Valid Layer 4 Spend Conditions

| Spending Scenario | RK_1 | RK_2 | RK_3 | Timelock (largest_epoch_timestamp) |
|-------------------|:----:|:----:|:----:|:----------------------------------:|
| Scenario 22       | ✅    | ✅    |      | ✅                                  |
| Scenario 23       | ✅    |      | ✅    | ✅                                  |
| Scenario 24       |      | ✅    | ✅    | ✅                                  |


---

# Example Miniscript Output Descriptor

For this example, the `smallest_epoch_timestamp` is: 1672531200 (Jan 1 2023, midnight gmt), the `between_epoch_timestamp` is: 1673740800 and `largest_epoch_timestamp` is: 1675209600 (Feb 1 2023, midnight gmt)

- MINT-005 Output Descriptor: 
<code>wsh(andor(multi(2,$PAK_1$,$PAK_2$,$PAK_3$),or_i(and_v(v:pkh($SAK$),after(`between_epoch_timestamp`)),thresh(2,pk($PK_1$),s:pk($PK_2$),s:pk($PK_3$),snl:after(`smallest_epoch_timestamp`))),and_v(v:thresh(2,pkh($RK_1$),a:pkh($RK_2$),a:pkh($RK_3$)),after(`larget_epoch_timestamp`))))</code>


- Source Policy (FOR REFERENCE PURPOSES ONLY):
<code>"or(99@and(thresh(2,pk($PAK_1$),pk($PAK_2$),pk($PAK_3$)),or(99@thresh(2,pk($PK_1$),pk($PK_2$),pk($PK_3$),after(`smallest_epoch_timestamp`)),and(pk($SAK$),after(`between_epoch_timestamp`)))),and(thresh(2,pk($RK_1$),pk($RK_2$),pk($RK_3)),after(`largest_epoch_timestamp`)))"</code>





## Layer 1 Example Spend

Signed by: $PK_1$, $PK_2$, $PAK_1$, $PAK_2$

[Reference Testnet
Transaction](https://mempool.space/signet/tx/2836d6af6b5c4bb01e926391f64771fb333193676040b24d4236ba0bb89a7008)

## Layer 2 Example Spend 

Signed by: $PK_1$, $PK_2$, $SAK$

[Reference Testnet
Transaction](https://mempool.space/signet/tx/36aa3dfd0c7b4f4d8c7924c411e240920e4b4d36950ca59f68098b77162ae54d)

## Layer 3 Example Spend

[Reference Testnet
Transaction](https://mempool.space/signet/tx/bc75e9c7bd62168134a6283a56c2a0bf3c872cc6703d9566f1851309d5ef7465)

## Layer 4 Example Spend

Signed by: $RK_1$, $RK_2$

[Reference Testnet
Transaction](https://mempool.space/signet/tx/1d35568360a3a11309c77c893142a0c0cf58ed9cfce981c5492c66fb795f1872)
