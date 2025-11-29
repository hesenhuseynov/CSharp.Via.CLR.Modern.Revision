1.2 Combining Managed Modules into Assemblies — Expanded + Modernized Author Edition


⭐ Assemblies: The Real Unit of Execution in .NET

Although compilers produce modules, the CLR itself never works directly with modules.
Instead, it operates exclusively on assemblies — the true building blocks of .NET applications.

An assembly is more than just a file. It is a logical container, a self-describing unit that defines:

what code it contains,

what resources it owns,

what external components it requires,

and under what version, culture, and security context it should be loaded.

When you think of a “component” in .NET, you are really thinking of an assembly.

⭐ Logical vs Physical Assemblies

This is a point that many developers misunderstand when starting with .NET:

```mermaid
flowchart LR
    subgraph Modules["Managed modules and resource files"]
        M1[Managed module<br/>(IL + metadata)]
        M2[Managed module<br/>(IL + metadata)]
        R1[Resource file<br/>(jpeg, gif, html, ...)]
        R2[Resource file<br/>(jpeg, gif, html, ...)]
    end

    subgraph Tool["Tool combining modules into an assembly"]
        C1["Compiler / Assembly Linker<br/>CSC.exe / VBC.exe / AL.exe"]
    end

    subgraph Assembly["Final Assembly"]
        A1[Managed module<br/>(IL + metadata)]
        A2[Managed module<br/>(IL + metadata)]
        AR1[Resource file<br/>(jpeg, gif, html, ...)]
        AR2[Resource file<br/>(jpeg, gif, html, ...)]
    end

    M1 --> C1
    M2 --> C1
    R1 --> C1
    R2 --> C1
    C1 --> Assembly


A module is a physical file.
An assembly is a logical identity.

You may physically have:

one .dll file → 1 assembly

one .dll containing multiple .netmodules → 1 assembly

a single-file published app bundling dozens of assemblies → “physically” 1 file, but logically many assemblies

a NativeAOT app → no IL at all, yet the concept of an assembly still existed at build time

This clear separation allows .NET to scale into scenarios the original CLR design never anticipated.

⭐ How Multiple Files Become One Assembly

Historically, tools like AL.exe (Assembly Linker) combined modules and resources into one final assembly.

The process is:

Modules (IL + metadata)
   + Resources (images, data, etc.)
   + Manifest data
= Assembly


The manifest becomes the key.
It is metadata about the metadata — describing every file, type, and version inside the assembly.

Today, most developers don’t invoke AL.exe directly.
The .NET SDK, MSBuild, and Roslyn hide all of this complexity and produce assemblies automatically.

⭐ The Assembly Manifest — The Brain of an Assembly

The manifest stores crucial metadata:

the list of files in the assembly

the assembly’s identity (name, version, culture, public key token)

referenced assemblies and their versions

exported types

hashing and integrity information

resource mapping

In modern .NET, the manifest is also used by:

the linker and trimmer

single-file bundling

ReadyToRun code generation

NativeAOT compilation

AssemblyLoadContext isolation

It is the mechanism that keeps .NET deployment consistent and self-contained — no registry, no COM registration, no “DLL Hell.”

⭐ Why .NET Needs Assemblies (Even in 2025)

Even though .NET 8/9 can generate native executables, can bundle everything into a single file, and can strip unused IL, assemblies still matter because they are the unit of:

versioning

code visibility

dependency tracking

load-time binding

reflection

runtime contract enforcement

The CLR always asks:

“Which assembly defines this type?”
“Is the version compatible?”
“Where is the manifest information?”

Even if the physical representation changes, this logical concept remains essential.

⭐ Assemblies Improve Deployment

One of the most painful drawbacks of classic Windows programming was COM registration and system-wide dependency installation. Assemblies solved this by making every component self-describing.

The result?

XCOPY deployment

No registry entries

No global path pollution

No AD DS (Active Directory) dependencies

No GAC conflicts (in .NET Core+ the GAC is gone anyway)

The entire dependency graph is inside the assembly itself.

This is why deployment in modern .NET is radically simpler than in .NET Framework or COM.