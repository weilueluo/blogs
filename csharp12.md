# CSharp 12 In a Nutshell

C# is a general-purpose, type-safe, object-oriented, platform-neutral programming language. Its goal is to boost programmer productivity.

- Unified type system, all types share the same base class.
- Class and interfaces
- Properties, methods and events
    - functions are values
    - events are function members, simplify acting on state changes
    - support patterns for purity
- Automatic memory management
    - does not eliminate pointers, just make it unnecessary most of the time.
- Supports windows 7+, macOS, Linux, Android & IOS, windows 10 devices.
    - Browser through *Blazor* technology

## CLRs, BCLs & Runtimes

<img src="https://raw.githubusercontent.com/weilueluo/note-images/master/2024/10/upgit_20241021_1729525161.png" alt="image-20241021163920905" style="zoom:80%;" />

- **.NET** .NET 8 is Microsoft's flagship open-source runtime, its update history as follows: .NET Core 1.x → NET Core 2.x → .NET Core 3.x → .NET 5 → .NET 6 → .NET 7 → .NET 8. After .NET Core 3, Microsoft removed “Core” from the name and skipped version 4 to avoid confusion with .NET Framework 4.x. .NET framework 4.x is something comes before .NET Core 1.x but it is still supported and in popular use.
- **Windows Desktop and WinUI 3** Both are intended for writing client-rich application that runs on Windows 10 and above. WinUI 3 is a successor of Universal Windows Platform (UWP), and was released in 2022. Windows Desktop APIs existed since 2006 and has huge third party library and community support.
- **MAUI** Multi-platform App UI, for developing mobile application on android and iOS, can be used for desktop apps via Mac Catalyst and WinUI 3. It is an evolution of Xamarin.
- For cross-platform desktop application, a third-party library called Avalonia offers an alternative to MAUI, which is simpler than MAUI with almost complete WPF compatibility.
- **.NET Framework** .NET Framework is Microsoft’s original Windows-only runtime for writing web and rich-client applications that run (only) on Windows desktop/server. No major new releases planned, but the latest release will continue to be supported and maintained due to wealth of existing applications.







- readonly function modifier means it does not modify properties, but not itself cant be modified.

- use @prefix `@using` to use reserved word as identifier.

- use `using var reader = File.OpenText(...)` to close file when out of scope

- use `checked` to explicitly check for arithmetic overflow exception (vice versa `unchecked`)

- string interpolation `$"number is: {x}"`

- collection expression: `char[] vowels = ['a','e','i','o','u'];`, declare collection with square bracket

- value type like `struct` is initialized with bitwise zero, otherwise it is null
    <img src="https://raw.githubusercontent.com/weilueluo/note-images/master/2024/10/upgit_20241024_1729777835.png" alt="image-20241024145035435" style="zoom:80%;" />

- The design principle here is that **users expect the lexical structure of the brackets and commas to be consistent across declaration, initialization and dereferencing**.

    - about 2d array https://ericlippert.com/2009/08/17/arrays-of-arrays/

- array bound check is necessary, but optimization can prevent necessary checks, e.g. in a loop and c# provide unsafe code to bypass it.

- prepend `ref` keyword function parameter to accept value by reference into a function

- `string` is value type

- use `out` to return values back from method.

- use `_` when you dont care about a variable

    - for backward compatibility purpose, it will not work if you have an underscore variable in scope.

- use `in` like a `const ref` method argument, useful for reducing overhead for copying large value type.

    - both caller and callee must use the `in` keyword

- last param as varargs `int Sum (params int[] ints)`

- optional params `void Foo (int x = 23) { Console.WriteLine (x); }`

- named arguments
    ```
    Foo (x:1, y:2);  // 1, 2 
    void Foo (int x, int y) { Console.WriteLine (x + ", " + y); }
    ```

    - you can mix named and positional arguments

- When calling ref function, you can call without assigning result to `ref` variable, this fallback to ordinary value: `string localX = GetXRef();`

- You can also prevent a ref from being modified: `static ref readonly string Prop => ref x;`

- `var` implicit typed local variable

- use `new()` when the class can be inferenced from left hand side

- multi initialize: `a = b = c = d = 0`

- assignment in expression: `y = 5 * (x = 2)`

- binary operators, except for assignment, lambda and null-coalescing operators are left-associative

- null-coalescing operator: `string s2 = s1 ?? "nothing"`

- null conditional
    ```c#
    int x = sb?.ToString().Length;  // Illegal: int cant be null]
    int? x = sb?.ToString().Length;  // ok, int? can be null
    ```

- you can open a scope anytime with curly brackets `{}`

