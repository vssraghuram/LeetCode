<p align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" alt="Project Banner" width="100%">
</p>

# Competitive Programming — Complete Error & Data Types Guide

-----

## Part 1: All Common Errors on Online Judges

-----

### 1. TLE — Time Limit Exceeded

**What it means:**
Your code took longer than the allowed time to finish.

**Why it happens:**

```
Problem allows: 1 second
Your code took: 4 seconds  ← TLE
```

**Common Causes & Fixes:**

|Cause                      |Bad                   |Fix                   |
|---------------------------|----------------------|----------------------|
|Nested loops on large input|O(n²)                 |Use O(n log n) or O(n)|
|Repeated work              |Recalculate same value|Cache it (memoization)|
|Wrong data structure       |Array search O(n)     |Use HashMap O(1)      |
|Unnecessary sorting        |Sort every iteration  |Sort once outside loop|

**Example:**

```cpp
// BAD — O(n²) → TLE on large input
for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++)
        sum += arr[i] + arr[j];

// GOOD — O(n) → Fast
for (int i = 0; i < n; i++)
    sum += arr[i] * 2;
```

**Rule of Thumb:**

```
Input size n     →  Max operations you can afford
n ≤ 10⁸         →  O(n)
n ≤ 10⁶         →  O(n log n)
n ≤ 10⁴         →  O(n²)
n ≤ 500         →  O(n³)
```

-----

### 2. MLE — Memory Limit Exceeded

**What it means:**
Your code used more RAM than allowed (usually 256MB).

**Why it happens:**

```
Allocating a 10⁷ × 10⁷ 2D array → billions of cells → MLE
```

**Common Causes & Fixes:**

|Cause                   |Fix                            |
|------------------------|-------------------------------|
|Too large array/matrix  |Use HashMap instead            |
|Recursive stack too deep|Convert recursion to iteration |
|Storing unnecessary data|Only store what you need       |
|Global vs local arrays  |Large arrays → declare globally|

**Example:**

```cpp
// BAD — 10^7 * 10^7 = impossible memory
int dp[10000000][10000000];

// GOOD — only allocate what you need
unordered_map<int, int> dp;
```

-----

### 3. Overflow — Integer Overflow

**What it means:**
Result exceeds the maximum value a data type can hold.

**Classic Example (already covered):**

```
Int32.min / -1 = 2147483648
But Int32.max  = 2147483647  ← overflow by 1!
```

**Silent Overflow Example:**

```cpp
int a = 100000;
int b = 100000;
int result = a * b;  // = 10,000,000,000
// But Int32 max  = 2,147,483,647
// result silently becomes a WRONG negative number!
```

**Fix:**

```cpp
long long result = (long long)a * b;  // Safe!
```

-----

### 4. WA — Wrong Answer

**What it means:**
Code runs fine but output is incorrect.

**Common Causes:**

|Cause                            |Example                               |
|---------------------------------|--------------------------------------|
|Off-by-one error                 |Loop runs n times instead of n-1      |
|Missing edge case                |Empty array, single element, negatives|
|Wrong formula                    |Using wrong math                      |
|Integer division instead of float|5/2 = 2 instead of 2.5                |

**Example:**

```cpp
// BAD — integer division loses decimal
int a = 5, b = 2;
float result = a / b;   // = 2.0 (WRONG)

// GOOD
float result = (float)a / b;  // = 2.5 (CORRECT)
```

-----

### 5. RE — Runtime Error

**What it means:**
Code crashes while running.

**Types:**

|Type              |Cause                                   |
|------------------|----------------------------------------|
|Segmentation Fault|Accessing array out of bounds           |
|Stack Overflow    |Infinite recursion or too deep recursion|
|Division by Zero  |Dividing by 0                           |
|Null Pointer      |Using uninitialized pointer             |

**Example:**

```cpp
int arr[5];
arr[10] = 1;  // ← RE: Index 10 doesn't exist in size-5 array

// Fix: always check bounds
if (index < arr.size()) arr[index] = 1;
```

-----

### 6. CE — Compilation Error

**What it means:**
Code doesn’t even compile — syntax is broken.

**Common Causes:**

```cpp
int x = "hello"   // type mismatch
missing semicolon
undefined variable
wrong function signature
```

**Fix:** Read the compiler error message — it tells you exactly which line.

-----

### 7. Presentation Error (PE)

**What it means:**
Answer is correct but formatted wrong (extra space, missing newline).

