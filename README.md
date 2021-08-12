# Simple CSV Schema

Simple CSV Schema is a pragmatic approach for defining [CSV](https://datatracker.ietf.org/doc/html/rfc4180) schemas. Simple CSV Schema provides a CSV schema description via JSON.

Main features:

+ Easy schema definiton via JSON.
+ Dedicated to the CSV format.
+ Supports the following data types: string, enum, enum-set, bool, int, float, date, time, date-time, json and xml.
+ Supports CSV tables with ordered and unordered columns.
+ Supports CSV tables with or without header line.

## The JSON schema

The JSON schema definition for Simple CSV Schema you'll find [here](src/simple-csv-schema.json). For validating a Simple CSV Schema file against the JSON Schema defintion you can use a tool like [jsonschemavalidator.net](https://www.jsonschemavalidator.net/).

## Examples

This repository contains the folllowing examples:

* [Sample 01a: List of Students](samples/sample01a.scsv.json)
* [Sample 01b: List of Courses](samples/sample01b.scsv.json)
* [Sample 01c: List of Subjects](samples/sample01b.scsv.json)

## Why yet another CSV schema definition?

Unlike JSON or XML there is no well established schema definition language for CSV. But we are of course not the first ones with a proposal:

+ The [CSV Schema Language](https://digital-preservation.github.io/csv-schema/csv-schema-1.2.html) is a language for defining and validating CSV data. It uses its [own domain language](http://digital-preservation.github.io/csv-schema/csv-schema-1.2.html#ebnf) for describing the schema. 
+ The [Table Schema](https://specs.frictionlessdata.io/table-schema/) is a format to declare a schema for tabular data (CSV is just one example). The schema is designed to be expressible in JSON.

Both approaches are well thought but sadly didn't suit our needs.

Simple CSV Schema is an alternative schema description which is heavily inspired by both existing schema languages.

## Simple CSV Schema structure

A Simple CSV Schema document is a JSON document which can be validated against the [Simple CSV Meta Schema](src/simple-csv-schema.json). 

It consists of a JSON object with following properties:

+ `title` : A title for the CSV Schema. **This property is required**.
+ `description` : An optional description for the CSV Schema.
+ `table` : A table object. **This property is required**.

### Table 

A table is a JSON object with following properties:

+ `name` : Defines the name of the CSV table. **This property is required**.
+ `description` : An optional description of the table.
+ `type` : Specifies the table type. .**This property is required**. Possible values are:
    + `ordered` : A table with columns appearing in defined order.
    + `unordered` : A table with columns appearing in any order.
    + `headless` : A table with no header row. Columns must appear in defined order.
+ `delimiterChar` : Defines the delimiter character of the CSV format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV format. Default value is `"`.
+ `additionalColumns` : If `true` additional non-specified columns within the CSV document are allowed. Default value is `false`.
+ `columns` : An array of column definitions. **This property is required**.

### Columns

The `columns` array describes all columns within a CSV table. A column is a JSON object with following properties:

+ `name` : The name of the column. **This property is required**.
+ `description` : An optional description of the column.
+ `type` : The data type of the column. **This property is required**. Possible values are:
    + `string`
    + `int`
    + `float`
    + `bool`
    + `enum`
    + `enum-set`
    + `time`
    + `date`
    + `date-time`
    + `json`
    + `xml`
+ `optional` : If `true` the column must not be present in the CSV table. Default value is `false`. 
+ `nullable` : If `true` values of this column can be empty. Default value is `false`.
+ `pattern` : An optional regular expression which must always match with values of the column.

### About optional columns

If the table type is:

+ `ordered`, a column with `optional=true` marks an optional column. If the column is missing in the table all other columns must still apear in defined order. 
+ `headless`, a column with `optional=true` marks the start of an optional column range. If the column is missing in the table all columns after this column are of course also missing. 

### Data types

A data type defines the type of value which is expected in a column. Based on the data type additional schema properties are available:

#### string

Represents a text string. The following additional schema properties are available:

+ `format` : Specifies the expected format of a string value. Possible values are:
    + `none` : Any string value. This is the default value.
    + `uuid` : An uuid acccording to [rfc4122](https://datatracker.ietf.org/doc/html/rfc4122). 
    + `uri` : An uri acccording to [rfc3986](https://datatracker.ietf.org/doc/html/rfc3986).
    + `email` : An email address acccording to [rfc5322, section 3.4.1](https://datatracker.ietf.org/doc/html/rfc5322#section-3.4.1).
    + `base64` : A base64 encoded string acccording to [rfc4648](https://datatracker.ietf.org/doc/html/rfc4648). 
+ `minLength` : Specifies the minimum character length of a string value.
+ `maxLength` : Specifies the maximun character length of a string value.

#### int

Represents an integral number. The following additional schema properties are available:

+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### float

Represents a floating number. The following additional schema properties are available:

+ `formats` : An array of valid format string according to [.NET Custom numeric format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-numeric-format-strings). **This property is required**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### bool

Represents a boolean value. The following additional schema properties are available:

+ `trueValues` : An array of valid strings each representing a logical true value. **This property is required**.
+ `falseValues` : An array of valid strings each representing a logical false value. **This property is required**.

#### enum

Represents an enumeration value. The following additional schema properties are available:

+ `members` : An array of value objects each representing one enumeration value. **This property is required**.

#### enum-set

Represents an set of enumeration values formatted as csv string. The following additional schema properties are available:

+ `members` : An array of value objects each repesenting one enumeration value. **This property is required**.
+ `delimiterChar` : Defines the delimiter character of the CSV string format. Default value is `,`.
+ `quoteChar` : Defines the quote character of the CSV string format. Default value is `"`.

#### date-time

Represents a datetime value. The following additional schema properties are available:

+ `formats` : An array of valid format strings according to [.NET Custom date and time format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings). **This property is required**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### date

Represents a date only value. The following additional schema properties are available:

+ `formats` : An array of valid format strings according to [.NET Custom date and time format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings). **This property is required**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### time

Represents a time only value. The following additional schema properties are available:

+ `formats` : An array of valid format strings according to [.NET Custom date and time format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings). **This property is required**.
+ `minValue` : Specifies the minimum allowed value.
+ `maxValue` : Specifies the maximun allowed value.

#### json

Represents a json formatted string. The following additional schema properties are available:

+ `schema` : Specifies a [JSON Schema](https://json-schema.org/draft/2020-12/json-schema-core.html). **This property is required**.
    + `uri` : An uri to a valid JSON Schema. **This property is required**.

#### xml

Represents a xml formatted string. The following additional schema properties are available:

+ `schema` : Specifies a [XML Schema](https://www.w3.org/TR/xmlschema-0/). **This property is required**.
    + `uri` : An uri to a valid XML Schema. **This property is required**.

## What's missing?

A data schema has two purposes:

+ Semantical documentation of the data structure
+ The possibility to validate real data against the data schema.

The documentation part is done. What's still missing is a reference validator.

## Can I help?

Yes, that would be much appreciated. Simple CSV Schema is currently a draft. We are open for any feedback! Use the Issue Tracker for asking and/or submitting a Pull Request.

## Code of Conduct

The [STÜBER SYSTEMS Code of Conduct](https://www.stueber.co.uk/code-of-conduct.php) was adopted in this project.
