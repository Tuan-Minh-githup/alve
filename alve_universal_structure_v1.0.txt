# ALVE UNIFIED APPLICATION STRUCTURE v1.0

## 📌 PURPOSE

This document defines the **unified folder structure** that ALL Alve applications MUST follow. This is the "blueprint" for creating any Alve app (Nexus, Guard, Studio, etc).

**Special Case:** Alve Apps has ONE exception: `Alve_All_App_Data/` folder (see below).

---

## 🏗️ COMPLETE STRUCTURE TEMPLATE

```
Alve_[AppName]/
│
├── .alve/                                   # 🔑 METADATA (Non-negotiable)
│   ├── identity.json                      # App identity & signature
│   │   └── {
│   │       "app_id": "ALV-NEXUS",
│   │       "app_name": "Alve Nexus",
│   │       "version": "1.0.0",
│   │       "author": "Alve Team",
│   │       "description": "...",
│   │       "signature": "HMAC-key",
│   │       "release_date": "2026-05-16",
│   │       "min_alve_apps_version": "1.0.0"
│   │     }
│   │
│   └── manifest.alve                     # Active modules
│       └── {
│           "modules": ["core", "data", "network"],
│           "features": ["auto_update", "resource_monitoring"],
│           "dependencies": ["python>=3.10", "psutil"]
│         }
│
├── Alve_Core/                              # 🧠 IMMUTABLE CORE
│   │                                       # (Copy/Paste 100% across all apps)
│   │
│   ├── Bin/                                # Platform tools (immutable)
│   │   ├── 7z.exe (Windows)
│   │   ├── 7z (Linux binary)
│   │   ├── metadata_verify.hash           # SHA-256 checksum file
│   │   └── platform_tools/
│   │       ├── windows/
│   │       │   ├── registry_tool.exe
│   │       │   └── process_manager.exe
│   │       └── linux/
│   │           ├── systemd_helper
│   │           └── process_monitor
│   │
│   ├── Runtimes/                          # Runtime bundles (pre-extracted)
│   │   ├── Java_Vault/
│   │   │   ├── jre8/                      # JRE 8.0.x
│   │   │   │   ├── bin/
│   │   │   │   ├── lib/
│   │   │   │   └── release
│   │   │   ├── jre17/                     # JRE 17.x
│   │   │   └── jre21/                     # JRE 21.x
│   │   ├── Python_Internal/               # Bundled Python
│   │   │   └── python3.11/
│   │   │       ├── bin/
│   │   │       ├── lib/
│   │   │       └── include/
│   │   └── runtime_status.alve            # {"java": "21.0.1", "python": "3.11.4"}
│   │
│   ├── Bridge/                            # IPC Communication (immutable)
│   │   ├── ipc_server.py                 # Unix Socket / Named Pipe handler
│   │   ├── message_handler.py            # JSON-RPC 2.0 processor
│   │   ├── auth.py                       # HMAC-SHA256 verification
│   │   └── handlers/
│   │       ├── freeze_handler.py
│   │       ├── status_handler.py
│   │       └── signal_handler.py
│   │
│   ├── Monitor/                           # 82/1.6 Rule Enforcer (immutable)
│   │   ├── resource_monitor.py           # Monitor CPU/RAM
│   │   ├── kill_strategy.py              # Graceful → Force kill logic
│   │   ├── rules.json                    # Thresholds & timeouts
│   │   │   └── {
│   │   │       "memory_threshold_percent": 82,
│   │   │       "cpu_threshold_percent": 82,
│   │   │       "reserved_ram_mb": 1600,
│   │   │       "grace_period_seconds": 30
│   │   │     }
│   │   └── enforcement.py                # Execute kill signals
│   │
│   ├── Diagnostic/                        # Error Logging (immutable)
│   │   ├── logger.py                     # Structured logging
│   │   ├── error_codes.json              # Error definitions
│   │   └── recovery_hints.json           # Recovery suggestions
│   │
│   ├── Security/                          # Integrity & Auth (immutable)
│   │   ├── integrity_checker.py          # SHA-256 verification
│   │   ├── signature_verifier.py         # HMAC-SHA256 check
│   │   └── permission_manager.py         # File permission enforcement
│   │
│   ├── Source/                            # Core Source Code (replaceable)
│   │   ├── __init__.py
│   │   ├── main.py                       # Entry point
│   │   ├── constants.py                  # Global constants
│   │   ├── exceptions.py                 # Custom exceptions
│   │   ├── logger.py                     # Logging config
│   │   ├── models.py                     # Core data models
│   │   ├── persistence.py                # Data storage
│   │   └── managers.py                   # Business logic
│   │
│   └── config.alve                       # Core configuration
│       └── {
│           "version": "1.0.0",
│           "update_channel": "stable",
│           "signature_verification": true
│         }
│
├── Specifics/                              # 🎯 APP-SPECIFIC LOGIC
│   │                                       # (Varies by app, this folder is unique)
│   │
│   ├── Source/                            # App-specific code
│   │   ├── app_specific_logic.py
│   │   ├── models.py                     # App-specific data models
│   │   ├── managers/
│   │   │   ├── server_manager.py         # (For Nexus only)
│   │   │   ├── project_manager.py        # (For Nexus only)
│   │   │   └── ...
│   │   └── handlers/
│   │       └── [app-specific handlers]
│   │
│   ├── Resources/                         # App assets
│   │   ├── Icons/
│   │   │   ├── app_icon.png
│   │   │   ├── notification_icons/
│   │   │   └── ...
│   │   ├── Themes/
│   │   │   ├── light_mode.json           # {"primary": "#ffffff", ...}
│   │   │   ├── dark_mode.json
│   │   │   └── custom_theme.json
│   │   └── Sounds/
│   │       ├── notification.wav
│   │       ├── error.wav
│   │       └── success.wav
│   │
│   └── Tools/                             # App utilities
│       ├── setup_wizard.py               # Initial setup
│       ├── diagnostics.py                # Self-test
│       └── [custom utilities]
│
├── Recovery/                               # 🛟 LOCAL RECOVERY
│   │                                       # (App-specific recovery data)
│   │
│   ├── logs/                             # Error logs
│   │   ├── app_[YYYY-MM-DD].log
│   │   └── error_[YYYY-MM-DD].log
│   │
│   ├── session.json                      # Current operation state
│   │   └── {
│   │       "stage": "RUNTIME",
│   │       "last_operation": "server_start",
│   │       "timestamp": "2026-05-16T22:30:00",
│   │       "error": null
│   │     }
│   │
│   ├── recovery_manifest.json            # Integrity checklist
│   │   └── {
│   │       "files": [...],
│   │       "checksums": {...},
│   │       "version": "1.0.0"
│   │     }
│   │
│   ├── [app_name]_backup/               # Local backup directory
│   │   ├── Core_Source_Master.7z        # Compressed core
│   │   ├── JRE_All_Versions.7z          # Compressed runtimes
│   │   └── Bin_Tools.7z                 # Compressed tools
│   │
│   └── .extracting                      # Lock file (during extraction)
│
├── User_Space/                             # 👤 USER DATA (SACRED!)
│   │                                       # (Never touched by updates)
│   │
│   ├── global_registry.alve             # App registry (managed by Alve Apps)
│   │   └── {...installed_apps...}
│   │
│   ├── user_preferences.json            # User settings
│   │   └── {
│   │       "language": "en-US",
│   │       "theme": "dark",
│   │       "monitor_thresholds": {
│   │         "memory_percent": 82,
│   │         "cpu_percent": 82
│   │       }
│   │     }
│   │
│   ├── Projects/                        # (App-specific)
│   │   ├── [Project_1]/
│   │   │   ├── structure.tree
│   │   │   ├── config.alve
│   │   │   ├── Instances/
│   │   │   │   └── [Server_1]/
│   │   │   │       ├── server.json
│   │   │   │       ├── world/
│   │   │   │       └── logs/
│   │   │   └── Logs_History/
│   │   └── [Project_N]/
│   │
│   ├── Backups/                        # User-initiated backups
│   │   ├── Backup_[YYYY-MM-DD-HHmmss]/
│   │   │   └── [compressed project]
│   │   └── Backup_Server-[DATE]/       # Server-specific backup
│   │
│   ├── Logs/                           # User activity logs
│   │   ├── activity_[YYYY-MM-DD].log
│   │   └── stats_[YYYY-MM-DD].json
│   │
│   └── Database/                       # User data persistence
│       ├── data.db                    # SQLite (optional)
│       └── [app-specific data]
│
├── Temp/                                   # ⏳ TEMPORARY FILES
│   │                                       # (Cleaned up automatically)
│   │
│   └── .tmp_buffer/                    # Staging for copy operations
│       ├── [in-progress files]
│       └── [extraction temp]
│
├── Assets/                                 # 🎨 SHARED ASSETS (Optional)
│   │                                       # (Used by all instances)
│   │
│   ├── Icons/                          # Global icons
│   │   └── [shared icon files]
│   ├── Themes/                         # Global themes
│   │   └── [theme files]
│   └── Sounds/                         # Global sounds
│       └── [sound files]
│
├── Documents/                              # 📚 DOCUMENTATION
│   │
│   ├── README.md                       # Quick start
│   ├── INSTALLATION.md                 # Installation guide
│   ├── USER_GUIDE.md                   # User manual
│   ├── API.md                          # API documentation
│   ├── DEVELOPER.md                    # Developer guide
│   │
│   ├── Guides/
│   │   ├── 01_Quick_Start.md
│   │   ├── 02_Advanced_Usage.md
│   │   └── 03_Troubleshooting.md
│   │
│   ├── Policies/
│   │   ├── Privacy_Policy.txt
│   │   ├── Terms_of_Service.txt
│   │   └── EULA.txt
│   │
│   ├── Legal/
│   │   ├── Credits.txt                # Acknowledgments
│   │   ├── Open_Source_Licenses.txt
│   │   └── Copyright_Notice.txt
│   │
│   └── Templates/
│       ├── sample_structure.tree
│       └── optimized_config.json
│
├── main.py                                 # 📍 ENTRY POINT
│   └── if __name__ == "__main__": main()
│
├── requirements.txt                        # 📦 DEPENDENCIES
│   └── psutil>=5.9.0
│
├── requirements-dev.txt                    # 🔧 DEV DEPENDENCIES
│   └── pytest>=7.0
│
├── pyproject.toml                          # 🐍 PYTHON PROJECT CONFIG
│   └── [build system, metadata, etc]
│
├── .gitignore                              # 📝 GIT CONFIG
│   └── __pycache__/
│      *.pyc
│      User_Space/
│      Recovery/logs/
│
├── CHANGELOG.md                            # 📋 VERSION HISTORY
│   └── ## Version 1.0.0 (2026-05-16) ...
│
├── LICENSE                                 # ⚖️ LICENSE (GPL v3 / MIT)
│   └── [license text]
│
└── version.txt                             # 📌 VERSION STRING
    └── 1.0.0
```

