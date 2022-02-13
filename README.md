# ✨ So you want to sponsor a contest

This `README.md` contains a set of checklists for our contest collaboration.

Your contest will use two repos: 
- **a _contest_ repo** (this one), which is used for scoping your contest and for providing information to contestants (wardens)
- **a _findings_ repo**, where issues are submitted. 

Ultimately, when we launch the contest, this contest repo will be made public and will contain the smart contracts to be reviewed and all the information needed for contest participants. The findings repo will be made public after the contest is over and your team has mitigated the identified issues.

Some of the checklists in this doc are for **C4 (🐺)** and some of them are for **you as the contest sponsor (⭐️)**.

---

# Contest setup

## ⭐️ Sponsor: Provide contest details

Under "SPONSORS ADD INFO HERE" heading below, include the following:

- [ ] Name of each contract and:
  - [ ] source lines of code (excluding blank lines and comments) in each
  - [ ] external contracts called in each
  - [ ] libraries used in each
- [ ] Describe any novel or unique curve logic or mathematical models implemented in the contracts
- [ ] Does the token conform to the ERC-20 standard? In what specific ways does it differ?
- [ ] Describe anything else that adds any special logic that makes your approach unique
- [ ] Identify any areas of specific concern in reviewing the code
- [ ] Add all of the code to this repo that you want reviewed
- [ ] Create a PR to this repo with the above changes.

---

# ⭐️ Sponsor: Provide marketing details

