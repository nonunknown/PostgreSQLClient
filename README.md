# PostgreSQLClient for Godot Engine (GDscript)

<img src="./GodotPostgresql.svg">
Godot PostgreSQL Client is a GDscript script / class that allows you to connect to a Postgres backend and run SQL commands there. It is able to send data and receive it from the backend. Useful for managing player user data on a multiplayer game, by saving a large amount of data on a dedicated Postgres server from GDscript.

The class is written in pure GDScript which allows it not to depend on GDNative. This makes it ultra portable for many platforms. You can see a taste of using the Postgresql connector for Godot in the "Helloworld.gd" file.

Currently, the script is not stable and lacks features. So it is not recommended to use it in production at this time however you can test it.

INSTALLATION PROCEDURE:
=======================
It is assumed that you have installed the latest version of PostgreSQL and that you have created a database. Download the file "PostgreSQLClient.gd" then include it in your Godot project folders ("res: //"). The PostgreSQLClient class should now be accessible from any GDscript script. Otherwise, set the script to "Auto load" in your project settings.

PostgreSQLClient DOCUMENTATION (NOT FINALIZED):
====================================

**PROPERTIES:**

| Type | Properties | Default value |
| --- | --- | --- |
| `float` | PROTOCOL_VERSION *const* | 3.0 |
| `dictionary` | parameter_status *reader alone* | {} |

**METHODS:**

| Type | Method |
| --- | --- |
| `Error` | connect_to_host(url: String, connect_timeout: int = 30) |
| `Array` | execute(sql: String) |
| `void` | rollback(process_id: int, process_key: int) |
| `void` | close(clean_closure: bool = true) |
| `void` | set_ssl_connection() |

**SIGNALS:**

| Signal |
| --- |
| connection_closed(was_clean_close: bool) |
| connection_error() |
| connection_established() |

---

**ENUMARATIONS:**

`enum` DataTypePostgreSQL

- BOOLEAN = 16 --- Postgresql data type of type `boolean`.
- SMALLINT = 21, --- Postgresql data type of type `smallint`.
- INTEGER = 23, --- Postgresql data type of type `integer`.
- BIGINT = 20, --- Postgresql data type of type `bigint`.
- REAL = 700, --- Postgresql data type of type `real`.
- DOUBLE_PRECISION = 701, --- Postgresql data type of type `double precision`.
- TEXT = 25, --- Postgresql data type of type `text`.
- JSON = 114, --- Postgresql data type of type `json`.
-	JSONB = 3802, --- Postgresql data type of type `jsonb`.
-	BITEA = 17, --- Postgresql data type of type `bitea`.
-	POINT = 600, --- Postgresql data type of type `point`.
-	BOX = 603, --- Postgresql data type of type `box`.
-	LSEG = 601, --- Postgresql data type of type `lseg`.
-	LINE = 628, --- Postgresql data type of type `line`.
-	CIRCLE = 718 --- Postgresql data type of type `circle`.

NOTE: Not all PostgreSQL data types are supported by PostgreSQLClient, but will be in a future release with a PostgreSQL to native GDscript type conversion table in the documentation.
This enumeration will be useful in the next version of the Client with the arrival of the PostgreSQLQueryResult object.

--

**Property Descriptions**
- `float`  PROTOCOL_VERSION *const*

Default value: `3.0`

Version number (minor.major) of the PostgreSQL protocol used when connecting to the backend

---

- `dictionary` parameter_status *reader alone*

Default value: {}

A dictionary that contains various information about the execution state of the server. For security reasons the dictionary is always empty if the frontend is disconnected from the backend and updates once the connection is established.

Noted that the server is free to return whatever value it wants. Always remember to check the presence of the key before accessing the associated value.

Example of a typical value that a backend might return. Values may differ depending on the backend:
```
{"DateStyle":"ISO, DMY", "IntervalStyle":"postgres", "TimeZone":"Europe/Paris", "application_name":"", "client_encoding":"UTF8", "integer_datetimes":"on", "is_superuser":"off", "server_encoding":"UTF8", "server_version":"12.7 (Ubuntu 12.7-0ubuntu0.20.04.1)", "session_authorization":"samuel", "standard_conforming_strings":"on"}
```

---

**Method Descriptions**
- `Error`  connect_to_host(url: String, connect_timeout: int = 30)

Allows you to connect to a Postgresql backend at the specified `url`.

The url parameter is a PostgreSQL url ideally in the form "postgresql://user:password@host:port/databasename".
All other PostgreSQL url syntaxes specified in this page [https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING] are not yet fully supported.

Noted that the default port for PostgreSQL is `5432`.

---

- `Array`  execute(sql: String)

