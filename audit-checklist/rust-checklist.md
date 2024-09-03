# Rust and Substrate Security Checklist

| ✅ | Issue Title | Summary / Recommendation |
|----|---------------|----------------|
|    | **Overflow/Underflow Protection** | Substrate lacks default overflow/underflow protection, which can lead to potential bugs in math operations, especially when `total < threshold`, risking an underflow during proposal rejections. | To prevent these bugs, use safe math functions like `checked_add()` and `checked_sub()` for all arithmetic operations. |
|    | **Signer Check** | Verify that the right parties have signed a transaction. | Signed and secure. Use Anchor's `Signer<'info>` type. |
|    | **Address Check** | Verify that the account has the expected address (public key). | Use Anchor constraint, e.g., `has_one`. |
|    | **Ownership Check** | Verify that an account is owned by the expected program. | Secure. Use Anchor's `Account<'info, T>` type that checks the owner. |
|    | **Arbitrary CPI (Cross Program Invocation)** | Verify that the target program you want to invoke has the correct address. | Secure. Use Anchor's `Program<'info, T>` type that checks the program's address. |
|    | **Math and Logic Issues** | - Beware of arithmetic and precision issues. Use checked arithmetic. <br> - Validate account data and instruction parameters. <br> - Ensure instructions are executed in the correct order. <br> - Prevent unintended behavior when passing duplicate accounts. | Follow these checks to avoid logical flaws and potential vulnerabilities. |
|    | **Reinitialization and Revival Attacks** | - Avoid re-initializing an already initialized account. <br> - Avoid using an already closed account. | Validate initialization states to prevent these issues. |
|    | **Other Issues** | - Verify account data type to avoid type spoofing. <br> - Use a canonical bump to avoid multiple valid PDAs. <br> - Do not use shared/global PDA authorities; use account-specific PDAs instead. | Implement these best practices for secure account management. |
|    | **Check Rounding Direction** | In Rust, there are several ways to round floating-point numbers: <br> - `round()`: rounds either up or down. <br> - `ceil()`: rounds up. <br> - `floor()`: rounds down. | Double-check the direction to ensure the intended rounding behavior. |
|    | **Order of operations** | Dividing before multiplying can lead to precision loss. | Always ensure to check the order of operations. |


## References
- [Video: Secure Coding Practices](https://youtu.be/Qkf9QwSfHAM?list=PLzUrW5H8-hDev3XOSY-Wqzb6O2wwn3I43)
- [Video: Rust Security - Foundations](https://youtu.be/q7yjmhxyvc0)

## More resources
- https://exvul.com/rust-smart-contract-security-guide-in-solana/
- https://github.com/0xsanny/solsec 