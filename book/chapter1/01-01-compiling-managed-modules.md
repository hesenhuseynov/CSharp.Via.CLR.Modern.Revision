Chapter 1 – The CLR’s Execution Model (Original + Modern Notes)


1.1 Compiling Source Code into Managed Modules — Modern .NET 8/9 Edition

Modern .NET (versions 5 through 9) still follows the foundational model introduced by the original CLR: high-level languages targeting .NET compile source code into Intermediate Language (IL) together with rich metadata. These two structures form a managed module—the fundamental unit consumed by the runtime. The conceptual model remains the same, but its practical implementation has expanded significantly to support cross-platform runtimes, new compiler technologies, and advanced JIT optimizations.

Modern .NET is fully cross-platform, and the physical file format of a managed module now depends on the target operating system.
On Windows, modules are emitted as PE32 or PE32+ files; on Linux, they use ELF; and on macOS, they use Mach-O. Single-file publishing can embed and dynamically extract these modules at runtime, and NativeAOT can eliminate IL entirely by generating a fully native binary. Therefore, the older .NET Framework assumption—“a managed module is always a PE file”—is now Windows-only.

A variety of languages can target the CLR, but the actively maintained ones are C# (via the Roslyn compiler), F#, Visual Basic, and IL Assembler (ilasm.exe). Dynamic languages such as IronPython and IronRuby are no longer maintained. C++/CLI also belongs to the past: it is supported only on Windows under .NET Framework and is not part of .NET 5–9. Modern .NET uses P/Invoke, reverse P/Invoke, function pointers (delegate*), and NativeAOT interop rather than mixed-mode assemblies.

All modern .NET compilers follow the same high-level pipeline: they analyze the source code, validate syntax and semantics, generate IL instructions, and emit metadata describing types, members, signatures, generics, and referenced assemblies. The final output is a managed module containing IL, metadata, and optional embedded resources. These components enable JIT compilation, reflection, runtime type checking, GC root tracking, debugging, serialization, and a wide range of runtime services.

Roslyn fundamentally changed .NET compilation. It introduced incremental compilation, semantic models, analyzers, refactoring tools, and source generators—capabilities unattainable with the early CLR toolchain. Today the typical compilation pipeline looks like:

C# → Roslyn → IL + Metadata → Managed Module (DLL/EXE)

F# → F# Compiler → IL + Metadata

IL → ilasm.exe → IL + Metadata

Managed languages differ from native languages (e.g., C++ or Rust), which emit machine code directly. In .NET, IL is verified and then compiled into native code by the JIT. Modern runtimes apply tiered JIT compilation and On-Stack Replacement (OSR) to optimize hot paths dynamically. This hybrid model provides both portability and high performance.

Security features such as Data Execution Prevention (DEP) and Address Space Layout Randomization (ASLR) are enforced by the operating system. The CLR integrates with these protections automatically, and modern .NET requires no special configuration from developers.

In summary, while the fundamental architecture of the early CLR remains intact, modern .NET significantly expands its capabilities with cross-platform modules, advanced compilation pipelines, and optional native-only execution paths. The managed module—defined by IL and metadata—remains the core building block enabling these features.
