# Type notation

JavaScript is an untyped language, but we still need to communicate expectations
to other people. This document tries to describe a way of communicating these
expectations.

In particular, we're interested in:

  - A **structural type system**, which closely models dynamic language and OO idioms.
  - Something that allows static analysis where possible.
  - Something that allows deriving runtime contracts.

> **NOTE**  
> This document is a draft.


## 1. Types

Types are logical propositions for a particular expression or value in a
program. Types in this notation are matched structurally.


### 1.1. Primitive types

This notation defines the following names for primitive types in JS:

  - `Undefined` — matches the value `undefined`
  - `Null` — matches the value `null`
  - `Number` — matches any primitive numeric value
  - `String` — matches any primitive textual value
  - `Boolean` — matches any primitive boolean value

### 1.2. Tuple types

Sometimes we're interested in a sequence of types with a fixed size. That's
captured by the Tuple type, which uses the `,` operator to determine this
sequence of types.

```ml
(* A sequence of types, first String, then Number *)
type String, Number
:
["Hello", 1]
```

### 1.3. Record types

A record is a collection of key/value pairs that is matched structurally. The
object to be matched must contain *at least* the exact keys described in the
type. Matching partial records is further described later in this document.

```ml
(* Matches a record containing x: and y: keys *)
type { x: Number, y: Number }
:
{ x: 10, y: 20 }
```

### 1.4. Function types

A function in JavaScript is always variadic, but the intention of a particular
piece of code usually isn't. Functions may also not be pure, and we'd like to
signal that intention. So, a function type must be able to provide expectations
for input, output, and observable effects.

```ml
(* A nullary function *)
type () => String
:
() => "Hello"

(* A simple function type, receives a Number, outputs a String *)
type (Number) => String
:
(x) => x.toString()

(* A binary function. Multiple arguments use Tuple notation *)
type (Number, Number) => Number
:
(x, y) => x * y

(* Variadic arguments are captured with the Ellipsis operator `...`
   The function below takes one String, and many numbers, and returns
   a string
 *)
type (String, ...Number) => String
:
(label, ...values) => label + ": " + values.join(", ")

(* Effects are provided with the `::` operator *)
type ('a as { name: String }) => Undefined :: (throws TypeError), (mutates 'a)
:
(person) => {
  if (typeof person.name !== "string") {
    throw new TypeError("Expected a person.");
  }
  person.name = person.name.toUpperCase();
}

(* A `this` type may be given with the special operator `.` *)
type { name: String }.() => Undefined :: mutates
:
function() {
  this.name = this.name.toUpperCase();
}
```

### 1.5. Top type

This notation defines `Any` as the top type, which stands in for any type.

### 1.6. Union types

Because idiomatic expressions in an untyped language may work on more than one
concrete type, we need a concept of `union` to signal the types that are
accepted. The union operator `or` is used for this:

```ml
Undefined or Null       (* Accepts either Undefined or Null *)
```

### 1.7. Intersection types

Functions in JavaScript may have more than one type signature. To capture these
cases, we use intersection types. Intersections are described with the `and`
operator:

```ml
(* A function that works on Numbers or Strings *)
type (Number) => Number
 and (String) => String
:
(x) => {
  if (typeof x === "number") {
    return x + 1;
  } else {
    return x + "!";
  }
2}
```

### 1.8. Optional parameters

Sometimes parameters or keys in a record are optional. This is supported by the
intersection types above, but since they are common place in JS, this notation
also provides the `?` postfix operator to make writing these signatures simpler:

```ml
(* A function with optional parameters *)
type (String, String?) => String
:
(path, encoding = "utf8") => fs.readFileSync(path, encoding)

(* Equivalent to *)
type (String) => String
 and (String, String) => String


(* A record with optional parameters *)
type { name: String, age: Number? }
:
{ name: "Alice" }
{ name: "Alice", age: 12 }

(* Equivalent to *)
type { name: String }
 and { name: String, age: Number }
```


### 1.9. Type aliases

