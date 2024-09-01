## Rust checklist

____
**Issue Summary:**  
Substrate lacks default overflow/underflow protection, which can lead to potential bugs in math operations, especially when `total < threshold`, risking an underflow during proposal rejections.

**Recommendation:**  
To prevent these bugs, use safe math functions like `checked_add()` and `checked_sub()` for all arithmetic operations.

____