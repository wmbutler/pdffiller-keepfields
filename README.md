PDF Filler (Node.js)
======
[![NPM](https://nodei.co/npm/pdffiller-keepfields.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/pdffiller-keepfields/)

A node.js PDF form field data filler and FDF generator toolkit. This essentially is a wrapper around the PDF Toolkit library <a target="_blank" href="http://www.pdflabs.com/tools/pdftk-the-pdf-toolkit/">PDF ToolKit</a>.

NOTE: pdffiller failed to keep data if it was already included on a form. I added some logic to retain this data if it exists and released pdffiller-keepfields as a substitute.

Quick start
-----------

```bash
npm install pdffiller-keepfields --save
```

Import the module using:

```js
var pdfFiller = require('pdffiller-keepfields');

// ...
```


## Examples

#### Fill PDF with existing FDF Data
```javascript
var pdfFiller   = require('pdffiller-keepfields');

var sourcePDF = "test/test.pdf";
var destinationPDF =  "test/test_complete.pdf";
var data = {
    "last_name" : "John",
    "first_name" : "Doe",
    "date" : "Jan 1, 2013",
    "football" : "Off",
    "baseball" : "Yes",
    "basketball" : "Off",
    "hockey" : "Yes",
    "nascar" : "Off"
};

pdfFiller.fillForm( sourcePDF, destinationPDF, data, function(err) {
    if (err) throw err;
    console.log("In callback (we're done).");
});

```

This will take the test.pdf, fill the fields with the data values
and create a complete filled in PDF (test_filled_in.pdf). Note that the
resulting PDF will be read-only.

Alternatively,

```javascript

var shouldFlatten = false;

pdfFiller.fillFormWithFlatten( sourcePDF, destinationPDF, data, shouldFlatten, function(err) {
    if (err) throw err;
    console.log("In callback (we're done).");
})
```

Calling
`fillFormWithFlatten()` with `shouldFlatten = false` will leave any unmapped fields
still editable, as per the `pdftk` command specification.


#### Generate FDF Template from PDF
```javascript
var pdfFiller   = require('pdffiller-keepfields');

var sourcePDF = "test/test.pdf";

// Override the default field name regex. Default: /FieldName: ([^\n]*)/
var nameRegex = null;  

var FDF_data = pdfFiller.generateFDFTemplate( sourcePDF, nameRegex, function(err, fdfData) {
    if (err) throw err;
    console.log(fdfData);
});

```

This will print out this
```json
{
    "last_name" : "",
    "first_name" : "",
    "date" : "",
    "football" : "",
    "baseball" : "",
    "basketball" : "",
    "hockey" : "",
    "nascar" : ""
};
```

#### Map form fields to PDF fields
```javascript
var pdfFiller = require('pdffiller-keepfields');

var convMap = {
    "lastName": "last_name",
    "firstName": "first_name",
    "Date": "date",
    "footballField": "football",
    "baseballField": "baseball",
    "bballField": "basketball",
    "hockeyField": "hockey",
    "nascarField": "nascar"
};

var fieldJson = [
    {
        "title" : "lastName",
        "fieldfieldType": "Text",
        "fieldValue": "John"
    },
    {
        "title" : "firstName",
        "fieldfieldType": "Text",
        "fieldValue": "Doe"
    },
    {
        "title" : "Date",
        "fieldType": "Text",
        "fieldValue": "Jan 1, 2013"
    },
    {
        "title" : "footballField",
        "fieldType": "Button",
        "fieldValue": false
    },
    {
        "title" : "baseballField",
        "fieldType": "Button",
        "fieldValue": true
    },
    {
        "title" : "bballField",
        "fieldType": "Button",
        "fieldValue": false
    },
    {
        "title" : "hockeyField",
        "fieldType": "Button",
        "fieldValue": true
    },
    {
        "title" : "nascarField",
        "fieldType": "Button",
        "fieldValue": false
    }
];

var mappedFields = pdfFiller.mapForm2PDF( fieldJson, convMap );
console.log(mappedFields);
```

This will print out the object below.
```json
{
    "last_name" : "John",
    "first_name" : "Doe",
    "date" : "Jan 1, 2013",
    "football" : "Off",
    "baseball" : "Yes",
    "basketball" : "Off",
    "hockey" : "Yes",
    "nascar" : "Off"
};
```

#### Convert fieldJson to FDF data
```javascript
var pdfFiller   = require('pdffiller-keepfields');

var fieldJson = [
    {
        "title" : "last_name",
        "fieldfieldType": "Text",
        "fieldValue": "Doe"
    },
    {
        "title" : "first_name",
        "fieldfieldType": "Text",
        "fieldValue": "John"
    },
    {
        "title" : "date",
        "fieldType": "Text",
        "fieldValue": "Jan 1, 2013"
    },
    {
        "title" : "football",
        "fieldType": "Button",
        "fieldValue": false
    },
    {
        "title" : "baseball",
        "fieldType": "Button",
        "fieldValue": true
    },
    {
        "title" : "basketball",
        "fieldType": "Button"
        "fieldValue": false
    },
    {
        "title" : "hockey",
        "fieldType": "Button"
        "fieldValue": true
    },
    {
        "title" : "nascar",
        "fieldType": "Button"
        "fieldValue": false
    }
];

var FDFData = pdfFiller.convFieldJson2FDF( fieldJson );
console.log(FDFData)
```

This will print out this
```json
{
    "last_name" : "John",
    "first_name" : "Doe",
    "date" : "Jan 1, 2013",
    "football" : "Off",
    "baseball" : "Yes",
    "basketball" : "Off",
    "hockey" : "Yes",
    "nascar" : "Off"
};
```
