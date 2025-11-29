# 1.1 Compiling Source Code into Managed Modules — Modern .NET 8/9 Edition

When you begin building an application on **modern .NET** (versions 5 through 9), the first step remains consistent with the original CLR model: you write source code in a high-level language, and a compiler processes that source into a **managed module**.

A managed module contains two fundamental components:
* **Intermediate Language (IL):** A CPU-agnostic instruction set.
* **Metadata:** Structural information describing all types, members, signatures, and references.

Although this model originated in the .NET Framework era, modern .NET expands it with cross-platform hosting, improved tooling, runtime optimizations, and optional native-only deployment models.

## Choosing a Language

Historically, languages differed significantly in capability: **native C/C++** provided low-level control (manual memory management, raw pointers), while **Visual Basic** enabled rapid GUI and COM development with higher-level abstractions.

> **The Common Language Runtime changed this landscape.**  
> Language choice now affects mostly syntax, ecosystem, and developer productivity—not fundamental runtime capabilities.

The CLR provides all .NET languages with:
* Memory management (garbage collection)
* Exception handling
* Threading primitives and synchronization
* Assembly loading and type resolution
* Type-safety/runtime checks and host/OS-integrated security boundaries (where applicable)

At runtime, the CLR has no knowledge of which language produced the module—it sees only **IL + metadata**. Therefore, modern .NET developers choose languages based on expressiveness, libraries, and tooling rather than runtime differences.

### Modern .NET officially supports:
* **C#** (Roslyn)
* **F#**
* **Visual Basic**
* **IL Assembler** (`ilasm.exe`)

_Note: Languages like IronPython and IronRuby are unmaintained today. C++/CLI is Windows-only and not part of .NET 5–9._

## How Modern Compilers Produce Managed Modules

Regardless of the source language, modern .NET compilers follow the same conceptual steps:

1. Parse source code into an **abstract syntax tree (AST)**.
2. Perform **semantic analysis** (type checking, overload resolution, generics validation).
3. Emit **IL instructions** that describe program behavior.
4. Emit **metadata** describing:
   * types
   * methods
   * fields
   * parameters
   * generics
   * attributes
   * referenced assemblies

The output is a managed module (DLL or EXE) containing:
* IL
* Metadata
* CLR headers
* Optional resources

A managed module is the smallest physical unit that contains **IL + metadata**, but the CLR’s primary **loading/binding unit** is the **assembly**.

**Roslyn**, the modern compiler platform, adds capabilities that were not part of early .NET, such as:
* Incremental compilation
* Analyzers and refactorings
* Source generators
* Rich semantic models

Yet the final output—**IL + metadata**—still follows the original CLR design.

## Managed Modules Are Still PE/COFF (Even on Linux/macOS)

In modern .NET, managed modules/assemblies (`.dll`/`.exe`) are still emitted in **PE/COFF** format (the same container used on Windows). What changes cross-platform is the **native host/runtime** that loads these assemblies, not the managed assembly file format itself.

Single-file publishing may bundle assemblies into a single native host file, and NativeAOT may produce a fully native binary—these are **deployment changes**, not “ELF/Mach-O managed module formats.”

## Runtime Interpretation of IL and Metadata

At execution time, the CLR:
1. Loads the assembly/module.
2. Reads metadata to understand type layouts, method signatures, references, and generics.
3. Optionally verifies IL (when verification is enabled and applicable).
4. **JIT-compiles** IL into native CPU instructions.
5. Executes the native code.

Modern .NET includes advanced optimizations:
* **Tiered JIT:** Fast initial compilation, then optimized recompilation for hot code.
* **On-Stack Replacement (OSR):** Hot loops can be recompiled while running.
* **Profile-guided optimizations (PGO).**

## Metadata: The Backbone of Managed Execution

Metadata is a structured set of tables stored alongside IL. It describes:
* What the module defines (types, members).
* What the module references (external types).

**Metadata enables:**
* IDE tooling (IntelliSense)
* Reflection
* Serialization
* Dynamic code generation
* GC cooperation (tracking object references)
* JIT decisions and (where applicable) verification
* Language interoperability

Because metadata is embedded into the module, IL and metadata cannot fall out of sync.

## Managed vs Unmanaged Code

* **Native languages** (C, C++, Rust) compile directly into CPU-specific instructions.
* **Managed languages** (C#, F#, VB) compile into IL.

This gives .NET:
* Portability
* Runtime checks
* GC memory management
* Runtime optimizations
* Safer execution patterns

C++/CLI historically mixed managed and unmanaged code in the same module. Modern .NET typically uses clearer boundaries:
* P/Invoke and reverse P/Invoke
* Function pointers (`delegate*`)
* NativeAOT-focused interop

## Security: DEP/NX and ASLR (OS-Level)

.NET applications generally benefit from OS-level mitigations such as DEP/NX and ASLR depending on the OS and deployment model. These are primarily enforced at the **process/OS** level rather than being properties of IL modules themselves.

---

### Summary

The original CLR idea—compiling high-level code into **IL + metadata**—remains central to modern .NET.

What changed significantly is the ecosystem around it:
* Cross-platform hosting and deployment options
* Roslyn-era compiler tooling
* Tiered JIT + OSR optimizations
* NativeAOT native-only binaries
* Cleaner, more explicit interop patterns

Even with these advancements, IL + metadata remain the foundation of the managed execution model.
