
Python comes with several useful built-in functions to work with collections. We saw `sorted` in the previous section, and you surely know `len` which returns the number of items in a collection. Here, I will present lesser known functions that can be truly useful: `any`, `all`, `zip`, `sum`, `min` and `max`.

# List comprehension and generator expressions

List comprehension enables you to transform a list into another list. For example, to create the list of the first 10 even numbers, we transform the list `[0,..,9]` : `evens = [n*2 for n in range(10)]`. Starting from Python 3, `range` does not return a list but a generator. So, to be precise, here we transform a range into a list. 

By adding an `if` clause, list comprehension can also be used to filter the source list. Here is another way to create the list of the first 10 even numbers: `evens = [n for n in range(20) if n%2 == 0]`.   

There are situations where the created list is only iterated once, typically in a for loop or as an argument of a function which expects an iterator. In these cases, you can use a generator expression instead of a list comprehension. A generator does not create the full list, but creates an iterator objects. The iterator expression is lazy, it will create the items on demand. When used in a for loop, a new item is generated at each iteration. A generator expression can possibly save a lot of memory and is more efficient. 

To create a generator expression, simply replace the surrounding brackets by braces: `evens = (n*2 for n in range(10))`. A generator expression can only be iterated once and cannot be accessed by index. Nevertheless, there are many cases where they can be used instead of a list comprehension.

# Tests on collections with `any` and `all`

`any` and `all` have a single argument which must be a iterator, so they can handle lists, generator expressions, sets, dictionaries... `any` returns `True` if at least one element of the collection is true. Obviously, `all` returns `True` if all the elements of the collections are true. E.g. : 

```python
any([True,False,False]) # True
any([False,False,False]) # False
all([True,True,True]) # True
all([True,True,False]) # False
```

You might think that lists of booleans are not so common and that adding two built-in functions just for them is not very useful. However, with list comprehension you can easily transform an existing list into a list of booleans.

Consider a list of characters : `characterList`. With `all` you can write : 

```python
# Transform a list of character into a list of boolean.
# Each boolean represents the status of the character
if all([character.isDead for character in characterList]):
    lostGame()
```

instead of : 

```python
lost = True
for character in characterList:
    if not character.isDead:
        lost = False
        break

if lost:
    lostGame()
```

The call to `all` uses a generator expression. When you pass a generator expression to a single argument function, you can omit the surrounding braces.  

`any` and `all` are efficient, because they will stop the evaluation as soon as possible. `any` stops on the first true item. Conversely, `all` stops on the first false item. 

# Combined iterations with `zip`

With `zip` you can iterate several iterators at the same time. For example :

```python
destinations = buildNextDestinations()
for character, destination in zip(characterList, destinations):
    character.moveTo(destination)
```

The length of the returned iterator is the length of the shortest parameter. If you prefer : `zipLen = min(map(len,zipParams))`.  

Often, when you iterate a collection you need the index of the current item. With `zip` you can write: 

```python
for index,character in zip(characterList,range(len(characterList)):
    doSomethingWithIndexAndCharater()
```

instead of the more error prone:

```python
index = 0
for character in characterList:
    doSomethingWithIndexAndCharater()
    index += 1
```

Actually, this pattern is so common that Python comes with a built-in function just for that: `enumerate`.
```python
for index,character in enumerate(characterList):
    doSomethingWithIndexAndCharater()
```

Here is another nice use case for `zip`. Consider the list `path=['A','B','C','D']` representing an ordered sequence, for example a path returned by a path finding algorithm. You can easily build the edges of the path with: `zip(path,path[1:])`. This call will return an iterator containing the pairs: `(A,B)`,`(B,C)` and `(C,D)`. Since `path[1:]` has a length of 3, as expected, the result contains 3 pairs.   

Note that `zip` is not limited to 2 iterators and works with an arbitrary number of iterators. Also, `zip` does not build a new collection, it returns an iterator that you can use in a `for` loop. If you want to reuse the result several times, you can build a list: `list(zip(coll1,coll2,coll3))`.  

# Reducing functions: `sum`, `min` and `max`

In functional programming terminology, reducing a collection means iterating the collection in order to create a single value. `sum`, `min` and `max` all work on number collections, but with generator expressions their scope is much wider. For example:

```python
myScore = sum(chest.value for chest in chestList)
opponentStrength = max(character.strength for character in opponentList)
```

`min` and `max` do not support empty collections and will raise a ValueError exception when they receive one. `sum` returns 0  for an empty collection.