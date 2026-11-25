# Specification 01: System Overview & Dual Execution Modes

[← Back to Documentation Index](README.md)

---

## 1. Overview & Paradigm

The **Python File Organizer** is engineered with a **dual-mode architecture** to satisfy two distinct user personas:
1. **Interactive Users (Beginner / Desktop users):** Prefer a visual, conversational Terminal User Interface (TUI) with safety confirmations, progress bars, and guided choices.
2. **Automated / Scripting Users (Sysadmins / Developers):** Prefer composable, non-interactive CLI commands with flags (`--dry-run`, `-y`, `--by date`) suitable for cron jobs and shell scripts.

---

## 2. End-to-End System Flowchart

The following flowchart details how user input flows from initial entry through interactive or direct command routing:

```mermaid
flowchart TD
    Start(["Start Application"]) --> ModeCheck{"CLI Arguments\nProvided?"}

    %% Branch A: Direct CLI
    ModeCheck -->|"Yes\n(e.g. organize / duplicates)"| DirectDispatch["⚡ Direct CLI Dispatcher\n(Parse flags: --dry-run, --by, -y)"]
    DirectDispatch --> RunDirectCommand["Execute Core Subcommand"]

    %% Branch B: Interactive Mode
    ModeCheck -->|"No or 'interactive'"| TUIWizard["📱 Launch Interactive Wizard"]
    TUIWizard --> DisplayMenu["Display Options Menu:\n1. Find Duplicates\n2. Organize Folder\n3. Undo Last Operation\n4. Quit"]

    DisplayMenu --> UserSelect{"User Selection"}

    UserSelect -->|"Option 1"| RouteDup["🔍 Feature 03: Duplicate Scanner"]
    UserSelect -->|"Option 2"| RouteOrg["📁 Feature 04: Organization Engine"]
    UserSelect -->|"Option 3"| RouteUndo["↩️ Feature 05: Undo & Journaling"]
    UserSelect -->|"Option 4"| ExitApp(["Exit Process"])

    RouteDup --> SummaryBox["📊 Render Operation Summary Card\n(Files moved, Duplicates purged, Space freed)"]
    RouteOrg --> SummaryBox
    RouteUndo --> SummaryBox
    RunDirectCommand --> SummaryBox

    SummaryBox --> SessionCheck{"Session Mode"}
    SessionCheck -->|"Interactive"| DisplayMenu
    SessionCheck -->|"Direct CLI"| ExitApp

    %% Clickable Interactive Nodes
    click RouteDup "03-duplicate-scanner.md" "View Duplicate Scanner Specification"
    click RouteOrg "04-organization-engine.md" "View Organization Engine Specification"
    click RouteUndo "05-undo-and-journaling.md" "View Undo & Rollback Specification"
    click TUIWizard "02-interactive-wizard.md" "View Interactive Wizard Specification"
    click DirectDispatch "06-cli-reference.md" "View Direct CLI Reference"
```

---

## 3. High-Level Component Layers

```mermaid
flowchart LR
    subgraph UI ["Layer 1: Presentation & Routing"]
        CLI["Typer CLI Parser"]
        TUI["Rich Interactive Wizard"]
        Renderer["Rich Tables & Telemetry"]
    end

    subgraph Core ["Layer 2: Business Logic Engines"]
        DupEngine["Duplicates Engine\n(Fast 3-Stage Hashing)"]
        OrgEngine["Organization Engine\n(Type / Date / Size)"]
        UndoEngine["Rollback Engine\n(Reverse Transaction)"]
    end

    subgraph Storage ["Layer 3: File System & Safety"]
        Trash["OS Trash (send2trash)"]
        Journal[".organizer_history.json"]
        FS[("Target File System")]
    end

    CLI --> Core
    TUI --> Core
    TUI --> Renderer
    CLI --> Renderer

    DupEngine --> Trash
    DupEngine --> FS
    OrgEngine --> FS
    OrgEngine --> Journal
    UndoEngine --> Journal
    UndoEngine --> FS
```

---

## 4. Next Steps

* To inspect the step-by-step interactive navigation, see **[Specification 02: Interactive Wizard](02-interactive-wizard.md)**.
* To review direct terminal subcommands, see **[Specification 06: Direct CLI Reference](06-cli-reference.md)**.
