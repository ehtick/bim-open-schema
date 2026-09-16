# Contributing to and Understanding the Code

This repository holds the BIM Open Schema specification, its C# reference
implementation, and sample files. The specification is the code: the types in
`src/Ara3D.BimOpenSchema` define the tables, columns, and enums that a `.bos`
archive contains, and the version constant lives in `BimOpenSchema.cs`.

## Code Organization

| Project | What it holds |
|---|---|
| `src/Ara3D.BimOpenSchema` | The schema itself: the data records that serialize to Parquet, the names and types of common Revit parameters, and an object model for convenient programmatic access. |
| `src/Ara3D.BimOpenSchema.IO` | Reading and writing `.bos` archives (Parquet in a zip), plus export to Excel, DuckDB, and BFAST. |
| `src/Ara3D.BimOpenSchema.DuckDb` | Loading BOS data into DuckDB and querying it. |
| `src/Ara3D.BimOpenSchema.Harmonizer` | Canonical names and units across models exported by different tools. |
| `src/Ara3D.BimOpenSchema.DataModel` and `.DataModel.IO` | A relational, source-snapshot model built from BOS data, with validation and spatial indexing. |
| `src/Ara3D.BimOpenSchema.BuildingModel*` | A higher-level building model with source mapping, workflows, and DuckDB projection. |
| `tests/` | Unit tests for the DataModel and BuildingModel projects. Tests that convert IFC files into BOS live with the IFC code in [BIM Open Toolkit](https://github.com/ara3d/bim-open-toolkit). |
| `examples/` | Sample `.bos` files generated from the Autodesk sample projects. |

## Code As Specification

The official specification of the current version of BIM Open Schema is:

- [src/Ara3D.BimOpenSchema/BimOpenSchema.cs](src/Ara3D.BimOpenSchema/BimOpenSchema.cs)
- [src/Ara3D.BimOpenSchema/BimGeometry.cs](src/Ara3D.BimOpenSchema/BimGeometry.cs)

There is no separate copy to keep in sync. A change to the on-disk tables is a
change to these files and to `Manifest.CurrentVersion`.

## Building

Requires the .NET 8 SDK.

```bash
dotnet build BimOpenSchema.slnx
dotnet test BimOpenSchema.slnx
```

Dependencies come from nuget.org except two: the `Platonic.Core` and
`Platonic.Analyzers` packages used by the DataModel and BuildingModel projects
come from a `Platonic.CSharp` checkout next to this repo (see `Platonic.props`),
and the `Ara3D.*` packages track the version in `Directory.Build.props`.

When this repo is checked out as a submodule of a host repo, the host's
`Directory.Build.props` and `nuget.config` take precedence, so package versions
and feeds follow the host.

Some tests read large sample models from a directory named by the
`BOS_SAMPLE_DIRECTORY` environment variable and are skipped when it is unset.

## Related Code in Other Repositories

- [Revit 2025 Exporter](https://github.com/ara3d/ara3d-sdk/tree/main/ext/Ara3D.BimOpenSchema.Revit2025) in the Ara 3D SDK, built on [Ara3D.Bowerbird.Revit.Samples](https://github.com/ara3d/ara3d-sdk/tree/main/ext/Ara3D.Bowerbird.RevitSamples). The files that do the bulk of the work are `BimOpenSchemaRevitBuilder.cs` and `MeshGatherer.cs`.
- [BIM Open Schema Browser](https://github.com/ara3d/ara3d-sdk/tree/main/apps/Ara3D.BimOpenSchema.Browser), a WPF grid viewer with GLB and Excel export.
- [Ara3D.DataTable](https://github.com/ara3d/ara3d-sdk/blob/main/src/Ara3D.DataTable), [Ara3D.Models](https://github.com/ara3d/ara3d-sdk/blob/main/src/Ara3D.Models), and [Ara3D.Geometry](https://github.com/ara3d/ara3d-sdk/blob/main/src/Ara3D.Geometry), the general-purpose libraries this code depends on.
- [BIM Open Toolkit](https://github.com/ara3d/bim-open-toolkit), which converts IFC files to BOS and runs analyses over the result.

# Coding FAQ

## Where is the "Load" or "Read" Function for a .BOS file?

`ReadParquetFromZip` and `ReadBimGeometryFromParquetZipAsync` in
`src/Ara3D.BimOpenSchema.IO/ParquetUtils.cs`. A `.bos` file is a zip of Parquet
tables, one per record type in the schema.

## Where is the "Save" or "Write" Function for a .BOS file?

`WriteParquetToZip` in the same file. Build an `IBimData` with `BimDataBuilder`,
convert it to a data set, then write it.

## How do I add a new parameter to export from Revit?

Follow the pattern in [src/Ara3D.BimOpenSchema/CommonRevitParameters.cs](src/Ara3D.BimOpenSchema/CommonRevitParameters.cs)
to add the parameter name and type. Then add the code that reads it from the
Revit document in `BimOpenSchemaRevitBuilder.cs` in the Ara 3D SDK. If reading
the value can throw, guard it.

# Contributions

Contributions are welcome if they conform to the style and design of the
existing code. Open an [issue](https://github.com/ara3d/bim-open-schema/issues)
with questions or suggestions.
