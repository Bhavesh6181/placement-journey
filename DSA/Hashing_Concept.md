# 🔑 Hashing — Complete DSA Quick Revision Guide

> **How to use this file:** Read top to bottom once properly. After that, use the Table of Contents to jump directly to what you need before solving problems.

---

## 📚 Table of Contents

1. [What is Hashing?](#1-what-is-hashing)
2. [How HashMap Works Internally](#2-how-hashmap-works-internally)
3. [Complexity Cheatsheet](#3-complexity-cheatsheet)
4. [Core Operations (Code)](#4-core-operations-code)
5. [Key Patterns in DSA Problems](#5-key-patterns-in-dsa-problems)
   - [Pattern 1 — Frequency Count](#pattern-1--frequency-count)
   - [Pattern 2 — Two Sum / Complement Lookup](#pattern-2--two-sum--complement-lookup)
   - [Pattern 3 — Prefix Sum + HashMap](#pattern-3--prefix-sum--hashmap)
   - [Pattern 4 — Sliding Window + HashMap](#pattern-4--sliding-window--hashmap)
   - [Pattern 5 — Group / Categorize by Key](#pattern-5--group--categorize-by-key)
   - [Pattern 6 — First / Last Seen Index](#pattern-6--first--last-seen-index)
   - [Pattern 7 — Counting Distinct / Unique Elements](#pattern-7--counting-distinct--unique-elements)
   - [Pattern 8 — HashSet for Visited / Seen](#pattern-8--hashset-for-visited--seen)
6. [HashMap vs HashSet vs Array — When to Use What](#6-hashmap-vs-hashset-vs-array--when-to-use-what)
7. [Common Mistakes to Avoid](#7-common-mistakes-to-avoid)
8. [Classic Problems Mapped to Patterns](#8-classic-problems-mapped-to-patterns)
9. [Things to Remember Before Every Hash Problem](#9-things-to-remember-before-every-hash-problem)
10. [Edge Cases Checklist](#10-edge-cases-checklist)

---

## 1. What is Hashing?

Hashing is a technique to store and retrieve data in **O(1) average time** using a **key → value** mapping.

```
Key  →  Hash Function  →  Index in Array  →  Value stored there
"apple"  →  hash("apple")  →  4  →  "fruit"
```

**In simple words:**
Instead of searching through every element (O(n)), you convert your key into an index directly and jump there instantly.

### The Hash Function
A hash function takes any key and produces a fixed-size number (the index).

```
hash(key) = some_computation(key) % table_size
```

A good hash function:
- Is **fast** to compute
- Spreads keys **uniformly** across the table
- Produces **few collisions** (two keys mapping to same index)

### What is a Collision?
When two different keys produce the same hash index.

```
hash("abc") = 5
hash("xyz") = 5   ← Collision!
```

**How collisions are handled:**
- **Chaining** — Each index holds a linked list. Multiple keys at same index form a chain.
- **Open Addressing** — If index is taken, probe for next empty slot.

Java's `HashMap` uses **Chaining**. Worst case (all keys collide) = O(n), but average is O(1).

---

## 2. How HashMap Works Internally

```
Index 0:  → null
Index 1:  → [("apple", 5)] → [("mango", 3)]   ← Chaining (collision)
Index 2:  → null
Index 3:  → [("cat", 7)]
Index 4:  → null
...
Index n:  → [("dog", 2)]
```

**Java HashMap specifics (good to know):**
- Default capacity = **16 buckets**
- Load factor = **0.75** → When 75% full, it **resizes (doubles)** and **rehashes** all keys
- After Java 8: If a chain at one bucket grows beyond **8 nodes**, it converts to a **Red-Black Tree** → O(log n) worst case at that bucket

**Python dict specifics:**
- Uses open addressing (not chaining)
- Resizes when 2/3 full
- Maintains insertion order (Python 3.7+)

---

## 3. Complexity Cheatsheet

| Operation | Average Case | Worst Case |
|-----------|-------------|------------|
| Insert (put) | O(1) | O(n) |
| Lookup (get) | O(1) | O(n) |
| Delete (remove) | O(1) | O(n) |
| Contains key | O(1) | O(n) |
| Iterate all keys | O(n) | O(n) |

> **Worst case O(n)** happens only when all keys collide into one bucket — practically never with a good hash function. **Treat it as O(1) in interviews.**

**Space Complexity:** O(n) — where n is the number of key-value pairs stored.

---

## 4. Core Operations (Code)

### Java

```java
// --- HashMap ---
Map<String, Integer> map = new HashMap<>();

map.put("apple", 3);                    // insert / update
map.get("apple");                       // returns 3, or null if missing
map.getOrDefault("mango", 0);          // safe get with default
map.containsKey("apple");              // true/false
map.containsValue(3);                  // true/false (O(n))
map.remove("apple");                   // delete key
map.size();                            // number of entries
map.isEmpty();                         // true/false

// Increment frequency — most common pattern
map.put(key, map.getOrDefault(key, 0) + 1);

// Iterate
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    String k = entry.getKey();
    int v = entry.getValue();
}
for (String key : map.keySet()) { }
for (int val : map.values()) { }

// --- HashSet ---
Set<Integer> set = new HashSet<>();
set.add(5);
set.contains(5);    // true
set.remove(5);
set.size();
```

### Python

```python
# --- dict (HashMap) ---
mp = {}
mp["apple"] = 3                     # insert / update
mp.get("apple", 0)                  # safe get with default
"apple" in mp                       # containsKey
mp["apple"]                         # get (KeyError if missing)
del mp["apple"]                     # delete
len(mp)                             # size

# Increment frequency
mp[key] = mp.get(key, 0) + 1

# Counter — built-in frequency map
from collections import Counter
freq = Counter([1, 2, 2, 3, 3, 3])  # {3:2, 2:2, 1:1}
freq.most_common(2)                 # top 2 elements

# defaultdict — avoids KeyError, sets default type
from collections import defaultdict
mp = defaultdict(int)               # default value = 0
mp[key] += 1                        # works without checking existence
mp = defaultdict(list)              # default value = []
mp[key].append(val)

# Iterate
for k, v in mp.items(): pass
for k in mp.keys(): pass
for v in mp.values(): pass

# --- set (HashSet) ---
s = set()
s.add(5)
5 in s          # contains
s.remove(5)
s.discard(5)    # remove without error if missing
```

---

## 5. Key Patterns in DSA Problems

---

### Pattern 1 — Frequency Count

**What it is:** Count how many times each element appears.

**When you see these words:** "most frequent", "k most common", "majority element", "anagram check", "duplicate", "count occurrences"

**Template:**
```python
freq = {}
for x in arr:
    freq[x] = freq.get(x, 0) + 1

# Now freq[x] = how many times x appeared
```

**Example Problems:**
- Top K Frequent Elements
- Valid Anagram (compare two frequency maps)
- Majority Element (freq > n/2)
- First Unique Character

**Anagram Check:**
```python
def isAnagram(s, t):
    return Counter(s) == Counter(t)
```

---

### Pattern 2 — Two Sum / Complement Lookup

**What it is:** For each element, check if its "complement" (what you need to reach the target) already exists in the map.

**When you see these words:** "two numbers that sum to", "pair with difference", "find if complement exists"

**Template:**
```python
seen = {}
for i, x in enumerate(arr):
    complement = target - x
    if complement in seen:
        return [seen[complement], i]
    seen[x] = i
```

**Key Insight:** Instead of checking all pairs O(n²), store what you've seen so far and check if the pair needed is already there — O(n).

**Variations:**
- Two Sum → `target - x`
- Pair with difference k → `x - k` or `x + k`
- Three Sum → Fix one element, Two Sum on rest

---

### Pattern 3 — Prefix Sum + HashMap

**What it is:** Track running sum and use a map to find if a subarray with required sum exists.

**When you see these words:** "subarray sum equals k", "number of subarrays with sum", "contiguous subarray", "sum divisible by k"

**Core Idea:**
```
If prefixSum[j] - prefixSum[i] = k
Then subarray from i+1 to j has sum = k
So: we need prefixSum[j] - k = prefixSum[i] to exist
```

**Template:**
```python
def subarraySum(nums, k):
    count = 0
    prefix_sum = 0
    seen = {0: 1}          # ← CRITICAL: base case, empty subarray has sum 0

    for x in nums:
        prefix_sum += x
        count += seen.get(prefix_sum - k, 0)
        seen[prefix_sum] = seen.get(prefix_sum, 0) + 1

    return count
```

**Why `{0: 1}` at start?** If the entire prefix itself equals k, we need to count it. Pre-seeding 0 handles that.

**Variation — Sum divisible by k:**
```python
# Store (prefix_sum % k) instead of prefix_sum
seen = {0: 1}
prefix_sum = 0
for x in nums:
    prefix_sum = (prefix_sum + x) % k
    ...
```

---

### Pattern 4 — Sliding Window + HashMap

**What it is:** Maintain a window of elements, use a map to track contents of window, expand/shrink based on condition.

**When you see these words:** "longest substring with at most k distinct", "minimum window substring", "no repeating characters"

**Template — Longest substring with at most k distinct characters:**
```python
def lengthOfLongestSubstringKDistinct(s, k):
    freq = {}
    left = 0
    result = 0

    for right in range(len(s)):
        freq[s[right]] = freq.get(s[right], 0) + 1

        while len(freq) > k:              # shrink window
            freq[s[left]] -= 1
            if freq[s[left]] == 0:
                del freq[s[left]]
            left += 1

        result = max(result, right - left + 1)

    return result
```

**Template — No repeating characters (k=all unique):**
```python
def lengthOfLongestSubstring(s):
    last_seen = {}
    left = 0
    result = 0

    for right, ch in enumerate(s):
        if ch in last_seen and last_seen[ch] >= left:
            left = last_seen[ch] + 1     # jump left past the duplicate
        last_seen[ch] = right
        result = max(result, right - left + 1)

    return result
```

---

### Pattern 5 — Group / Categorize by Key

**What it is:** Group elements that share some property under a common key.

**When you see these words:** "group anagrams", "group by", "find all elements that are equal under some transformation"

**Template:**
```python
groups = defaultdict(list)

for word in words:
    key = tuple(sorted(word))     # ← the "canonical key"
    groups[key].append(word)

return list(groups.values())
```

**The trick is choosing the right key:**

| Problem | Key to use |
|---------|-----------|
| Group Anagrams | `tuple(sorted(word))` |
| Group by first letter | `word[0]` |
| Group numbers by remainder | `num % k` |
| Group by digit frequency | `tuple(Counter(word).items())` |
| Isomorphic strings | encode the pattern: `(0,1,0,2,...)` |

---

### Pattern 6 — First / Last Seen Index

**What it is:** Store the index where you first (or last) saw a key. Use this to calculate distances, lengths of subarrays, etc.

**When you see these words:** "longest subarray", "first occurrence", "distance between", "index of first duplicate"

**Template:**
```python
first_seen = {}

for i, x in enumerate(arr):
    if x in first_seen:
        # Found again! Use the stored index
        length = i - first_seen[x]
    else:
        first_seen[x] = i    # store only FIRST occurrence
```

**Example — Longest subarray with equal 0s and 1s:**
```python
# Replace 0 with -1, find longest subarray with sum = 0
# Then use Prefix Sum + first_seen index pattern
```

---

### Pattern 7 — Counting Distinct / Unique Elements

**What it is:** Count how many distinct elements exist, or find if all elements are unique.

**When you see these words:** "distinct values", "all unique", "duplicates exist", "number of unique"

**Template:**
```python
# Check if all unique
def allUnique(arr):
    return len(arr) == len(set(arr))

# Count distinct
distinct_count = len(set(arr))

# Find duplicates
seen = set()
duplicates = []
for x in arr:
    if x in seen:
        duplicates.append(x)
    seen.add(x)
```

---

### Pattern 8 — HashSet for Visited / Seen

**What it is:** Use a set to track what you've already visited/processed to avoid revisiting.

**When you see these words:** "cycle detection", "avoid revisiting", "graph traversal", "find if path exists"

**Template:**
```python
visited = set()

def dfs(node):
    if node in visited:
        return
    visited.add(node)
    for neighbor in graph[node]:
        dfs(neighbor)
```

**Longest Consecutive Sequence (classic):**
```python
def longestConsecutive(nums):
    num_set = set(nums)
    best = 0

    for n in num_set:
        if n - 1 not in num_set:      # only start from beginning of sequence
            current = n
            streak = 1
            while current + 1 in num_set:
                current += 1
                streak += 1
            best = max(best, streak)

    return best
```

---

## 6. HashMap vs HashSet vs Array — When to Use What

| Structure | Use When | Example |
|-----------|----------|---------|
| **HashMap** | You need key → value mapping, need to store both key and associated data | Frequency count, index storage, grouping |
| **HashSet** | You only care about presence/absence, no associated value | Visited nodes, check duplicates, distinct elements |
| **Array (as map)** | Keys are integers in a known small range (0–26 for lowercase letters, 0–100, etc.) | Character frequency (26 slots), digit frequency |

**Array as frequency map (faster than HashMap for small ranges):**
```python
# For lowercase letters only — use array of size 26
freq = [0] * 26
for ch in s:
    freq[ord(ch) - ord('a')] += 1
```
This is **O(1) guaranteed** (no hash collisions), uses less memory, and is faster in practice.

**When NOT to use HashMap:**
- When keys are a small contiguous integer range → use array
- When you need **sorted order** → use TreeMap (Java) / sorted dict
- When space is critical and elements are few → use array

---

## 7. Common Mistakes to Avoid

### ❌ Mistake 1: Not handling the "not found" case
```python
# WRONG — KeyError if key missing
count = freq[key]

# RIGHT
count = freq.get(key, 0)
```

### ❌ Mistake 2: Forgetting base case in Prefix Sum
```python
# WRONG — misses subarrays starting from index 0
seen = {}

# RIGHT — always seed with {0: 1}
seen = {0: 1}
```

### ❌ Mistake 3: Using mutable types as keys
```python
# WRONG — lists are not hashable
mp[[1,2,3]] = "value"   # TypeError

# RIGHT — convert to tuple
mp[tuple([1,2,3])] = "value"
```

### ❌ Mistake 4: Modifying a map while iterating it
```python
# WRONG
for k in mp:
    del mp[k]    # RuntimeError

# RIGHT
for k in list(mp.keys()):
    del mp[k]
```

### ❌ Mistake 5: Assuming HashMap is ordered (old Java versions)
```python
# In Java, HashMap has NO guaranteed order
# Use LinkedHashMap to maintain insertion order
# Use TreeMap for sorted order

# In Python 3.7+, dict maintains insertion order ✓
```

### ❌ Mistake 6: Counting when you should store index (or vice versa)
```python
# Two Sum needs INDEX, not count
seen[x] = i      # store index
# Frequency problems need COUNT
freq[x] = freq.get(x, 0) + 1
```

---

## 8. Classic Problems Mapped to Patterns

| Problem | Pattern | Key Idea |
|---------|---------|----------|
| Two Sum | Complement Lookup | Store index, check `target - x` |
| Valid Anagram | Frequency Count | Compare two freq maps |
| Group Anagrams | Group by Key | Key = `sorted(word)` |
| Subarray Sum Equals K | Prefix Sum + Map | `seen[prefixSum - k]` |
| Longest Substring No Repeat | Sliding Window | Jump left on duplicate |
| Longest Substring K Distinct | Sliding Window | Shrink when `len(freq) > k` |
| Top K Frequent Elements | Frequency Count + Heap | Count then heapify |
| Majority Element | Frequency Count | freq > n/2 |
| First Unique Character | Frequency Count | Find first char with freq = 1 |
| Longest Consecutive Sequence | HashSet + Start Detection | Only start from `n-1 not in set` |
| Contains Duplicate | HashSet | Add to set, check before adding |
| 4Sum II | HashMap | Store sums of two pairs |
| Isomorphic Strings | Group by Pattern | Encode character pattern |
| Word Pattern | Group by Pattern | Same structure as Isomorphic |
| Happy Number | HashSet (Cycle Detection) | Seen? → Cycle = not happy |
| Minimum Window Substring | Sliding Window + Map | Track required chars, shrink |
| Contiguous Array (0s and 1s) | Prefix Sum + First Index | Replace 0→-1, find sum=0 |
| Subarray Divisible by K | Prefix Sum (mod) | Store `prefixSum % k` |

---

## 9. Things to Remember Before Every Hash Problem

**Ask yourself these 5 questions before coding:**

1. **What is my key? What is my value?**
   - Frequency problem → key=element, value=count
   - Index problem → key=element, value=index
   - Grouping problem → key=canonical form, value=list

2. **Do I need HashMap or HashSet?**
   - Need to store extra info (index, count) → HashMap
   - Just need to check presence → HashSet

3. **Is there a better structure than HashMap?**
   - Small integer range → Array
   - Need sorted order → TreeMap / SortedDict

4. **Do I need a base case? (Prefix Sum problems always need `{0:1}`)**

5. **What happens when key is not found?** (Always use `.get(key, default)`)

---

**Quick Pattern Identifier:**

```
"Find if pair/triplet exists"          → Complement Lookup (Two Sum style)
"Count occurrences / frequency"        → Frequency Map
"Subarray with sum = k"                → Prefix Sum + HashMap
"Longest/Shortest window/substring"    → Sliding Window + HashMap
"Group elements sharing property"      → Group by Canonical Key
"Avoid revisiting / cycle detection"   → HashSet for Visited
"All unique / any duplicate"           → HashSet size comparison
"First/Last index of element"          → Store index in HashMap
```

---

## 10. Edge Cases Checklist

Before submitting any hashing solution, verify:

- [ ] **Empty input** — empty array, empty string → return 0, [], or appropriate value
- [ ] **Single element** — does your logic still work?
- [ ] **All same elements** — e.g., `[3,3,3,3]` — does frequency map handle this?
- [ ] **Negative numbers** — HashMap handles negatives as keys fine; be careful with modulo in Python vs Java
  - Python: `-7 % 3 = 2` (always positive)
  - Java: `-7 % 3 = -1` (can be negative) → use `((n % k) + k) % k`
- [ ] **Overflow** — very large prefix sums in Java → use `long` not `int`
- [ ] **Duplicate keys** — putting same key again overwrites value; is that what you want?
- [ ] **Order matters?** — HashMap is unordered; if order matters use LinkedHashMap / preserve indices
- [ ] **Base case for prefix sum** — did you seed `{0: 1}`?

---

## 🔁 Quick Revision Summary (Read this every time)

```
HASHING = O(1) lookup by converting key → index via hash function

CORE PATTERNS:
1. Frequency Map          → count occurrences
2. Complement Lookup      → Two Sum style
3. Prefix Sum + Map       → subarray sum problems
4. Sliding Window + Map   → substring/subarray with constraints
5. Group by Canonical Key → anagrams, isomorphic
6. First Seen Index       → longest subarray, distances
7. Distinct / Unique      → set operations
8. Visited Set            → graph traversal, cycle detection

ALWAYS CHECK:
→ Key vs Value (what am I storing?)
→ HashMap vs HashSet vs Array
→ Base case {0:1} for prefix sum
→ .getOrDefault() / .get(key, 0) — never bare dict access
→ Negatives and overflow
→ Empty input and single element
```

---

*Made for DSA quick revision — update this file as you encounter new patterns!*