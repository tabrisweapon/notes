## Learning Python Essence
### Module
import some_module 会让该module中的代码运行一次，再import就不行了。并且该module会作为一个变量存在在当前作用域。
### Syntax Sugar
#### String
```python
S = 'Spam'
S[-1] # return last item
>>> S[1:] 'pam'
>>> S[0:3] 'Spa'
>>> S[:3] 'Spa'
>>> S[:-1] 'Spa'# Everything but the last
>>> S[:] 'Spam'# Same as S[0:3]
>>> S * 8 'SpamSpamSpamSpamSpamSpamSpamSpam'
>>> S[0] = 'z' # Immutable objects cannot be changed ...error text omitted...
>>> help(S.replace)
Help on built-in function replace:

>>> msg = """
aaaaaaaaaaaaa
bbb'''bbbbbbbbbb""bbbbbbb'bbbb
cccccccccccccc
"""
>>> msg
'\naaaaaaaaaaaaa\nbbb\'\'\'bbbbbbbbbb""bbbbbbb\'bbbb\ncccccccccccccc\n'
```
#### List
```python
>>> L = [123, 'spam', 1.23] # A list of three different-type objects

>>> L.append('NI')
>>> L
[123, 'spam', 1.23, 'NI']
>>> L.pop(2)
1.23
>>> L
[123, 'spam', 'NI']
# Growing: add object at end of list
# Shrinking: delete an item in the middle # "del L[2]" deletes from a list too

# matrix
>>> col2 = [row[1] for row in M] >>> col2
[2, 5, 8]
>>> M
[[1, 2, 3], [4, 5, 6], [7, 8, 9]]

>>> [row[1] + 1 for row in M] # Add 1 to each item in column 2 [3, 6, 9]
>>> [row[1] for row in M if row[1] % 2 == 0] # Filter out odd items [2, 8]
>>> diag = [M[i][i] for i in [0, 1, 2]] >>> diag
[1, 5, 9]
>>> doubles = [c * 2 for c in 'spam'] >>> doubles
['ss', 'pp', 'aa', 'mm']
```
#### Dictionary
```py
>>> D
{'a': 1, 'c': 3, 'b': 2, 'e': 99}
>>> if not 'f' in D: print('missing') # Python's sole selection statement
missing

```
page 128
讲了下type checking，以及并不推荐在py中这么做。因为py是面向interface，这段还简单提了下多态polymorphism，这段过后应该再看一遍
