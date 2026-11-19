# C++ for DSA & Coding Interviews

This chapter is a focused crash course on C++ features that come up repeatedly in DSA and coding interviews. Instead of covering the entire language, we will concentrate only on the parts that matter for solving problems efficiently in interviews.

---

## 1. Common Includes

On platforms like LeetCode, most headers are included automatically. But when writing C++ locally, in competitive programming, or on whiteboard/interview platforms (CoderPad, HackerRank, CodeSignal), you need to know what to include. 

Here are the headers that cover 95% of DSA problems:

```cpp
#include <vector>
#include <string>
#include <unordered_map>
#include <unordered_set>
#include <map>
#include <set>
#include <queue>          // queue and priority_queue
#include <stack>
#include <deque>
#include <algorithm>      // sort, lower_bound, reverse, etc.
#include <climits>        // INT_MAX, INT_MIN, LLONG_MAX
#include <numeric>        // accumulate, iota, gcd
#include <functional>     // greater<>, function<>
#include <sstream>        // stringstream for parsing
#include <cmath>          // sqrt, abs, pow, ceil, floor

using namespace std;      // acceptable in interviews, not in production
```

### Purpose of Each Header

| Header | What It Provides | DSA Use Case |
| :--- | :--- | :--- |
| `<vector>` | `vector` | Dynamic arrays, adjacency lists, DP tables |
| `<string>` | `string` | String manipulation, character operations |
| `<unordered_map>` | `unordered_map` | Frequency counting, Two Sum, $O(1)$ average lookups |
| `<unordered_set>` | `unordered_set` | Visited tracking, duplicate detection |
| `<map>` / `<set>` | `map`, `set` | Sorted access, range queries ($O(\log N)$ balanced BST) |
| `<queue>` | `queue`, `priority_queue` | BFS, heaps, Dijkstra's algorithm |
| `<stack>` | `stack` | Monotonic stack, iterative DFS, parenthesis matching |
| `<deque>` | `deque` | Sliding window maximum, double-ended operations |
| `<algorithm>` | `sort`, `lower_bound`, `reverse`, etc. | Sorting, binary search, STL algorithms |
| `<climits>` | `INT_MAX`, `INT_MIN`, `LLONG_MAX` | Sentinel values, overflow boundaries |
| `<numeric>` | `accumulate`, `iota`, `gcd` | Prefix sums, range initialization, number theory |
| `<functional>` | `greater<>`, `function<>` | Min-heap comparator, recursive lambdas |
| `<sstream>` | `stringstream` | String tokenization, parsing space/delimiter-separated words |
| `<cmath>` | `sqrt`, `ceil`, `floor`, `abs`, `pow` | Mathematical computations |

---

## 2. Data Types in Memory: Signed vs. Unsigned & Ranges

C++ is a statically typed language. Every variable occupies a fixed number of bytes in memory, where $1 \text{ Byte} = 8 \text{ Bits}$. A data type with $N$ bits can represent $2^N$ unique states.

### How Numbers are Stored in Memory

```
┌────────────────────────────────────────────────────────┐
│                   8-bit Byte Layout                    │
├────────┬───┬───┬───┬───┬───┬───┬───────────────────────┤
│ Bit 7  │ 6 │ 5 │ 4 │ 3 │ 2 │ 1 │ Bit 0 (LSB)           │
│  (MSB) │   │   │   │   │   │   │                       │
└────────┴───┴───┴───┴───┴───┴───┴───────────────────────┘
```

#### 1. Unsigned Representation
- All $N$ bits are used to store the numerical magnitude.
- Cannot represent negative values.
- **Formula for Range:** $0$ to $2^N - 1$.
- Example ($8\text{ bits}$): $00000000_2 = 0$ to $11111111_2 = 255$.

#### 2. Signed Representation (Two's Complement)
- Modern architectures store signed integers using **Two's Complement**.
- The **Most Significant Bit (MSB)** acts as the **sign bit**:
  - `0` indicates a positive number or zero.
  - `1` indicates a negative number.
- **Formula for Range:** $-2^{N-1}$ to $2^{N-1} - 1$.
- **Why is the negative range 1 larger than the positive range?**
  - Zero ($0$) uses the sign bit `0` ($00000000_2$), leaving $2^{N-1} - 1$ positive values.
  - The negative side doesn't have to share a representation for zero, so it can represent all $2^{N-1}$ negative values (e.g., in 8-bit, $-128$ to $+127$).

