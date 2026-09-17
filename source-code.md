# The Specification as Code

This repository holds only the BIM Open Schema specification and sample files.
The specification is the code: the C# types in `src/Ara3D.BimOpenSchema` define
the tables, columns, enums, and manifest that a `.bos` archive contains. There is
no prose copy to keep in sync. A change to the on-disk tables is a change to
these files and to `Manifest.CurrentVersion`.

## What is in the spec

| File | What it defines |
|---|---|
| `src/Ara3D.BimOpenSchema/BimOpenSchema.cs` | The non-geometry tables: manifest, entities, descriptors, parameters, documents, relations, diagnostics, and the shared string, number, and point pools. |
| `src/Ara3D.BimOpenSchema/BimGeometry.cs` | The geometry tables: instances, meshes, vertex and index buffers, materials, and transforms. |
| `src/Ara3D.BimOpenSchema/BimGeometryTableName.cs` | The names of the geometry Parquet files. |
| `src/Ara3D.BimOpenSchema/BimData.cs` | A plain record set implementing `IBimData`, one array per table. |
| `src/Ara3D.BimOpenSchema/CommonRevitParameters.cs` | The canonical names and types of the parameters an exporter writes. |
| `examples/` | Sample `.bos` files generated from the Autodesk sample projects. |

The rule for what belongs here: types that map one-to-one to tables, columns,
manifest fields, enums, and constants. No builders, no extension methods, no
package references. The project compiles against the .NET 8 base class library
and nothing else, so it can be read by an implementer in any language.

## Building

Requires the .NET 8 SDK.

```bash
dotnet build BimOpenSchema.slnx
```

The project packs as `Ara3D.BimOpenSchema` on NuGet. When this repo is checked
out as a submodule of a host repo, the host's `Directory.Build.props` takes
precedence for version and package metadata.

## Where the code that uses the spec lives

The C# reference implementation is in
[BIM Open Toolkit](https://github.com/ara3d/bim-open-toolkit):

- `src/Ara3D.BimOpenSchema.ObjectModel`: builders, accessors, and a navigable object model.
- `src/Ara3D.BimOpenSchema.IO`: reading and writing `.bos` archives (Parquet in a zip), with export to Excel, DuckDB, and BFAST.
- `src/Ara3D.BimOpenSchema.DuckDb`: loading BOS data into DuckDB and querying it.
- `src/Ara3D.BimOpenSchema.Harmonizer`: canonical names and units across models exported by different tools.
- `src/Ara3D.BimOpenSchema.DataModel` and `src/Ara3D.BimOpenSchema.BuildingModel*`: higher-level relational and building models built from BOS data.
- `src/Ara3D.Ifc.Bos`: IFC to BOS conversion.
- `plugins/Ara3D.BIMOpenSchema.Revit2025`: the Revit 2025 exporter.
- `apps/Ara3D.BimOpenSchema.Browser`: a WPF grid viewer with glTF and Excel export.
- `tests/Ara3D.BimOpenSchema.Tests` and siblings: the tests for all of the above.

Other implementations:

- [Ara3D WebGL](https://github.com/ara3d/ara3d-webgl) loads and views BOS files in the browser.
- [BIM Open Schema Reader](https://bim-open-schema-reader.vercel.app) queries BOS data in the browser with DuckDB.

## FAQ

**Where is the read or write function for a .bos file?** In the toolkit,
`ParquetUtils.cs` in `src/Ara3D.BimOpenSchema.IO`. A `.bos` file is a zip of
Parquet tables, one per record type in the schema.

**How do I add a new parameter to export from Revit?** Add its name and type to
`CommonRevitParameters.cs` here, following the existing pattern. Then add the
code that reads it from the Revit document in `BimOpenSchemaRevitBuilder.cs`
under the toolkit's `plugins/Ara3D.BIMOpenSchema.Revit2025`.

**How do I change a table?** Edit the record in `BimOpenSchema.cs` or
`BimGeometry.cs`, bump `Manifest.CurrentVersion`, then update the reader and
writer in the toolkit and regenerate the files in `examples/`.

## Contributions

Contributions are welcome if they conform to the style and design of the
existing code. Open an [issue](https://github.com/ara3d/bim-open-schema/issues)
with questions or suggestions.
