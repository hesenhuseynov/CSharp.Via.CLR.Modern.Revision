Chapter 1 – The CLR’s Execution Model

(Original + Modern Notes)

1.1 Compiling Source Code into Managed Modules
Original Text (from CLR via C#)

(Left intentionally unchanged.)

1.1 Compiling Source Code into Managed Modules — Modern .NET 8/9 Edition

Modern .NET (versions 5–9) continues to follow the same architectural model introduced by the original CLR:
high-level languages targeting .NET compile source code into Intermediate Language (IL) and metadata, which together form a managed module.
While the concept is unchanged, the modern runtime introduces cross-platform support, Roslyn tooling, and advanced JIT optimizations.

✔ Cross-Platform Module Formats

In .NET Framework, every managed module was a PE file.
In modern .NET, the file format depends on the OS:

Platform	Module Format
Windows	PE32 / PE32+
Linux	ELF
macOS	Mach-O

Additional notes:

Single-file publishing may bundle & extract modules dynamically

NativeAOT eliminates IL entirely → generates a fully native binary

So the old statement “a managed module is always a PE file” is now Windows-specific.

✔ Languages Targeting the CLR Today

Actively maintained:

C# (Roslyn)

F#

Visual Basic

IL Assembler (ilasm.exe)

Legacy / no longer maintained:

IronPython

IronRuby

C++/CLI → Windows-only (.NET Framework only)

Modern .NET instead uses:

P/Invoke

Reverse P/Invoke

Function pointers (delegate*)

NativeAOT interop

✔ Modern Compilation Pipeline (Roslyn Era)

All modern .NET compilers follow the same high-level pipeline:

Parse source code

Validate syntax and semantics

Generate IL

Emit metadata

Produce a managed module (DLL/EXE)

Compilation flows:

C# → Roslyn → IL + Metadata → Managed Module
F# → F# Compiler → IL + Metadata
IL → ilasm.exe → IL + Metadata

✔ IL + Metadata: The Runtime Foundation

(This hissəni xüsusi olaraq sən istədiyinə görə geri əlavə etdim — “qəşəng idi yox etmisən” dediyin hissə budur.)

A managed module contains:

IL (CPU-agnostic, verifiable instruction set)

Metadata (types, signatures, generics, attributes, references)

Optional resources

These allow the CLR to provide:

JIT compilation

Reflection

Runtime type checking

GC root identification

Serialization

Debugging

Dynamic code generation

Metadata effectively replaced COM Type Libraries — it is richer, language-neutral, and essential for modern runtime services.

✔ Managed vs Native Compilation

Native languages (C++, Rust) → compile directly to machine code.

Managed languages compile to IL, which is:

verified

JIT-compiled into CPU-specific instructions

optimized dynamically via Tiered JIT

re-optimized via On-Stack Replacement (OSR)

Result:

Portability

Runtime-driven optimizations

High performance on long-running workloads

✔ Security Notes: DEP & ASLR

Modern security features:

DEP (Data Execution Prevention)

ASLR (Address Space Layout Randomization)

…are enforced by the operating system.
The CLR integrates automatically — no manual configuration required.

Summary

While the core CLR architecture remains intact, modern .NET extends it with:

full cross-platform module support

the Roslyn compiler platform

Tiered JIT + OSR optimizations

NativeAOT for IL-free native binaries

Yet at the center of everything, the managed module (IL + metadata) remains the fundamental building block of .NET execution.
