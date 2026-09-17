# Ara3D.BimOpenSchema

The **BIM Open Schema (BOS)** specification as C# types: a column-oriented
representation of Building Information Modeling data.

BOS expresses federated BIM data as a set of tables (entities, parameters,
relations, geometry, and shared string, number, and point pools). It is
optimized for compact storage and fast loading into analytical tools (Parquet
files, DuckDB, or in-memory workflows), not for ad-hoc querying.

This package has no dependencies. It contains the record types, enums, and
constants that define the tables, and nothing else. The version is
`Manifest.CurrentVersion`.

## Key types

- `IBimData` and `BimData`: the root container, one array per table
- `Manifest`: version, generator application, export options
- `Entity`, `Parameter`, `ParameterDescriptor`: BIM elements and their properties
- `EntityRelation`: relationships between entities
- `BimGeometry`: tessellated geometry linked to entities, as flat columns
- `CommonRevitParameters`: the canonical parameter names an exporter writes

## Working with the data

Builders, accessors, serialization, and the IFC and Revit converters live in
[BIM Open Toolkit](https://github.com/ara3d/bim-open-toolkit), starting with
`Ara3D.BimOpenSchema.ObjectModel` and `Ara3D.BimOpenSchema.IO`.

## License

MIT, see [LICENSE](../../LICENSE).
