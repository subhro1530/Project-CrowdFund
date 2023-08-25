## Getting Started

Create a project using this example:

```bash
npx thirdweb create --contract --template hardhat-javascript-starter
```

You can start editing the page by modifying `contracts/Contract.sol`.

To add functionality to your contracts, you can use the `@thirdweb-dev/contracts` package which provides base contracts and extensions to inherit. The package is already installed with this project. Head to our [Contracts Extensions Docs](https://portal.thirdweb.com/contractkit) to learn more.

## Building the project

After any changes to the contract, run:

```bash
npm run build
# or
yarn build
```

to compile your contracts. This will also detect the [Contracts Extensions Docs](https://portal.thirdweb.com/contractkit) detected on your contract.

## Deploying Contracts

When you're ready to deploy your contracts, just run one of the following command to deploy you're contracts:

```bash
npm run deploy
# or
yarn deploy
```

## Releasing Contracts

If you want to release a version of your contracts publicly, you can use one of the followings command:

```bash
npm run release
# or
yarn release
```

## Join our Discord!

For any questions, suggestions, join our discord at [https://discord.gg/thirdweb](https://discord.gg/thirdweb).


#   Building a smart contract for crowdfunding from people using web3.js

##  Steps :
1.  In the terminal write the following command to download and configure _thirdweb_
```bash
npx thirdweb@latest create --contract
```
You will be aasked some questions.

2.  Our crowd funding smart contract:
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.9;

contract MyContract {
    struct Campaign {
        address owner;
        string title;
        string description;
        uint256 target;
        uint256 deadline;
        uint256 amountCollected;
        string image;
        address[] donators;
        uint256[] donations;
    }

    mapping (uint256 => Campaign) public campaigns;

    uint256 numberOfCampaigns=0;

    function createCampaign(address _owner,string memory _title,string memory _description, uint256 _target, uint256 _deadline, string memory _image) public returns(uint256) {
        Campaign storage campaign=campaigns[noOfCampaigns];

        //is everything okay?
        require(campaign.deadline<block.timestamp,"The deadline should be a date in the future.");

        campaign.owner=_owner;
        campaign.title=_title;
        campaign.description=_description;
        campaign.target=_target;
        campaign.deadline=_deadline;
        campaign.amountCollected=0;
        campaign.image=_image;

        noOfCampaigns++;

        return noOfCampaigns-1;
    }

    function donateToCampaign(uint256 _id) public payable {
        uint256 amount=msg.value;

        Campaign storage campaign=campaigns[_id];

        campaign.donators.push(msg.sender);
        campaign.donations.push(amount);

        (bool sent,)=payable(campaign.owner).call{value:amount}("");
        if(sent){
            campaign.amountCollected=campaign.amountCollected+amount;
        }
    }
    function getDonators(uint256 _id) view public returns(address[] memory,uint256[] memory) {
        return (campaigns[_id].donators,campaigns[_id].donations);
    }
    function getCampaigns() public view returns(Campaign[] memory){
        Campaign[] memory allCampaigns=new Campaign[](numberOfCampaigns);

        for (uint i = 0; i < numberOfCampaigns; i++) {
            Campaign storage item=campaigns[i];
            allCampaigns[i]=item;
        }
        return allCampaigns;
    }
}
```