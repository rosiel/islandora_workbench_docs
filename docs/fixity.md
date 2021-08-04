## Transmission fixity

Islandora Workbench supports transmission fixity validation, which means it can detect if files are not ingested intact, in other words, that the files became corrupted during ingest. It does this by generating a checksum (a.k.a. "hash") for each file before it is ingested, and then after the file is ingested, Workbench asks Drupal for a checksum on the file using the same hash algorithm. If the two checksums are identical, the file was not corrupted during the ingest process. If they are not identical, the file became corrupted.

This functionality is available within `create` and `add_media` tasks. Only files named in the `file` CSV column are checked.

To enable this feature, include the `fixity_algorithm` option in your `create` or `add_media` configuation file, specifying one of "md5", "sha1", or "sha256" hash algorithms. For example, to use the "md5" algorithm, include the following in your config file:

```yaml
fixity_algorithm: md5
```

## Comparing checksums to known values

This comparison can be done both during the transmissin fixity check and during the `--check` phase, as described below.

If you want Workbench to optionally compare the hash it generates for a file to a known checksum value (for example, one generated by a platform you are migrating from), include a `checksum` column in your CSV file for `create` or `add_media` tasks. No further configuration other than indicating the `fixity_algorithm` is necessary. If that column is present, Workbench will compare the hash it generates with the value in that column and report matches and mismatches. Note that the checksum in your CSV must have been generated using the same algorithm specified in your `fixity_algorithm` configuration setting.

## Validating checksums during --check

If you have pregenerated checksum values for your files (as described in the "Comparing checksums to known values" section, above), you can tell Workbench to compare those checksums with checksums during its `--check` phase. To do this, include the following options in your `create` or `add_media` configuration file:

```yaml
fixity_algorithm: md5
validate_fixity_during_check: true
```

You must also include both a `file` and `checksum` column in your input CSV, and ensure that the checksums in the CSV column were generated using the algorithm named in the `fixity_algorithm` setting. Results of the checks are written to the log file.

Some things to note:

* Workbench will log the outcome of all checksum comparisions, whether they are successful or mismatches.
* If there is a mismatch, Workbench will continue to ingest the file and create the accompanying media. For this reason, it is prudent to perform your checksum validation during the `--check` phase, in case any comparisons fail.
* Validation during `--check` happens entirely on the computer running Workbench. It does not contact Drupal for checksum information (since the files haven't yet been ingested into Islandora at that point).
* Fixity checking slows Workbench (and also Drupal if you perform transmission fixity checks) down to a certain extent, especially when files are large. This is unavoidable since calculating a file's checksum requires reading it into memory.