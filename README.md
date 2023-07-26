# Archetype HashLock Contract

The repository contains the source code of a hashlock smart contract written with Archetype language and a jsligo file for creating hashes that can be used as parameters for entry points.

**NB:** source refers to the address of the account that originates the call

## HashLock Smart Contract

The storage of the smart contract contains two variables, `unused` (bool) and `commits` (big map). `unused` variable is used for checking either the contract has been used or the solution has been committed. The other variable, `commits`, is used for storing commits associated with their submitters. The variable `hashed` is not stored in the storage since it is a constant variable and is hardcoded in the compiled michelson code of the smart contract.

`commit_hash` entry point is used by sources to submit their salted hashes to the smart contract. The salted hash must be created by using the concatenation of the source's public address (wallet address) and user's solution with sha256 algorithm.

`reveal` entry point is used for checking whether the source's solution matches with the solution harcoded in the contract. If the contract has been used before (unused variable is false), then the contract will fail. The contract will also check the source's salted hash by using the submitted solution and source's public address. If solutions match, source's submitted lambda function is executed.

**NB:** In the original jsligo code, contract checks if the `reveal` entry point is executed after 24 hours of the submition the commit hash. I changed 24 hours to 10 seconds to be able to test the contract.

**NB** In the original code, user has to submit a lamda function to the `reveal` entry point. I changed the type of lambda function to `option` for making the testing phase easier. I also changed the return type of lambda function to `option` since list of operations are not required to be returned in Archetype.

The address of the smart contract on ghostnet:
```
KT1HBPCLSZeBZyPEHDV6oBwnPtcrh3nSBRk4
```

Sample command for calling the `commit_hash` entry point:
```bash
octez-client transfer 0 from alice to KT1KNcWLX9y4D1AGawzgrDGSPxw3UNB2L11r --entrypoint commit_hash --arg '0x8136253ff38c26c5e685d6b5618377364d0e9121ec4ceaf12fe0f19ac2417fff' --burn-cap 0.2
```

Sample command for calling the `reveal` entry point:
```bash
octez-client transfer 0 from alice to KT1D3wsTbc1NRQ2NhtoQcP55yx74yv64Hdrp --entrypoint reveal --arg '(Pair 0xf5 None)' --burn-cap 0.2
```

## Get Hashes JsLIGO Script

`get_hashes.jsligo` contract is used for creating hashes which can be used for the entry points of the contract hashlock_contract.arl

first parameter: bytes representation of the solution <br>
second parameter: (optional): wallet address of source for creating commit hash

first output: hash representation of given solution <br>
second output: commit hash

Sample command to run the script:
```bash
ligo run dry-run get_hashes.jsligo '[0x1a, Some("tz1aSkwEot3L2kmUvcoxzjMomb9mvBNuzFK6")]' '[0x, 0x]'
```