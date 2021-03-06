## 5. Know what functions C++ silently writes and calls

- default constructor if no constructor defined by user.
- copy constructor if not defined by user.
- assignment operator if not defined by user.
- destructor if not defined by user.


## 6. Explicitly disallow the use of compiler-generated functions you do not want

## 7. Declare destructor virtual in polymorphic base classes
- polymorphic base class should declare virtual destructors.
  If a class has any virtual functions, it should have a virtual destructor.
- class not designed to be base classes or not designed to be used polymorphically
  should not declare virtual destructor.

## 8. Prevent exceptions from leaving destructors
- Destructors should never emit exceptions.
  If functions called in a destructor may throw, the destructor should catch any exceptions,
- If class clients need to be able to react to exceptions thrown during an operation,
  the class should provide a regular function that performs the operation.

## 9. Never call virtual functions during construction or destruction

## 12. Copy all part of a object

- Copying functions should be sure to copy all of an object’s data members
  and all of its base class parts.
- Don’t try to implement one of the copying functions in terms of the other.
  Instead, put common functionality in a third function that both call.


## 13. Use objects to manage resources

- To prevent resource leaks,
  use RAII objects that acquire resources in their constructors and release them in their destructors.
- Two commonly useful RAII classes are tr1::shared_ptr and auto_ptr.
  tr1::shared_ptr is usually the better choice, because its behavior when copied is intuitive.
  Copying an auto_ptr sets it to null.


## 14. Think carefully about copying behaviour in resource-managing objects

- Copying an RAII object entails copying the resource it manages,
  so the copying behavior of the resource determines
  the copying behavior of the RAII object.
- Common RAII class copying behaviours are:
  - Disallowing copying
  - Performing reference counting
  - Copy the underlying resource
    such as the standard string type
  - Transfer ownership of the underlying resource, use ~auto_ptr~


## 15. Provide access to raw resources in resource-managing class

- APIs often require access to raw resources,
  so each RAII class(like shared_ptr) should offer a way to get at the resource it manages.
- Access may be via explicit conversion or implicit conversion.
  In general, explicit conversion is safer,
  but implicit conversion is more con- venient for clients.

**shared_ptr** and **auto_ptr** provide both:

- get method
- pointer dereferencing operators overriding.


## 16. Use the same form in corresponding uses of new and delete

- If you use [] in a new expression, you must use [] in the corresponding delete expression.
- If you don’t use [] in a new expression, you mustn’t use [] in the corresponding delete expression.
- Be careful of ~typedef~.


## 17. Store newed objects in smart pointers in standalone statements

- Store newed objects in smart pointers in standalone statements.
  Failure to do so can lead to subtle resource leaks when exceptions are thrown.

``` cpp
  // ~priority()~ may be called between ~new Widget~ and ~shared_ptr~.
  processWidget(std::tr1::shared_ptr<Widget>(new Widget), priority());
```


# Designs and Declarations

## 18. Make interfaces easy to use correctly and hard to use incorrectly


- Good interfaces are easy to use correctly and hard to use incorrectly.
  You should strive for these characteristics in all your interfaces.
- Way to facilitate correct use include consistency in interfaces and
  behavioural compatibility with built in types.
- Ways to prevent errors include:
  - creating new types
  - restricting operations on types, constraining object values
  - and eliminating client resource management responsibilities
- ~tr1::shared_ptr~ supports custom deleters.
  This prevents the cross-DLL problem,
  can be used to automatically unlock mutexes.


## 19. Treat class design as type design.

- Before defining a new type, be sure to consider all the issues in this item.


## 20. Prefer pass-by-ref-to-const to pass-by-value

- Prefer pass-by-reference-to-const over pass-by-value.
  It’s typically more efficient and it avoids the slicing problem.
- The rule doesn’t apply to
  built-in types and STL iterator and function object types.
  For them, pass-by-value is usually appropriate.


## 21. Don't try to return a reference when you must return an object

