# Factum

_What if every function remembered it's own output, and knew when and where to acquire it's own inputs?_

Factum is a simple agent-inspired framework for computational DAG composition and execution.

## Theory

Object Oriented Programming (OOP) was originally conceived, at least in part, as an abstraction of the actor model of distributed functional programming in a serial computing environment.

Fact represents a simple return to that vision. It's a functional paradigm facilitated by an object oriented implementation.

Core to the idea is the `Fact` object: a class that defines `inputs`, a `function` and an `output`. The inputs can change, but their not meant to. Thus the Fact represents the function in a particular context: a fact - it always represents the same information.

It's basically a function that is responsible for gathering it's own inputs and caching it's output (so other function objects can efficiently gather their own inputs).

These `Fact` objects, taken together are wired up to require inputs from each other and generally form a directed acyclic graph, though there is no mechanism to enforce a DAG structure.

# Use

```
from factum import Fact

def a_node(self, **kwargs):
    print('A running!')
    return 1

def b_node(self, **kwargs):
    print('B running!')
    return 2

def c_node(self, **kwargs):
    print('C running!')
    return kwargs['A'] + kwargs['B']

def d_node(self, **kwargs):
    return self._transformation(**kwargs)

def _transformation(self, cthing, **kw):
    print('D running!')
    return cthing + 1

# Fact(function, inputs, name)

a = Fact(a_node)
b = Fact(b_node, None, 'Beta')
c = Fact(c_node, {'A': a, 'B': b})
d = Fact(d_node, {'cthing': c})
d.add_method(_transformation)

d.run()
>>> A running!
>>> B running!
>>> C running!
>>> D running!
>>> 4

# cached
d.run()  
>>> 4

b.run(-1)
>>> B running!
>>> 2

# re-runs downstream of change
d.run()
>>> C running!
>>> D running!
>>> 4

d.visualize()

```
