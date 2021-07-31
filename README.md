# nsdict

## NSDict is a namespaced dictionary-like container class for Python

This class stores data in a hierarchical fashion, with namespaced strings as keys.

Internally, an NSDict stores keys and values in a dictionary.
Keys have to be strings, but values can be anything, including other NSDicts, hence the hierarchy.

When an NSDict is queried with a namespaced string, the string is broken into its various branches,
and each NSDict corresponding to a branch is queried until the final leaf NSDict, and its values
are returned, which may be a single value, or again a whole NSDict.

NSDicts can be updated, and only leaf values will be overwritten. Leaf NSDicts will be updated
with the incoming data.

NSDicts can be used to represent text data from a filesystem, for example, with paths represented
by the namespace branches, and key/value pairs from the files themselves stored as the leaf
key/values.

Since values can be arrays, or anything else Python permits, data in the tree can be postprocessed
and reassigned for iteration.

## Example usage in a Python shell:

```python
	# import the NSDict code

	>>> from nsdict import NSDict

	# pepare a dict with some initial data.
	# Note that the keys are all of the form "{string0}.{string1}..."
	# These form our namespaces, for example {string1} lives in the namespace of {string0}

	>>> d = { 
	...     "root.branch1.leaf1": "value1",
	...     "root.branch1.leaf2": "value2",
	...     "root.branch2.leaf3": "value3",
	... }

	# create an NSDict from the data
	>>> D = NSDict(d)

	# The string __repr__ method of an NSDict spits out the namespaced keys and values in the container:
	>>> D
	root.branch2.leaf3: value3
	root.branch1.leaf1: value1
	root.branch1.leaf2: value2

	# D is of type NSDict
	>>> type(D)
	<class 'nsdict.NSDict'>

	# D has the following key, in this case only one, the cleverly named "root" namespace
	>>> D.keys()
	['root']

	# Let's see what root has for keys():
	>>> D["root"].keys()
	['branch2', 'branch1']

	# ...and let's see what root.branch1 has for keys...
	>>> D["root.branch1"].keys()
	['leaf1', 'leaf2']

	# Since NSDict claims to be a dict-like container, let's iterate over the key/value pairs in the
	# root namespace:
	>>> for key, value in D["root"].iteritems():
	...     print "[%s] -> [%s]" % (key, value)
	... 
	[branch2] -> [leaf3: value3]
	[branch1] -> [leaf1: value1
	leaf2: value2]

	# So it looks like branch1 contains another NSDict...

	# NSDicts can return a dict of their contents. This will assemble the namespaced keys as single individual
	# keys in a dict, with their corresponding values:
	>>> for key in D.dict().keys():
	...     print "[%s] -> [%s]" % (key, D[key])
	... 
	[root.branch1.leaf2] -> [value2]
	[root.branch1.leaf1] -> [value1]
	[root.branch2.leaf3] -> [value3]

	# When we grab a subtree in a namespace:
	>>> E = D["root.branch1"]
	>>> E
	leaf1: value1
	leaf2: value2

	# we can see it is also an NSDict,
	>>> type(E)
	<class 'nsdict.NSDict'>

	# and it has the namespaced keys of that namespace, now as root keys, with their values
	>>> E.dict().keys()
	['leaf1', 'leaf2']
```

## Hierarchy

Basically, the structure from the example
```python
D = NSDict({ 
	"root.branch1.leaf1": "value1",
	"root.branch1.leaf2": "value2",
	"root.branch2.leaf3": "value3",
})
```

can be visualised as the following: 

```
NSDict
{_store}
  └── 'root': NSDict
              {_store}
                ├── 'branch1': NSDict
                │                {_store}
                │                  ├── 'leaf1': 'value1'
                │                  └── 'leaf2': 'value2'
                └── 'branch2': NSDict
                                 {_store}
                                   └── 'leaf3': 'value3'
```
where NSDict refers to an NSDict instance, and the \_store
is the dictionary that it used to contain its branches and values.

So basically an NSDict is a hierarchical tree structure, with a namespaced string key system.

## But, why in the ever loving f...?

Because I can.

Also, it is useful for making environments for simple computer languages, such as [pretzyl](https://github.com/vigilantesculpting/pretzyl).