---

## ✨ SPECIAL CASE: Alve_Apps Structure

Alve Apps has **ONE EXTRA FOLDER** that breaks the pattern:

```
Alve_Apps/
│
├── [All above standard folders]
│
├── Alve_All_App_Data/                    # 🔑 UNIQUE TO ALVE APPS ONLY!
│   ├── Nexus/                           # Complete copy of Alve Nexus
│   ├── Guard/                           # Complete copy of Alve Guard
│   ├── Studio/                          # Complete copy of Alve Studio
│   ├── Nexus_v2/                        # Alternative instances
│   └── .global_registry.json            # Central instance tracker
│
└── [Rest of standard structure]
```

**Why only Alve Apps?**
- Alve Apps is the **central hub** that manages sub-apps
- This folder is the **deduplication & centralization point**
- Sub-apps don't have this (they ARE the apps)
- When sub-app migrates, its 100% copy goes here

---

## 📐 FOLDER PURPOSES & PERMISSIONS

| Folder | Purpose | Updatable? | User Modifiable? |
|--------|---------|-----------|-----------------|
| `.alve/` | App identity & signature | ❌ NO | ❌ NO |
| `Alve_Core/` | Shared tools, runtimes, core logic | ✅ System only | ❌ NO |
| `Specifics/` | App-specific code & resources | ✅ YES | ⚠️ Advanced |
| `Recovery/` | Local recovery & logs | ✅ Auto cleanup | ⚠️ Manual backup |
| `User_Space/` | User data & settings | ✅ App updates config | ✅ YES (precious) |
| `Temp/` | Temporary files | ✅ Auto cleanup | ❌ NO |
| `Assets/` | Shared resources | ✅ App updates | ❌ NO |
| `Documents/` | Docs & guides | ✅ App updates | ✅ Reference |

