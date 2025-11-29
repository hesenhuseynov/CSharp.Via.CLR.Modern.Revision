# 1.1 Compiling Source Code into Managed Modules — Modern .NET 8/9 Edition

When you begin building an application on **modern .NET** (versions 5 through 9), the first architectural step remains identical to what the original CLR design established more than two decades ago: you write source code in a high-level language, and a compiler processes that source into a **managed module**.

A managed module contains two fundamental components:
* **Intermediate Language (IL):** A CPU-agnostic instruction set.
* **Metadata:** Rich structural information describing all types, members, signatures, and references.

Although this model originated in the .NET Framework era, modern .NET expands it dramatically with cross-platform execution, improved compilation tools, new runtime optimizations, and optional native-only deployment models.

## Choosing a Language

Historically, languages differed significantly in capability: **native C/C++** provided extremely low-level control (manual memory management, direct thread creation, raw pointers), while **Visual Basic** enabled rapid GUI and COM development with higher-level abstractions.

> **The Common Language Runtime changed this landscape.** > Language choice now affects only syntax and developer productivity, not runtime capability.

The CLR provides **all** languages with:
* Memory management (garbage collection)
* Exception handling
* Thread creation and synchronization
* Assembly loading
* Security enforcement

At runtime, the CLR has no knowledge of which language produced the module — it sees only **IL + metadata**. Therefore, modern .NET developers choose languages based on expressiveness and tooling, rather than runtime differences.

### Modern .NET officially supports:
* **C#** (Roslyn)
* **F#**
* **Visual Basic**
* **IL Assembler** (`ilasm.exe`)

_Note: Languages like IronPython and IronRuby are unmaintained today. C++/CLI exists only in the Windows-only .NET Framework and is not part of .NET 5–9._

## How Modern Compilers Produce Managed Modules

Regardless of the source language, all modern .NET compilers follow the same conceptual steps:

1. Parse the source code and build an **abstract syntax tree**.
2. Perform **semantic analysis** (type checking, overload resolution, generics validation).
3. Emit **IL instructions** that describe program behavior.
4. Emit **rich metadata** describing:
    * types
    * methods
    * fields
    * parameters
    * generics
    * attributes
    * referenced assemblies

The output is a **managed module** (DLL or EXE) containing:
* IL
* Metadata
* CLR Header
* Optional resources

This module is the smallest physical unit the CLR can load and execute.

**Roslyn**, the modern compiler platform, adds features that did not exist in earlier .NET versions:
* Incremental compilation
* Analyzers
* Refactorings
* Source generators
* Semantic models

Yet the final output — **IL + metadata** — remains consistent with the original CLR design.

## Managed Modules Are Not Always PE Files Anymore

In the .NET Framework era, all managed modules used Windows **PE32/PE32+** format because the runtime existed only on Windows. Modern .NET is cross-platform:

| Platform | Module Format |
| :--- | :--- |
| **Windows** | PE32 / PE32+ |
| **Linux** | ELF |
| **macOS** | Mach-O |

**Additional behaviors:**
* **Single-file publishing:** Bundles modules into one executable and extracts them at runtime.
* **NativeAOT:** Can eliminate IL entirely and generate a fully native binary.

Thus, the older statement _“a managed module is a PE file”_ now applies only to Windows-specific builds.

## Runtime Interpretation of IL and Metadata

At execution time, the CLR:
1. Loads the managed module.
2. Reads metadata to understand type layouts, method signatures, references, and generics.
3. Verifies that IL is type-safe (if verification is enabled).
4. **JIT-compiles** IL into native CPU instructions.
5. Executes the native code.

Modern .NET includes advanced optimizations:
* **Tiered JIT:** Initial “quick” compilation, then optimized recompilation.
* **On-Stack Replacement (OSR):** Hot loops recompiled while running.
* **Profile-guided optimizations (PGO).**

These features did not exist in the original CLR era and greatly improve runtime throughput.

## Metadata: The Backbone of Managed Execution

Metadata is a structured set of tables stored alongside IL. It describes both:
* What the module defines (types, members).
* What the module references (external types).

**Metadata enables:**
* IntelliSense and IDE tooling
* Reflection
* Serialization
* Dynamic code generation
* GC root tracking (knowing which fields reference objects)
* JIT code verification
* Language interop

Because metadata is embedded directly into the module, IL and metadata can never fall out of sync.

## Managed vs Unmanaged Code

* **Native languages** (C, C++, Rust) compile directly into CPU-specific instructions.
* **Managed languages** (C#, F#, VB) compile into IL.

This gives .NET:
* Portability
* Runtime verification
* GC memory management
* Runtime optimizations
* Safer execution

C++/CLI was historically the only language capable of mixing managed and unmanaged code in the same module. Modern .NET instead promotes clearer separation using:
* P/Invoke
* Reverse P/Invoke
* Function pointers (`delegate*`)
* NativeAOT interop

## Security: DEP and ASLR

All managed modules automatically benefit from OS-level security features:
* **Data Execution Prevention (DEP)**
* **Address Space Layout Randomization (ASLR)**

These are enforced by Windows, Linux, and macOS, and require no explicit configuration from .NET developers.

---

### Summary

The foundational idea introduced in the original CLR — compiling high-level language code into **IL + metadata** — remains the core of modern .NET.

But the ecosystem has evolved dramatically:
* Cross-platform file formats (PE, ELF, Mach-O)
* Roslyn and advanced compiler tooling
* Tiered JIT + OSR runtime optimizations
* NativeAOT native-only binaries
* Cleaner interop models

Even with these advancements, the managed module remains the fundamental building block of .NET execution.
