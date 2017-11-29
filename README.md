# Swift initialisation: Required vs Designated vs Convenience init

## Designated
- Desginated intialisers are primary intialisers for a class.
- They fully initialise all properties introduced in that class.
- They call an appropriate super intialiser to initialise all remaining properties in super its superclass.

```
A designated initializer must call a designated initializer from its immediate superclass.
```

## Prerequisite
- Class A has designated init a1 and a2

```
class A {
	init(a: Int, b: Int) {}
	init(c: Int) {}
}
```

- Class B inherits class A
- Class B has a designated init b, all initialisers from class A will be ignored (we can only initialise B with init b, not a1 or a2)

```
class B: A {
	init(d: Int) { super.init() }
}

B(d: 1)
B(a: 1, b: 2) // compile error
B(c: 2) // OK
```

- If class B doesn't have any designated initialisers, all designated initialisers from class A will be passed to B; which means you can initialise B with a1 or a2

```
class B: A {
	// don't have any designated intialisers
}

B(d: 1)
B(a: 1, b: 2) // oK
B(c: 2) // OK
```


## Required
- A `Required` initialiser ensure that a class and all its sub-classes can be initialised with that init
- A has an required initialiser a1

```
class A {
	init(a: Int, b: Int) {}
	required init(c: Int) {}
}
```

- B inherits A and has its own designated initialiser b, so that, all the intialisers from A will be ignored
-> Conflict!!!
Therefore, all the required initalisers from A must be implemented in B; otherwise, there will be compile error.

```
class B: A {
	init(d: Int) { super.init() }
	required required init(c: Int) { super.init(c: c) }
}

B(d: 1)
B(a: 1, b: 2) // compile error
B(c: 2) // OK
```

Or, we can change the initialiser b to `convenience`

## Convenience
- Supporting intialisers

```
- A convenience initialiser must call another initializer from the same class. (1)
- A convenience initializer must ultimately call a designated initializer.
```
- (1) means a convenience initialiser can call another convenience or designated intialier from the same class level (absolutely not from its superclass)
- (2) means convenience init call another and that another call another and so on; but there is an convenience initialiser in that chain finally call an designated intilisers:

```
ConI_1 -> ConI_2 -> ... -> ConI_n -> DesI // expected
ConI_1 -> ConI_2 -> ... -> ConI_n -> ConI_1 // failed
```

- When a class only has `convenience` initialisers, initialisers from its superclass are usable.
- Therefore, when super class has `required` initialisers and you don't want reimplement them all in super class, you use `convenience` intialisers.

```
class A {
	init(a: Int, b: Int) {}
	required init(c: Int) {}
}

class B: A {
	convenience init(d: Int) { super.init() }
}

B(d: 1)
B(a: 1, b: 2) // OK
B(c: 2) // OK
```

# References
- https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Initialization.html
- https://stackoverflow.com/questions/41596785/whats-the-difference-between-a-required-initializer-and-a-designated-initialize
- http://www.codingexplorer.com/designated-initializers-convenience-initializers-swift/