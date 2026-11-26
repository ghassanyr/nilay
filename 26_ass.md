# CENG203 Data Structures Lab Worksheet #7: Answer Key

**2026 - 2027 Fall Semester | Course ID 30476 | Sections 1 & 2 | Lab date 19.11.2026 | Duration 120 minutes**

Part B only. Answers are kept short; each question is listed by its title and sub-question labels.

## The Array Is the Tree (easy) (10 pts)

**Expected output**

```text
[Q1] parent(4) = 1  leftChild(1) = 3  rightChild(1) = 4
[Q1] parent(1) = 0  parent(2) = 0  parent(0) = 0
[Q1] 20 15 10 4 0  max-heap? true
[Q1] 10 4 15  max-heap? false
[Q1] 9 9 9  max-heap? true
[Q1] 50 30 40 10 20 35  max-heap? true
[Q1] 5 3 4 6  max-heap? false
```

**a)** Fill in the table for the array 20 15 10 4 0 (write - for an index that does not exist), then draw the array as a tree in the box.

| Index | Value | Parent index | Left child index | Right child index |
|---|---|---|---|---|
| 0 | 20 | - | 1 | 2 |
| 1 | 15 | 0 | 3 | 4 |
| 2 | 10 | 0 | - | - |
| 3 | 4 | 1 | - | - |
| 4 | 0 | 1 | - | - |

```text
            20
      15            10
   4      0
```

**b)** Two of the five arrays are not max-heaps. For each of them name the child and the parent that break the rule. In 50 30 40 10 20 35 the value 35 stands on the last level and is larger than the 30 one level above it - why is that allowed?

- [10 4 15]: the child 15 (index 2) is larger than its parent 10 (index 0).

- [5 3 4 6]: the child 6 (index 3) is larger than its parent 3 (index 1).

- 35 is not a child of 30. Its parent is 40 (index 2), and 35 < 40, so the rule holds. Only parent-child pairs are compared.

**c)** parent(1) and parent(2) are both 0. Which property of int division lets one formula serve the left and the right child? What does parent(0) return, and what would (i - 1) / 2 give for i = 0 if i had the unsigned type size_t?

- Integer division truncates: (1 - 1) / 2 = 0 and (2 - 1) / 2 = 1 / 2 = 0, so both children map to parent 0.

- parent(0) = (0 - 1) / 2 = -1 / 2 = 0 in C++ (truncation toward zero). The 0 is an accident: the root has no parent, so the function should handle i = 0 separately.

- With size_t, 0 - 1 wraps to 2^64 - 1; divided by 2 it gives 9223372036854775807, which is not a valid index.

**d)** The BST of Week 6 needs a node with two pointers; the heap needs only an array. Which of the two heap rules makes this possible, and why would the same idea waste memory for a BST? Is every max-heap also a binary search tree?

- The shape rule (complete tree, no gaps) fixes the position of every node, so the links become index arithmetic and no pointers are needed.

- A BST has no fixed shape: an array would need holes (a chain of n nodes needs about 2^n slots).

- No. [20 15 10 4 0] is a max-heap, but its inorder 4 15 0 20 10 is not sorted, so it is not a BST.

## Insert and Bubble Up - Small Numbers (easy) (10 pts)

**Expected output**

```text
[Q2] insert 10: swaps = 0  heap: 10
[Q2] insert  4: swaps = 0  heap: 10 4
[Q2] insert 15: swaps = 1  heap: 15 4 10
[Q2] insert 20: swaps = 2  heap: 20 15 10 4
[Q2] insert  0: swaps = 0  heap: 20 15 10 4 0
[Q2] size = 5  valid = true
       20
   15      10
  4   0
```

**a)** Trace the five insertions by hand: the output shows only the result, the table asks for the steps.

| Insert | Array right after push_back | Swaps (child ↔ parent) | Array after bubbleUp |
|---|---|---|---|
| 10 | 10 | none | 10 |
| 4 | 10 4 | none | 10 4 |
| 15 | 10 4 15 | 15 <-> 10 (1 swap) | 15 4 10 |
| 20 | 15 4 10 20 | 20 <-> 4, then 20 <-> 15 (2 swaps) | 20 15 10 4 |
| 0 | 20 15 10 4 0 | none | 20 15 10 4 0 |