- imports

    - ```c#
        using System;
        System.Console.Writeline("x");
        ```

    - ```c#
        using static System.Console;
        Writeline("x");
        ```

    - ```c#
        global using System;
        ```

- All type names are converted to fully qualified names

- you can call `using xxx` within some namespace

- using alias: `using R = System.Reflection; class Program { R.PropertyInfo p; }`

- `::` namespace qualification, e.g. `global::A.B()`, useful for referring to hidden namespace.





- naming
    - private: camel-cased with underscore `_firstName`
    - local variable: camel-cased `firstName`
    - public: Cap case: `FirstName`

- multi-field declare

    ```c#
    static readonly int legs = 8,                    
    					eyes = 2;
    ```

- const `public const string Message = "Hello World"`

- `static readonly` maybe different each time the program runs (e.g. `DateTime.Now`), but `const` will always be the same (e.g. `PI`).

- adding `static` modifier to a local method prevent it from seeing other local variables.

- `this` refers to instance itself, invalid when static

- property's `get` and `set` method can be overridden

    - internally, they are compiled to `get_XXX` and `set_XXX`

- custom indexer
    ```c#
    public string this[int wordNum]   // indexer
    {
        get { return words[wordNum]; }
        set { words[wordNum] = value; }
    }
    ```

- static field initializer runs in order they are declared
    ```c#
    class Foo {  
        public static int X = Y;    // 0  
        public static int Y = 3;    // 3 
    }
    ```

- finalizer / destructor `~ClassName() { ... }`

- partial methods
    <img src="https://raw.githubusercontent.com/weilueluo/note-images/master/2024/10/upgit_20241027_1730047552.png" alt="image-20241027164551840" style="zoom:80%;" />

- `nameof` operator returns the name of the symbol

    ```c#
    nameof(count);  // count
    nameof(StringBuilder.Length);  // Length
    ```

- use `as` to downcast and evaluate to `null` if fails. `Stock s = a as Stock`

- 



historically speaking, relying on constructors for object initialization could be advantageous in that it allowed us to create fields with read-only access, but this also means we abandoned object initializer (caller side initialization). To solve this problem, c# 9 introduced `init` keyword to only allow a property to be set either in constructor or object initializer.

Optional parameters have two drawbacks:

- It does not easily allow *non-destructive mutation* (`obj with { ... }`).
- when used in library, it hinders backward compatibility, because adding an optional parameters breaks assembly's binary compatibility with existing consumers
    - When application code compiles with library code, library's optional parameter values are copied to the application code, effective making `library.Test()` becomes `library.Test(optionalBool=True)` in the application's binary code. If later library decided to change `optionalBool` to be something else, the application code is not updated automatically. Furthermore, because there is no optional parameter in the binary, if library decided to add a new optional parameters, it breaks application code because now the signature has changed, and application cant find library's new method. It does not just use the default value like python.
- 



| Modifier Type         | Modifier           |
|-----------------------|--------------------|
| Static modifier       | `static`           |
| Access modifiers      | `public` `internal` `private` `protected` |
| Inheritance modifier  | `new`              |
| Unsafe code modifier  | `unsafe`           |
| Read-only modifier    | `readonly`         |
| Threading modifier    | `volatile`         |



| Category | Operator symbol | Operator name | Example | User-overloadable |
|----------|------------------|---------------|---------|-------------------|
| Primary  | .                | Member access | `x.y`   | No                |
| Primary  | ?. and ?[]        | Null-conditional | `x?.y` or `x?[0]` | No |
| Primary  | ! (postfix)       | Null-forgiving | `x!.y` or `x![0]` | No |
| Primary  | -> (unsafe)       | Pointer to struct | `x->y` | No |
| Primary  | ()               | Function call | `x()`   | No                |
| Primary  | []               | Array/index   | `a[x]`  | Via indexer       |
| Primary  | ++               | Post-increment | `x++`   | Yes               |
| Primary  | --               | Post-decrement | `x--`   | Yes               |
| Primary  | new              | Create instance | `new Foo()` | No          |
| Primary  | stackalloc       | Stack allocation | `stackalloc(10)` | No |
| Primary  | typeof           | Get type from identifier | `typeof(int)` | No |
| Primary  | nameof           | Get name of identifier | `nameof(x)` | No  |
| Primary  | checked          | Integral overflow check on | `checked(x)` | No |
| Primary  | unchecked        | Integral overflow check off | `unchecked(x)` | No |
| Primary  | default          | Default value | `default(char)` | No   |



