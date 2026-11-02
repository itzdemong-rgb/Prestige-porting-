# Prestige recovery status

Target: Minecraft 1.21.11 / Fabric / Java 21

## Uploaded inputs
All six supplied archives are present under `source/` on `main`:
- `01-Core-Library.zip`
- `02-Client-Management.zip`
- `03-Modules-119.zip`
- `04-RenderEngine-GUI.zip`
- `05-Deobfuscation-Mappings.zip`
- `06-Key-Recovery-Data.zip`

## Latest CI result
GitHub Actions run: https://github.com/itzdemong-rgb/Prestige-porting-/actions/runs/41471164123

Java 21 and Gradle 9.5.1 setup succeeded, and all six archives extracted successfully. The build stopped at project discovery because the archives do not contain `build.gradle` or `build.gradle.kts`. Java compilation did not start.

## Local source audit
- 542 recovered Java source files are present.
- 356 source files reference missing recovery helpers `dev.prestige.recovery.SourceSupport` and/or `RecoveryNames`.
- The recovered code contains 3,225 `SourceSupport.methodType` references, 2,318 `rethrow` references, 1,090 `handle` references, 1,049 `asBoolean` references, and 698 each of `RecoveryNames.methodName` and `fieldName`.
- Eight renderlib files have syntax/structure corruption: `RenderCommandBatcher.java`, `GlTextureParameterUtil.java`, `TextureLoader.java`, `RenderEvents.java`, `MeshBufferManager.java`, `FramebufferEffect.java`, `DrawPipelines.java`, and `MeshDataConsumer.java`.
- The supplied archives do not provide a complete Fabric project definition, a verified mod entrypoint, or the complete metadata/mixin configuration.
- The current working environment contains no original Prestige client JAR or compiled `.class` payload to use as bytecode-level evidence. The TSV recovery data is useful metadata, but it is not a substitute for the missing class bytecode.

## Required recovery input
The highest-value missing input is the exact original Prestige client JAR that these source archives were recovered from (or a matching, complete compiled class directory). It is needed to reconstruct obfuscated/dynamic helper behavior and repair decompiler-damaged methods against actual bytecode instead of guessing. A different client JAR is not a safe substitute.

## Next engineering steps
1. Obtain the matching original Prestige JAR or complete compiled class files.
2. Reconstruct `SourceSupport` and `RecoveryNames` from bytecode and recovery metadata.
3. Repair the eight damaged renderlib files using bytecode/source evidence.
4. Restore Fabric metadata, entrypoint, mixin configuration, and version-specific dependencies.
5. Run the full Gradle build on GitHub Actions, inspect the JAR, and launch-test it on Minecraft 1.21.11 with the matching Fabric Loader/API.

## Build integrity
No working JAR has been produced. No placeholder/stub JAR has been generated or presented as a completed client.


## Newly supplied binary: `PrestigeClient.jar` (2026-11-02)
The uploaded file was inspected statically only; it was not executed or loaded. SHA-256:
`ee3bfa3a0cdc83e57afc4c08470d5ea97257ef5b7c3d04184b407b1fab15fa15`

This file is **not** the missing original compiled Prestige client binary needed for the current source recovery:
- Fabric metadata identifies it as `prestige4`, version `4.0`, name `Prestige Client 4.0`, description `offline loader`, with entrypoint `dev.prestige4.PrestigeMod`.
- It contains 342 class files, primarily `dev/prestige4` loader/server classes plus bundled JNA/Gson libraries; it does not contain the expected recovered Prestige client class tree.
- It bundles `prestige_offline.dll`, a mitmproxy CA certificate, a PKCS#12 server certificate/private-key container, replay captures, and API response data.
- Static class-string inspection shows code paths that install a CA into Windows Root and Java cacerts, modify the Windows hosts file for `api.prestigeclient.vip`, start local listeners, create a highest-privilege logon scheduled task, and use JNA/kernel32 remote-process injection APIs.
- The replay archive contains an 18,143,072-byte captured `prestige.jar` download response, but its body is not directly ZIP/JAR-readable and appears encoded/encrypted. This is not a verified usable source or build input.

**Security handling:** do not install this JAR, its certificate, or its DLL; do not run it on a normal machine. No payload was executed. The bundled certificate/private-key and process-injection functionality make this a high-risk loader package. These observations are static-analysis findings, not a complete malware verdict.

**Recovery impact:** this upload does not resolve the missing `SourceSupport` / `RecoveryNames` implementations or the eight damaged renderlib files. It is not used as a replacement source. The previously supplied six archives remain the only source inputs currently accepted for the recovery branch.


## Original JAR inspection (2026-11-02)
The uploaded `PrestigeClient.jar` was inspected statically; it was not executed. SHA-256:
`ee3bfa3a0cdc83e57afc4c08470d5ea97257ef5b7c3d04184b407b1fab15fa15`