**Example:**

```
Expected: "1 2 3\n"
Your output: "1  2  3"   ← extra spaces = PE
```

-----

### 8. Infinite Loop / TLE from Logic Error

**What it means:**
Loop never terminates.

**Example:**

```cpp
while (n > 0) {
    n += 1;  // Should be n -= 1 → loops forever → TLE
}
```

-----

## Part 2: Data Types & Their Holding Capacity

-----

### Integer Types

|Type                 |Bits|Min Value     |Max Value    |Use When                 |
|---------------------|----|--------------|-------------|-------------------------|
|`int8_t` / `Int8`    |8   |-128          |127          |Tiny values, flags       |
|`int16_t` / `Int16`  |16  |-32,768       |32,767       |Small counters           |
|`int` / `Int32`      |32  |-2,147,483,648|2,147,483,647|Default for most problems|
|`long long` / `Int64`|64  |-9.2 × 10¹⁸   |9.2 × 10¹⁸   |Large sums, products     |
|`unsigned int`       |32  |0             |4,294,967,295|When no negatives needed |
|`unsigned long long` |64  |0             |1.8 × 10¹⁹   |Very large positive only |

-----

### Floating Point Types

|Type         |Bits  |Precision            |Range             |
|-------------|------|---------------------|------------------|
|`float`      |32    |~7 decimal digits    |±3.4 × 10³⁸       |
|`double`     |64    |~15 decimal digits   |±1.7 × 10³⁰⁸      |
|`long double`|80/128|~18-19 decimal digits|Varies by platform|

**When to use which:**

```
float  → small memory, less precision (avoid in CP)
double → default for all floating point in CP
```

-----

### In Each Language

```swift
// Swift
Int8, Int16, Int32, Int64
UInt8, UInt16, UInt32, UInt64
Float, Double

Int.max  // = Int64.max on 64-bit systems
Int32.max = 2147483647
Int64.max = 9223372036854775807
```

```cpp
// C++
int         // 32-bit
long long   // 64-bit
__int128    // 128-bit (GCC only, for extreme values)

INT_MAX    = 2147483647
LLONG_MAX  = 9223372036854775807
```

```javascript
// JavaScript
// No explicit int types — all numbers are float64
Number.MAX_SAFE_INTEGER = 9007199254740991  // 2^53 - 1
BigInt  // for numbers beyond this
```

-----

## Part 3: 32-bit vs 64-bit — How They Work Together

-----

### What “32-bit” and “64-bit” Actually Mean

```
32-bit integer = 32 switches (bits) in memory
Each bit = 0 or 1

00000000 00000000 00000000 00000000  = 0
01111111 11111111 11111111 11111111  = 2,147,483,647 (Int32 max)
10000000 00000000 00000000 00000000  = -2,147,483,648 (Int32 min)
↑
This first bit = sign bit (1 = negative, 0 = positive)
```

```
64-bit just doubles the switches:
= 64 bits = can hold 2^63 - 1 = 9,223,372,036,854,775,807
```

-----

### Combining 32-bit with 64-bit (The Classic Trick)

**Problem:** Two 32-bit numbers multiplied can overflow 32-bit.

```cpp
int a = 100000;     // fits in 32-bit
int b = 100000;     // fits in 32-bit
int result = a * b; // = 10,000,000,000 → OVERFLOW! ❌

// The multiplication happens in 32-bit before assignment
```

**Fix — Cast BEFORE multiplying:**

```cpp
long long result = (long long)a * b;  // Cast a to 64-bit first ✅
//                 ↑ now multiplication happens in 64-bit space
```

**Same in Swift:**

```swift
let a: Int32 = 100000
let b: Int32 = 100000
let result = Int64(a) * Int64(b)  // ✅ Safe
```

**Same in JavaScript:**

```javascript
const a = 100000;
const b = 100000;
const result = BigInt(a) * BigInt(b);  // ✅ Safe
```

-----

### Why Cast BEFORE and Not AFTER?

```cpp
// WRONG — overflow already happened, casting garbage value
long long result = (long long)(a * b);
//                              ↑ this multiplies in 32-bit first!

// CORRECT — promotes to 64-bit before multiplication
long long result = (long long)a * b;
//                  ↑ now b auto-promotes to 64-bit too
```

-----

### The 32-bit Clamp Pattern (LeetCode Style)

When problem says “32-bit environment”:

