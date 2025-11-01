# Algorithms

## N-Queens

```python

def nQueensAll(N):
    if N < 4:
        raise ValueError("Board too small")

    res = []

    def backtrack(row, path):
        if row == N:
            res.append(path[:])
            return
        for col in range(N):
            safe = True
            for (r, c) in path:
                if (c == col
                        or (r - c) == (row - col)
                        or (r + c) == (row + col)):
                    safe = False
                    break
            if safe:
                path.append((row, col))
                backtrack(row + 1, path)
                path.pop()

    backtrack(0, [])
    return res
```
Thought Process:

At first, I only knew that no two queens could be on the same row or column, but I had to figure out how to deal with diagonals. After some research, I found out that the left diagonal could be found using (row – column) and right diagonal (row + column). I took some time to understand the backtracking solution next. 

Coding it:

 I began by creating an empty list to store the results. I decided to put the backtracking function inside nQueensAll(N) so I wouldn’t have to worry about any issues with scope when passing in variables like N and I knew I needed to call backtrack non-recursively inside the other function with 0 and an empty list passed in to begin the backtracking recursion.
 Recursion: I made a base case for when the last row is hit which adds a copy of the path list to the list of results then returns. Then, I set the column to true to start, inside that same loop- I also looped through the path using a tuple to find squares that can be attacked and set them to false. If the square is safe, we put a queen there recursively pass in the path and move to the next row. When it is done recursing, the last entry of path is popped off the stack so it can start over and find a new set of solutions. 
Finally, I returned the results.

## Radix Sort

```python
def radix_base(values_to_sort, base):
    if not values_to_sort or base < 2:
        raise ValueError()

    for val in values_to_sort:
        if not isinstance(val, int) or val < 0:
            raise ValueError()

    max_val = max(values_to_sort)
    exponent = 1
    new_list = values_to_sort[:]

    while max_val // exponent > 0:
        buckets = [[] for _ in range(base)]

        for val in new_list:
            digit = (val // exponent) % base
            buckets[digit].append(val)

        new_list = [num for bucket in buckets for num in bucket]
        exponent *= base

    return new_list
```
 Isolating the digits was my big challenge here. To do that, I used the max value in the list, so I could create a condition for the loop to exit because after the left-most digit place (MSD) was processed, there would be nothing else to do. With a little thought, I figured I could go through each value in the list, divide the value by the base and take the remainder then I would end up with the LSD and know which bucket the value goes in. The idea became to repeat this process on all the digits in each number. Finally, just combine the lists and you have one sorted list! By the end, I understood how the base controlled which digit I was isolating, and how repeating the process for every digit place led to a fully sorted list.


## Dirty Deck

```python
from playingcard import PlayingCard, CardSuit, _valid_rank_, _convert_to_rank
from collections.abc import Container
import unittest
import random


_full_deck_ = [PlayingCard(s, r) for s in CardSuit for r in range(1, 14)]


class DirtyDeck(Container):

    def __init__(self, *, hide=None):
        self.deck = _full_deck_.copy()
        self.hidden = None
        if hide is not None:
            if not _valid_rank_(hide):
                raise ValueError(f"{hide} is not a card rank")
            self.hidden = _convert_to_rank(hide)

    def __str__(self):
        retstr = ""
        for c in self.deck:
            retstr += f"{str(c)} "
        return retstr

    def __contains__(self, c):
        return c in self.deck

    def __len__(self):
        return len(self.deck)

    def __iter__(self):
        return iter(self.deck)

# -- To shuffle an array a of n elements (indices 0..n − 1):

# for i from n − 1 down to 1 do
#      j ← random integer such that 0 ≤ j ≤ i
#      exchange a[j] and a[i]

    def shuffle(self):
        for i in range(self.__len__() - 1, 0, -1):
            j = random.randint(0, i)
            self.deck[j], self.deck[i] = self.deck[i], self.deck[j]
        if self.hidden is not None:
            for card in self.deck[:]:
                if card.rank == self.hidden:
                    self.deck.remove(card)
                    self.deck.append(card)

    def deal(self):
        if self.__len__() <= 13:
            raise ResourceWarning("low deck")

        return self.deck.pop(0)


if __name__ == "__main__":

    d = DirtyDeck()  # rework as unittests
    print(d)
    print(f"len={len(d)}")

    for rank in [10, "Jack", "Queen", "King", "Ace", "Joker", 2]:
        _ = DirtyDeck(hide=rank)

    try:
        _ = DirtyDeck(hide=15)
    except Exception:
        print("invalid hide fails")

```
I began by reviewing playingcard.py to understand how to access each card’s rank, which I needed for several methods. After fixing a small typo in the starter code that prevented access to some attributes, I focused on implementing deal(). I added an exception to prevent dealing when too few cards remained, then removed and returned the top card with pop(0).

