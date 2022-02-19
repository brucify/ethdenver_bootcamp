ETHDenver Smart Contract Developer Bootcamp

Build
-----

	$ npm init --yes 
	$ yarn add hardhat
	$ yarn hardhat # Select 'Create an empty hardhat.config.js'
	$ yarn add --dev @nomiclabs/hardhat-ethers ethers
	$ yarn add --dev dotenv 

Edit `hardhat.config.js`:

```js
require("@nomiclabs/hardhat-ethers");
require('dotenv').config()

/**
* @type import('hardhat/config').HardhatUserConfig
*/
module.exports = {
 defaultNetwork: "rinkeby",
   networks: {
       hardhat: {
           // // If you want to do some forking, uncomment this
           // forking: {
           //   url: MAINNET_RPC_URL
           // }
       },
       localhost: {
       },
       rinkeby: {
           url: process.env.RINKEBY_RPC_URL,
           accounts: [process.env.PRIVATE_KEY],
           saveDeployments: true,
       }
   },
 solidity: "0.7.3",
};
```

Add `.env` file, with the RPC URL from Alchemy app:

```
RINKEBY_RPC_URL='https://url.from.alchemy.app'
PRIVATE_KEY='123'
```

Add `contracts/MyFirstContract.sol`:

```solidity
pragma solidity =0.7.3;
contract MyFirstContract {

   uint256 number;


   function setNumber(uint256 _num) public {
       number = _num;
   }


   function getNumber() public view returns (uint256){
       return number;
   }
}
```

	$ yarn hardhat compile

Add `scripts/deploy.js`:

```js
async function main() {

   const [deployer] = await ethers.getSigners();

   console.log(
     "Deploying contracts with the account:",
     deployer.address
   );

   console.log("Account balance:", (await deployer.getBalance()).toString());

   const MyFirstContract = await ethers.getContractFactory("MyFirstContract");
   const deployedContract = await MyFirstContract.deploy();
   console.log("Deployed MyFirstContract contract address:", deployedContract.address);


   await deployedContract.setNumber(7)

   let result = BigInt(await deployedContract.getNumber()).toString()
   console.log('Stored value in contract is: ', result)


 }

 main()
   .then(() => process.exit(0))
   .catch(error => {
     console.error(error);
     process.exit(1);
   });
```

Deploy to Rinkeby:

	$ yarn hardhat run scripts/deploy.js 
