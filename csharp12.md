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

- `var` im