| Category        | Operator symbol | Operator name                 | Example                              | User-overloadable |
|-----------------|-----------------|-------------------------------|--------------------------------------|-------------------|
| Unary           | await           | Await                         | `await myTask`                       | No                |
| Unary           | sizeof          | Get size of struct            | `sizeof(int)`                        | No                |
| Unary           | +               | Positive value of             | `+x`                                 | Yes               |
| Unary           | -               | Negative value of             | `-x`                                 | Yes               |
| Unary           | !               | Not                           | `!x`                                 | Yes               |
| Unary           | ~               | Bitwise complement            | `~x`                                 | Yes               |
| Unary           | ++              | Pre-increment                 | `++x`                                | Yes               |
| Unary           | --              | Pre-decrement                 | `--x`                                | Yes               |
| Unary           | ()              | Cast                          | `(int)x`                             | No                |
| Unary           | ^               | Index from end                | `array[^1]`                          | No                |
| Unary           | * (unsafe)      | Value at address              | `*x`                                 | No                |
| Unary           | & (unsafe)      | Address of value              | `&x`                                 | No                |
| Range           | ..              | Range of indices              | `x..y`                               | No                |
| Range           | ..^             |                               | `x..^y`                              | No                |
| Switch & with   | switch          | Switch expression             | `num switch { 1 => true, _ => false }`| No                |
| Switch & with   | with            | With expression               | `rec with { X = 123 }`               | No                |
| Multiplicative  | *               | Multiply                      | `x * y`                              | Yes               |
| Multiplicative  | /               | Divide                        | `x / y`                              | Yes               |
| Multiplicative  | %               | Remainder                     | `x % y`                              | Yes               |
| Additive        | +               | Add                           | `x + y`                              | Yes               |
| Additive        | -               | Subtract                      | `x - y`                              | Yes               |
| Shift           | <<              | Shift left                    | `x << 1`                             | Yes               |
| Shift           | >>              | Shift right                   | `x >> 1`                             | Yes               |
| Shift           | >>>             | Unsigned shift right          | `x >>> 1`                            | Yes               |
| Relational      | <               | Less than                     | `x < y`                              | Yes               |
| Relational      | >               | Greater than                  | `x > y`                              | Yes               |
| Relational      | <=              | Less than or equal to         | `x <= y`                             | Yes               |
| Relational      | >=              | Greater than or equal to      | `x >= y`                             | Yes               |
| Relational      | is              | Type is or is subclass of     | `x is y`                             | No                |
| Relational      | as              | Type conversion               | `x as y`                             | No                |





| Category            | Operator symbol | Operator name                  | Example                     | User-overloadable |
|---------------------|-----------------|--------------------------------|-----------------------------|-------------------|
| Equality            | ==              | Equals                         | `x == y`                    | Yes               |
| Equality            | !=              | Not equals                     | `x != y`                    | Yes               |
| Bitwise And         | &               | And                            | `x & y`                     | Yes               |
| Bitwise Xor         | ^               | Exclusive Or                   | `x ^ y`                     | Yes               |
| Bitwise Or          | |               | Or                             | `x | y`                     | Yes               |
| Conditional And     | &&              | Conditional And                | `x && y`                    | Via &             |
| Conditional Or      | ||              | Conditional Or                 | `x || y`                    | Via |             |
| Null coalescing     | ??              | Null coalescing                | `x ?? y`                    | No                |
| Conditional         | ?:              | Conditional                    | `isTrue ? thenThis : elseThis` | No             |
| Assignment and lambda | =             | Assign                         | `x = y`                     | No                |
| Assignment and lambda | *=            | Multiply self by               | `x *= 2`                    | Via *             |
| Assignment and lambda | /=            | Divide self by                 | `x /= 2`                    | Via /             |
| Assignment and lambda | %=            | Remainder & assign to self      | `x %= 2`                    | Via %             |
| Assignment and lambda | +=            | Add to self                    | `x += 2`                    | Via +             |
| Assignment and lambda | -=            | Subtract from self             | `x -= 2`                    | Via -             |
| Assignment and lambda | <<=           | Shift self left by             | `x <<= 2`                   | Via <<            |
| Assignment and lambda | >>=           | Shift self right by            | `x >>= 2`                   | Via >>            |
| Assignment and lambda | >>>=          | Unsigned shift self right by   | `x >>>= 2`                  | Via >>>           |
| Assignment and lambda | &=            | And self by                    | `x &= 2`                    | Via &             |
| Assignment and lambda | ^=            | Exclusive-Or self by           | `x ^= 2`                    | Via ^             |
| Assignment and lambda | |=            | Or self by                     | `x |= 2`                    | Via |             |
| Assignment and lambda | ??=           | Null-coalescing assignment      | `x ??= 0`                   | No                |
| Assignment and lambda | =>            | Lambda                         | `x => x + 1`                | No                |





