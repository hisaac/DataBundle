# DataBundle

## Introduction

DataBundle is an open, lightweight, and portable data interchange format designed for ease of sharing, collaborative editing, and long-term archival. Inspired by the TextBundle specification, DataBundle primarily stores structured data in TSV format, supplemented by JSON metadata and optional assets.

## Structure of a DataBundle

A DataBundle file is a directory package (`.databundle`) containing:

- **data.tsv** *(required)*: The primary structured data file.
- **metadata.json** *(required)*: Describes the schema, data types, formatting rules, relationships, sorting/filtering rules, and formulas.
- **assets/** *(optional)*: A directory containing files (images, documents, etc.) referenced by relative URLs within the TSV.
- **linked/** *(optional)*: Additional TSV files that provide relational or linked data.

### Example Structure

```text
example.databundle/
├── data.tsv
├── metadata.json
├── assets/
│   ├── image1.png
│   └── document1.pdf
└── linked/
    ├── supplementary.tsv
    └── lookup.tsv
```

## Data TSV Format

- Standard TSV format adhering to [IANA's TSV Media Type](https://www.iana.org/assignments/media-types/text/tab-separated-values).
- UTF-8 encoding without BOM.
- Asset references use relative URLs, e.g., `assets/image1.png`.

## Metadata JSON Format

The metadata file (`metadata.json`) provides context for interpreting the data and managing presentation. It includes:

### Required Properties

- **columns** *(array of objects)*: Describes each TSV column:
	- `name`: Column name matching TSV header.
	- `type`: Data type (`string`, `number`, `date`, `boolean`, `file`, etc.).
	- `format` *(optional)*: Display format information (e.g., date format, currency).

### Optional Properties

- **document** *(object)*: General information about the DataBundle:
	- `title`: Human-readable title.
	- `description`: Brief description or notes.
	- `created`: ISO 8601 creation timestamp.
	- `modified`: ISO 8601 modification timestamp.
- **formulas** *(array of objects)*: Spreadsheet-style formulas to be applied:
	- `target`: Column or cell range.
	- `formula`: Formula expression.
- **sorting** *(array of objects)*:
	- `column`: Column name.
	- `order`: Sort order (`ascending`, `descending`).
- **filtering** *(array of objects)*:
	- `column`: Column name.
	- `criteria`: Filter criteria.
- **relationships** *(array of objects)*:
	- `type` *(optional)*: Specifies relationship type (`one-to-one`, `one-to-many`). Defaults to `one-to-many` if unspecified.
	- `sourceColumn`: Column in the primary TSV.
	- `linkedFile`: Path to TSV file in `linked/`.
	- `linkedColumn`: Corresponding column in the linked TSV.
	- `linkedCells` *(optional)*: Explicit cell-level mappings for one-to-one relationships:
		- `sourceRow`: Row number in primary TSV.
		- `linkedRow`: Row number in linked TSV.

### Example Metadata

```json
{
	"columns": [
		{ "name": "ProductID", "type": "string" },
		{ "name": "Price", "type": "number", "format": "$0.00" },
		{ "name": "ReleaseDate", "type": "date", "format": "YYYY-MM-DD" },
		{ "name": "Image", "type": "file" }
	],
	"document": {
		"title": "Product Catalog",
		"description": "A basic product catalog example.",
		"created": "2025-03-23T10:00:00Z",
		"modified": "2025-03-23T10:15:00Z"
	},
	"formulas": [
		{ "target": "TotalPrice", "formula": "Quantity * Price" }
	],
	"sorting": [
		{ "column": "ReleaseDate", "order": "descending" }
	],
	"relationships": [
		{
			"type": "one-to-one",
			"sourceColumn": "UserProfileID",
			"linkedFile": "linked/profiles.tsv",
			"linkedColumn": "ProfileID",
			"linkedCells": [
				{"sourceRow": 1, "linkedRow": 3},
				{"sourceRow": 2, "linkedRow": 7},
				{"sourceRow": 3, "linkedRow": 12}
			]
		}
	]
}
```

## Assets

Assets referenced in the TSV file must reside in the `assets/` folder. Asset references must use relative paths.

## Linked Data

- Linked data is stored in additional TSV files in the `linked/` directory.
- Relationship metadata defines links between the primary TSV and linked TSVs.

## Usage

DataBundles facilitate data sharing and collaboration without proprietary format lock-in. They support:

- Data-driven applications.
- Simple spreadsheet or database functionality.
- Interoperable exchange between various systems.

## Versioning

Future spec versions will maintain backward compatibility. The metadata file can specify a schema version if needed for future evolution.

## Acknowledgements

This specification takes inspiration from the [TextBundle](https://textbundle.org) specification by Brett Terpstra ([@ttscoff](https://github.com/ttscoff)).

---

This specification is open for feedback and improvement.
