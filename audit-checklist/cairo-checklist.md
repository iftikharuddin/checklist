# Cairo and Starknet Security Checklist

- L1<->L2 cancellation feature is implemented on both side or nah?
- Min gas of 20k wei is required for cross-chain communication (L1->L2)
- Constructor is not run during upgrade, so if there is any state variables which don't have setters they will not be update-able once upgrade happens
- Make sure to verify the `to` address is in sync with felt252 when sending L1->L2
- Must check the payload size limit, arbitrary tokens can be used to DoS the payload size and leads to DoS
- Openzepplin contracts < v0.16.0 has a [bug](https://github.com/OpenZeppelin/cairo-contracts/security/advisories/GHSA-w2px-25pm-2cf9) in 2 step transfer-ship 
- 