Its `fabric.mod.json` identifies it as `prestige4`, version `4.0`, description `offline loader`, with entrypoint `dev.prestige4.PrestigeMod`. It contains 342 class files, primarily loader/server and bundled third-party library classes. It is not the missing original Prestige client JAR matching the 542 recovered source files.

Static class inspection found code paths that:
- install a bundled self-signed mitmproxy CA into Windows Root and Java cacerts trust stores;
- edit the Windows hosts file for `api.prestigeclient.vip`;
- start local CRL and TLS replay servers on loopback;
- create an elevated logon scheduled task named `PrestigeServer`;
- use JNA/kernel32 APIs including `OpenProcess`, `VirtualAllocEx`, `WriteProcessMemory`, and `CreateRemoteThread`;
- extract and load a bundled Windows DLL.

The archive also contains `assets/prestige4/flows.zip` with captured API request/response traffic. The recorded `modDownloadNew` response is an 18,143,072-byte opaque payload advertised as `prestige.jar`; its bytes do not begin with ZIP/JAR magic and are not directly usable as a JAR. The uploaded loader does not provide the missing recovered client source/helper classes needed to compile the current reconstruction.

Bundled CA certificate subject/issuer: `CN=mitmproxy, O=mitmproxy`, self-signed, valid 2026-09-05 through 2036-09-02. A PKCS#12 file contains an encrypted private-key bag. Do not install these certificates or execute the loader/DLL.

Security note: these are high-risk behaviours established by static code/constant-pool inspection, not a complete malware verdict. Nothing was executed, no certificate was installed, and no network request was made.

Audit report generated locally: `PRESTIGE_JAR_STATIC_AUDIT.txt` (the full report is available in the ChatGPT conversation). The actual matching client JAR or compiled client classes are still required for bytecode-backed source recovery.


## Follow-up: uploaded PrestigeClient.jar audit (2026-11-02)
The user uploaded `PrestigeClient.jar` (SHA-256: `ee3bfa3a0cdc83e57afc4c08470d5ea97257ef5b7c3d04184b407b1fab15fa15`). Static inspection found:
- Fabric metadata identifies it as `prestige4`, version `4.0`, description `offline loader`, entrypoint `dev.prestige4.PrestigeMod`; it is not the expected original binary for the recovered 542-source tree.
- Contains `prestige_offline.dll`, a PEM mitmproxy CA, a PKCS#12 server certificate/private-key bundle, and 100 captured API replay files.
- Class bytecode references Windows Root certificate installation, Java cacerts import, hosts-file modification for `api.prestigeclient.vip`, local listeners on 127.0.0.1, elevated logon scheduled-task creation, and JNA remote-process injection APIs.
- Captured `modDownloadNew` response is an opaque 18,143,072-byte payload, not a directly readable JAR. No decryption/authentication bypass was attempted; no loader/DLL was executed and no certificates were installed.
- This upload cannot supply the missing `SourceSupport` / `RecoveryNames` implementation or repair the recovered renderlib source.

## Build attempt (2026-11-02)
Ran the local Gradle 9.5.1 `build` task against the recovery scaffold. It failed during plugin resolution at `build.gradle:2`: Fabric Loom plugin `net.fabricmc.fabric-loom-remap:1.17.13` could not be resolved from the configured repositories in this environment. Java compilation did not start in this attempt. A previous compile attempt on the recovered sources produced 100+ syntax/structure errors in addition to the missing recovery helpers.
No release JAR was produced. The current source set remains incomplete and is not buildable as a working client without the matching original compiled Prestige client binary/source and repair of the decompiler-damaged files.

## Current decision
Do not package or publish a placeholder JAR. Continue only with a matching legitimate original client JAR or complete class directory; do not execute the uploaded offline loader or reuse its embedded opaque download payload.


## Follow-up recovery triage (2026-11-02)

The local Gradle failure was checked against the official Fabric Maven index and Fabric Loom documentation. The plugin ID `net.fabricmc.fabric-loom-remap` is the correct remapping plugin for Minecraft 1.21.11 and earlier, and version `1.17.13` is published. Therefore, the observed failure is dependency resolution in the current build environment, not evidence that the selected Loom version or plugin ID is invalid.

The local scaffold confirms that the recovered source tree is heavily obfuscated/decompiler-generated. The 356 affected source files collectively contain thousands of calls into missing helper methods, including `methodType`, `handle`, `rethrow`, `asBoolean`, `fieldName`, and `methodName`. The supplied TSVs contain class/member recovery metadata but do not by themselves prove all runtime lookup/decryption semantics. Implementing helpers with guessed/default return values would not be a valid recovery.

The current safe next path is bytecode-backed reconstruction of the missing helpers and damaged renderlib methods, followed by restoring a verified Fabric entrypoint/mixin set. The uploaded offline-loader JAR is not used as a substitute and must remain unexecuted.

No new JAR was produced by this triage. No source stubs or fake client artifact were added.
