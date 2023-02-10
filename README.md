# enteropathway_api_tutorial

* [Enteropathway](https://enteropathway.org) is a manually curated human gut specific database for metabolic pathways. 

* Registration of a user account is needed to access Enteropathway by REST API.

* This tutorial is based on the [Enteropathway REST API](https://enteropathway.org/#/api).

## Requirements
* [jq](https://stedolan.github.io/jq/)

## How to use
### Authorization token

* The `username` and `password` can be obtained by registration of a user account on the [registration page](https://enteropathway.org/#/register).

* `user_name` and `pass_word` should be changed to their own username and password respectively.

* Obtained `TOKEN` can be confirmed by `echo $TOKEN`.

* `TOKEN` will be expired in <strong>one day</strong>.

  * If `TOKEN` will be expired, Try to get `TOKEN` again.

```bash
TOKEN=`curl -X POST -H "Accept: application/json" \
 --header "Content-Type: application/json" \
 --data '{"username":"user_name","password":"pass_word"}' \
 https://enteropathway.org/api/authenticate | \
 jq '.access_token' -r`
```

### Customization via uploading JSON format data

* Customization setting which is defined as the JSON format can be uploaded by REST API to customize metabolic pathways.

* The customization parameters are shown in this [link](#common-parameters-for-customization).

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
 -H "Content-Type: application/json" \
 --data \
 '{
  "mapping": {
    "customizations": [
      "EPR0001 #FF0000 W20 0.5",
      "EPR0002",
      "C07316 #FF0000 W15",
      "EPC0003 #FF0000 W15 0.5"
    ],
    "highlight_whole_module": false,
    "clear_original_colors": false,
    "module_box_highlight": "none"
  }
 }' \
 https://enteropathway.org/api/customization/mapping \
 --output mapping.pdf
 ```


### Customization via uploading file

* [Customization setting file](/customization.txt) can be uploaded by REST API to customize metabolic pathways.

* The customization parameters are shown in this [link](#common-parameters-for-customization).

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
 -F 'customization_file=@customization.txt' \
 -F 'highlight_whole_module=false' \
 -F 'clear_original_colors=true' \
 -F 'module_box_highlight=box-only' \
 https://enteropathway.org/api/customization/mapping/file \
 --output mapping.pdf
```

### Enrichment analysis via uploading JSON format data

* List of KO or reaction can be uploaded by REST API to perform enrichment analysis.

* The enrichment analysis parameters are shown in this [link](#common-parameters-for-enrichment-analysis).

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
 -H "Content-Type: application/json" \
 --data \
 '{
  "mapping": {
    "elements": [
      "EPR0001",
      "EPR0002"
    ],
    "color": "#FF0000",
    "input_type": "reactions",
    "p_value": 0.05,
    "clear_original_colors": false,
    "module_box_highlight": "none"
  }
 }' \
 https://enteropathway.org/api/customization/mapping/enrichment \
 --output enrichment.pdf
```

### Enrichment analysis via uploading file

* [KO list file](/ko_list.txt) or [Reaction list file](/reaction_list.txt) can be uploaded by REST API to perform enrichment analysis.

* The enrichment analysis parameters are shown in this [link](#common-parameters-for-enrichment-analysis).

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
 -F 'elements_file=@ko_list.txt' \
 -F 'color=#ff0000' \
 -F 'input_type=ko' \
 -F 'p_value=0.05' \
 -F 'clear_original_colors=true' \
 -F 'module_box_highlight=none' \
 https://enteropathway.org/api/customization/mapping/enrichment/file \
 --output enrichment.pdf
```

### Common parameters for customization
* `customization_file` : The input file name [string].
* `--output` : The output file name [string].
* `highlight_whole_module` : Highlifht all the module if at least one reaction is matched [boolean: true/false].
* `clear_original_colors` : Sets the default elements color to grey [boolean: true/false].
* `module_box_highlight` : How to highlight the module box of mateched modules [string: box/box-only/none].
  * `box` : highlights the module title box in addition to module reactions.
  * `box-only` : highlights the module title box only, and ignores module reactions.
  * `none` : do not highlight the box.

### Common parameters for enrichment analysis
* `elements_file` : The input file name [string].
* `--output` : The output file name [string].
* `input_type` : The type of the list [string: ko/reactions].
* `color` : Color of enriched modules [string].
* `p_value` : The cutoff value for p-value filtering [float number].
* `clear_original_colors` : Sets the default elements color to grey [boolean: true/false].
* `module_box_highlight` : How to highlight the module box of mateched modules [string: box/box-only/none].
  * `box` : highlights the module title box in addition to module reactions.
  * `box-only` : highlights the module title box only, and ignores module reactions.
  * `none` : do not highlight the box.