- [ ] Your logo (URL or add file to this repo - SVG or other vector format preferred)
- [ ] Your primary Twitter handle
- [ ] Any other Twitter handles we can/should tag in (e.g. organizers' personal accounts, etc.)
- [ ] Your Discord URI
- [ ] Your website
- [ ] Optional: Do you have any quirks, recurring themes, iconic tweets, community "secret handshake" stuff we could work in? How do your people recognize each other, for example? 
- [ ] Optional: your logo in Discord emoji format

---

# Contest prep

## ⭐️ Sponsor: Contest prep
- [ ] Make sure your code is thoroughly commented using the [NatSpec format](https://docs.soliditylang.org/en/v0.5.10/natspec-format.html#natspec-format).
- [ ] Modify the bottom of this `README.md` file to describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the C4 Wardens should keep in mind when reviewing. ([Here's a well-constructed example.](https://github.com/code-423n4/2021-06-gro/blob/main/README.md))
- [ ] Please have final versions of contracts and documentation added/updated in this repo **no less than 8 hours prior to contest start time.**
- [ ] Ensure that you have access to the _findings_ repo where issues will be submitted.
- [ ] Promote the contest on Twitter (optional: tag in relevant protocols, etc.)
- [ ] Share it with your own communities (blog, Discord, Telegram, email newsletters, etc.)
- [ ] Optional: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.
- [ ] Designate someone (or a team of people) to monitor DMs & questions in the C4 Discord (**#questions** channel) daily (Note: please *don't* discuss issues submitted by wardens in an open channel, as this could give hints to other wardens.)
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# Redacted Cartel contest details
- $28,500 USDC main award pot
- $1,500 USDC gas optimization award pot
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2022-02-redacted-cartel-contest/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts February 15, 2022 00:00 UTC
- Ends February 17, 2022 23:59 UTC

This repo will be made public before the start of the contest. (C4 delete this line when made public)

> Name of each contract and:
>   source lines of code (excluding blank lines and comments) in each
>   external contracts called in each
>   libraries used in each

BribeVault.sol
- Source lines of code: 357
- External contract calls
    - Line 187: IERC20(token).safeTransferFrom(briber, address(this), amount);
    - Line 283-285: (bool sentFeeRecipient, ) = feeRecipient.call{value: feeAmount}(
                    ""
                );
    - Line 291-293: (bool sentDistributor, ) = distributor.call{
                    value: distributorAmount
                }("");
    - Line 296: IERC20(token).transfer(feeRecipient, feeAmount);
    - Line 297: IERC20(token).transfer(distributor, distributorAmount);
    - Line 310: IRewardDistributor(distributor).updateRewardsMetadata(distributions);
    - Line 322: IRewardDistributor(distributor).updateRewardsMetadata(distributions);
    - Line 337: IERC20(token).transfer(msg.sender, amount);
    - Line 352: (bool sentAdmin, ) = msg.sender.call{value: amount}("");
- Libraries
    - SafeERC20
    - Common

RewardDistributor.sol
- Source lines of code: 209
- External contract calls
    - Line 179: IERC20(token).safeTransfer(_account, _amount);
    - Line 181: payable(_account).transfer(_amount);
- Libraries
    - SafeERC20
    - MerkleProof
    - Common

TokemakBribe.sol
- Source lines of code: 303
- External contract calls
    - Line 201-203: IBribeVault(bribeVault).getBribe(
                generateBribeVaultIdentifier(proposal, round, token)
            );
    - Line 235-241: IBribeVault(bribeVault).depositBribeERC20(
            bribeIdentifier,
            rewardIdentifier,
            token,
            amount,
            msg.sender
        );
    - Line 275-280: IBribeVault(bribeVault).depositBribe{value: msg.value}(
            // NOTE: Native token bribes have bribeVault set as the address
            bribeIdentifier,
            rewardIdentifier,
            msg.sender
        );
- Libraries
    - None

> Describe any novel or unique curve logic or mathematical models implemented in the contracts

N/A

> Does the token conform to the ERC-20 standard? In what specific ways does it differ?

Hidden Hand does not have its own token.

> Describe anything else that adds any special logic that makes your approach unique

The goal of Hidden Hand is to facilitate voter incentivization for any protocol - flexibility and customizability is its core differentiation from other popular platforms, such as Votium and Votemak, which generally cater to one protocol. You will find that our architecture and methods of accessing, storing, and segregating data and funds adheres to that goal.

> Identify any areas of specific concern in reviewing the code

Please review our README first to get an understanding of architecture, user roles, and action flow. Thank you.

# Hidden Hand

### Contract Overview

**BribeVault.sol**
- Custodies all bribe tokens until they are ready to be distributed
- Stores data related to bribe tokens and amounts, and proofs for verifying correctness of the arbitrary data related to computing bribe token distributions
- Retrieves protocol fees before transferring funds to the RewardDistributor, and sets the data necessary for ensuring only eligible users can claim rewards

**RewardDistributor.sol**
- Custodies only the amount of bribe tokens that can be claimed by users
- Verifies claim data to ensure only eligible users can claim rewards
- Provides an interface for BribeVault to set and update the claim-verification data

**TokemakBribe.sol**
- Stores Tokemak-specific data used by the client (i.e. frontend app) to display info that directs users to take the right actions that make them eligible for rewards (e.g. vote for proposal X to get Y reward)
- Contains the logic for generating identifiers that are used for storing and accessing relevant BribeVault data
- Transfers all bribe tokens to the BribeVault as they are received and creates or updates the relevant records locally and on BribeVault

### User Roles

**Explicit (defined in-contract)**
- Admins: Owner-level accounts (e.g. multisig, founder account, etc.) with the ability to call any permissioned method. Their primary responsibilities are role management and funds disbursement (e.g. transferring funds from BribeVault to RewardDistributor along with the required proofs)
- Team Members: Team member accounts with the ability to call permissioned methods pertaining to the maintainenance and continued operation of the protocol (e.g. setting the proposals or updating the round in TokemakBribe)

**Implicit**
- Briber: Accounts that submit actions to be taken along with rewards for doing so (e.g. calling a deposit method on TokemakBribe to specify a proposal to vote for and the incentives for voting on it)
- Bribee: Accounts that take action and claim the associated rewards (e.g. voting on a proposal specified in TokemakBribe and claiming their share of the reward after the voting round is over)
- Auditor: Entities that verify data and claim proofs (i.e. comparing data against their hashes and merkle roots) to confirm that the rewards were distributed correctly (e.g. verifying that the voting activity on a bribed proposal aligns with the reward distribution).

![Contract Diagram](https://i.imgur.com/B7Kr2aD.png)

_<p align="center">General interaction between the contracts and users</p>_

### Action-chain: Tokemak CoRE Round

A sequential series of actions taken by different users from the beginning of a Tokemak CoRE round (assume the round has started) to the reward claims
1. On the TokemakBribe contract, team members set the round, and set the proposals and the deadlines by which bribers can deposit rewards
2. On the TokemakBribe contract, bribers specify the proposals and include the incentives for voting on them. The TokemakBribe contract transfers all funds to the BribeVault contract as they are received (i.e. it does not custody funds beyond the deposit method calls) and updates the relevant data on the BribeVault contract
3. Outside of the Hidden Hand contract scope, voters place their votes on the proposals 
4. Outside of the Hidden Hand contract scope, after the Tokemak CoRE round ends, proposal data is compiled and these two things happen:
- The following is derived from the data: its hash (KECCAK-256) and merkle roots (one for each bribe token in the round and contains the accounts and amounts each should receive)
- The data file is uploaded to a publicly-accessible location and can be used to verify the hash and merkle roots by an auditor

_NOTE: The data contains everything necessary to generate the merkle roots for reward distribution and also any relevant contextual information (e.g. if a voter is forwarding their rewards to another account, we will include both addresses in the data - only the reward-forwarded account is necessary to generate the merkle roots but knowing the origin/voter account may be useful in an audit)._

5. On the BribeVault contract, an admin calls the transfer bribe method (the data's hash and merkle roots are used here), which results in funds being transferred from the BribeVault to the RewardDistributor
6. On the RewardDistributor contract, eligible accounts (voters or accounts that are forwarded rewards) call the claim method and are transferred their rewards