The shuffle() method took a bit more thought, but following the lab instructions helped. I used a backward loop to swap cards randomly, then handled the hidden rank case by moving any matching cards to the bottom. The hardest part was debugging randomness—since each run can look different, it’s tricky to tell whether an issue is fixed or just didn’t appear that time. Overall, this lab was straightforward and helped reinforce my understanding of loops, conditionals, and randomness in Python.

## Breadth First Search

```python
from edgegraph import (
    VertexEL,
    GraphEL,
)


def bfs(graph: GraphEL, start: VertexEL):

    if graph is None or start is None:
        raise ValueError("Invalid graph or vertex")
    if start.name not in graph._vertices:
        return []

    seen = set()

    seen.add(start)

    tuple_list = [(start, )]

    i = 0
    while i < len(tuple_list) and tuple_list[i]:
        new_list = []
        for vertex in tuple_list[i]:
            for edge in graph.incident(vertex):
                v1, v2 = edge.ends()
                neighbor = v1 if v2 == vertex else v2
                if neighbor not in seen:
                    seen.add(neighbor)
                    new_list.append(neighbor)
        if new_list:
            tuple_list.append(tuple(new_list))
        i += 1

    return tuple_list
```
Breadth-First Search (BFS) is a way to explore a graph level by level. In my bfs function, I start by checking that both the graph and the starting vertex are valid. Then I create a data structure to keep track of which vertices I’ve already visited (I used a set here because the order does not matter and there can be no duplicate vertices in a graph) and a tuple_list that stores the vertices level by level. Each tuple represents one level of the search.

Inside the while loop, I look at each vertex in the current level. For every edge that touches that vertex, I find the neighboring vertex using v1 if v2 == vertex else v2. If that neighbor hasn’t been seen, I mark it as seen and add it to the next level. When that next level is all seen, I append it to tuple_list. By the end, tuple_list holds the vertices grouped by their distance from the start.

## Cuckoo Hashing

