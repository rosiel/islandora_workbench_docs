## The configuration file

Workbench uses a YAML configuration whose location is indicated in the `--config` argument. This file defines the various options it will use to create, update, or delete Islandora content (such as which CSV file to use and what directory your images are in). The simplest configuration file needs only the following four options:

```yaml
task: create
host: "http://localhost:8000"
username: admin
password: islandora
```

In this example, the `task` being performed is creating nodes (and optionally media). Other tasks are `create_from_files`, `update`, `delete`, `add_media`, and `delete_media`. Some of the configuration settings documented below are used in all tasks, while others are only used in specific tasks.

## Configuration settings

The settings defined in a configuration file are documented below, grouped into broad funcational categories for easier reference. The order of the options in the configuration file doesn't matter, and settings do not need to be grouped together in any specific way in the configuration file.

### Required settings

| Setting | Required | Default value | Description |
| --- | --- | --- | --- |
| task | ✔️ | | One of 'create', 'create_from_files', update', delete', 'add_media', or 'delete_media' |
| host | ✔️ | | The hostname, including `http://` or `https://` of your Islandora repository, and port number if not the default 80. This value must be wrapped in quotation marks. |
| username |  ✔️ | | The username used to authenticate the requests. This Drupal user should be a member of the "Administrator" role. If you want to create nodes that are owned by a specific Drupal user, include their numeric user ID in the `uid` column in your CSV. |
| password |  ✔️ | | The user's password. |

### Drupal settings

