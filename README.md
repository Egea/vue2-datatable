# @egeatech/vue2-datatable-component

This is a fork of Datatable for Vue.js 2.x of Ken Berkeley
`npm i @egeatech/vue2-datatable-component`

[Original repository](https://onewaytech.github.io/vue2-datatable) |  [Original documentation](https://OneWayTech.github.io/vue2-datatable/doc)

[npm-url]: https://www.npmjs.com/package/vue2-datatable-component


## Parameters (*) => new on this fork:

| Pro |Desc|Type|Optional values|Default value|Required|
|--- |--- |--- |--- |--- |--- |
|columns|Defination of columns|Array|-|-|Y|
|data|Data of the current page (rows)|Array|-|-|Y|
|total|Total number of the records|Number|-|-|Y|
|query|Query object|Object|-|-|Y|
|selection|Container for multi-select|Array|-|-|N|
|summary|Summary row|Object|-|-|N|
|xprops|Carrier for extra props passed to dynamic components|Object|-|-|N|
|HeaderSettings|Whether to render HeaderSettings|Boolean|true / false|false|N|
|Pagination|Whether to render pagination relevant|Boolean|true / false|true|N|
|pageSizeOptions|options for PageSizeSelect|Array|-|[10, 20, 40, 80, 100]|N|
|tbl-class|Classes for \<table>|String / Object / Array|-|-|N|
|tbl-style|Inline styles for \<table>|String / Object / Array|-|-|N|
|fixHeaderAndSetBodyMaxHeight|(Just as its name implies)|Number|-|-|N|
|support-backup|Whether to enable backup of HeaderSettings|Boolean|true / false|false|N|
|support-nested|Whether to enable nested components feature (accordion mode is available)|Boolean / String|true / false / 'accordion'|false|N|
|`(*)` loading|Display loading slot in table body|Boolean|-|false|N|
|`(*)` enableClickableRows|Enable the on-row-click event|Boolean|-|true|N|
|`(*)` enableSearchRow|Add a row for filtering the results|Boolean|-|false|N|


## Column Options (*) => new on this fork:

|Attr|Desc|Type|Optional values|Default value|Required|
|--- |--- |--- |--- |--- |--- |
|title|Displayed title|String|-|-|N|
|label|Label in HeaderSettings (title will take its place if not set)|String|-|-|N|
|field|Field name of the row|String|-|-|N|
|explain|Explanation of the field (tooltip)|String|-|-|N|
|sortable|Is sortable|Boolean|true / false|false|N|
|visible|Is visible|Boolean / String (if the type is String, the visibility is not allowed to toggle)|true / false / 'true' / 'false'|true|N|
|fixed|Is fixed|Boolean / String|true / false / 'left' / 'right'|-|N|
|group|Group name|String|-|-|N|
|colClass|Classes for the whole column|String / Object|-|-|N|
|colStyle|Inline styles for the whole column|Object|-|-|N|
|thClass|Classes for \<th>|String|-|-|N|
|thStyle|Inline styles for \<th>|Object|-|-|N|
|thComp|dynamic component for \<th>|String / Object|-|-|N|
|tdClass|Classes for \<td>|String|-|-|N|
|tdStyle|Inline styles for \<td>|Object|-|-|N|
|tdComp|dynamic component for \<td>|String / Object|-|-|N|
|`(*)` fireRowClick|fire the on-row-click event if the cell is clicked|Boolean|-|true|N|
|`(*)` searchable|enable the filtering input (enableSearchRow has to be set true)|Boolean|-|false|N|
|`(*)` searchField|fire the search and order queries with another string as field|String|-|-|N|
|`(*)` searchOptions|use a dropdown as search mode with a set of values `{label: '' value: ''}`, the label will be displayed, the value will be used for the search|Array|-|-|N|


## New component slots:

* **spinner slot**: used to insert your own custom spinner (to use with the loading parameter above)

    `<template v-slot:spinner> Loading... </template>`
* **scoped-slots** named as the table fields: you can access the cell/row information and override the cell content
    * *data*: for the cell field
    * *row*: for the row object
    
    `<template v-slot:surname="col"><span style="color: darkred"><i>{{ col.data }}</i></span></template>`


## Other personalization:
* fields can be written as object paths (like 'user.house.address')
* small style and translation adjustments
* on-row-click event (bound to cells, cells can be excluded using fireRowClick option)