```python
from collections.abc import Collection
from math import e, modf, floor, sqrt
from itertools import filterfalse, chain
from copy import copy
import unittest


# DO NOT CHANGE ANY CODE BETWEEN LINE X AND LINE Y
# ******* THIS IS LINE X ******************

GOLDEN = (1.0 + 5.0 * 0.5) / 2.0


def swap(a, b):
    return b, a


# ***************************************************
# why do we inherit from Collection rather than Set?
# because Set requires too many methods to be defined


class CuckooSet(Collection):

    # *** course helper routines *******
    def _hash2_(self, obj, table_size):
        try:
            h = hash(obj)  # may raise exception
        except Exception:
            raise TypeError("unhashable key")

        h %= table_size

        f1, _ = modf(h * e)
        f2, _ = modf(h * GOLDEN)
        h1 = floor(table_size * f1)
        h2 = floor(table_size * f2)
        if h1 == h2:
            h2 = (h2 + 7) % table_size
        return h1, h2

    def _members_(self, tab):  # returns iterator
        return filterfalse((lambda x: x is None), tab)

    def _allmembers_(self):
        return chain(self._members_(self.htab1), self._members_(self.htab2))

    # ** course methods ****

    def __init__(self, iter=[], *, s=128):
        if s < 4:
            raise ValueError("set size too small")
        self._size_ = s
        self._MAXSWAPS_ = floor(s * 0.6)
        self.htab1 = [None] * s
        self.htab2 = [None] * s
        for i in iter:
            self.add(i)

    def __len__(self):
        count1 = len(list(self._members_(self.htab1)))
        count2 = len(list(self._members_(self.htab2)))
        return count1 + count2

    def _resize_(self):
        oldself = copy(self)
        self.__init__(oldself, s=oldself._size_ * 2)

    def __str__(self):
        fstr = ""
        for v in self._allmembers_():
            if len(fstr):
                fstr += ", "
            fstr += str(v)
        return fstr

    def __iter__(self):
        return self._allmembers_()
# ******* THIS IS LINE Y ******************

    def __contains__(self, x):
        if x is None:
            raise ValueError("key may not be None")

        h1, h2 = self._hash2_(x, len(self.htab2))

        return x == self.htab1[h1] or x == self.htab2[h2]

    def add(self, x):
        if x is None:
            raise ValueError("key may not be None")
        h1, h2 = self._hash2_(x, len(self.htab1))
        counter = 0
        while counter < self._MAXSWAPS_:
            if self.htab1[h1] is None:
                self.htab1[h1] = x
                return
            else:
                x, self.htab1[h1] = swap(x, self.htab1[h1])
                h1, h2 = self._hash2_(x, len(self.htab1))

                if self.htab2[h2] is None:
                    self.htab2[h2] = x
                    return
                else:
                    x, self.htab2[h2] = swap(x, self.htab2[h2])
                    h1, h2 = self._hash2_(x, len(self.htab1))
                    counter += 1
        self._resize_()
        self.htab1 = [None] * len(self.htab1)
        self.htab2 = [None] * len(self.htab2)
        data = [x for x in self.htab1 + self.htab2 if x is not None]
        for value in data:
            self.add(value)
        self.add(x)

    def remove(self, x):
        if x is None:
            raise ValueError("key may not be None")
        if not self.__contains__(x):
            raise KeyError("key not found")
        h1, h2 = self._hash2_(x, len(self.htab1))
        if x == self.htab1[h1]:
            self.htab1[h1] = None
        elif x == self.htab2[h2]:
            self.htab2[h2] = None

    def discard(self, x):
        if x is None:
            raise ValueError("key may not be None")
        h1, h2 = self._hash2_(x, len(self.htab1))
        if x == self.htab1[h1]:
            self.htab1[h1] = None
        elif x == self.htab2[h2]:
            self.htab2[h2] = None
```
In the add method, each value gets two possible locations determined by two hash functions. First, we try to place the value in the first table. If that spot is already full, it swaps the existing element out and tries to insert it into its second position in table two. If that spot is also full, the process repeats, swapping values back and forth until an open spot is found or the swap limit is reached. When the counter hits max swaps, the table resizes and rehashes all elements to make more space (I wonder if there may be a way to improve this by not having to refill the table each time?).

The __contains__ method is simple—it uses the same two hash locations to check for the element in both tables. Because every value can only ever exist in one of two specific locations, membership can be determined in O(1) time.

The remove method works by first checking if the item exists using __contains__. If it does, the function finds which table it’s in and sets that slot to None. If it isn’t found, it raises a KeyError.

The discard method is similar to remove, but it skips the error-handling part. It tries to delete the key from either table if it exists, but if it doesn’t, nothing happens.

## Lists as Trees