**b)** Why does insert put the new value at the end of the array first, instead of looking for its final place at once?

- The end is the only free leaf, so the tree stays complete with no gaps.

- From there the value can only move up one path, at most one swap per level.

**c)** How many swaps can a single insert need at most when the heap then holds n values? Give the number for n = 5, n = 1000 and n = 1 000 000, and say why a value equal to its parent does not move.

- At most one swap per level, so at most floor(log2 n) swaps.

- n = 5: 2 swaps. n = 1000: 9 swaps. n = 1 000 000: 19 swaps.

- A value equal to its parent does not move: bubbleUp swaps only when the value is strictly larger.

## Remove the Maximum and Bubble Down (medium) (15 pts)

**Expected output**

```text
[Q3] getMax = true  top = 20  size is still 5
[Q3] removeMax = 20: swaps = 2  heap: 15 4 10 0
[Q3] removeMax = 15: swaps = 1  heap: 10 4 0
[Q3] removeMax = 10: swaps = 1  heap: 4 0
[Q3] removeMax =  4: swaps = 0  heap: 0
[Q3] removeMax =  0: swaps = 0  heap: (empty)
[Q3] removeMax on an empty heap = false
```

**a)** Trace the five removals by hand.

| Removed | Array after the last value moved to the root | Swaps (parent ↔ larger child) | Array after bubbleDown |
|---|---|---|---|
| 20 | 0 15 10 4 | 2 (0 <-> 15, then 0 <-> 4) | 15 4 10 0 |
| 15 | 0 4 10 | 1 (0 <-> 10) | 10 4 0 |
| 10 | 0 4 | 1 (0 <-> 4) | 4 0 |
| 4 | 0 | none | 0 |
| 0 | (empty) | none | (empty) |

**b)** Why is the last value moved to the root? What would go wrong if instead the larger child simply moved up into the empty root, then its larger child moved up, and so on?

- The last value fills the gap at the root and keeps the tree complete; only the root is out of place, so one bubbleDown repairs the heap.

- If the larger child kept moving up, the hole would travel down to a leaf and leave a gap at the bottom: the array would no longer be a complete tree, and the last value would still need a place.

**c)** bubbleDown swaps with the larger child. Show with the array 0 15 10 4 (first row of your table) what happens if it swaps with the smaller child instead.

- The root 0 swaps with the smaller child 10: [10 15 0 4].

- Now 15 sits below 10 and is larger than it, so the max-heap rule is broken. Only the larger child is safe.

**d)** The values leave as 20, 15, 10, 4, 0. Why is the order of removal always descending, whatever the order of insertion was? Compare the cost of getMax and removeMax.

- The root is always the largest value, so each removal takes the current maximum, and the new root after bubbleDown is again the largest remaining value.

- The order is therefore always descending, whatever the insertion order.

- getMax costs O(1) (read heap[0]); removeMax costs O(log n) (one bubbleDown path).

## The Guessing Game - Random Numbers (medium) (15 pts)

**a)** Predict the insert phase.

| Insert | Swaps | Heap array after the insert |
|---|---|---|
| 3 | 0 | 3 |
| 17 | 1 | 17 3 |
| 1 | 0 | 17 3 1 |
| 8 | 1 | 17 8 1 3 |
| 9 | 1 | 17 9 1 3 8 |
| 2 | 1 | 17 9 2 3 8 1 |

**b)** Draw the final heap as a tree and check it against the three lines that printTree prints. Which values are leaves? Where is the smallest value - and is the smallest value of a max-heap always in the last position?

```text
       17
   9       2
  3   8   1
```

- Leaves: 3, 8 and 1. The smallest value 1 is in the last position here, but not always: [9 8 3 7] is a max-heap whose smallest value 3 sits at index 2.

**c)** Predict the removal phase.

| Removed | Swaps | Heap array after the removal |
|---|---|---|
| 17 | 2 | 9 8 2 3 1 |
| 9 | 2 | 8 3 2 1 |
| 8 | 1 | 3 1 2 |
| 3 | 0 | 2 1 |
| 2 | 0 | 1 |
| 1 | 0 | (empty) |

