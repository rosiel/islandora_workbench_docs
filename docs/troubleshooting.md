
!!! note
    If you are encountering problems not described here, please [open an issue](https://github.com/mjordan/islandora_workbench/issues) and help improve Islandora Workbench!

## Workbench is failing to ingest some nodes and is leaving messages in the log mentioning HTTP response code 422.

This is probably caused by unexpected data in your CSV file that Workbench's `--check` validation is not finding. If you encounter these messages, please [open an issue](https://github.com/mjordan/islandora_workbench/issues) and share any relevant entries in your Drupal log so we can track down the problem.

One of the most common causes of this error is that one or more of the vocabularies being poplated in your CSV contain required fields other than the defult term name, someting which Islandora Workbench [cannot currently handle](https://github.com/mjordan/islandora_workbench/issues/111). While it is possible to have Workbench create these fields, the additional fields will need to be included in the input CSV. If you have an opinion on how that can be best done (while keeping the CSV relatively human-readable), please leave a comment on the linked Github issue.

## --check is telling me that one the rows in my CSV file has more columns than headers

The most likely problem is that one of your CSV values contains a comma but is not wrapped in double quotes. 

## The text in my CSV does not match how it looks when I view it in Drupal

If a field is configured in Drupal to use [text filters](https://www.drupal.org/node/213156), the HTML that is displayed to the user may not be exactly the same as the content of the node add/edit form field. If you check the node add/edit form, the content of the field should match the content of the CSV field. If it does, it is likely that Drupal is apply a text filter.

## My Islandora uses a custom media type and I need to tell Workbench what file field to use.

If you need to create a media that is not one of the standard Islandora types (Image, File, Digital Document, Video, Audio, Extracted Text, or FITS Technical metadata), you will need to include the  `media_file_fields` setting in your config file, like this:


```
media_file_fields:
 - mycustommedia_machine_name: field_custom_file
 - myothercustommedia_machine_name: field_other_custom_file
```

This configuration setting adds entries to the following default mapping of media types to file field names:

```
'file': 'field_media_file',
'document': 'field_media_document',
'image': 'field_media_image',
'audio': 'field_media_audio_file',
'video': 'field_media_video_file',
'extracted_text': 'field_media_file',
'fits_technical_metadata': 'field_media_file'
```
