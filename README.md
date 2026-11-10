# Intecre.File

A TwinCAT 3 library for files: keep a structure in a JSON file, write and read CSV files, write text files, and check that a file exists. Built and tested on TwinCAT 3.1.4026.

How to use each block, with examples, sizes, messages and known issues: [docs/user-guide.html](docs/user-guide.html). Download it and open it in a browser, because GitHub shows HTML files as source.

## What is in it

- **Structure persistence**: `T_StructPersistence` keeps one structure in a JSON file. It reads the file at start-up, creates it if it is missing, and saves the structure again after it changes.
- **JSON files**: `T_StructToJsonFileWriter` and `T_StructFromJsonFileReader` write any structure to a file as JSON and read it back, through Beckhoff's `Tc3_JsonXml`. The size limit is the library parameter `JsonParams.JSON_DOC_SIZE` (10 000 characters).
- **CSV files**: `T_CsvFileWriter` builds a file field by field and row by row, then writes or appends it. `T_CsvFileReader` hands back one field at a time with its row and column. The separator is a comma or a semicolon.
- **Text files**: `T_TxtFileWriter` builds a file from strings and lines, then writes or appends it.
- **Utilities**: `T_CheckFileExists`.

Every block works over several PLC cycles. Start an operation with a method, call the instance every cycle, and wait for `Busy` to fall.

## Getting it

1. **The release**: `Intecre_File.library` and the TcPkg package `Intecre.File.<version>.nupkg` are attached to each [GitHub release](https://github.com/Intecre/twincat-files/releases). Install the `.library` with the Library Repository dialog in the XAE or with `RepTool`. Or put the `.nupkg` in a folder that is a TcPkg source and run `tcpkg install Intecre.File`.
2. **The `.library` file** `Intecre_File.library` in the repository root, and the packages under `dist/`.
3. **The source**: open `Intecre-File.sln` and save the `Intecre_File` PLC project as a library.

Reference it as the placeholder `Intecre.File` (namespace `Intecre_File`).

## Dependencies on TwinCAT 4026

All references are placeholders resolved to the newest installed version.

| Library | TcPkg package | Feed |
|---|---|---|
| Intecre.Utilities 4.1 or later | `Intecre.Utilities` | [twincat-utils releases](https://github.com/Intecre/twincat-utils/releases), Intecre feed |
| Tc2_Standard | `TwinCAT.XAE.PLC.Lib.Tc2_Standard` | stable, part of `TwinCAT.StandardRM.XAE` |
| Tc2_System | `TwinCAT.XAE.PLC.Lib.Tc2_System` | stable, part of `TwinCAT.StandardRM.XAE` |
| Tc2_Utilities | `TwinCAT.XAE.PLC.Lib.Tc2_Utilities` | stable, part of `TwinCAT.StandardRM.XAE` |
| Tc3_JsonXml | `TwinCAT.XAE.PLC.Lib.Tc3_JsonXml` | stable, part of `TwinCAT.StandardRM.XAE` |

The test project also needs [TcUnit](https://tcunit.org) 1.3 or newer, which has no TcPkg package: install its `.library` by hand.

## Building and testing

The solution `Intecre-File.sln` holds two PLC projects:

- `Intecre_File`: the library.
- `TestRunner`: the TcUnit suites, one `*_Tests` function block per library block under `Tests/`, with helpers under `Helpers/`. The tests write their files to `C:\TcUnit_IntecreFile`.

`TestRunner` references the library through the placeholder, so the library must be installed before the tests build. The loop while developing is:

1. Change the library, then *Save as library and install* (or `tclib publish`).
2. Build `TestRunner` and activate the configuration on a runtime. The TwinCAT user-mode runtime on the engineering PC is enough.
3. Read the results in the Error List.

Current state: 7 suites, 35 tests. 24 pass on 4026 with the user-mode runtime. The 11 that fail are known issues, listed with their work-arounds in section 12 of the [user guide](docs/user-guide.html#limits).

## Releasing

Versions follow `major.minor.build.revision` in the `.plcproj`; `Global_Version` and `F_GetVersion` are generated from it. Publishing uses Intecre TwinCAT Library Tools (`tclib`), configured in `File Handling/Intecre_File/Intecre_File.libtools.json`. It compiles, saves `Intecre_File.library` to the repository root, installs it, packs the TcPkg package into `dist/`, and tags the commit `v<version>`.

```bash
tclib publish --project "File Handling/Intecre_File/Intecre_File.plcproj"
```

Release notes are kept in the library description (Project Information in the XAE, or `tclib props`).

## Contributing

- All comments are welcome.
- Add or extend a TcUnit suite with every change.

## Who do I talk to?

- Mark Lazarides, Intecre Ltd.