**d)** Insert the same six numbers in the same order into the binary search tree of Week 6 (on paper). Compare the two trees: the height, the place of the largest value, how you would look for the value 9, and what decides the shape.

- BST from 3, 17, 1, 8, 9, 2: root 3; left child 1 with right child 2; right child 17 with left child 8, which has right child 9.

- Height: heap 2 (3 levels); BST 3 (path 3 - 17 - 8 - 9). The largest value is the heap root, but in the BST it is at depth 1 (17).

- To find 9: the BST follows one path (3, 17, 8, 9); the heap has no order, so it may need to check every node.

- The heap shape depends only on n; the BST shape depends on the insertion order.

## Interactive Mode (medium) (10 pts)

| You type | The program answers |
|---|---|
| 1, then 5 | 5 inserted, swaps = 0 |
| 1, then 30 | 30 inserted, swaps = 1 |
| 1, then 12 | 12 inserted, swaps = 0 |
| 1, then 30 | 30 inserted, swaps = 1 |
| 3 | Array: 30 30 12 5 and the tree on three lines: 30 - 30  12 - 5 |
| 2 | Removed 30 |
| 2 | Removed 30 |
| 4 | Heap cleared. |
| 2 | Heap is empty! |
| 0 | back to the main menu |

**a)** Play the session. The number 30 is inserted twice: why does the second 30 need exactly one swap, where do the two 30s stand afterwards, and does a heap accept duplicates?

- The second 30 is pushed to index 3 (child of 5), is larger than 5 and swaps up: 30 30 12 5, one swap.

- It stops beside the root because it is equal, not larger, to its parent.

- The two 30s stand at the root (index 0) and at index 1 (left child). A heap accepts duplicates: the rule is parent >= child.

**b)** Option 2 on an empty heap prints Heap is empty!. Which function notices that the heap is empty, which one prints the message, and why is the work divided like this? What would be wrong with a getMax() that returns −1 for an empty heap?

- removeMax notices that the heap is empty and returns false.

- The menu code (case 2 of heapMenu) prints Heap is empty!.

- The class only reports the result and the menu decides what to say, so the same class can be used in other programs.

- A getMax that returns -1 would be wrong, because -1 could be a real value in the heap. A bool result with a reference parameter avoids this.

**c)** Suppose you had written the interactive mode as a separate file interactive.cpp with its own int main() and added it to the project. Which error does the build report, and why? How does the project offer several “programs” with a single main()?

- Error LNK2005: main already defined in main.obj. A project has exactly one main().

- Several programs are offered by normal functions (heapMenu, runWeek07) called from that one main(), so no second main is needed.

## Order Analysis - Increasing and Decreasing Input (hard) (20 pts)

**Expected output (totals only)**

```text
[Q6] insert swaps: increasing = ?  decreasing = ?
[Q6] remove swaps: increasing = ?  decreasing = ?
[Q6] n = 1000 increasing: insert swaps = 7987  remove swaps = 6988
[Q6] n = 1000 decreasing: insert swaps = 0  remove swaps = 7317
```

**a)** Before you switch the block on, predict the swaps of every insert and the array after it - the two insert totals follow from your table. Then set SHOW_Q6 to true, build, run, compare, and write the four totals behind the question marks into the box.

| up: insert | Swaps | Array after | down: insert | Swaps | Array after |
|---|---|---|---|---|---|
| 1 | 0 | 1 | 20 | 0 | 20 |
| 2 | 1 | 2 1 | 18 | 0 | 20 18 |
| 3 | 1 | 3 1 2 | 15 | 0 | 20 18 15 |
| 4 | 2 | 4 3 2 1 | 10 | 0 | 20 18 15 10 |
| 5 | 2 | 5 4 2 1 3 | 5 | 0 | 20 18 15 10 5 |
| 6 | 2 | 6 4 5 1 3 2 | 1 | 0 | 20 18 15 10 5 1 |

- [Q6] insert swaps: increasing = 8   decreasing = 0

- [Q6] remove swaps: increasing = 3   decreasing = 4

**b)** Explain the two extremes: why is increasing order the worst case for insert and decreasing order the best case? How many swaps does the k-th insert need in the worst case?

- Increasing: each new value is the largest so far and must rise to the root. The k-th insert needs floor(log2 k) swaps in the worst case (the depth of its leaf).