---

### Complete Data Types, Memory & Range Reference Table

| Type | Bytes / Bits | Signed / Unsigned | Formula for Range | Exact Range | DSA Use Case |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `bool` | 1 byte (8 bits) | Unsigned (boolean) | `0` or `1` | `false` (0), `true` (1) | Visited arrays, flags, DP base states |
| `char` | 1 byte (8 bits) | Signed | $-2^7$ to $2^7 - 1$ | $-128$ to $127$ | Characters, ASCII operations |
| `unsigned char` | 1 byte (8 bits) | Unsigned | $0$ to $2^8 - 1$ | $0$ to $255$ | Extended ASCII, byte buffers, bitmasks |
| `short` | 2 bytes (16 bits) | Signed | $-2^{15}$ to $2^{15} - 1$ | $-32,768$ to $32,767$ | Memory-saving small numbers (rarely used in DSA) |
| `unsigned short`| 2 bytes (16 bits) | Unsigned | $0$ to $2^{16} - 1$ | $0$ to $65,535$ | Port numbers, small counters |
| `int` | 4 bytes (32 bits) | Signed | $-2^{31}$ to $2^{31} - 1$ | $-2,147,483,648$ to $2,147,483,647$ ($\approx \pm 2.14 \times 10^9$) | **Default for 90% of DSA**: indices, counters, graph nodes |
| `unsigned int` | 4 bytes (32 bits) | Unsigned | $0$ to $2^{32} - 1$ | $0$ to $4,294,967,295$ ($\approx 4.29 \times 10^9$) | Bitmasking, hash function computations |
| `long long` | 8 bytes (64 bits) | Signed | $-2^{63}$ to $2^{63} - 1$ | $-9,223,372,036,854,775,808$ to $9,223,372,036,854,775,807$ ($\approx \pm 9.22 \times 10^{18}$) | Prefix sums, multiplication, large constraints ($> 10^9$) |
| `unsigned long long` | 8 bytes (64 bits) | Unsigned | $0$ to $2^{64} - 1$ | $0$ to $18,446,744,073,709,551,615$ ($\approx 1.84 \times 10^{19}$) | 64-bit bitmasks, rolling hash modulo $2^{64}$ |
| `size_t` | 8 bytes (on 64-bit systems) | Unsigned | $0$ to $2^{64} - 1$ | $0$ to $18.4 \times 10^{18}$ | Return type of `.size()`, `sizeof`, pointer offsets |
| `float` | 4 bytes (32 bits) | IEEE 754 Single Precision | $\pm 1.18 \times 10^{-38}$ to $\pm 3.4 \times 10^{38}$ | $\sim 7$ decimal digits precision | Rarely used in DSA (imprecise) |
| `double` | 8 bytes (64 bits) | IEEE 754 Double Precision | $\pm 2.23 \times 10^{-308}$ to $\pm 1.79 \times 10^{308}$ | $\sim 15\text{--}17$ decimal digits precision | Geometry, probability, floating-point divisions |

---

### Critical Traps in DSA with Signed vs. Unsigned

#### Trap 1: The Signed-Unsigned Comparison Gotcha
In C++, when a signed integer is compared to an unsigned integer, the signed integer is **implicitly converted to unsigned**. If the signed integer is negative, it wraps around to a massive positive number!

```cpp
int i = -1;
vector<int> nums = {1, 2, 3}; // nums.size() returns size_t (unsigned)

if (i < nums.size()) {
    // You expect this to be TRUE, but it is FALSE!
    // -1 cast to size_t becomes 18,446,744,073,709,551,615
}
```

> [!TIP]
> Always cast `.size()` to `int` when doing comparisons or arithmetic:
> ```cpp
> int n = (int)nums.size();
> if (i < n) { /* Safe! */ }
> ```

#### Trap 2: Reverse Loop Underflow with `size_t`
Using `size_t` or `unsigned` in reverse loops creates an infinite loop because unsigned values can never be negative:

```cpp
// ❌ Infinite Loop Bug!
for (size_t i = nums.size() - 1; i >= 0; i--) {
    // When i is 0, i-- wraps around to 18446744073709551615, NEVER becoming < 0!
}

// ✅ Correct: Use signed int
for (int i = (int)nums.size() - 1; i >= 0; i--) {
    // Safely terminates when i becomes -1
}
```

