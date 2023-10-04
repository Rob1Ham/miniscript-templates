# mint-001

## 3 Key Degrading Multisig

## Motivation

The 2-of-3 multisig serves as a standard in multisignature solutions, offering a well-balanced combination of redundancy, security, flexibility, and practicality for securing Bitcoin. Loss of a single key does not spell disaster, and key distribution enhances security. Miniscript's time feature enables further disaster recovery. If two keys are lost, a timelock can convert the wallet to a 1-of-3 multisig. The proposal outlines four conditions through Miniscript's `thresh()`:

### 2 Layer Conditions to be satisfied using:

#### Base expressions (type B).

##### timelock: **relative** or **absolute** [^timelock] [^either]

- **older(**int**)** [^older]

- **after(**int**)** [^after]

#### Key expressions[^k_type] (type K).

- **pk(**$Key_1$**)**

- **pk(**$Key_2$**)**

- **pk(**$Key_3$**)**

Initially, the wallet requires 2-of-3 keys, functioning as a traditional 2-of-3 multisig. After the timelock expires, only one key is needed to spend the funds.

### More on Timelock Values

- There are no valid timelock values that change the security or correctness properties beyond those implied by the [relative](mint-000.md#absolute-block-height-based-timelocks) or [absolute](mint-000.md#block-height-based-timelocks) timelocks themselves. The reference transactions on testnet [^reftx1] [^reftx2] [^reftx3] [^reftx4] use short-duration timelocks. However, practical applications will vary.

- Relative timelock descriptors provide a persistent structure, requiring a self-send to extend timelock security, thus enabling standardized timelock durations.

- Absolute timelock descriptors need frequent updates; setting long-term timelock values becomes cumbersome. Updating these descriptors entails transferring coins to a new descriptor with revised absolute timelock values.

##### Suggested Relative Block Height Timelocks:

- **older(**32800**)** - Halfway point of block height relative timelock [^278days]

- **older(**65535**)** - Maximum duration of a block height relative timelock [^455days]

##### Suggested Relative Epoch Timelocks:

- **older(**4224680**)** - Approximate Halfway point of epoch time relative timelock[^180days]

- **older(**4259839**)** -- Maximum duration of an epoch time relative timelock[^388days]

##### Suggested Relative Epoch Timelocks: older(4224679)

- Mid-point epoch time relative timelock (\~180 days, 6 months) older(4259839)

- Max duration epoch time relative timelock (\~388 days)

	- It is suggested that the first **Relative Epoch Timelock** occurs prior to the next. "Cascading" the time locks sequentially is not necessary but eases reasoning about the intended purpose and resulting [Bitcoin Script](https://en.bitcoin.it/wiki/Script) structure.

<!--
@RandyMcMillan - "Cascading TimeLocks" may be a better term to refer to these structures. "Degrading Timelocks" has a negative connotation."
-->

Below is a reference diagram on how the 3 Key Time Layered Multisig operates across time:

---
#### Layer 1

| 3 Key Layered Timelock | $Key_1$ | $Key_2$ | $Key_3$ |  | $Timelock$ |
|:-------------|:-------------:|:---------------:|:-------------:|:-:|:-:|
|$Key_n$ + $Key_n$ + $Lock$ | ![assets/key.png](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![assets/key.png](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | ![assets/key.png](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) | `AND` | ![assets/key.png](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/lock.png) |

##### Valid Spending Conditions

<!-- PLUS means concatenate -->

|![](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) $Key_1$| `AND` |![](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) $Key_2$ || ![](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) $Key_1$ | `AND` |![](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) $Key_3$| | ![](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) $Key_2$| `AND` | ![](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) $Key_3$ |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|

<center>

$\left[ Key_1 \space AND \space Key_2 \right] \space OR \space \left [ Key_1 \space AND \space Key_3 \right] \space OR \space \left[ Key_2 \space AND \space Key_3 \right]$

</center>

##### Timelock Expires

|![](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) $Key_1$ | `OR` |![](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) $Key_2$ | `OR` |![](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/key.png) $Key_3$ | `AND` |![](https://raw.githubusercontent.com/bitcoincore-dev/miniscript-templates/main/assets/unlock.png) $Time \space Unlock$ |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|

<center>

$\left[ Key_1 \space OR \space Key_2 \space OR \space Key_3 \right]$ $\space AND \space$ $\left[ \space Time \space Unlock \space \right]$

</center>

---
# Example Miniscript Output Descriptors

## 3 Key Time Lock Multisig

### Relative Blockheight Timelock

#### Policy:

<code>thresh(2,pk(XPUB1),pk(XPUB2),pk(XPUB3),older(100))</code>

#### Miniscript:

<code>thresh(2,pk(XPUB1),s:pk(XPUB2),s:pk(XPUB3),sln:older(100))</code>

#### Descriptor:

<code>wsh(thresh(2,pk(XPUB1),s:pk(XPUB2),s:pk(XPUB3),snl:older(100)))</code>

[Reference Testnet
Transaction 1](https://mempool.space/testnet/tx/13a204ec065f76878ee1f59f79b3eb2cea2b3fda4d8938e6cfa6a8394d090769)

### Absolute Blockheight Timelock

#### Policy:

<code>thresh(2,pk(XPUB1),pk(XPUB2),pk(XPUB3),older(2477600))</code>

#### Miniscript:

<code>thresh(2,pk(XPUB1),s:pk(XPUB2),s:pk(XPUB3),sln:older(2477600))</code>

#### Descriptor:

<code>wsh(thresh(2,pk(XPUB1),s:pk(XPUB2),s:pk(XPUB3),snl:after(2477600)))</code>

[Reference Testnet
Transaction 2](https://mempool.space/testnet/tx/df8a6946816a839f4de9d511ad902d740cc45ddddca3296de8fc11d1fd0c26f4)

### Absolute Epochtime Timelock

#### Policy:

<code>thresh(2,pk(XPUB1),pk(XPUB2),pk(XPUB3),after(1694563200))</code>


#### Miniscript:

<code>"thresh(2,pk(XPUB1),s:pk(XPUB2),s:pk(XPUB3),sln:after(1694563200))"</code>

#### Descriptor:

<code>wsh(thresh(2,pk(XPUB1),s:pk(XPUB2),s:pk(XPUB3),snl:after(1694563200)))</code>

[Reference Testnet
Transaction 3](https://mempool.space/testnet/tx/c0b80a8103e6af92a9bf8e7fb1faa8d073dae929138a2c6d747404cb46e6d690)

### Relative Epochtime Timelock

#### Policy:

<code>thresh(2,pk(XPUB1),pk(XPUB2),pk(XPUB3),older(4194400))</code>

#### Miniscript:

<code>thresh(2,pk(XPUB1),s:pk(XPUB2),s:pk(XPUB3),sln:older(4194400))</code>

#### Descriptor:

<code>wsh(thresh(2,pk(XPUB1),s:pk(XPUB2),s:pk(XPUB3),snl:older(4194400)))</code>

<!-- REF: https://github.com/bitcoin-core/btcdeb -->
<!--
btcc XPUB1 OP_CHECKSIG OP_SWAP XPUB2 OP_CHECKSIG OP_ADD OP_SWAP XPUB3 OP_CHECKSIG OP_ADD OP_SWAP OP_IF 0 OP_ELSE 0x600040 OP_CHECKSEQUENCEVERIFY OP_0NOTEQUAL OP_ENDIF OP_ADD 0x2 OP_EQUAL
-->

<!--
055850554231ac7c055850554232ac937c055850554233ac937c63006703600040b29268930330783287
-->

<!-- REF: https://github.com/bitcoin/bitcoin -->
<!--
bitcoin-cli decodescript 055850554231ac7c055850554232ac937c055850554233ac937c63006703600040b29268930330783287
-->

<!--
{
  "asm": "5850554231 OP_CHECKSIG OP_SWAP 5850554232 OP_CHECKSIG OP_ADD OP_SWAP 5850554233 OP_CHECKSIG OP_ADD OP_SWAP OP_IF 0 OP_ELSE 4194400 OP_CHECKSEQUENCEVERIFY OP_0NOTEQUAL OP_ENDIF OP_ADD 3307568 OP_EQUAL",
  "desc": "raw(055850554231ac7c055850554232ac937c055850554233ac937c63006703600040b29268930330783287)#06n0r7qn",
  "type": "nonstandard",
  "p2sh": "2NCDFD8rBJkeYg1b2JTbHY9u9fCyCDXyURb",
  "segwit": {
    "asm": "0 5faa61a33ff1862186751e57157eb1303f1f04fe4a230124fb449d4efdee900a",
    "desc": "wsh(raw(055850554231ac7c055850554232ac937c055850554233ac937c63006703600040b29268930330783287))#kanqcyum",
    "hex": "00205faa61a33ff1862186751e57157eb1303f1f04fe4a230124fb449d4efdee900a",
    "address": "tb1qt74xrgel7xrzrpn4ret32l43xql37p87fg3szf8mgjw5al0wjq9qu0e4a8",
    "type": "witness_v0_scripthash",
    "p2sh-segwit": "2Mx35TF9QY8QzQVGJqncavidbRbCrS4u1Lq"
  }
}
-->

[Reference Testnet
Transaction 4](https://mempool.space/testnet/tx/1a9ba5a5a37a0df72dfbc28f57de89ce35bda1819afa73712bc29caa32164687)

(Future Addition: Taproot-based keyset for MiniTapScript once integrated
into Core)

[^278days]: **~278 days** ***assuming constant hashrate***

[^455days]: **~455 days** ***assuming constant hashrate***

[^180days]: **~180 days** ***6 months***

[^388days]: **~388 days**

[^pk_key1]: **`pk(key1) = c:pk_k(key1)`** --> **`<key1> CHECKSIG`**

[^pk_key2]: **`pk(key2) = c:pk_k(key2)`** --> **`<key2> CHECKSIG`**

[^pk_key3]: **`pk(key3) = c:pk_k(key3)`** --> **`<key3> CHECKSIG`**

[^pk_key4]: **`pk(key4) = c:pk_k(key4)`** --> **`<key4> CHECKSIG`**

[^pk_key5]: **`pk(key5) = c:pk_k(key5)`** --> **`<key5> CHECKSIG`**

[^abs_timelock]: **after(**int**)**, **older(**int**)**: Require that the **nLockTime** or **nSequence** value is at least (**int**).

[^rel_timelock]: **after(**int**)**, **older(**int**)**: Require that the **nLockTime** or **nSequence** value is at least (**int**).

[^timelock]: **NUM** cannot be **0**.

[^older]: **older(**n**)** = \<n\> **CHECKSEQUENCEVERIFY**

[^after]: **after(**n**)** = \<n\> **CHECKLOCKTIMEVERIFY**

[^either]: either **relative**[^rel_timelock] or **absolute**[^abs_timelock]

[^and]: **LOGICAL AND (&&)** [more](https://en.cppreference.com/w/cpp/language/operator_logical)

[^or]: **LOGICAL OR (||)** [more](https://en.cppreference.com/w/cpp/language/operator_logical)

[^k_type]: **Key** expressions (**K Type**) take their inputs from the top of the stack, but instead of verifying a condition directly they always push a public key onto the stack, for which a signature is still required to satisfy the expression. **A "K type" can be converted into a "B type" using the c: wrapper (CHECKSIG)**. <!-- P. Wuille -->

[^reftx1]: [Reference Testnet Transaction 1](https://mempool.space/testnet/tx/13a204ec065f76878ee1f59f79b3eb2cea2b3fda4d8938e6cfa6a8394d090769)

[^reftx2]: [Reference Testnet Transaction 2](https://mempool.space/testnet/tx/c0b80a8103e6af92a9bf8e7fb1faa8d073dae929138a2c6d747404cb46e6d690)

[^reftx3]: [Reference Testnet Transaction 3](https://mempool.space/testnet/tx/c0b80a8103e6af92a9bf8e7fb1faa8d073dae929138a2c6d747404cb46e6d690)

[^reftx4]: [Reference Testnet Transaction 3](https://mempool.space/testnet/tx/1a9ba5a5a37a0df72dfbc28f57de89ce35bda1819afa73712bc29caa32164687)

[lock]: ./assets/lock.png "lock"

[key]: ./assets/key.png "key"