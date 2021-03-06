# Factum

_What if every function remembered its own output, and knew when and where to acquire its own inputs?_

Factum is a simple agent-inspired framework for computational DAG composition and execution.

## Theory

Object Oriented Programming (OOP) was originally conceived, at least in part, as an abstraction of the actor model of distributed functional programming in a serial computing environment.

Factum represents a simple return to that vision. It's a functional paradigm facilitated by an object oriented implementation.

Core to the idea is the `Fact` object: a class that defines `inputs`, a `function` and an `output`. The inputs can change, but they're not meant to. Thus the Factum represents the function in a particular context: a fact - it always represents the same information.

It's basically a function that is responsible for gathering its own inputs and caching its output (so other function objects can efficiently gather their own inputs).

These `Fact` objects, taken together are wired up to require inputs from each other and generally form a directed acyclic graph, though there is no mechanism to enforce a DAG structure.

# Use

```
from factum import Fact

def a_node():
    print('A running!')
    return 1

def b_node():
    print('B running!')
    return 2

def c_node(*args):
    print('C running!')
    return args[0] + args[1]

def d_node(self, **kwargs):
    return self._transformation(**kwargs)

def _transformation(self, cthing):
    print('D running!')
    return cthing + 1

# Fact(function, inputs, name)

a = Fact(a_node)
b = Fact(b_node, None, 'Beta')
c = Fact(c_node)
a > c
b > c
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