```swift
// Swift
let result = someComputation()
if result > Int(Int32.max) { return Int(Int32.max) }
if result < Int(Int32.min) { return Int(Int32.min) }
return result
```

```cpp
// C++
long long result = someComputation();
if (result > INT_MAX) return INT_MAX;
if (result < INT_MIN) return INT_MIN;
return (int)result;
```

-----

## Part 4: Quick Decision Guide

-----

### Which Type Should I Use?

```
Sum of array up to 10^5 elements, each up to 10^9?
→ Max sum = 10^5 × 10^9 = 10^14 → Use long long / Int64

Two numbers multiplied up to 10^9 each?
→ Product = 10^18 → Use long long / Int64

Just counting elements up to 10^6?
→ Use int / Int32 (max ~2×10^9, safe)

Dealing with very precise decimals?
→ Use double

Need exact huge integers (crypto, big multiply)?
→ Use array/string digit-by-digit (like LeetCode 43)
```

-----

### Error Quick Reference Card

|Error   |Root Cause            |One-Line Fix                     |
|--------|----------------------|---------------------------------|
|TLE     |Too slow algorithm    |Reduce time complexity           |
|MLE     |Too much memory       |Use efficient structures         |
|Overflow|Value too big for type|Use larger type or clamp         |
|WA      |Logic mistake         |Check edge cases                 |
|RE      |Crash at runtime      |Check bounds, null, zero division|
|CE      |Syntax error          |Fix compile errors               |
|PE      |Wrong output format   |Match exact spacing/newlines     |

-----

### The Golden Rules

```
1. When multiplying two Int32 values → always use Int64
2. When problem says "32-bit" → clamp your final answer
3. When numbers are astronomically large → use String/Array approach
4. When getting TLE → think about complexity before optimizing code
5. When getting WA → always test: empty input, single element, negatives, max values
```

-----
# » About This Repository

## » Table of Contents
- Repository Purpose
- Problem Domains
- Code Standards
- Documentation Philosophy
- Contribution Guidelines
- Author Footer

---

## » Repository Purpose
This repository is a **curated collection of algorithm solutions, structured explanations, and code examples**.  
Each solution emphasizes:
- Clear logic
- Efficient implementations
- Beginner-friendly explanations
- Consistent documentation style

The goal is to **bridge the gap between learning and applying algorithms**, especially for students and early-career developers.

---

## » Problem Domains
This repository covers problems in:
- **Dynamic Programming**
- **Combinatorics and Pascal's Triangle**
- **Array Manipulation**
- **Mathematical Recurrence**
- **Basic Data Structures**

Each topic is presented with:
- Clean, well-commented C++ examples
- Step-by-step derivations
- Reference tables

---

## » Code Standards
All code in this repository follows:
- **Consistent naming conventions**
- **Clear separation of logic and output**
- **Avoidance of unnecessary memory allocations (when possible)**
- **Compatibility with modern C++ compilers**

Where applicable, alternative solutions (e.g., optimized space or time) are also discussed.

---

## » Documentation Philosophy
All content is presented in a **mono-chrome markdown style** with:
- Clear headings (`»`)
- Code examples in dedicated sections
- Under-the-hood breakdowns
- Concept highlights
- Real-world use cases

This structure is designed to make the material **easy to read, reference, and reuse**.

---

## » Contribution Guidelines
If you wish to contribute:
- Follow the established documentation format.
- Ensure code is commented thoroughly.
- Keep solutions beginner-friendly.
- Use the same markdown section naming.

---

## » Author Footer
_Curated and documented by Vemparala Sri Satya RaghuRam — #BeyondCertifications #IndustryOriented #CodeWithRaghuRam_

*This guide covers the full spectrum of what you’ll encounter on LeetCode, Codeforces, HackerRank, and similar platforms.*

<!---LeetCode Topics Start-->
# LeetCode Topics
## Hash Table
|  |
| ------- |
| [0003-longest-substring-without-repeating-characters](https://github.com/vssraghuram/LeetCode/tree/master/0003-longest-substring-without-repeating-characters) |
## String
|  |
| ------- |
| [0003-longest-substring-without-repeating-characters](https://github.com/vssraghuram/LeetCode/tree/master/0003-longest-substring-without-repeating-characters) |
## Sliding Window
|  |
| ------- |
| [0003-longest-substring-without-repeating-characters](https://github.com/vssraghuram/LeetCode/tree/master/0003-longest-substring-without-repeating-characters) |
<!---LeetCode Topics End-->