# Kotlin

## Scope functions

[Official doc](https://kotlinlang.org/docs/scope-functions.html)

| Function   | Usage                                                                                                     | Return                                                  | Context object available as |
|------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------|-----------------------------|
| .let { }   | perform actions on nullable object or transform an object and chain results                               | the result of the lambda                                | it (argument)               |
| .run { }   | transform or perform operations on an object and return a specific result                                 | the result of the lambda                                | this (receiver) or none     |
| .also { }  | perform additional actions while returning the object itself                                              | the original object it was called on ("context object") | it                          |
| .apply { } | configure an object by calling its functions or properties (ex building an object in a more readable way) | the original object it was called on ("context object") | this                        |
| .with { }  | execute multiple operations on an object without explicit reference                                       | the result of the lambda                                | this                        |

From the doc: [ref](https://kotlinlang.org/docs/scope-functions.html#function-selection)

- Executing a lambda on non-nullable objects: let
- Introducing an expression as a variable in local scope: let
- Object configuration: apply
- Object configuration and computing the result: run
- Running statements where an expression is required: non-extension run
- Additional effects: also
- Grouping function calls on an object: with

### Renaming the "it" argument:

object.also { myCustomNameForIt -> .... }