```python
def findKey(k,t):
    if k is None:
        raise ValueError("null key")
    if t is None:
        raise ValueError("no tree")
    try:
        return t.index(k)
    except ValueError:
        raise LookupError("key not found")


def addKey(k,t):
    if k is None:
        raise ValueError("no key")
    if t == None:
        raise ValueError("no tree")
    if type(k) != type(t[index]):
        raise TypeError("mixed types not allowed")

    index = 0

    while True:
        while len(t) <= index:
            t.append(None)

        if t[index] is None:
            t[index] = k
            return t
        if k < t[index]:
            index = index * 2 + 1
        elif k > t[index]:
            index = index * 2 + 2
        else:
            return t


def deleteKey(k, t):
    if type(k) != type(t[index]):
        raise TypeError("mixed types not allowed")

    index = findKey(k, t)

    left_index = index * 2 + 1
    right_index = index * 2 + 2

    if t[index] is None:
        return t

    if isLeaf(t[index], t):
        t[index] = None
        return t

    if hasOneChild(t[index], t):
        if index < len(t) and t[left_index] is not None:
            t[index] = t[right_index]
            t[right_index] = None
        else:
            t[index] = t[left_index]
            t[left_index] = None

    if right_index >= len(t) or t[right_index] is None:
        return t
    successor_index = findMinIndex(right_index, t)
    t[index] = t[successor_index]
    t[successor_index] = None
    return t


def isLeaf(node, tree):
    index = findKey(node, tree)
    left_index = index * 2 + 1
    right_index = index * 2 + 2

    left_missing = left_index >= len(tree) or tree[left_index] is None
    right_missing = right_index >= len(tree) or tree[right_index] is None

    return left_missing and right_missing


def hasOneChild(node, tree):
    index = findKey(node, tree)
    left_index = index * 2 + 1
    right_index = index * 2 + 2

    left_exists = left_index < len(tree) and tree[left_index] is not None
    right_exists = right_index < len(tree) and tree[right_index] is not None

    return left_exists ^ right_exists


def findMinIndex(index, tree):
    while True:
        left_index = index * 2 + 1
        if left_index >= len(tree) or tree[left_index] is None:
            break
        index = left_index
```
A list representing a tree is one where the root is the very first index and as you move down the list, the value in the (i * 2 + 1) index is the left child while the value in the ( i * 2 + 2) index is the right child of tree[i].
One of the challenges I faced working with a list representing a tree was understanding how to properly reference the children and values inside the nodes because I had previously only used linked lists where I could just point to node.left for example. 
Finding and adding were easy enough, to add I just needed to keep comparing node until I found and empty spot on the left or right side. When it came to deleting, I knew I would have three cases to deal with.
Nodes with: 

•	No children

•	One child

•	Two children

I wrote Boolean methods to find leaves and nodes with a single child. To determine where to put nodes with two children, I had to create another method to find the index of the successor so I could replace the node with its in order successor. 



## Counting String Permutations

```python
from collections import Counter


def countPermStr(string1, string2):
    if string1 is None or string2 is None:
        raise ValueError("Cannot pass in None")
    if string2 == "":
        raise ValueError("string2 cannot be empty")
    if len(string2) > len(string1):
        raise ValueError("string2 cannot be longer than string1")

    target_counter = Counter(string2)
    matches = 0

    window_size = len(string2)
    window_counter = Counter(string1[:window_size])

    if window_counter == target_counter:
        matches += 1

    for i in range(window_size, len(string1)):
        start_char = string1[i - window_size]
        end_char = string1[i]

        window_counter[start_char] -= 1
        if window_counter[start_char] == 0:
            del window_counter[start_char]

        window_counter[end_char] += 1

        if window_counter == target_counter:
            matches += 1

    return matches
```

I quickly got a working solution using a simple loop that compared each substring window to the target, which gave me time to focus on improving efficiency. Intuitively, I realized I could optimize the runtime by maintaining a running count of characters within the current window. Instead of rebuilding the count each time, I updated it dynamically as characters entered and left the window, effectively simulating a sliding motion across the string.
## Palindrome Number