| Setting | Required | Default value | Description |
| --- | --- | --- | --- |
| content_type |  | islandora_object | The machine name of the Drupal node content type you are creating or updating. |
| drupal_filesystem | | fedora:// | One of 'fedora://', 'public://', or 'private://'. Only used with Drupal 8.x - 9.1; starting with Drupal 9.2, the filesystem is automatically detected from the media's configuration. |
| allow_adding_terms |  | false | Determines if Workbench will add taxonomy terms if they do not exist in the target vocabulary. See more information in the "[Taxonomy reference fields](/fields/#field-types)" section. |
| published | | true | Whether nodes are published or not. Applies to `create` task only. Set to false if you want the nodes to be unpublished. Note that whether or not a node is published can also be set at a node level in the CSV file in the status base field, as described in the "Base Fields" section. Values in the CSV override the value of published set here. |
| validate_title_length |  | true | Whether or not to check if title values in the CSV exceed Drupal's maximum allowed length of 255 characters. Defaults to true. Set to false if you are using a module that lets you override Drupal's maximum title length, such as [Node Title Length](https://www.drupal.org/project/title_length) or [Entity Title Length](https://www.drupal.org/project/entity_title_length). Also, if your task is `update`, you should set this to false if `title` is not one of the fields you are updating. |
| list_missing_drupal_fields |  | false | Set to `true` to tell Workbench to provide a list of fields that exist in your input CSV but that cannot be matched to Drupal field names (or reserved column names such as "file"). If `false`, Workbench will still check for CSV column headers that it can't match to Drupal fields, but will exit upon finding the first such field. This option produces a list of fields instead of exiting on detecting the first field.|

### Input data location settings

| Setting | Required | Default value | Description |
| --- | --- | --- | --- |
| input_dir |  | input_data | The full or relative path to the directory containing the files and metadata CSV file. |
| input_csv |  | metadata.csv | Path to the CSV metadata file. Can be absolute, or if just the filename is provided, will be assumed to be in the directory named in `input_dir`. Can also be the URL to a Google spreadsheet (see the "[Using Google Sheets as input data](/preparing_data/#using-a-google-sheet-as-the-input-csv-file)" section for more information). |
| google_sheets_csv_filename |  | google_sheet.csv | Local CSV filename for data from a Google spreadsheet. See the "[Using Google Sheets as input data](/preparing_data/#using-a-google-sheet-as-the-input-csv-file)" section for more information. |
| google_sheets_gid |  | 0 | The "gid" of the worksheet to use in a Google Sheet. See  "[Using Google Sheets as input data](/preparing_data/#using-a-google-sheet-as-the-input-csv-file)" section for more information. |
| excel_worksheet |  | Sheet1 | If using an Excel file as your input CSV file, the name of the worksheet that the CSV data will be extracted from. |

### Output CSV settings

| Setting | Required | Default value | Description |
| --- | --- | --- | --- |
| output_csv | | | The full or relative path to a CSV file with one record per node created by Workbench. See "[The output CSV file](/output_csv/)" section for more information. |

### CSV input file settings

| Setting | Required | Default value | Description |
| --- | --- | --- | --- |
| id_field |  | id | The name of the field in the CSV that uniquely identifies each record. |
| delimiter |  | , [comma]| The delimiter used in the CSV file, for example, "," or "\t". If omitted, defaults to ",". |
| subdelimiter |  | &#124; [pipe]| The subdelimiter used in the CSV file to define multiple values in one field. If omitted, defaults to "&#124;". |
| csv_field_templates |  |  | Used in the `create` and `update` tasks only. A list of Drupal field machine names and corresponding values that are copied into the CSV input file. More detail provided in the "[CSV field templates](/field_templates/)" section.|
| ignore_csv_columns |  |  | Used in the `create` and `update` tasks only. A list of CSV column headers that Workbench should ignore.|

### Media settings

| Setting | Required | Default value | Description |
| --- | --- | --- | --- |
| nodes_only |  | false | Include this option in `create` tasks, set to `true`, if you want to only create nodes and not their accompanying media. See the "Creating nodes but not media" section for more information. 'fedora://'. |
| allow_missing_files |  | false | Determines if empty `file` values are allowed. If set to true, empty file values are allowed and will result in nodes without attached media. Defaults to false (which means all file values must contain the name of a file that exists in the `input_data` directory). |
| media_use_tid | | `http://pcdm.org/use#OriginalFile`  | The term ID for the term from the "Islandora Media Use" vocabulary you want to apply to the media being created. You can provide a term URI instead of a term ID, for example `"http://pcdm.org/use#OriginalFile"`. You can specify multiple values for this setting by joining them with the subdelimiter configured in the `subdelimiter` setting; for example, `media_use_tid: 17|18`. You can also set this at the object level by including `media_use_tid` in your CSV file; values there will override the value set in your configuration file. |
| media_type [singular] |  | | Overrides, for all media being created, Workbench's default definition of whether the media being created is an image, file, document, audio, or video. Used in the `create`, `add_media`, and `create_from_files` tasks. More detail provided in the "[Setting Media Types]
| media_types [plural] |  | | Overrides default media type definitions on a per file extension basis. Used in the `create`, `add_media`, and `create_from_files` tasks. More detail provided in the "Setting Media Types" section. |
| delete_media_with_nodes |  | true | When a node is deleted using a `delete` task, by default, all if its media are automatically deleted. Set this option to false to not delete all of a node's media (you do not generally want to keep the media without the node). |
| use_node_title_for_media |  | false | For remote files, if set to `true`, name the downloaded copy using the parent node's title value. If `false`, downloaded file's name will depend on the application/website that is serving up the file. |
| delete_tmp_upload |  | false | For remote files, if set to `true`, remote file is deleted after it is used to create media. If `false`, downloaded file will remain in your `input_dir`. |
| media_file_fields |  | | Defines the name of the field that refers to the file in an Islandora media. For more information, see this [Troubleshooting entry](/troubleshooting/#my-islandora-uses-a-custom-media-type-and-i-need-to-tell-workbench-what-file-field-to-use).|

### Islandora model settings

| Setting | Required | Default value | Description |
| --- | --- | --- | --- |
| model [singular]|  |  | Used in the `create_from_files` task only. Defines the term ID from the the "Islandora Models" vocabulary for all nodes created using this task. Note: one of `model` or `models` is required. More detail provided in the "[Creating nodes from files](/creating_nodes_from_files/)" section.|
| models [plural] |  |  | Used in the `create_from_files` task only. Provides a mapping bewteen file extensions and terms in the "Islandora Models" vocabulary. Note: one of `model` or `models` is required. More detail provided in the [Creating nodes from files](/creating_nodes_from_files/)" section.|

### Paged and compound content settings

See the section "[Creating paged content](/paged_and_compound/)" for more information.

| Setting | Required | Default value | Description |
| --- | --- | --- | --- |
| paged_content_from_directories |  | false | Set to true if you are using the "[Using subdirectories](/paged_and_compound/#using-subdirectories)" method of creating paged content. |
| paged_content_sequence_seprator |  | - [hyphen]| The character used to separate the page sequence number from the rest of the filename. Used when creating paged content with the "[Using subdirectories](/paged_and_compound/#using-subdirectories)" method. |
| paged_content_page_model_tid |  | | Required if `paged_content_from_directories` is true. The the term ID from the Islandora Models taxonomy to assign to pages. |
| paged_content_page_display_hints |  | | The term ID from the Islandora Display taxonomy to assign to pages. If not included, defaults to the value of the `field_display_hints` in the parent's record in the CSV file. |
| paged_content_page_content_type |  | | Set to the machine name of the Drupal node content type for pages created using the "[Using subdirectories](/paged_and_compound/#using-subdirectories)" method if it is different than the content type of the parent (which is specified in the content_type setting). |
| secondary_tasks |  | | A list of configuration file names that are executed as secondary tasks after the primary task to create compound objects. See "[Using a secondary task](/paged_and_compound/#using-a-secondary-task)" for more information. |

### Logging settings

See the "[Logging](/logging/)" section for more information.

| Setting | Required | Default value | Description |
| --- | --- | --- | --- |
| log_file_path | | workbench.log | The path to the log file, absolute or relative to `workbench`. |
| log_file_mode | | a [append] | Set to "w" to overwrite the log file, if it exists. |
| log_json |  | false | Whether or not to log the raw JSON POSTed, PUT, or PATCHed to Drupal. Useful for debugging. |


### Miscellaneous settings

| Setting | Required | Default value | Description |
| --- | --- | --- | --- |
| pause |  | | Defines the number of seconds to pause between each REST request to Drupal. Include it in your configuration to lessen the impact of Islandora Workbench on your site during large jobs, for example pause: 1.5. |
| user_agent |  | Islandora Workbench | String to use as the User-Agent header in HTTP requests. |
| allow_redirects |  | true | Whether or not to allow Islandora Workbench to respond to HTTP redirects. |
| bootstrap |  | | Absolute path to one or more scripts that execute prior to Workbench connecting to Drupal. Scripts can be in any language, and need to be executable. For an example of using this feature to run a script that generates sample Islandora content, see the "[Generating sample Islandora content](/generating_sample_content/)" section. |
| progress_bar |  | false | Show a progress bar when running Workbench instead of row-by-row output. |
| drupal_8 |  | false | Tells Workbench that it is communicating with a Drupal version lower than 9.2. Intended to be used in integration tests only. |


When you run Islandora Workbench with the `--check` argument, it will verify that all configuration options required for the current task are present, and if they aren't tell you so.

!!! note
    Islandora Workbench automatically converts any configuration keys to lowercase, e.g., `Task` is equivalent to `task`.

## Validating the syntax of the configuration file

When you run Workbench, it confirms that your configuration file is valid YAML. This is a syntax check only, not a content check. If the file is valid YAML, Workbench then goes on to perform a long list of application-specific [checks](/check).

If this syntax check fails, some detail about the problem will be displayed to the user. The same information plus the entire Python stack trace is also logged to a file named "workbench.log" in the Islandora Workbench directory. This file name is Workbench's default log file name, but in this case (validating the config file's YAML syntax), that file name is used regardless of the log file location defined in the configuation's `log_file_path` option. The reason the error is logged in the default location instead of the value in the configuration file (if one is present) is that the configuration file isn't valid YAML and therefore can't be parsed.

## Example configuration files

These examples provide inline annotations explaining why the settings are included in the configuration file. Spaces are included for readability.

### Create nodes only, no media

```yaml
task: create
host: "http://localhost:8000"
username: admin
password: islandora

# This setting tells Workbench to create nodes with no media.
# Also, this tells --check to skip all validation of "file" locations.
# Other media settings, like "media_use_tid", are also ignored.
nodes_only: true
```

### Use a custom log file location

```yaml
task: create
host: "http://localhost:8000"
username: admin
password: islandora

# This setting tells Workbench to write its log file to the location specified
# instead of the default "workbench.log" within the "islandora_workbench" directory.
log_file_path: /home/mark/workbench_log.txt
```

### Include some CSV field templates

```yaml
task: create
host: "http://localhost:8000"
username: admin
password: islandora

# The values in this list of field templates are applied to every row in the
# input CSV file before the CSV file is used to populate Drupal fields. The
# field templates are also applied during the "--check" in order to validate
# the values of the fields.
csv_field_templates:
 - field_member_of: 205
 - field_model: 25
```

### Create nodes and media from files (no input CSV file)

```
task: create_from_files
host: "http://localhost:8000"
username: admin
password: islandora

# The files to create the nodes from are in this directory.
input_dir: /tmp/sample_files

# This tells Workbench to write a CSV file containing node IDs of the
# created nodes, plus the field names used in the target content type
# ("islandora_object" by default).
output_csv: /tmp/sample_files.csv

# All nodes should get the "Model" value corresponding to this URI.
model: 'https://schema.org/DigitalDocument'
```
