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

** Arbitrary CPI (Cross Program Invocation)**
Verify that the target program you want to invoke has correct address

____