```python
class Solution(object):
    def isPalindrome(self, x):
        n = str(x)
        if n == n[::-1]:
            return True
        return False       
       
```

## Roman to Integer

```java
class Solution {
    public int romanToInt(String s) {
        HashMap<Character,Integer> hashtable = new HashMap<>();
         hashtable.put('I', 1);
         hashtable.put('V', 5);
         hashtable.put('X', 10);
         hashtable.put('L', 50);
         hashtable.put('C', 100);
         hashtable.put('D', 500);
         hashtable.put('M', 1000);
         int sum = 0;

        for(int i = 0; i < s.length(); i++){
            int current = hashtable.get(s.charAt(i));
            if(i + 1 < s.length() && current < hashtable.get(s.charAt(i + 1))){
                 sum -= current;
            } else{
                sum += current;
            }
    }
        return sum;



 }
}
```
## Find the Index of the First Occurence in a String

```java

class Solution {
    public int strStr(String haystack, String needle) {
        int index = haystack.indexOf(needle);
        if(index == -1){
            return -1;
        }
        return index;
        
    }
}
```

## Length of Last Word

```java
class Solution {
    public int lengthOfLastWord(String s) {
       s = s.trim();
       int lastIndex = s.lastIndexOf(" ");
       String lastWord = s.substring(lastIndex + 1);
       return lastWord.length();

    }
}
```

## Contains Duplicate

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i] == nums[i + 1]) {
                return true;
            }
        }
        return false;

    }
}
```

## Sign of the Product of an Array

```java
class Solution {
    public int arraySign(int[] nums) {
        int k = 0;
        for(int i = 0; i < nums.length; i++){
            if(nums[i] < 0){
                k++;
            }
            if(nums[i] == 0){
                return 0;
            }
        }
        if(k % 2 == 0){
            return 1;
        }

        return -1;
    }
}
```

## To Lower Case

```java
class Solution {
    public String toLowerCase(String s) {
        StringBuilder built = new StringBuilder();
        for(int i = 0; i < s.length(); i++){
            char c = s.charAt(i);
            switch(c){
                case 'A':
                    built.append('a');
                    continue;
                case 'B':
                    built.append('b');
                    continue;
                case 'C':
                    built.append('c');
                    continue;
                case 'D':
                    built.append('d');
                    continue;
                case 'E':
                    built.append('e');
                    continue;
                case 'F':
                    built.append('f');
                    continue;
                case 'G':
                    built.append('g');
                    continue;
                case 'H':
                    built.append('h');
                    continue;
                case 'I':
                    built.append('i');
                    continue;
                case 'J':
                    built.append('j');
                    continue;
                case 'K':
                    built.append('k');
                    continue;
                case 'L':
                    built.append('l');
                    continue;
                case 'M':
                    built.append('m');
                    continue;
                case 'N':
                    built.append('n');
                    continue;
                case 'O':
                    built.append('o');
                    continue;
                case 'P':
                    built.append('p');
                    continue;
                case 'Q':
                    built.append('q');
                    continue;
                case 'R':
                    built.append('r');
                    continue;
                case 'S':
                    built.append('s');
                    continue;
                case 'T':
                    built.append('t');
                    continue;
                case 'U':
                    built.append('u');
                    continue;
                case 'V':
                    built.append('v');
                    continue;
                case 'W':
                    built.append('w');
                    continue;
                case 'X':
                    built.append('x');
                    continue;
                case 'Y':
                    built.append('y');
                    continue;
                case 'Z':
                    built.append('z');
                    continue;
                default:
                    built.append(c);
                    continue;
            }
        }
        return built.toString();
    }
}
```
## Move Zeroes

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int j = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
                j++;
            }
        }
    }
}
```

## Add Digits

```java
class Solution {
    public int addDigits(int num) {
        int digit = num % 10;

        if(num < 10){
            return num;
        }
       return addDigits(digit + num /10);
        
    }
}
```