- Decreasing: each new value is smaller than its parent, so nothing moves: 0 swaps, the best case.

**c)** With 1000 values the insert phase needs 7987 swaps in one order and 0 in the other - but emptying the heap costs about 7000 swaps in both cases. Why does the order of insertion hardly matter for removeMax, and why is a removal expensive almost every time?

- removeMax moves the last value (usually a small leaf value) to the root and sinks it back down, so about log2 n swaps are needed almost every time, in any order.

- Insertion order changes the values in the heap but not this sinking path, so the removal cost is similar in both cases.

**d)** Fill in the cost table (O-notation, n values in the heap).

| Operation | Best case | Worst case |
|---|---|---|
| insert | O(1) | O(log n) |
| removeMax | O(1) | O(log n) |
| getMax | O(1) | O(1) |
| build a heap with n inserts | O(n) | O(n log n) |

**e)** Every removal phase of this worksheet printed the values from the largest to the smallest. Describe in three steps how a heap can sort an array of n numbers into ascending order, and give the total cost.

- 1. Insert all n values into a max-heap: O(n log n).

- 2. Remove the maximum n times, writing each removed value into the array from the back (position n-1 first, then n-2, and so on).

- 3. The array is now in ascending order. Total cost: O(n log n).

## The Priority Queue (hard) (20 pts)

**Expected output**

```text
[Q7] Deniz (priority 9, ticket 3)
[Q7] Berk (priority 5, ticket 1)
[Q7] Ece (priority 5, ticket 4)
[Q7] Ali (priority 2, ticket 0)
[Q7] Cem (priority 2, ticket 2)
[Q7] dequeue on an empty queue = false
```

**a)** Fill in the table.

| Patient | Priority | Ticket | Key in the heap | Leaves as number |
|---|---|---|---|---|
| Ali | 2 | 0 | 2999 | 4 |
| Berk | 5 | 1 | 5998 | 2 |
| Cem | 2 | 2 | 2997 | 5 |
| Deniz | 9 | 3 | 9996 | 1 |
| Ece | 5 | 4 | 5995 | 3 |

**b)** Why does the key contain 999 - ticket and not + ticket? In which order would the five patients leave with key = priority * 1000 + ticket? What goes wrong for the 1001st patient?

- 999 - ticket makes the earlier ticket the larger key, so among equal priorities the earlier patient leaves first.

- With + ticket the later patient would be bigger, and the order would be Deniz, Ece, Berk, Cem, Ali.

- The 1001st patient (ticket 1000) gets key 5000 + (999 - 1000) = 4999, which reads as priority 4, ticket 0. The key spills into the range of the next lower priority: wrong priority and wrong order (hence MAX_TICKETS).

**c)** A priority queue could also be built from the structures of earlier weeks. Fill in the cost for n waiting items, then say in the box why the heap is the usual choice.

| Structure | enqueue | dequeue (highest priority) |
|---|---|---|
| unsorted array or linked list | O(1) (add at the end or head) | O(n) (scan for the max) |
| linked list kept sorted (Week 3) | O(n) (find the place) | O(1) (take the first) |
| binary search tree (Week 6) | O(log n) if balanced, O(n) worst | O(log n) if balanced, O(n) worst |
| heap | O(log n) | O(log n) |

- The heap is the usual choice: both operations cost O(log n) even in the worst case, with no pointers and a compact array.

- A sorted list gives O(1) dequeue but O(n) enqueue; an unbalanced BST can degrade to O(n).

**d)** A hospital numbers urgency the other way round: 1 is the most urgent, so the smallest number must leave first. Describe two ways to get that: one that turns MaxHeap into a min-heap (which comparisons change?) and one that leaves MaxHeap untouched and changes only the key of PriorityQueue.

- Min-heap: reverse the comparisons. bubbleUp swaps while the value is smaller than its parent; bubbleDown picks the smaller child; the check becomes "no child smaller than its parent".

- Keep MaxHeap, change the key: key = (999 - priority) * 1000 + (999 - ticket). Priority 1 gives the largest key; dequeue returns priority = 999 - key / 1000.

**e)** Commit and push (Week 7 - heaps). Write your repository URL here:

- Student's own repository URL.

- - End of Worksheet -