#### Trap 3: The `abs(INT_MIN)` Asymmetry Bug
Because the negative range has one more value than the positive range ($-2^{31}$ vs $2^{31}-1$):
```cpp
int x = INT_MIN; // -2,147,483,648
int y = abs(x);  // ❌ Undefined Behavior / Overflow! +2,147,483,648 cannot fit in a 32-bit signed int
```
If a problem can have numbers down to `INT_MIN` and asks for absolute values, cast to `long long` first:
```cpp
long long y = abs((long long)x); // Safe!
```

---

### The Integer Overflow Trap & `long long`

The most dangerous pitfall with types in C++ is **integer overflow**. In C++, signed integer overflow is **undefined behavior (UB)**. The compiler can do literally anything, including optimizing away your bounds checks or producing silent, incorrect results.

Consider the classic binary search calculation:

```cpp
// ❌ BUG: if left + right > INT_MAX, this is undefined behavior
int mid = (left + right) / 2;

// ✅ CORRECT: safe from overflow
int mid = left + (right - left) / 2;
```

This is not a theoretical concern. When `left` and `right` are both above 1 billion, their sum exceeds the 32-bit signed integer limit ($2,147,483,647$). In C++, the compiler might optimize the code in ways that make the bug impossible to debug.

#### When Should You Use `long long` Instead of `int`?
Always use `long long` in these cases:
1. **Prefix sums of arrays with large values:** The sum of $10^5$ elements each up to $10^9$ can reach $10^{14}$, far exceeding the $2.14 \times 10^9$ limit of `int`.
2. **Multiplication of two large integers:** For example, $n \times (n - 1)$ where $n \approx 10^5$ results in $\approx 10^{10}$, causing immediate 32-bit overflow.
3. **Factorial or combination calculations:** $n!$ or $\binom{n}{k}$ grow astronomically fast.
4. **Modulo arithmetic:** Intermediate sums or products can exceed `int` range before taking `% MOD`.

#### The `1LL *` Trick & Cast Placement
The `1LL *` trick is the idiomatic way to promote an expression to `long long` before an overflow happens:

```cpp
int n = 100000;

// ❌ Overflow: 100000 * 99999 > INT_MAX, undefined behavior
int wrong = n * (n - 1);

// ✅ Safe: 1LL promotes the entire expression to long long
long long correct = 1LL * n * (n - 1);

// ✅ Also safe: explicit cast BEFORE multiplication
long long alsoCorrect = (long long)n * (n - 1);

// ❌ WRONG: casting AFTER multiplication is too late, UB already happened!
long long stillWrong = (long long)(n * (n - 1));
```

> [!IMPORTANT]
> **The placement of the cast matters!**
> `(long long)n * (n - 1)` first promotes `n` to `long long`, which then forces 64-bit multiplication.
> But `(long long)(n * (n - 1))` computes standard 32-bit `int` multiplication first (which overflows into garbage/UB), and then casts that already-corrupted result.

---

### The `size_t` Container Trap

Every STL container's `.size()` method returns `size_t`, which is an unsigned integer. Subtracting from an empty container leads to catastrophic underflow:

```cpp
vector<int> v;  // empty vector: v.size() == 0

// ❌ BUG: v.size() is 0 (unsigned), subtracting 1 wraps to 18,446,744,073,709,551,615!
for (int i = 0; i < v.size() - 1; i++) {
    // This loop attempts to run ~18 quintillion times!
}

// ✅ SAFE Option 1: cast to int first
for (int i = 0; i < (int)v.size() - 1; i++) {
    // (int)0 - 1 = -1; loop correctly does not execute
}

// ✅ SAFE Option 2: check container size first
if (v.size() >= 2) {
    for (int i = 0; i < v.size() - 1; i++) {
        // Safe to iterate
    }
}
```

---

## 3. Modern C++ Essentials: Ternary, `auto` & Structured Bindings

### The Ternary Operator (`?:`)
The ternary operator is a compact alternative to `if-else` for inline decisions:

```cpp
int result = (a > b) ? a : b;  // Inline equivalent of max(a, b)
string label = (count == 1) ? "item" : "items";
```

### Type Deduction with `auto`
The `auto` keyword lets the compiler deduce the type for you. It shines with verbose iterators and nested STL containers:

```cpp
// Cleaner iterator syntax
auto it = myMap.find(key); // Replaces: unordered_map<int, int>::iterator it = ...

// Structured binding extraction
auto [key, value] = *myMap.begin();
```

### Structured Bindings (C++17)
Structured bindings let you unpack pairs, tuples, structs, and map entries directly into named variables:

```cpp
#include <iostream>
#include <utility>
#include <tuple>
#include <unordered_map>
using namespace std;

void demonstrateStructuredBindings() {
    // 1. Unpacking a std::pair
    pair<int, int> point = {10, 20};
    auto [x, y] = point;
    cout << "Point: x=" << x << ", y=" << y << "\n";

    // 2. Unpacking a std::tuple
    tuple<string, int, double> student = {"Alice", 101, 3.95};
    auto [name, rollNo, gpa] = student;
    cout << name << " (" << rollNo << ") GPA: " << gpa << "\n";

    // 3. Iterating over an unordered_map / map cleanly
    unordered_map<string, int> freq = {{"apple", 5}, {"banana", 3}};
    for (const auto& [word, count] : freq) {
        cout << word << " appears " << count << " times\n";
    }
}
```

---

## 4. Operators and Control Flow

### Modular Arithmetic with `%`
Modular arithmetic appears in hashing, cyclic array problems, and large combinatorics questions.

In C++, integer division **truncates toward zero**, meaning `%` can return a **negative result** when the dividend is negative:
```cpp
int r = -7 % 3; // Produces -1, NOT 2!
```

#### Safe Positive Modulo Formula:
To guarantee a non-negative modulo in the range $[0, m - 1]$:
```cpp
int positiveMod = ((n % m) + m) % m;
```

#### The `10^9 + 7` Modulo Pattern:
Many problems request output modulo $10^9 + 7$ (`1e9 + 7`):
```cpp
const int MOD = 1e9 + 7;

long long result = 0;
for (int i = 0; i < n; i++) {
    // Apply modulo at every step of accumulation to avoid overflow
    result = (result + values[i]) % MOD;
}
return (int)result;
```

---

### Short-Circuit Evaluation (`&&` and `||`)
C++ evaluates logical operators from left to right and stops as soon as the truth value is determined (**short-circuiting**). This is essential for clean bounds checking without nested `if` statements:

```cpp
// ✅ Safe: If i or j is out of bounds, grid[i][j] is NEVER accessed!
if (i >= 0 && i < rows && j >= 0 && j < cols && grid[i][j] == 1) {
    // Cell is within matrix bounds AND equal to 1
}
```

---

### Bitwise Operations
Bit manipulation problems are common in DSA. Here are the core operators:

| Operator | Meaning | Example | DSA Use Case |
| :---: | :--- | :--- | :--- |
| `&` | Bitwise AND | `n & 1` | Check odd/even (`n & 1 == 1` is odd) |
| `\|` | Bitwise OR | `mask \| (1 << i)` | Turn on / set bit $i$ |
| `^` | Bitwise XOR | `a ^ b` | Find single non-duplicate number, swap |
| `~` | Bitwise NOT | `~mask` | Invert all bits |
| `<<` | Left shift | `1 << i` | Multiply by $2^i$, generate bitmask |
| `>>` | Right shift | `n >> 1` | Integer division by 2 |

#### Common Bitmask Patterns
```cpp
// 1. Check if bit i is set (0-indexed)
bool isSet = (mask >> i) & 1;

// 2. Set bit i (turn bit on)
mask |= (1 << i);

// 3. Clear bit i (turn bit off)
mask &= ~(1 << i);

// 4. Toggle bit i (invert bit)
mask ^= (1 << i);

// 5. Brian Kernighan's Algorithm: clear lowest set bit
n &= (n - 1);

// 6. Fast Popcount (GCC/Clang built-ins, works on LeetCode)
int bits32 = __builtin_popcount(mask);       // for 32-bit int
int bits64 = __builtin_popcountll(mask64);   // for 64-bit long long
```

> [!NOTE]
> C++ does not have a dedicated unsigned right shift (`>>>`) operator like Java. Right-shifting a signed negative `int` performs an **arithmetic shift** (sign-extends by filling with `1`s). For a **logical shift** (filling with `0`s), cast to an unsigned type first: `(unsigned int)n >> 1`.

