<div align="center">
  <img src="https://github.com/user-attachments/assets/501c98ee-809c-4376-b32d-6d38ae07c489" width="100%" alt="Raghuram's DSA Notes Banner"/>
</div>

<div align="center">

![Trees](https://img.shields.io/badge/Trees-161b22?style=for-the-badge&labelColor=161b22&color=161b22)
![Graphs](https://img.shields.io/badge/Graphs-161b22?style=for-the-badge&labelColor=161b22&color=161b22)
![DP](https://img.shields.io/badge/DP-161b22?style=for-the-badge&labelColor=161b22&color=161b22)
![Recursion](https://img.shields.io/badge/Recursion-161b22?style=for-the-badge&labelColor=161b22&color=161b22)
![Sorting](https://img.shields.io/badge/Sorting-161b22?style=for-the-badge&labelColor=161b22&color=161b22)

</div>

# 12. Integer to Roman

## Table of Contents
- [Problem](#problem)
- [Original Code (Before Optimization)](#original-code-before-optimization)
- [Dry Run of Original Code](#dry-run-of-original-code)
- [Q&A Log — Everything Discussed](#qa-log--everything-discussed)
- [Optimized Greedy Code (After Optimization)](#optimized-greedy-code-after-optimization)
- [Alternative Approach: Digit-Position Lookup](#alternative-approach-digit-position-lookup)
- [Multi-Language Implementations](#multi-language-implementations)
- [Quick Revision Sheet](#quick-revision-sheet)

---

## Problem

Convert an integer (1 to 3999) into its Roman numeral string representation.

---

## Original Code (Before Optimization)

```python
class Solution:
    def intToRoman(self, num: int) -> str:
        value_symbols = [
            (1000, 'M'), (900, 'CM'), (500, 'D'), (400, 'CD'),
            (100, 'C'), (90, 'XC'), (50, 'L'), (40, 'XL'), (10, 'X'),
            (9, 'IX'), (5, 'V'), (4, 'IV'), (1, 'I')
        ]

        res = []

        for value, symbol in value_symbols:
            if num == 0:
                break
            count = num // value
            res.append(symbol * count)
            num -= count * value

        return "".join(res)
```

**Approach:** Greedy, value-by-value table lookup. Sorted largest → smallest, including subtractive pairs (CM, XC, IV, etc.) baked directly into the table so no special-case logic is needed.

---

## Dry Run of Original Code

**Input:** `num = 1994`

| value | symbol | num before | count = num // value | symbol × count | num after |
|---|---|---|---|---|---|
| 1000 | M | 1994 | 1 | "M" | 994 |
| 900 | CM | 994 | 1 | "CM" | 94 |
| 500 | D | 94 | 0 | "" | 94 |
| 400 | CD | 94 | 0 | "" | 94 |
| 100 | C | 94 | 0 | "" | 94 |
| 90 | XC | 94 | 1 | "XC" | 4 |
| 50 | L | 4 | 0 | "" | 4 |
| 40 | XL | 4 | 0 | "" | 4 |
| 10 | X | 4 | 0 | "" | 4 |
| 9 | IX | 4 | 0 | "" | 4 |
| 5 | V | 4 | 0 | "" | 4 |
| 4 | IV | 4 | 1 | "IV" | 0 |
| 1 | I | — | — | — | loop breaks |

`res = ['M', 'CM', '', '', '', 'XC', '', '', '', '', '', 'IV']`
`"".join(res)` → **"MCMXCIV"** ✅

**Why it never goes wrong:** the table is sorted biggest → smallest and already contains the subtractive forms (CM, XC, IV...), so greedily taking "as much as fits" at each step can never overshoot. Every awkward digit (4 and 9 in any place) has a dedicated entry, so there's no case in the 1–3999 range where greedy picks incorrectly.

---

## Q&A Log — Everything Discussed

**Q1: How does this dividing mechanism actually work — do we divide by every symbol's number?**

Not divide the *original* `num` by every value — divide the **shrinking remainder** by each value, in descending order. Each division's quotient tells you how many copies of that symbol are needed; the remainder carries forward into the next symbol's division. Once `num` hits 0, the loop `break`s early and skips the rest. In the 1994 example, only 4 of 13 symbols produced a nonzero count (M, CM, XC, IV) — the other 9 still divided, but landed on `count = 0`.

**Q2: What if we used a length/digit-based mechanism instead — e.g., two-digit places multiply with tens symbols, four-digit places multiply with thousands?**

This describes the **place-value decomposition** approach: instead of one flat 13-entry table walked in a loop, build 4 separate lookup tables — one per digit position (thousands, hundreds, tens, ones) — each indexed 0–9. Every position follows the identical repeating shape (0 → "", 1 → single symbol, ..., 4 → subtractive pair, 5 → mid symbol, ..., 9 → subtractive pair to next milestone); only the symbols themselves change per position. Pull out each digit using division + modulo, then index directly into the matching table — no loop required.

**Q3 (self-assessment): How was this thinking?**

The instinct was correct and non-trivial — independently arriving at place-value decomposition is a real alternative to the greedy method, not something everyone thinks of. The one imprecision: the mechanism isn't "multiply with the symbol" (that's how thousands works, repeating `'M'` up to 3 times) — for hundreds/tens/ones it's **lookup-by-digit** into a precomputed table that already encodes the subtractive forms, not repeated multiplication. Right direction, mechanism detail needed sharpening.

**Q4: Explain each and every digit-extraction line in the optimized code.**

For a place at position `P` (1, 10, 100, or 1000):
- `num // P` shifts the target digit down to the units position.
- `% 10` strips off any larger digits still attached, isolating just that one digit.
- Thousands skips `% 10` because `num ≤ 3999`, so `num // 1000` is already a single digit (0–3) with nothing bigger left to strip.

Example with `num = 3947`:
| Position | Expression | Result | Table lookup |
|---|---|---|---|
| Thousands | `3947 // 1000` | 3 | `thousands[3]` = "MMM" |
| Hundreds | `(3947 // 100) % 10` = `39 % 10` | 9 | `hundreds[9]` = "CM" |
| Tens | `(3947 // 10) % 10` = `394 % 10` | 4 | `tens[4]` = "XL" |
| Ones | `3947 % 10` | 7 | `ones[7]` = "VII" |

Concatenated: "MMM" + "CM" + "XL" + "VII" = **"MMMCMXLVII"** (3000+900+40+7 = 3947 ✓)

**Q5: Just translate the Roman numbers into digits (table reference).**

| Digit | thousands | hundreds | tens | ones |
|---|---|---|---|---|
| 0 | "" | "" | "" | "" |
| 1 | "M" | "C" | "X" | "I" |
| 2 | "MM" | "CC" | "XX" | "II" |
| 3 | "MMM" | "CCC" | "XXX" | "III" |
| 4 | — | "CD" | "XL" | "IV" |
| 5 | — | "D" | "L" | "V" |
| 6 | — | "DC" | "LX" | "VI" |
| 7 | — | "DCC" | "LXX" | "VII" |
| 8 | — | "DCCC" | "LXXX" | "VIII" |
| 9 | — | "CM" | "XC" | "IX" |

**Q6: Explain "XL".**

XL = 40. X (10) appears *before* L (50) → subtractive rule applies → 50 − 10 = 40. Order is what flips it: **LX** (X *after* L) = 60 (50 + 10, additive). The subtractive rule only applies to specific recognized pairs: IV, IX, XL, XC, CD, CM — each meaning "one unit below the next milestone."

**Q7: We only built 3 entries for `thousands` (0–3). What if num were 9000?**

`thousands = ["", "M", "MM", "MMM"]` only has indices 0–3 because the problem caps `num` at 3999. `9000 // 1000 = 9`, and `thousands[9]` would throw an **IndexError** — the table simply isn't built for it.

**Why 3999 is the real ceiling:** the largest standard symbol is M (1000). A symbol can repeat at most 3 times before the rules require a subtractive form using the *next symbol up* (e.g. IIII → IV). But there is no symbol above M to subtract from, so there's no subtractive escape hatch past MMM. That hard-caps standard notation at MMM = 3000, making MMMCMXCIX (3999) the largest representable value.

**Fix if 9000+ needed:** replace the fixed lookup with unbounded string repetition for the thousands place only:
```python
thousands_part = "M" * (num // 1000)
```
This has no upper bound (9 → "MMMMMMMMM"), though it isn't classical/standardized notation. A "proper" historical fix would use the vinculum (overbar) convention to represent ×1000 multipliers, but that requires its own extended table since it isn't a standardized rule like the other three places.

---

## Optimized Greedy Code (After Optimization)

```python
class Solution:
    def intToRoman(self, num: int) -> str:
        vals = [(1000,'M'),(900,'CM'),(500,'D'),(400,'CD'),
                 (100,'C'),(90,'XC'),(50,'L'),(40,'XL'),
                 (10,'X'),(9,'IX'),(5,'V'),(4,'IV'),(1,'I')]
        res = []
        for value, symbol in vals:
            if num == 0:
                break
            count, num = divmod(num, value)
            if count:
                res.append(symbol * count)
        return "".join(res)
```

**What changed vs. original:**
- `divmod(num, value)` replaces separate `//` and `-=` lines — one call instead of two.
- `if count:` skips appending empty strings, avoiding 9 wasted insertions into `res` for inputs like 1994.
- Early-exit `if num == 0: break` preserved (this was already a good instinct in the original).

---

## Alternative Approach: Digit-Position Lookup

```python
class Solution:
    def intToRoman(self, num: int) -> str:
        thousands = ["", "M", "MM", "MMM"]
        hundreds  = ["", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"]
        tens      = ["", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"]
        ones      = ["", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"]

        return (thousands[num // 1000] +
                hundreds[(num // 100) % 10] +
                tens[(num // 10) % 10] +
                ones[num % 10])
```

**Why it's faster:** always exactly 4 array lookups + concatenation, regardless of `num` — no loop, no branching, no repeated subtraction. Both approaches are technically O(1) since `num` is bounded, but this one does dramatically less work per call.

**Tradeoff:** less generalizable — relies on Roman numerals' neat repeating 0–9 pattern per digit position. The greedy table approach would adapt more easily to a hypothetical extended/different numeral system; this one needs hand-built tables for every position.

---

## Comparison Table

| Aspect | Greedy Table | Digit-Position Lookup |
|---|---|---|
| Structure | One 13-entry table, loop | Four 10-entry tables, no loop |
| Operations | Up to 13 divisions/iterations | Exactly 4 divisions + 4 mods |
| Generalizes to other ranges/systems | Yes, naturally | No, needs new tables per position |
| Readability | Very direct, value-driven | Requires understanding digit extraction |
| Speed | O(1), bigger constant | O(1), smaller constant |

---

## Multi-Language Implementations

### Python (Optimized Greedy)
```python
class Solution:
    def intToRoman(self, num: int) -> str:
        vals = [(1000,'M'),(900,'CM'),(500,'D'),(400,'CD'),
                 (100,'C'),(90,'XC'),(50,'L'),(40,'XL'),
                 (10,'X'),(9,'IX'),(5,'V'),(4,'IV'),(1,'I')]
        res = []
        for value, symbol in vals:
            if num == 0:
                break
            count, num = divmod(num, value)
            if count:
                res.append(symbol * count)
        return "".join(res)
```

### Swift
```swift
class Solution {
    func intToRoman(_ num: Int) -> String {
        let vals: [(Int, String)] = [(1000,"M"),(900,"CM"),(500,"D"),(400,"CD"),
                                       (100,"C"),(90,"XC"),(50,"L"),(40,"XL"),
                                       (10,"X"),(9,"IX"),(5,"V"),(4,"IV"),(1,"I")]
        var n = num
        var res = ""
        for (value, symbol) in vals {
            if n == 0 { break }
            let count = n / value
            if count > 0 {
                res += String(repeating: symbol, count: count)
                n -= count * value
            }
        }
        return res
    }
}
```

### JavaScript
```javascript
class Solution {
  intToRoman(num) {
    const vals = [[1000,"M"],[900,"CM"],[500,"D"],[400,"CD"],
                  [100,"C"],[90,"XC"],[50,"L"],[40,"XL"],
                  [10,"X"],[9,"IX"],[5,"V"],[4,"IV"],[1,"I"]];
    let res = "";
    for (const [value, symbol] of vals) {
      if (num === 0) break;
      const count = Math.floor(num / value);
      if (count) {
        res += symbol.repeat(count);
        num -= count * value;
      }
    }
    return res;
  }
}
```

### Java
```java
class Solution {
    public String intToRoman(int num) {
        int[] values = {1000,900,500,400,100,90,50,40,10,9,5,4,1};
        String[] symbols = {"M","CM","D","CD","C","XC","L","XL","X","IX","V","IV","I"};
        StringBuilder res = new StringBuilder();
        for (int i = 0; i < values.length && num > 0; i++) {
            int count = num / values[i];
            for (int j = 0; j < count; j++) res.append(symbols[i]);
            num -= count * values[i];
        }
        return res.toString();
    }
}
```

### C++
```cpp
class Solution {
public:
    string intToRoman(int num) {
        vector<pair<int,string>> vals = {
            {1000,"M"},{900,"CM"},{500,"D"},{400,"CD"},
            {100,"C"},{90,"XC"},{50,"L"},{40,"XL"},
            {10,"X"},{9,"IX"},{5,"V"},{4,"IV"},{1,"I"}
        };
        string res;
        for (auto& [value, symbol] : vals) {
            if (num == 0) break;
            int count = num / value;
            while (count--) res += symbol;
            num -= (num / value) * value;
        }
        return res;
    }
};
```

---

## Quick Revision Sheet

- **Greedy table method:** sort symbols largest → smallest, include subtractive pairs (CM, XC, IV...) directly in the table, divide remaining `num` by each value in order, append symbol × count, subtract used amount, repeat.
- **`divmod(num, value)`** = `(num // value, num % value)` in one call — quotient is count, remainder becomes new `num`.
- **Digit-position method:** decompose `num` into digits via `num // place` then `% 10`, use each digit (0–9) as a direct index into a precomputed per-position table.
- **Why `% 10` is needed for hundreds/tens/ones but not thousands:** dividing by a place value leaves higher digits still attached unless that place is already the highest possible (thousands, capped at 3999).
- **Subtractive rule:** smaller symbol *before* larger = subtract (XL = 40); smaller symbol *after* larger = add (LX = 60). Only applies to IV, IX, XL, XC, CD, CM.
- **3999 ceiling:** no symbol exists above M, so there's no subtractive escape past MMM (3000) — caps the largest standard numeral at 3999 (MMMCMXCIX).
- **If 9000+ needed:** `"M" * (num // 1000)` removes the array-bounds limit, at the cost of non-classical notation.
