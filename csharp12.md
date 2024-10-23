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