---

## 🎯 IMPLEMENTATION CHECKLIST

When creating a **new Alve app**, ensure:

- [ ] `.alve/identity.json` present & signed
- [ ] `.alve/manifest.alve` lists active modules
- [ ] `Alve_Core/` is complete copy (100% identical across apps)
- [ ] `Specifics/Source/` contains app logic
- [ ] `Specifics/Resources/` has app assets
- [ ] `Recovery/` has local backup & logs
- [ ] `User_Space/` is EMPTY at install (filled by user)
- [ ] `main.py` is valid entry point
- [ ] `requirements.txt` lists dependencies
- [ ] `CHANGELOG.md` documents versions
- [ ] `LICENSE` is present
- [ ] `README.md` explains purpose

---

## 📊 SIZE EXPECTATIONS

| Component | Size |
|-----------|------|
| Alve_Core/ (Bin + Runtimes) | ~5GB (shared) |
| Specifics/ (App code) | 1-500MB (varies) |
| Recovery/ | 10-100MB (logs + backup) |
| User_Space/ (Empty at install) | 0MB |
| Documents/ | 5-50MB |
| **Total (new app)** | **~5.5-6GB** |

**In Alve Apps:**
- Alve_Core/ (shared) = 5GB (once)
- Alve_All_App_Data/Nexus/ = 1GB (specifics + data)
- Alve_All_App_Data/Guard/ = 500MB
- Total = 6.5GB (instead of 24GB standalone)