---

### Loops and Traversal Patterns

```cpp
// 1. Index-based traversal (most common)
for (int i = 0; i < n; i++) { /* ... */ }

// 2. Backward traversal
for (int i = n - 1; i >= 0; i--) { /* ... */ }

// 3. Range-based for (by value: makes a copy of each element)
for (int num : nums) { /* ... */ }

// 4. Range-based for (by reference: allows direct modification)
for (int& num : nums) {
    num *= 2; 
}

// 5. Range-based for (by const reference: read-only, avoids copies for heavy objects)
for (const string& s : strList) { /* ... */ }

// 6. Structured bindings with range-based for
for (auto& [key, val] : myMap) { /* ... */ }
```

---

## 5. Functions, Pass-by-Reference & Performance

Argument passing is one of the most critical performance factors in C++ DSA.

### The Three Parameter Passing Styles

```cpp
// 1. Pass by Value (SLUG-SLOW: copies the entire container on call)
void processByValue(vector<int> nums) {
    nums[0] = 999; // Modifies only the local copy; caller unaffected
}

// 2. Pass by Reference (FAST: O(1) time/memory, caller's vector modified)
void processByReference(vector<int>& nums) {
    nums[0] = 999; // Directly modifies caller's vector
}

// 3. Pass by Const Reference (FAST: O(1) time/memory, read-only protection)
int calculateSum(const vector<int>& nums) {
    // nums[0] = 999; // ❌ Compile error: nums is read-only
    int sum = 0;
    for (int x : nums) sum += x;
    return sum;
}
```

### The $O(N^2)$ Recursive Trap
When you pass a `vector<int>` by value in a recursive function of depth $N$, C++ deep-copies the entire vector on every call. This silently turns an $O(N)$ algorithm into $O(N^2)$ time and blows up the memory limit (**TLE / MLE**). Always pass recursion state collections by reference (`&`)!

### Built-in `std::swap`
C++ provides a built-in `swap` in `<algorithm>` / `<utility>` that executes in $O(1)$ time in-place without needing temporary variables:

```cpp
swap(arr[i], arr[j]); // In-place swap for any type
```

---

## 6. Lambda Functions & Recursion

Lambdas are anonymous inline functions heavily utilized in DSA for custom sort comparators, min-heaps, and localized DFS helpers.

### Lambda Syntax & Capture Clauses
```
[capture](parameters) -> return_type {
    // function body
};
```

| Capture | Meaning | When to Use |
| :--- | :--- | :--- |
| `[]` | Captures nothing | Standalone comparators |
| `[&]` | Captures all outer variables **by reference** | Recursive helpers, DFS, backtracking |
| `[=]` | Captures all outer variables **by value** | Rarely needed in DSA (causes overhead) |
| `[&x, y]`| Captures `x` by ref, `y` by value | Fine-grained memory control |

#### Custom Sorting with Lambdas
```cpp
// Sort intervals by start time ascending; if start is equal, by end time descending
vector<vector<int>> intervals = {{1, 3}, {2, 6}, {1, 5}};

sort(intervals.begin(), intervals.end(), [](const vector<int>& a, const vector<int>& b) {
    if (a[0] != b[0]) return a[0] < b[0];
    return a[1] > b[1];
});
```

---

### Recursive Lambdas with `std::function`
A lambda assigned to `auto` cannot call itself recursively because its type is not fully deduced until after its definition. To create a recursive lambda (e.g., for DFS or tree traversal inside a method), use `std::function` from `<functional>`:

```cpp
#include <vector>
#include <functional>
using namespace std;

void runGraphDFS(int n, vector<vector<int>>& adj) {
    vector<bool> visited(n, false);

    // Explicit function signature allows recursive self-invocation
    function<void(int)> dfs = [&](int u) {
        visited[u] = true;
        for (int v : adj[u]) {
            if (!visited[v]) {
                dfs(v); // Recursive call
            }
        }
    };

    dfs(0); // Start DFS from node 0
}
```

---

### Canonical Backtracking Template
Here is the standard backtracking pattern in C++, illustrating pass-by-reference and automatic container deep copies:

