# mint-003

## 3 Key Joint Custody

## Motivation

To provide a custody arrangement in which an owner of Bitcoin (principle) is able to secure bitcoin by working with an Agent. Unlike existing models up to this point, ***where bitcoin either by the principle as a custodian, or share keys within a multisig threshold,** The goal here is to have the default requirements of bitcoin held in this joint custody to require both a threshold of keys from the Agent & Principle for movement of funds.

This introduces a concept of "Negative Control" where, by default, funds are not able to be moved unless both the Principle and Agent sign the transaction.

In the event the principle loses access to all of their keys, a secondary agent is available to work with the primary agent such that funds can be recovered after a set period of time.

Finally, in the event the principle no longer wishes to work with the agent, say after a contract expires, the custody defers to a set of recovery keys, which can be held either by the principle, or their own delegated managers of the recovery keys.


<!--

REVISIT

 ### 2 Layer Conditions to be satisfied using:

#### Base expressions (type B).

##### timelock: **relative** or **absolute** [^timelock] [^either]

- **older(**int**)** [^older]

- **after(**int**)** [^after] 

#### Key expressions[^k_type] (type K).

- **pk(**$Key_1$**)**

- **pk(**$Key_2$**)**

- **pk(**$Key_3$**)** -->



### More on Timelock Values

- There are two timelocks used for this MinT:

1. `smaller_epoch_timestamp` - The lower valued timelock is to signify the "recovery period" where the principle has lost access to 2 of their keys, and would like to use the secondary agent as a recovery partner to gain access to the funds.

2. `larger_epoch_timestamp` - The greater valued timelock, signifying the expiration of the contract, where the principle is able to unilaterally withdraw their bitcoin from the joint custody vault.

### Keys

In total, there are 10 keys in use for the 3 Key Joint Custody Vault, they are as follows:

| Key Names| Description | Key Abbreviations|
|:--|:--:|:--:|
|Principle Keys 1,2,3 |These keys belong to the owner of the Bitcoin. They are used as the default keys the principle uses to transact bitcoin for the length of the relationship with the agent in the Joint Custody vault. | $PK_1$, $PK_2$, $PK_3$|
|Primary Agent Keys 1,2,3 |These keys belong to the agent, who the principle has engaged with to fascilitate the securing of Bitcoin for a determined set of time. | $PAK_1$, $PAK_2$, $PAK_3$|
|Secondary Agent Key |This key is held by a 3rd party unassoicated with the other keys, in the event the principle has lost a majority of their keys, can sign transactions with the primary agent to move funds after a designated "Recovery Period" has started. | $SAK _1$ |
|Recovery Keys 1,2,3 |These keys in practice belong to the principle, they may even be keys related to the Principle Keys with a different derivation path, but can also be delegated key holders. After the initial joint custody vault agreement has ended, and the Recovery Period has ended, the recovery keys can unilaterally be used to withdraw money from the vault. | $RK_1$, $RK_2$, $RK_3$ |


Below is a reference diagram on how the 3 Key Joint Custody operates across time:

---

### Joint Custody Summary Layers

Layer is used as an abstraction to segement the different eligible spending conditions, going in an ascending order of timelock values. At the start, only "Layer 1" is accessible for spending funds, over time, other spending conditions become available, but this does not restrict the ability to spend from a proceeding layer.

| Layer | Layer Name              | Key Set 1                            | Condition Between Sets | Key Set 2                | Timelock Condition | Timelock                      |
|:-----:|:-----------------------:|:------------------------------------:|:----------------------:|:------------------------:|:------------------:|:------------------------------:|
| 1     | Default Spending Path   | $PK_1$, $PK_2$, $PK_3$ (2 of 3)      | AND                    | $_PAK_1$, $_PAK_2$, $_PAK_3$ | N/A              | None                          |
| 2     | Emergency Recovery Path | $_PAK_1$, $_PAK_2$, $_PAK_3$         | AND                    | $_SAK_1$                 | AND               | After (`smaller_epoch_timestamp`) |
| 3     | Sovereign Recovery Path | $RK_1$, $RK_2$, $RK_3$               | None                   | None                    | AND               | After (`larger_epoch_timestamp`)  |


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

AND

