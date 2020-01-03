# Depos Technologies

Depos creates a stablecoin, which is collateralized with tokenized debt instruments. This approach combines best of fiat-collateralized and crypto-collateralized approaches: it is fully transparent on the one hand and is well scalable on the other. On the top of that it has proven and robust bank-like business model and built-in on-chain liquidity support for stablecoin holders.

This is done alongside with the dBonds protocol for debt tokenization. For more info please visit [dBonds protocol repo](https://github.com/thedeposbank/zilliqa-dbonds)

## Depos DAO overview
**1.** On the one side `Depos DAO` holds collateral which consists of two parts:
  * Major part is tokenized bonds, which is quite stable in value and brings predictable fixed income to the system.
  * Minor part consists of liquid crypto (e.g. `ZIL`) which is volatile but doesn't affect the system much. Minor part serves as an easy gateway in-and-out from stablecoin to other crypto for `user`. This part brings income to the system collecting some fee for mint/redeem operations.

**2.** On the other side there is stablecoin supply.
From time to time system makes sure, that total supply of stablecoins equals to the total value of collateral, so that every stablecoin is 100% collateralized with valuable on-chain assets. To absorb all volatility/losses/revenue contract has its own stablecoin `capital`, which is originated and recapitalised by selling utility and governance `DPS` token.

### `Capital` and `DPS`
All system revenues/losses are absorbed by the `capital`, so that new stablecoins are either issued to the `capital` or are burnt from it depending on the case. `DPS` has `nominal price` which is simply a share of `capital` for one `DPS` token. `DPS` token can be used at nominal price to pay-off debt received in stablecoins. `DPS` is also traded on secondary market and has market price. In the case system experiences lack of `capital` to absorb sudden loss and keep every stablecoin collateralized, new `DPS` tokens are issued and sold to the market to replenish the `capital`.

## System design overview
The system is designed as follows
- There are two core token contracts now: `DUSD contract` for USD-pegged stablecoin token (more currencies in the future) and `DPS contract` for utility and governance token. Both follow standards with minor extentions for governance. Both cannot be changed. Both can be called by appropriate parties, none of them calls any transitions from the outside of the contract itself.
- `DUSD contract` also holds all tokens owned by the `Depos DAO`: dBond tokens, liquid tokens (ex. `ZIL`, more in the future) and internal `capital` in stablecoins. These funds can be transfered or used only by internal calls of other smart contracts and are out of control of any third party including `dev team`.
- All external calls and interactions with the system are done via `proxy contract`. The goal of this contract is to be publicly available and translate requests to the appropriate private contracts of the system with the logic implementation.
- Logic implementation is divided to several independent contracts and can be upated with time. Each contract can be called only either by `proxy contract` or by other logic implementation contract.
- Internal `capital` of the `Depos DAO` can be recapitalized by additional public or private sale of utility and governance `DPS` tokens. For every recapitalization a new `fundrasing contract` is published which accepts payments in exchange of `DPS` tokens according to the rules, terms and prices difened by this contract. That leads to the increase of the total supply of `DPS` tokens.
- On the first stage governance and ownership belongs to the corresponding admin accounts. In future these functions will be passed to `DPS` holders who will decide on changes in the system.



## What functionallity is supported

### How stablecoins are minted/redeemed
**1.** `user` can mint/redeem stablecoins directly via smartcontract in exchange of supported liquid crypto at current market rate. `user` can consider it as a direct decentralized one-call trade with the contract. As the value of collateral changes, total stablecoin supply also changes.

Notes: 
  * to avoid oracle front-running trade is made within next block after registering.
  * such a trade can be declined if it brings the system out of correct risk-management state (e.g. order is too large or system ran out of the liquidity)

**2.** `bond_issuer` can buy/sell stablecoins
for pre-authorized tokenized debt (see [dBonds](https://github.com/thedeposbank/zilliqa-dbonds)) at currrent debt-internal price. Only available for tokenized debts originated by the `bond_issuer` themselves. As the value of collateral changes, total stablecoin supply also changes.

Notes:
  * such a trade can be declined if it brings the system out of correct risk-management state (e.g. liquidity pool is going to be relativaly too large or too low to the whole supply)

**3.** Collateral in the form of either liquid crypto or tokenized bonds can be deposited in order to replenish `capital` with new-minted `DUSD` stablecoins (collateral is stored, stablecoins are issued to the `capital`).

Notes: 
  * If the `DPS` token is spent as a utility token, it does not affect the stablecoin supply but may change `nominal price` of `DPS` as the supply of `DPS` tokens may change.

**4.** When the collateral value changes and the stablecoin supply adjustment is triggered, system mints/burns to/from `capital` appropriate amount of stablecoins to maintain 1:1 collaterization.


### Support of tokenized debts lifecycle

**1.** Approve tokenized debt token contract

**2.** If the tokenized debt is paid off, contract can claim the payment back (see [dBonds](https://github.com/thedeposbank/zilliqa-dbonds))

### Governance and housekeeping

**1.** Pause/unpause contract

**2.** Blacklist/unblacklist address

**3.** Have different permissions for different administrative roles

**4.** Have account to manage permissions for roles

### Support of `DPS` utility functions

**1.** In order to have some amount of tokenized debt sold to the contract ` bond_issuer` has to stake appropriate amount of `DPS` inside the contract.

**2.** `DPS` can be used within [dBonds](https://github.com/thedeposbank/zilliqa-dbonds) protocol to pay debt

### Suport of `DPS` governance functions (in the future)

**1.** Approve/decline governance roles substitution

**2.** Approve/decline contract upgrades

**3.** Approve/decline system parameters upgrade


