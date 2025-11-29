  # 1.2 Combining Managed Modules into Assemblies — Expanded + Modernized Author Edition

## ⭐ Assemblies: The Real Unit of Execution in .NET

Although compilers produce modules (IL + metadata), the CLR’s binding and identity unit is the assembly. At runtime, the CLR resolves types using assembly identity, while the actual code and metadata still live in one or more loaded modules (including the manifest module).
An **assembly** is more than just a file. It is a logical container, a self-describing unit that defines:
* What code it contains
* What resources it owns
* What external components it requires
* Under what name, version, culture, and (optionally) signing/integrity characteristics it should be identified and bound

> **Key Takeaway:** When you think of a “component” in .NET, you are really thinking of an **assembly**.

## ⭐ Logical vs Physical Assemblies

This is a point that many developers misunderstand when starting with .NET:

### 🧩 Visualizing the Process

```mermaid
flowchart LR
    subgraph Inputs ["Inputs"]
        SC["Source Code (.cs/.vb)"]
        RES["Assets (embedded resources / content files)"]
    end

    subgraph Tool ["Build Pipeline"]
        direction TB
        C1["Compiler (Roslyn)"]
        B1["MSBuild / SDK (packaging)"]
    end

    subgraph Output ["Assembly (.dll/.exe) — physical file & logical identity"]
        direction TB
        MAN["📂 Manifest (assembly-level metadata)"]
        MOD["📦 Module (IL + Metadata)"]
        R_OUT["🖼️ Embedded resources / content mapping"]
    end

    SC --> C1
    C1 --> B1
    RES --> B1
    B1 --> Output
    
    style MAN fill:#f9f,stroke:#333,stroke-width:2px,color:black
    style Output fill:#e1f5fe,stroke:#333,stroke-width:2px,color:black
```

> **A module is a physical file.**
> **An assembly is a logical identity.**

You may physically have:
* **One .dll file** → 1 assembly (Standard)
* ** One manifest .dll/.exe + one or more separate .netmodule files → 1 logical assembly (Legacy/Rare)
* ** A single-file published app can bundle many assemblies into one physical file; they remain logically separate assemblies, and the runtime loads them from the bundle (sometimes extracting, depending on publish settings).
* **A NativeAOT app ships primarily as native code rather than IL; the assembly concept still exists at build/publish time, but reflection and dynamic loading behaviors may be reduced depending on trimming/AOT constraints.

This clear separation allows .NET to scale into scenarios the original CLR design never anticipated.

## ⭐ How Multiple Files Become One Assembly

Historically, tools like `AL.exe` (Assembly Linker) combined modules and resources into one final assembly.

The conceptual formula is:
```text
Modules (IL + metadata)
   + Resources (images, data, etc.)
   + Manifest data
= Assembly
```

The **Manifest** becomes the key. It is assembly-level metadata — describing the assembly’s identity, referenced assemblies, contained files (when applicable), and exported/forwarded types.

Today, most developers don’t invoke `AL.exe` directly. The **.NET SDK**, **MSBuild**, and **Roslyn** hide all of this complexity and produce assemblies automatically.

## ⭐ The Assembly Manifest — The Brain of an Assembly

The manifest stores crucial metadata:
* 📄 The list of files in the assembly
* 🆔 The assembly’s identity (name, version, culture, public key token)
* 🔗 Referenced assemblies and their versions
* 📤 Exported types
* 🔐 Hashing and integrity information
* 🗺️ Resource mapping

**In modern .NET, this assembly metadata participates in:**
* Publish-time analysis (linker/trimmer) over **IL + metadata + references**
* Single-file bundling (packaging), and ReadyToRun/NativeAOT publish pipelines
* Runtime loading decisions via the host (e.g., .deps.json + host policy), and optional isolation/unloadability via AssemblyLoadContext (application-controlled loading)

It is the mechanism that keeps .NET deployment consistent and self-contained — no registry, no COM registration, and dramatically less classic system-wide “DLL Hell” (though dependency/version conflicts can still happen within an app’s graph).

## ⭐ Why .NET Needs Assemblies (Even in 2025)

Even though **.NET 8/9** can generate native executables, can bundle everything into a single file, and can strip unused IL, assemblies still matter because they are the unit of:

* **Versioning**
* **Code visibility** (internal vs public)
* **Dependency tracking**
* **Load-time binding**
* **Reflection**
* **Runtime contract enforcement**

The CLR always asks:
1. *“Which assembly defines this type?”*
2. *“Is the version compatible?”*
3. *“Where is the manifest information?”*

Even if the physical representation changes, this logical concept remains essential.

## ⭐ Assemblies Improve Deployment

One of the most painful drawbacks of classic Windows programming was COM registration and system-wide dependency installation. Assemblies solved this by making every component **self-describing**.

**The result?**
* ✅ **XCOPY deployment** (Just copy files and run)
* ✅ **No Registry entries** required
* ✅ **No global path pollution**
* ✅ **No COM/ActiveX dependencies**
* ✅ **No GAC conflicts** (in .NET Core+ the GAC is gone)

An assembly records references to other assemblies, but in modern .NET the full dependency graph and binding decisions are also driven by deployment metadata (notably .deps.json) and the hosting/binding policy. This is why deployment in modern .NET is radically simpler than in .NET Framework or COM.

### On-demand and modular deployment

Assemblies also enable a deployment model that is difficult or impossible with native components:
the ability to partition rarely used features into separate files while still treating them as part of the
same logical component. In practice, optional features are most commonly shipped as separate assemblies/plugins (often loaded via AssemblyLoadContext) or as satellite resource assemblies (e.g., *.resources.dll). These remain separate assemblies with their own manifests, but can still be versioned and loaded consistently as part of the application’s overall dependency set.

This model is particularly valuable for applications that ship large optional subsystems—help
systems, images, localization resources, plug-ins, diagnostic modules, or domain-specific extension
packs. Instead of forcing all users to download or install these components, the application may place
them into auxiliary files that are retrieved on demand or included only in specific distribution bundles.
If a user never invokes the associated feature, the corresponding files are never loaded into memory
or even downloaded, reducing installation size, minimizing I/O overhead, and improving startup time.

Despite the physical separation, consistency is achieved through assembly identities + references and the application’s deployment metadata (notably .deps.json) and host binding policy. Each optional component (plugin assembly or *.resources.dll) has its own manifest and version, but the app can enforce compatibility by controlling which versions are deployed and loaded.

For localization, the runtime probes and loads satellite resource assemblies based on the current culture. For plugins, applications typically load optional assemblies on demand, optionally isolating them with AssemblyLoadContext to control dependency conflicts and unloadability (where supported).

Modern deployment modes (single-file publish, trimming, ReadyToRun, NativeAOT) can change the physical packaging and what metadata remains available, but the core model stays: assemblies remain the unit of identity/versioning, while optional functionality is usually delivered as separate assemblies/resources loaded as needed.
