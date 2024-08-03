# Dealing with existing code 

Dealing with existing code can be a challenge on its on. How can you fix or extend code your do not really understand?

## understanding existing code 

To start to understand code one approach to consider is to refacter it!

- move declarations of variables to where they are used first
- initialize all variables at declararion
- make variables `const` if possible
- if the initialization of a variable is the result of multiple statements, consider moving this code into a function.
- create lambda's to group pieces of code you understand and can now give a name, preverably a verb to express what it is doing.
- rename variables that do not have descriptive names, change the name as soon as your think "aha, but it is really X".
- prefer the most fitting tool for the job (unique_ptr iso shared_ptr, lock_guard iso unique_lock, etc)
- Use braces and indentation to express code flow
- remove dead code such as unreferences variables
- remove comments that do not add value
- add [[nodiscard]]
- change deeply nested code to fail early and factor out functions, the happy path is left-aligned
- move repeated code into function, or keep the result in a wider scope.
- add documentation to API functions once you understand them, for public API's

to be continued...