Never return
- a pointer or reference to a local stack object,
- a reference to a heap-allocated object,
- or a pointer or reference to a local static object
if there is a chance that more than one such object will be needed.

(Item 4 provides an example of a design where returning a reference to a local static is reasonable, at least in single-threaded environments.)



## 22.

- Declare data members private. It gives clients
  syntactically uniform access to data,
  affords fine-grained access control,
  allows invariants to be enforced,
  and offers class authors implementation flexibility.
- protected is no more encapsulated than public.


## 24.

why ctor isn't declared explicit.

## 33. Avoid hiding inherited names

- Names in derived classes hide names in base classes.
  Under public inheritance, this is never desirable.
- To make hidden name visible again, employ using declarations or
  forwarding functions.

## 34. Differentiate between inheritance of interface and inheritance of implementation.


- pure virtual functions must be redeclared by any concrete class that inherits them.
  and typically have no definition in abstract classes.
  The purpose of this is to have derived classes inherit a *function interface* only.

- simple virtual functions
  provide an implementation that derived classes may override.
  The purpose of declaring a simple virtual function is
  to have derived classes inherit a function interface as well as a default implementation.
  A better way is using pure virtual functions with a protected default implementation.

- non-virtual functions
  The purpose of declaring a non-virtual function is
  to have derived classes inherit a function interface as well as a mandatory implementation.


Two common mistakes:
- to declare all functions non-virtual.
  non-virtual destructors are problematic(Item 7).
- to declare all member functions virtual.


### TtR
- Inheritance of interface is different from inheritance of implementation.
  Under public inheritance, derived classes always inherit base class interfaces.
- Pure virtual functions specify inheritance of interface only.
- Simple (impure) virtual functions specify inheritance of interface plus inheritance of a default implementation.
- Non-virtual functions specify inheritance of interface plus inheritance of a mandatory implementation.


## 35. Consider alternatives to virtual functions.

- The Template Method Pattern via the Non-Virtual Interface Idiom
- The Strategy Pattern via Function Pointers
- The Classic Strategy Pattern

- Use the non-virtual interface idiom (NVI idiom), a form of the Template Method design pattern that wraps public non-virtual member functions around less accessible virtual functions.
- Replace virtual functions with function pointer data members, a stripped-down manifestation of the Strategy design pattern.
- Replace virtual functions with tr1::function data members, thus allowing use of any callable entity with a signature compatible with what you need. This, too, is a form of the Strategy design pattern.
- Replace virtual functions in one hierarchy with virtual functions in another hierarchy. This is the conventional implementation of the Strategy design pattern.


- Alternatives to virtual functions include the NVI idiom and various forms of the Strategy design pattern. The NVI idiom is itself an example of the Template Method design pattern.
- A disadvantage of moving functionality from a member function to a function outside the class is that the non-member function lacks access to the class’s non-public members.
- tr1::function objects act like generalized function pointers. Such objects support all callable entities compatible with a given target signature.

## 36. Never redefine an inherited non-virtual function

Non-virtual functions are statically bound(Item 37),
while virtual functions are dynamically bound.

See also Item 7.

## 37. Never redefine a function's inherited default parameter value.

Never redefine an inherited default parameter value,
because default parameter values are statically bound, while virtual functions —
the only functions you should be redefining — are dynamically bound.

And use non-virtual interface idiom(Item 35) to do DRY.


## 38. Model "has-a" or "is-implemented-in-terms-of" through composition.

- Composition has meanings completely different from that of public inheritance.
- In the application domain, composition means has-a. In the implementation domain, it means is-implemented-in-terms-of.

## 39. Use private inheritance judiciously

- Private inheritance means is-implemented-in-terms of.
  It’s usually inferior to composition,
  but it makes sense when a derived class needs access to protected base class members
  or needs to redefine inherited virtual functions.
- Unlike composition, private inheritance can enable the empty base optimization.
  This can be important for library developers who strive to minimize object sizes.


## 40. Use multiple inheritance judiciously

## 45. generalized version of copy functions

## 49 & 51 operator new and delete
