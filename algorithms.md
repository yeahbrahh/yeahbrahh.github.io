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