---

## 🔄 FOLDER UPDATE RULES

```
During System Update:
✅ Can update: Alve_Core/, Specifics/
❌ Cannot update: User_Space/, Recovery/logs

During Recovery:
✅ Can update: All
❌ User data ALWAYS preserved

During Uninstall:
✅ Delete: Alve_Core/, Specifics/, Recovery/, Temp/
❌ Keep: User_Space/ (user chooses)
```

---

## 🚀 MIGRATION INTO ALVE_ALL_APP_DATA

When migrating sub-app to Alve Apps:

```
Source: Alve_Nexus/ (Folder A)
Target: Alve_Apps/Alve_All_App_Data/Nexus/

Copy: 100% of Alve_Nexus/ → Target
├─ .alve/
├─ Alve_Core/ (will be deduplicated later)
├─ Specifics/
├─ Recovery/
├─ User_Space/
├─ Temp/
├─ Documents/
├─ main.py
├─ requirements.txt
└─ ...everything

Then:
├─ Add: .migration_config.json (points to new User_Space location)
├─ Delete: Original Alve_Nexus/ (Folder A)
├─ Update: Global registry
└─ Done!
```

---

## 💡 CUSTOMIZATION POINTS

Apps can customize:

✅ **Specifics/Source/** - App business logic
✅ **Specifics/Resources/** - Icons, themes, sounds
✅ **Specifics/Tools/** - Custom utilities
✅ **Recovery/** - App-specific backup format
✅ **User_Space/** - App-specific data schema
✅ **Documents/** - App-specific guides
❌ **Alve_Core/** - Locked (same for all apps)
❌ **.alve/** - Locked (identity & signature)

---

## 🎓 NAMING CONVENTIONS

```
Folders: PascalCase
├─ User_Space, Alve_Core, Alve_Apps, Recovery, Temp

Files:
├─ Python: snake_case (main.py, server_manager.py)
├─ Config: snake_case.json (user_preferences.json, rules.json)
├─ Docs: SCREAMING_SNAKE_CASE (README.md, CHANGELOG.md)
├─ Data: [name]_[YYYY-MM-DD].json (activity_2026-05-16.json)
└─ Archives: [name]_[timestamp].7z (backup_20260516_223000.7z)

Apps:
├─ App folder: Alve_[AppName] (Alve_Nexus, Alve_Guard)
├─ App ID: ALV-[SHORTNAME] (ALV-NEXUS, ALV-GUARD)
└─ Instance: [app]_[variant] (Nexus, Nexus_v2, Guard)
```

---

**LAST UPDATED:** 2026-05-16
**VERSION:** 1.0
**STATUS:** Final & Ready for Implementation