```cpp
#include <vector>
using namespace std;

class BacktrackingTemplate {
public:
    void backtrack(vector<int>& nums, int start, vector<int>& path, vector<vector<int>>& results) {
        // 1. Collect / Record State
        // In C++, push_back creates a deep copy of 'path' automatically!
        results.push_back(path);

        for (int i = start; i < (int)nums.size(); i++) {
            // 2. Make Choice
            path.push_back(nums[i]);

            // 3. Explore
            backtrack(nums, i + 1, path, results);

            // 4. Undo Choice (Backtrack)
            path.pop_back();
        }
    }

    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> results;
        vector<int> path;
        backtrack(nums, 0, path, results);
        return results;
    }
};
```

---

## 7. Pointers and Memory (DSA Essentials)

Pointers are fundamental to linked structures (Linked Lists, Binary Trees, Tries, Graphs).

### Standard Node Definitions

#### Singly Linked List Node:
```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode* next) : val(x), next(next) {}
};
```

#### Binary Tree Node:
```cpp
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode* left, TreeNode* right) : val(x), left(left), right(right) {}
};
```

### Essential Pointer Operations
```cpp
// 1. Dynamic allocation with new
ListNode* head = new ListNode(10);
head->next = new ListNode(20);

// 2. The Arrow Operator (->)
// head->val is shorthand for (*head).val
int value = head->val; // 10

// 3. Pointer Reassignment
ListNode* curr = head;
curr = curr->next;     // curr now points to the second node (val = 20)

// 4. Always use nullptr (Never use NULL or 0 in modern C++)
ListNode* emptyNode = nullptr;
```

### `new` and Memory Management in Interviews
- `new` allocates memory on the **heap** and returns its memory address.
- In production C++, every `new` must have a corresponding `delete` to prevent memory leaks.
- In coding interviews and online judges (LeetCode, HackerRank), **memory deallocation is universally waived**. The process terminates upon test suite completion, freeing the memory instantly. Focus on problem correctness and runtime complexity over manual calls to `delete`.

---

### Pointers vs. References

| Feature | Pointer (`*`) | Reference (`&`) |
| :--- | :--- | :--- |
| **Can be `nullptr`?** | Yes (`ListNode* p = nullptr`) | No, must refer to valid existing memory |
| **Can be rebound?** | Yes, can point to different addresses | No, once bound, cannot refer to another variable |
| **Syntax** | Requires `*`, `->`, `&` | Clean, behaves like an ordinary variable |
| **Primary DSA Role** | Dynamic node structures (Trees, Lists) | Function parameters (avoids copies) |

```cpp
int a = 10, b = 20;

// Reference: Alias to existing storage
int& ref = a;
ref = b; // Does NOT rebind ref to b; assigns value of b (20) into a!

// Pointer: Direct memory address holder
int* ptr = &a;
ptr = &b;      // Rebinds ptr to hold the address of b
ptr = nullptr; // Can safely be null
```

> [!TIP]
> **Rule of Thumb for DSA:**
> - Use **references (`&`)** for function arguments to avoid expensive copying.
> - Use **pointers (`*`)** for recursive tree and linked list nodes that can be `nullptr` or need to be rebound during traversal.

---

## 8. Key Quick-Reference Notes

### Note 1: Why Do We Use `using namespace std;`?
- **What it does:** It imports all standard library identifiers (e.g. `vector`, `string`, `cout`, `sort`) into the global namespace.
- **In Production (Bad Practice):** Causes namespace collisions if libraries introduce conflicting identifiers.
- **In Interviews (Standard & Recommended):** Interview code is short and self-contained; typing `std::` repeatedly wastes valuable interview time.

### Note 2: Min-Heap Comparator (`greater<T>`)
- By default, `std::priority_queue<int>` is a **Max-Heap** (largest element on top).
- To create a **Min-Heap**, specify all 3 template parameters:
  ```cpp
  priority_queue<int, vector<int>, greater<int>> minHeap;
  ```

### Note 3: Sentinel Values (`INT_MAX`, `INT_MIN`)
- Use sentinel values from `<climits>` as initializers or boundary guards:
  - `INT_MAX` ($\approx 2.14 \times 10^9$) for finding minimums or initializing graph distances in Dijkstra.
  - `INT_MIN` ($\approx -2.14 \times 10^9$) for finding maximums.
  - Sentinel dummy nodes (e.g., `ListNode dummy(0); dummy.next = head;`) eliminate edge-case checks for linked list insertions and deletions.