Type aliases serve two purposes: it supports documenting types for
disambiguation, when the types are concrete but too generic, and it supports
constraints. We'll talk more about constraints later in this document. Type
aliases are given inline with the `is` operator:

```ml
type (path is String, encoding is String) => String
:
(path, encoding) => fs.readFileSync(path, encoding)
```

### 1.10. Type parameters

Types might have parameters, sometimes called *generics* in mainstream
languages. Parameters in this notation are described implicitly, by just
prefixing an identifier with the `'` (apostrophe) character:

```ml
type ('value) => ('ignored) => 'value
:
(value) => (_) => value
```

Some objects are parametric, and you may want to provide a specific parameter to
them. This is the case with Array, for example. In this notation, specifying
parameters is done by simply juxtaposing the types:

```ml
type Array String
:
["Hello", "World"]
```

### 1.11. Row types

In order to support object-oriented idioms, and functions operating on records,
this notation defines row types. Row types allow matching part of a record, and
storing the unmatched part in a variable, which can then be used to reconstruct
the full type of the record later on. Rows are described with the special `|`
operator on records:

```ml
type ({ 'person | firstName: String, lastName: String })
  => { 'person | name: String }
:
(person) => {
  let newPerson = Object.assign(person, {
    name: person.firstName + ' ' + person.lastName
  });

  delete newPerson.firstName;
  delete newPerson.lastName;

  return newPerson;
}
```

### 1.12. Subtyping and variance

(TBD)


## 2. Constraints and dependent types

While the type system above should reasonably support most use cases, there are
times where you want to express more guarantees. This section defines
constraints for types that are related to other types, and dependent types for
types that depend on values.


### 2.1. Type constraints

A constraint can be provided in a type itself with the `where` operator. This
allows one to describe code that relies on higher-kinded polymorphism, for
example:

```ml
(* Applicative's .ap *)
type ('F ('a) => 'b) . ('F 'a) => 'F 'b
where 'F is Applicative
:
function ap(applicative) {
  return this.of(this.value(applicative.value));
}
```

(TBD)

### 2.2. Dependent types

(TBD)


## 3. A summary of types

Provided types:

```ml
(*** PRIMITIVES ***)
type Null            (* the null value *)
type Undefined       (* the undefined value *)
type String          (* the String primitive *)
type Boolean         (* the Boolean primitive *)
type Number          (* the Number primitive *)
type None = Null or Undefined

(*** BUILT IN OBJECTS ***)
type RegExp          (* RegExp objects *)
type Symbol          (* Symbol objects *)
type Date            (* Date objects *)
type Int8Array
type Uint8Array
type Uint8ClampedArray
type Int16Array
type Uint16Array
type Int32Array
type Uint32Array
type Float32Array
type Float64Array
type ArrayBuffer
type DataView
type Proxy

type Error           (* Error objects *)
type EvalError      <: Error
type InternalError  <: Error
type RangeError     <: Error
type ReferenceError <: Error
type SyntaxError    <: Error
type TypeError      <: Error
type URIError       <: Error

type Function = (Any...) -> Any

(*** PARAMETERISED TYPES ***)
type Object 'value
type Array 'element
type Map 'key 'value
type Set 'value
type WeakMap 'key 'value
type WeakSet 'value
type Promise 'value 'error
type Generator 'value
type Maybe 'value = None or 'value

(*** OTHER TYPES ***)
(* Tuples *)
type 'first, 'second

(* Function types *)
type ('input, 'optional?, ...'rest) => 'output

(* Record types *)
type { key: 'value }
type { 'row | key: 'value }

(* Unions *)
type 'left or 'right    (* matches one or the other *)

(* Disjoint unions can be given with dependent types *)
type { tag: "Nothing" } or { tag: "Just", value: 'a }

(* Intersections *)
type 'left and 'right   (* matches both *)
```

### 4. A summary of effects

Provided effects:

```ml
effect throws 'types
effect mutates 'types
effect io
```
