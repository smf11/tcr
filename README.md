# Token-Curated Registry

[ ![Codeship Status for skmgoldin/tcr](https://app.codeship.com/projects/b140cce0-ac77-0135-0738-52e8b96e2dec/status?branch=master)](https://app.codeship.com/projects/257003)

A hash-keyed [token-curated registry (TCR)](https://medium.com/@ilovebagels/token-curated-registries-1-0-61a232f8dac7). [Owner's Manual available](https://github.com/skmgoldin/tcr/blob/master/owners_manual.md).

Mainnet factory: [0xcc0df91b86795f21c3d43dbeb3ede0dfcf8dccaf](https://etherscan.io/address/0xcc0df91b86795f21c3d43dbeb3ede0dfcf8dccaf#code)

Rinkeby factory: [0x822415a1e4d0d7f99425d794a817d9b823bdcd0c](https://rinkeby.etherscan.io/address/0x822415a1e4d0d7f99425d794a817d9b823bdcd0c#code)

EPM: [tcr](https://www.ethpm.com/registry/packages/44)

## Initialize
The only environmental dependency you need is Node. Presently we can guarantee this all works with Node 8.
```
npm install
npm run install
npm run compile
```

## Tests
The repo has a comprehensive test suite. You can run it with `npm run test`. To run the tests with the RPC logs, use `npm run test gas`.

## Composition of the repo
The repo is composed as a Truffle project, and is largely idiomatic to Truffle's conventions. The tests are in the `test` directory, the contracts are in the `contracts` directory and the migrations (deployment scripts) are in the `migrations` directory. Furthermore there is a `conf` directory containing json files where deployments can be parameterized.

## Deploying your own TCR
To deploy your own TCR, first open up `conf/config.json`. The `paramDefaults` object in the config JSON will specify the starting parameters your TCR is deployed with. In the `token` object, set `deployToken` to `true` if you want to deploy this TCR's token as part of the TCR deployment. You can specifiy initial recipients of the token in the `tokenHolders` array. If you have already deployed a token, set `deployToken` to `false` and provide the token's address in the `address` property. The token should be EIP20.

The `package.json` includes scripts for deploying to rinkeby and mainnet. Modify `truffle.js` and `package.json` if you need other networks. You'll need a `secrets.json` file with a funded mnemonic on the `m/44'/60'/0'/0/0` HD path in the root of the repo to deploy. Your `secrets.json` should look like this:

```json
{
  "mnemonic": "my good mnemonic ..."
}
```

If you prefer to use an environment variable, your `.bashrc` or `.bash_profile` should look something like:

```bash
export MNEMONIC='my good mnemonic ...'
```

You can use [https://iancoleman.io/bip39/](https://iancoleman.io/bip39/) to generate a mnemonic and derive its `m/44'/60'/0'/0/0` address.

## Packages
The repo consumes several EPM packages. `dll` and `attrstore` are libraries used in PLCRVoting's doubly-linked list abstraction. `tokens` provides an ERC20-comaptible token implementation. All packages are installed automatically when running `npm install`.


## Detailed Deployment Instructions 
*step by step for current master branch*
*note: we set our mnemoic as an environment variable*
*note: we created our token ahead of time using myCrypto*

```
$ git clone https://github.com/skmgoldin/tcr.git
```

open truffle.js  <br/>
add *const infura_address = "YOUR_INFURA_ADDRESS"* before module.exports <br/>
concatenate infura_address to rinkeby hdWallet provider url <br/>
```
provider: () => new HDWalletProvider(mnemonic, 'https://rinkeby.infura.io/'+infura_address)
```
set rinkeby gas to 6000000 <br/>

edit tcr/conf/config.json according to instructions above <br/>

navigate to https://github.com/skmgoldin/tcr/blob/develop/package.json <br/>
copy deploy-proxies:rinkeby script and add to your package.json scripts <br/>
navigate to https://github.com/skmgoldin/tcr/tree/develop/scripts <br/>
copy deploy_proxies.js and add to your tcr/scripts directory <br/>

```
$ export MNEMONIC="YOUR_INFURA_MNEMONIC"
$ npm install
$ npm run install
$ npm run compile
$ npm run deploy-rinkeby
$ npm run deploy-proxies:rinkeby
```

!!! Save the console.log of contract addresses !!! <br/>


test Registry was succesfully deployed: <br/>

run a node terminal - using your infura_address and the Registry address we saved above <br/>
```
> Web3 = require('web3')
> web3 = new Web3(new Web3.providers.HttpProvider("https://rinkeby.infura.io/YOUR_INFURA_ADDRESS"));
> const fs = require('fs');
> const reg = JSON.parse(fs.readFileSync('./build/contracts/Registry.json', 'utf8'));
> const abi = reg.abi
> const addr='YOUR_REGISTRY_ADDRESS'
> var MyContract = web3.eth.contract(abi)
> var MyContractInstance = MyContract.at(addr)
> MyContractInstance.name()
```

if MyContract was created correctly, console will output the following when you run  <br/>
var MyContractInstance = MyContract.at(addr)  <br/>
```
Contract {
  _eth: 
   Eth {
     _requestManager:

     etc.....
```

if everything is working, console will output the following when you run <br/>
MyContractInstance.name()  <br/>
```
'your registry name'
```


### Helpful Resources
- https://medium.com/@tokencuratedregistry/the-token-curated-registry-whitepaper-bd2fb29299d6 <br/>
- https://gitter.im/Curation-Markets/Lobby <br/>
