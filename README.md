# Prestige Client — Android-friendly GitHub build

This repository is the GitHub Actions build workspace for the supplied Prestige source parts.

## Current source status
The six source ZIP archives are not yet present in this repository. Upload them into `source/`:
- `01-Core-Library.zip`
- `02-Client-Management.zip`
- `03-Modules-119.zip`
- `04-RenderEngine-GUI.zip`
- `05-Deobfuscation-Mappings.zip`
- `06-Key-Recovery-Data.zip`

The Gradle 9.5.1 distribution ZIP does not need to be uploaded; Actions installs Gradle 9.5.1 and Java 21 in the cloud.

## Build
Open **Actions → Prestige Android Build → Run workflow**. The workflow extracts the source archives, looks for a Gradle project, runs its build, and uploads the resulting JAR as an artifact.

Important: the supplied source parts currently do not include a root Gradle project definition, Fabric mod metadata, or confirmed runtime bootstrap dependencies. The workflow will report these blockers instead of pretending a JAR was built.
