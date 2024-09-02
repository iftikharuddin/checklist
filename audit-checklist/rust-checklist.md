## Rust checklist

____
**Issue Summary:**  
Substrate lacks default overflow/underflow protection, which can lead to potential bugs in math operations, especially when `total < threshold`, risking an underflow during proposal rejections.

**Recommendation:**  
To prevent these bugs, use safe math functions like `checked_add()` and `checked_sub()` for all arithmetic operations.

____

https://youtu.be/Qkf9QwSfHAM?list=PLzUrW5H8-hDev3XOSY-Wqzb6O2wwn3I43

**Signer check** 

verify that the right parties have signed a transaction.

Signed and secure. Use. Anchor's Signer<'info> type

____

**Address check**

Verify that the account has the expected address ( public key )

use anchor constraint e.g has_one

____

**Ownership check**

verify that an account is owned by expected program
Secure. Usse Anchor's Account<'info, T> type that checks the owner
____

**Arbitrary CPI (Cross Program Invocation)**

Verify that the target program you want to invoke has correct address

Secure. use Anchor's Program<'info, T> type that checks the program's address
____
**Math and logic issues**

- Beware of arithmetics and precision issues. -> use checked arithmetics
- Validate account data and instruction parameters.
- Make sure the instructions are executed in correct order.
- Prevent unintended behavior when passing duplicate accounts
____

**Reinitialization and Revival Attacks**
- You don't want to re-initialize an already initialized account.
- You don't want to use an already closed account.
____
**Other issues**
- Verify account data type to avoid type cosplay.
- Use canonical bump to avoid multiple valid PDAs.
- Do not use shared/global PDA authorities. Use account specific PDAs instead.