# 1.2 Combining Managed Modules into Assemblies — Expanded + Modernized Author Edition

## ⭐ Assemblies: The Real Unit of Execution in .NET

Although compilers produce modules, the CLR itself never works directly with modules. Instead, it operates exclusively on **assemblies** — the true building blocks of .NET applications.

An **assembly** is more than just a file. It is a logical container, a self-describing unit that defines:
* What code it contains
* What resources it owns
* What external components it requires
* Under what version, culture, and security context it should be loaded

> **Key Takeaway:** When you think of a “component” in .NET, you are really thinking of an **assembly**.

## ⭐ Logical vs Physical Assemblies

This is a point that many developers misunderstand when starting with .NET:

> **A module is a physical file.**
> **An assembly is a logical identity.**

You may physically have:
* **One .dll file** → 1 assembly (Standard)
* **One .dll containing multiple .netmodules** → 1 assembly (Legacy/Rare)
* **A single-file published app** bundling dozens of assemblies → “Physically” 1 file, but “Logically” many assemblies
* **A NativeAOT app** → No IL at all, yet the concept of an assembly still existed at build time

This clear separation allows .NET to scale into scenarios the original CLR design never anticipated.

### 🧩 Visualizing the Process

```mermaid
flowchart LR
    subgraph Inputs ["Inputs"]
        SC["Source Code (.cs/.vb)"]
        RES["Resources (.png, .json)"]
    end

    subgraph Tool ["Compilation & Linking"]
        C1["Compiler (Roslyn) / MSBuild"]
    end

    subgraph Output ["Final Assembly (Logical Unit)"]
        direction TB
        MAN["📂 MANIFEST (The Brain)"]
        IL["📄 IL Code"]
        META["📊 Metadata"]
        R_OUT["🖼️ Resources"]
    end

    SC --> C1
    RES --> C1
    C1 --> Output
    
    style MAN fill:#f9f,stroke:#333,stroke-width:2px,color:black
    style Output fill:#e1f5fe,stroke:#333,stroke-width:2px,color:black