Allows to send an SQL string to the backend that should run.
The `sql` parameter can contain one or more valid SQL statements. Returns an `Array` containing the result of the query (can be empty). The return value will be subject to change in the next version of the PostgreSQL client which will return an array of PostgreSQLQueryResult that will contain much more information about the result of the query.

---

- `void`  rollback(process_id : int, process_key : int)

Do not use because it is too unstable, will be subject to modification in future versions.

---

- `void`  close(clean_closure: bool = true)

Allows you to close the connection with the backend. If clean_closure is `true`, the frontend will notify the backend that it requests to close the connection. If `false`, the frontend forcibly closes the connection without notifying the backend (not recommended sof in exceptional cases).

Has no effect if the frontend is not already connected to the backend.

---

- `void`  set_ssl_connection()

Not working at the moment, don't use it. will be subject to change in future versions.

---

**Signal Descriptions**
- connection_closed(was_clean_close: bool)

Fires when the connection to the backend closes.
`was_clean_close` is `true` if the connection was closed correctly otherwise `false`.

---

- connection_error()

Do not listen to this signal, does not work. will be subject to change see removed in future versions.

---

- connection_established()

Trigger when the connection between the frontend and the backend is established. This is usually a good time to start making requests to the backend with `execute ()`.

---

DOCUMENTATION PostgreSQLQueryResult (NON FINALISÉE):
====================================================
**Descriptions**

The `PostgreSQLQueryResult` class is a subclass of `PostgreSQLClient` which is not intended to be created manually. It represents the result of an SQL query and provides an information and method report
 to use the result of the query. It is usually returned by the `PostgreSQLClient.execute()` method in an array of `PostgreSQLQueryResult`. This class is not currently used but will be in future versions.

**PROPERTIES:**

| Type | Properties | Default value |
| --- | --- | --- |
| `int` | number_of_fields_in_a_row *const* | 0 |
| `Array` | row_description *const* | \[\] |
| `Array` | data_row *const* | \[\] |
| `String` | command_tag *const* | \"\" |

**METHODS:**

| Type | Method |
| --- | --- |
| `Array` | get_field_value(field_name: String) |

---

**Property Descriptions**
`int`  number_of_fields_in_a_row *const*

Default value: `0`

Specifies the number of fields in a row (can be zero).

---

`Array`  row_description *const*

Default value: `[]`

An array that contains dictionaries. these dictionaries represent the description of the rows where the query was executed. The number of dictionary depends on the number of fields resulting from the result of the query which was executed.

Each dictionary is structured like this:

```
{
	"field_name": field_name,
	"table_object_id": table_object_id,
	"column_index": column_index,
	"type_object_id": type_object_id,
	"data_type_size": data_type_size,
	"type_modifier": type_modifier,
	"format_code": format_code
}
```

- Where the `field_name` value is a string that represents the name of the field.
- Where the value `table object_id` is an `int` which represents the identifier of the table object whether the field can be identified as a column from a specific table; otherwise 0.
- Where the `column_index` value is an `int` which represents the attribute number of the column if the field can be identified as a column from a specific table; otherwise zero.
- Where the `type_object_id` value is an `PostgreSQLClient.DataTypePostgreSQL` which represents the object ID of the data type of the field.
- Where the `data_type_size` value is an` int` which represents the size of the data type. Note: that negative values indicate variable width types.
- Where the `type_modifier` value is an` int` which represents the type modifier. Note: The meaning of the modifier is type specific.
- Where the `format_code` value is an` int` which represents the format code used for the field. Currently will be `0` (text) or` 1` (binary). In a RowDescription returned by the instruction variant of Describe, the format code is not yet known and will always be zero. This value is low-level PostgreSQL protocol information that is not useful in most cases. You can ignore this value.

---

`Array`  data_row *const*

Default value: `[]`

An `Array` that contains sub-arrays. these sub-arrays represent for most of the queries the rows of the table where the query was executed. The number of sub-tables depends on the query that has been made. These sub-arrays contain as many elements as `number_of_fields_in_a_row`. These elements are native GDscript types that represent the data resulting from the query.

Example return value:

```
[[1, "Hello my id is 1"], [2, "Hello my id is 2"], [3, "Hello my id is 3"]]
```
There are 3 rows.
Each row contains the value of 2 fields: The row identifier and a character string.

---

`String`  command_tag *const*

Default value: `""`

This is usually a single word that identifies which SQL command was completed.

---

**Method Descriptions**

`Array` get_field_value(field_name: String)

Function that returns all the values of a field.
`field_name` is the name of the field on which we get the values. Can be empty if the field name is unknown. The `field_name` parameter is case sensitive.

Contacts:
=======================
- Discord: Kuwazy#8194
- email: zenpolcorporation@gmail.com
