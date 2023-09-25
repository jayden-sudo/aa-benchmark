# AA Benchmark

A benchmark for AA (ERC-4337) smart contract accounts.

All accounts use single-ECDSA signatures.  We plan on expanding to other signing schemes in the future (multisig, RSA, etc.).

## Results (as of Aug 31, 2023)

**Disclaimer** the numbers are obtained from local simulations.  On-chain numbers might differ slightly.

Since these are gas numbers, lower is better.

|                  | Creation | Native transfer | ERC20 transfer | Total  |
| ---------------- | -------- | --------------- | -------------- | ------ |
| SimpleAccount    | 388815   | 107540          | 96432          | 592787 |
| Biconomy         | 275646   | 110629          | 99255          | 485530 |
| Etherspot        | 284456   | 109940          | 98849          | 493245 |
| Kernel v2.0      | 344687   | 116107          | 105003         | 565797 |
| Kernel v2.1      | 269168   | 112549          | 101419         | 483136 |
| Kernel v2.1-lite | 235161   | 107091          | 95702          | 437954 |

## How to add your implementation

1. Fork this repo.
2. Add your test file to `./test/`; create a folder if needed.
3. Inherit `src/TestBase.sol` and override `getSignature()`, `fillData()`, `createAccount()`, `getAccountAddr()`, `getInitCode()`.
    - `getSignature()`: should return appropriate signature based on `_op` (e.g. should return ECDSA signature).
    - `fillData()`: should return appropriate data for `userOp.callData`.
    - `createAccount()`: should create the wallet.  Skip this if your factory only allows for creation through the EntryPoint.
    - `getAccountAddr()`: should return the counterfactual address for the given `_owner`.
    - `getInitCode()`: should return `userOp.initCode`.
4. Write `setUp()` function to set the initial test condition.  For instance, it might deploy the factory and the implementation contract. **NOTICE** since we don't want this repo to have too many dependencies, please use `vm.etch` and use your predicted address & bytecode to deploy the contract instead of importing your whole code.  This will also make it easy to test without compiler setup fiasco.
5. Run `WRITE_GAS_PROFILE=true forge test -vv` this will show you the results of the benchmark and also output the result as a JSON file to `results/`.
6. Make a PR to this repository.

### TODO

- [ ] add paymaster benchmark
- [ ] allow non-ECDSA benchmark
    - [ ] maybe RSA?
- [ ] add CI for cheking/getting the gas results
- [ ] L2 gas cost calculation