| Valid Spending Scenario | $PAK_1$ | $PAK_2$ | $PAK_3$ |
|:-----------------------:|:-------:|:-------:|:-------:|
| Scenario A              | ✅       | ✅       |         |
| Scenario B              | ✅       |         | ✅       |
| Scenario C              |         | ✅       | ✅       |


#### Layer 2

| Emergency Recovery Path | $PAK_1$ | $PAK_2$ | $PAK_3$ | $SAK_1$ | BIP-113 greater than `smaller_epoch_timestamp` |
|:-----------------------:|:-------:|:-------:|:-------:|:-------:|:--------:|
| 2 of 3 PAKs AND SAK1 after Timelock | ![PAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![PAK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![SAK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) |



##### Valid Layer 2 Spend Conditions

| Spending Scenario | $_PAK_1$ | $_PAK_2$ | $_PAK_3$ | $_SAK_1$ | BIP-113 greater than `smaller_epoch_timestamp` |
|-------------------|:--------:|:--------:|:--------:|:--------:|:------------:|
| Scenario 1        | ✅        | ✅        |         | ✅        | Yes          | 
| Scenario 2        | ✅        |          | ✅        | ✅        | Yes          |
| Scenario 3        |          | ✅        | ✅        | ✅        | Yes          |

AND



---




#### Layer 3:
| Sovereign Recovery Path | $RK_1$ | $RK_2$ | $RK_3$ | Network BIP-113 time greater than `larger_epoch_timestamp` |
|:-----------------------:|:------:|:------:|:------:|:-------:|
| 3 of 3 RKs AND Timelock | ![RK1](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![RK2](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![RK3](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![Timelock](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) |

##### Valid Layer 3 Spend Conditions

| Spending Scenario | $RK_1$ | $RK_2$ | $RK_3$ | Network BIP-113 time greater than `larger_epoch_timestamp` |
|-------------------|:------:|:------:|:------:|:------------:|
| Scenario 1        | ✅      | ✅      |        | Yes          |
| Scenario 2        | ✅      |        | ✅      | Yes          |
| Scenario 3        |        | ✅      | ✅      | Yes          |


---

# Example Miniscript Output Descriptor

For this example, the `smaller_epoch_timestamp` is: 1672531200 (Jan 1 2023, midnight gmt) and `larger_epoch_timestamp` is: 1675209600 (Feb 1 2023, midnight gmt)

- MINT-003 Output Descriptor: 
<code>wsh(andor(multi(2,$PAK_1$,$PAK_2$,$PAK_3$),or_d(multi(2,$PK_1$,$PK_2$,$PK_3$),and_v(v:pkh($),after(`smaller_epoch_timestamp`))),and_v(v:thresh(2,pkh($RK_1$),a:pkh($RK_2$),a:pkh($RK_3$),after(`larger_epoch_timestamp`))))</code>


- Source Policy (FOR REFERENCE PURPOSES ONLY):
<code>or(99@and(thresh(2,pk($AGENT_1_XPUB),pk($AGENT_2_XPUB),pk($AGENT_3_XPUB)),or(99@thresh(2,pk($PRINCIPLE_1_XPUB),pk($PRINCIPLE_2_XPUB),pk($PRINCIPLE_3_XPUB)),and(pk($SECONDARY_AGENT_1_XPUB),after(1672531200)))),and(thresh(2,pk($RECOVERY_1_XPUB),pk($RECOVERY_2_XPUB),pk($RECOVERY_3_XPUB)),after(1675209600)))</code>


## Layer 1 Example Spend

Signed by: $PK_1$, $PK_2$, $PAK_1$, $PAK_2$

[Reference Testnet
Transaction](https://mempool.space/testnet/tx/4977152a5e1a07861c40d159513cb7888d06b5591ce8903e1145eafd284777f1)

## Layer 2 Example Spend 

Signed by: $PK_1$, $PK_2$, $SAK$

[Reference Testnet
Transaction](https://mempool.space/testnet/tx/6a35bcf91dc2d12ec286e9a3a47a3cf4998938050f86aee4f3175799c624f1ca)

## Layer 3 Example Spend

Signed by: $RK_1$, $RK_2$

[Reference Testnet
Transaction](https://mempool.space/testnet/tx/4d829311422ff286dce42bde0f3c979c46680e696e625d94dfa5be45d8a4cb27)


