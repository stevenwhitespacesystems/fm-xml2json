<!-- PROJECT LOGO -->
<br />
<p align="center">
  <a href="https://github.com/stevenwhitespacesystems/fm-xml2json">
    <img src="logo-alt.png" alt="Logo" width="140" height="104">
  </a>

  <h3 align="center">fm-xml2json</h3>

  <p align="center">
    An XML Parser that converts XML into JSON on the FileMaker Pro Platform.
    <br />
    <a href="https://github.com/stevenwhitespacesystems/fm-xml2json"><strong>Explore the docs »</strong></a>
    <br />
    <br />
    <a href="https://github.com/stevenwhitespacesystems/fm-xml2json#demo">View Demo</a>
    ·
    <a href="https://github.com/stevenwhitespacesystems/fm-xml2json/issues">Report Bug</a>
    ·
    <a href="https://github.com/stevenwhitespacesystems/fm-xml2json/issues">Request Feature</a>
  </p>
</p>

<!-- PROJECT SHIELDS -->
[![FileMaker Version][filemaker-shield]]()
[![FileMaker Platform][platform-shield]]()
[![Commit Shield][commit-shield]]()
[![Contributors][contributors-shield]]()
[![MIT License][license-shield]][license-url]
[![Facebook][facebook-shield]][facebook-url]
[![LinkedId][linkedin-shield]][linkedin-url]

<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About the Project](#about-the-project)
  * [Built With](#built-with)
* [Features](#features)
* [Getting Started](#getting-started)
  * [Prerequisites](#prerequisites)
    * [Version](#version)
    * [Custom Functions](#custom-functions)
    * [Limitations](#limitations)
* [Usage](#usage)
  * [Installation](#installation)
  * [Quick Start](#quick-start)
  * [Sample Conversions](#sample-conversions)
  * [Parameters](#parameters)
* [Demo](#demo)
* [Contributing](#contributing)
* [License](#license)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)
* [fmapi Product Suite](#fmapi-product-suite)
  * [fmapi Apps](#fmapi-apps)

<!-- ABOUT THE PROJECT -->
## About The Project

<p align="center">
  <a href="https://github.com/stevenwhitespacesystems/fm-xml2json">
    <img src="logo.png" alt="Logo" width="580" height="208">
  </a>
</p>

As we delved into the world of REST APIs using FileMaker, we stumbled across an issue.

*How do we handle XML responses?*

When the REST response is a JSON object, we don't have any issue and can make use of the JSON functions introduced in FileMaker 16 to manipulate that response as we need.

However, we found out during our development of [fmapi-aws-s3](https://whitespacesystems.co.uk/portfolio-item/filemaker-aws-s3-integration/) that REST APIs can respond in XML.

This was an issue and with any issue, solutions are born.

We present `fm-xml2json`.

A FileMaker script which when passed a **valid** XML string, will covert this string into a JSON object.

### Built With
* [FileMaker Pro](https://www.filemaker.com/)
* No 3rd party plugins.

<!-- FEATURES -->
## Features

* **Maintain Order of Elements**:
Most parsers will convert `<a/><b/><a/>` to `{a:[{},{}],b:{}}` which merges any node of same name into an array. This library can create the following to preserve the order of elements:
`{"elements":[{"type":"element","name":"a"},{"type":"element","name":"b"},{"type":"element","name":"a"}]}`.

This is very important and it is the main reason why this library was created. Read also [Compact vs Non-Compact](###compact-vs-non-compact) for more info.

* **Fully XML Compliant**:
Can parse: elements, attributes, texts, comments, CData, DOCTYPE, XML declarations, and Processing Instructions.

* **Minimal Dependencies**:
This script depends only on 2 custom functions and no plugins.

* **Change Property Key Name**:
Usually output of XML attributes are stored in `@attr`, `_atrr`, `$attr` or `$` in order to avoid conflicting with name of sub-elements.
This library store them in `attributes`, but most importantly, you can change this to whatever you like.

### Compact vs Non-Compact

Most XML to JSON converters (including online converters) convert `<a/>` to some compact output like `{"a":{}}`
instead of non-compact output like `{"elements":[{"type":"element","name":"a"}]}`.

While compact output might work in most situations, there are cases when elements of different names are mixed inside a parent element. Lets use `<a x="1"/><b x="2"/><a x="3"/>` as an example.
Most converters will produce compact output like this `{a:[{_:{x:"1"}},{_:{x:"3"}}], b:{_:{x:"2"}}}`,
which has merged both `<a>` elements into an array. If you try to convert this back to xml, you will get `<a x="1"/><a x="3"/><b x="2"/>`
which has not preserved the order of elements!

The reason behind this behavior is due to the inherent limitation in the compact representation. 
Because output like `{a:{_:{x:"1"}}, b:{_:{x:"2"}}, a:{_:{x:"3"}}}` is illegal (same property name `a` should not appear twice in an object). This leaves no option but to use array `{a:[{_:{x:"1"}},{_:{x:"3"}}]`.

The non-compact output, which is supported by this script, will produce more information and always guarantees the order of the elements as they appeared in the XML file.

Another drawback of compact output is the resultant element can be an object or an array and therefore makes the client code a little awkward in terms of the extra check needed on object type before processing.

NOTE: Although non-compact output is more accurate representation of original XML than compact version, the non-compact version is verbose and consumes more space.
This library provides both options. Use `# ( "compact": False )` if you are not sure because it preserves everything;
otherwise use `# ( "compact": True )` if you want to save space and you don't care about mixing elements of same name and losing their order.

Tip: You can reduce the output size by using shorter [key names](#options-for-changing-key-names).

<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

#### Version

The FileMaker script makes use of the [JSON Functions](https://fmhelp.filemaker.com/help/16/fmp/en/index.html#page/FMP_Help/json-functions.html) introduced in FileMaker 16.

This means that this script will only work with **FileMaker 16+** products.

Using the script with anything less than 16 will have unexpected behaviour.

#### Custom Functions

We make use of the #Name-Value custom function provided by [filemakerstandards](https://github.com/filemakerstandards/fmpstandards/tree/master/Functions/%23Name-Value).

Please copy the following Custom Functions to your solution before copying the script:
- [# ( name ; value )](https://raw.githubusercontent.com/filemakerstandards/fmpstandards/master/Functions/%23Name-Value/%23.fmfn)
- [#Assign ( parameters )](https://raw.githubusercontent.com/filemakerstandards/fmpstandards/master/Functions/%23Name-Value/%23Assign.fmfn)

#### Limitations

##### Large XML Data

FileMaker Pro is not the most efficient tool for text parsing. This script was designed primarily for small XML packets sent back from APIs. It wasn't designed, in mind, to convert large XML files into JSON.

However, if given enough time, this script will convert large XML files into JSON and has been optimised to the best of our ability.

Unfortunately, FileMaker can only go so far before it starts to fall over.

We've performed some benchmark tests to give you an idea of how long it takes the script to execute.

XML Size (KB) | Execution Time (ms) | Readable Time
------------- | ------------------- | -------------
4 | 958 | 0.9s
8 | 1644 | 1.6s
17 | 3430 | 3.4s
35 | 8767 | 8.7s
71 | 26435 | 26.4s
142 | 87973 | 1min 27.9s
283 | 314285 | 5mins 14.3s

*KB was determined using FileMaker's `Length ( field )` function.*

*Benchmark tests where performed using the #( "compact" ; True ) option.*

As stated above this script wasn't developed with the purpose of taking large XML data and converting it to JSON.

If you do have this need, we **strongly** suggest using a plugin or performing the conversion outside of FileMaker and then importing the result back into FileMaker.

##### FileMaker Text Parsing Functions

We have built the rules for identifying XML nodes in FileMaker using all the Text Functions available to us and from our testing it has handled all the testing XML data thrown at it.

However, there may be some fringe cases where this breaks down. If you discover such a case, please create a [Bug Report](https://github.com/stevenwhitespacesystems/fm-xml2json/issues) and we'll see if we can correct it. Unfortunately, there may be cases that can't be solved.

<!-- USAGE EXAMPLES -->
## Usage

### Installation

1. Make sure that the [Custom Functions](#custom-functions) have been added to your solution.
2. Copy the `fm-xml2json` script to your solution.

### Quick Start

**TODO**

### Sample Conversions

| XML | JS/JSON compact | JS/JSON non-compact |
|:----|:----------------|:--------------------|
| `<a/>` | `{"a":{}}` | `{"elements":[{"name":"a","type":"element"}]}` |
| `<a/><b/>` | `{"a":{},"b":{}}` | `{"elements":[{"name":"a","type":"element"},{"name":"b","type":"element"}]}` |
| `<a><b/></a>` | `{"a":{"b":{}}}` | `{"elements":[{"elements":[{"name":"b","type":"element"}],"name":"a","type":"element"}]}` |
| `<a> Hi </a>` | `{"a":{"_text":" Hi "}}` | `{"elements":[{"elements":[{"text":" Hi ","type":"text"}],"name":"a","type":"element"}]}` |
| `<a x="1.234" y="It's"/>` | `{"a":{"_attributes":{"x":"1.234","y":"It's"}}}` | `{"elements":[{"attributes":{"x":"1.234","y":"It's"},"name":"a","type":"element"}]}` |
| `<?xml?>` | `{"_declaration":{}}` | `{"declaration":{},"elements":[]}` |
| `<?go there?>` | `{"_instruction":{"go":"there"}}` | `{"elements":[{"instruction":"there","name":"go","type":"instruction"}]}` |
| `<?xml version="1.0" encoding="utf-8"?>` | `{"_declaration":{"_attributes":{"version":"1.0","encoding":"utf-8"}}}` | `{"declaration":{"attributes":{"encoding":"utf-8","version":"1.0"}},"elements":[]}` |
| `<!--Hello, World!-->` | `{"_comment":"Hello, World!"}` | `{"elements":[{"comment":"Hello, World!","type":"comment"}]}` |
| `<![CDATA[<foo></bar>]]>` | `{"_cdata":"<foo></bar>"}` | `{"elements":[{"cdata":"<foo></bar>","type":"cdata"}]}` |

### Parameters

The below options can be used as `# ( name ; value )` parameters for the script.

| `name`              | Default | Description |
|:--------------------|:--------|:------------|
| `compact`           | `false` | Whether to produce detailed object or compact object. |

The below options are under consideration to be developed but currently not in the script.

| `name`              | Default | Description |
|:--------------------|:--------|:------------|
| `nativeType`        | `false` | Whether to attempt converting text of numerals or of boolean values to native type. For example, `"123"` will be `123` and `"true"` will be `true` |
| `nativeTypeAttributes` | `false` | Whether to attempt converting attributes of numerals or of boolean values to native type. See also `nativeType` above. |
| `addParent`         | `false` | Whether to add `parent` property in each element object that points to parent object. |
| `ignoreDeclaration` | `false` | Whether to ignore parsing declaration property. That is, no `declaration` property will be generated. |
| `ignoreInstruction` | `false` | Whether to ignore parsing processing instruction property. That is, no `instruction` property will be generated. |
| `ignoreAttributes`  | `false` | Whether to ignore parsing attributes of elements.That is, no `attributes` property will be generated. |
| `ignoreComment`     | `false` | Whether to ignore parsing comments of the elements. That is, no `comment` will be generated. |
| `ignoreCdata`       | `false` | Whether to ignore parsing CData of the elements. That is, no `cdata` will be generated. |
| `ignoreDoctype`     | `false` | Whether to ignore parsing Doctype of the elements. That is, no `doctype` will be generated. |
| `ignoreText`        | `false` | Whether to ignore parsing texts of the elements. That is, no `text` will be generated. |

#### Options for Changing Key Names

To change default key names in the output object, use the following parameters:

| `name`              | Default | Description |
|:--------------------|:--------|:------------|
| `declaration_key`    | `"declaration"` or `"_declaration"` | Name of the property key which will be used for the declaration. For example, if `# ( "declaration_key" ; "$declaration" )` then output of `<?xml?>` will be `{"$declaration":{}}` *(in compact form)* |
| `instruction_key`    | `"instruction"` or `"_instruction"` | Name of the property key which will be used for the processing instruction. For example, if `# ( "instruction_key" ; "$instruction" )` then output of `<?go there?>` will be `{"$instruction":{"go":"there"}}` *(in compact form)* |
| `attributes_key`     | `"attributes"` or `"_attributes"` | Name of the property key which will be used for the attributes. For example, if `# ( "attributes_key" ; "$attributes" )` then output of `<a x="hello"/>` will be `{"a":{$attributes:{"x":"hello"}}}` *(in compact form)* |
| `comment_key`        | `"comment"` or `"_comment"` | Name of the property key which will be used for the comment. For example, if `# ( "comment_key" ; "$comment" )` then output of `<!--note-->` will be `{"$comment":"note"}` *(in compact form)* |
| `cdata_key`          | `"cdata"` or `"_cdata"` | Name of the property key which will be used for the cdata. For example, if `# ( "cdata_key" ; "$cdata" )` then output of `<![CDATA[1 is < 2]]>` will be `{"$cdata":"1 is < 2"}` *(in compact form)* |
| `doctype_key`        | `"doctype"` or `"_doctype"` | Name of the property key which will be used for the doctype. For example, if `# ( "doctype_key" ; "$doctype" )` then output of `<!DOCTYPE foo>` will be `{"$doctype":" foo}` *(in compact form)* |
| `text_key`           | `"text"` or `"_text"` | Name of the property key which will be used for the text. For example, if `# ( "text_key" ; "$text" )` then output of `<a>hi</a>` will be `{"a":{"$text":"Hi"}}` *(in compact form)* |

The below options are under consideration to be developed but currently not in the script.
| `name`              | Default | Description |
|:--------------------|:--------|:------------|
| `parent_key`         | `"parent"` or `"_parent"` | Name of the property key which will be used for the parent. For example, if `# ( "parent_key" ; "$parent" )` then output of `<a></b></a>` will be `{"a":{"b":{$parent:_points_to_a}}}` *(in compact form)* |
| `type_key`           | `"type"` | Name of the property key which will be used for the type. For example, if `# ( "type_key" ; "$type" )` then output of `<a></a>` will be `{"elements":[{"$type":"element","name":"a"}]}` *(in non-compact form)* |
| `name_key`           | `"name"` | Name of the property key which will be used for the name. For example, if `# ( "name_key" ; "$name" )` then output of `<a></a>` will be `{"elements":[{"type":"element","$name":"a"}]}` *(in non-compact form)* |
| `elements_key`       | `"elements"` | Name of the property key which will be used for the elements. For example, if `# ( "elements_key" ; "$elements" )` then output of `<a></a>` will be `{"$elements":[{"type":"element","name":"a"}]}` *(in non-compact form)* |

<!-- DEMO -->
## Demo

**TODO**

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to be learn, inspire, and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE` for more information.

<!-- CONTACT -->
## Contact

Steven McGill - [WhiteSpace Systems Ltd](https://whitespacesystems.co.uk) - [steven@whitespacesystems.co.uk](mailto:steven@whitespacesystems.co.uk)

Project Link: [fm-xml2json](https://github.com/stevenwhitespacesystems/fm-xml2json)

<!-- ACKNOWLEDGEMENTS -->
## Acknowledgements
* [Jeremy Bante's #Parameters](http://www.modularfilemaker.org/module/parameters/)
* [Inspired by xml-js](https://github.com/nashwaan/xml-js)
* [Computech IT Services](https://www.computech-it.co.uk)
* [Best-README-Template](https://github.com/othneildrew/Best-README-Template)

<!-- FMAPI SUITE -->
## fmapi Product Suite

When FileMaker 16 introduced cURL with the Insert from URL script step. Use cases for FileMaker Pro increased dramatically in relation to REST APIs.

It allowed developers to finally communicate with other web services and APIs without the need for 3rd party plugins. Integration with Couriers, Payment Gateways & Social Media Sites all became within touching distance.

However, without prior knowledge of cURL, HTTP Request Methods, HTTP Headers, JSON, OAuth Authentication, API Keys, API documentation etc, it can be extremely difficult to get started for the novice user or even the most proficient FileMaker developer.

And with that comes our goal, to simplify communications between your FileMaker App and the vast amount of Web Services available, no matter your ability level.

Read more over at [What is fmapi?](https://whitespacesystems.co.uk/filemaker-3rd-party-api-integration/)

### fmapi Apps

A collection of FileMaker apps that communicate directly with popular 3rd party REST APIs.

* [fmapi-vies-vat](https://whitespacesystems.co.uk/portfolio-item/filemaker-vies-vat-integration/) - Integrate the EU Commissions Vies VAT API directly into you FileMaker App allowing you to validate EU VAT Numbers.

<!-- MARKDOWN LINKS & IMAGES -->
[filemaker-shield]: https://img.shields.io/badge/filemaker-%3E%3D%2016.0.0-009edb.svg
[platform-shield]: https://img.shields.io/badge/platform-Pro%20%7C%20Go%20%7C%20Server%20%7C%20Webdirect%20%7C%20Cloud-purple.svg
[contributors-shield]: https://img.shields.io/github/contributors/stevenwhitespacesystems/fm-xml2json.svg
[license-shield]: https://img.shields.io/badge/license-MIT-blue.svg
[commit-shield]: https://img.shields.io/github/last-commit/stevenwhitespacesystems/fm-xml2json.svg
[license-url]: https://choosealicense.com/licenses/mit
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?logo=linkedin&colorB=0077B5
[linkedin-url]: https://www.linkedin.com/company/whitespace-systems-ltd/
[facebook-shield]: https://img.shields.io/badge/-facebook-white.svg?logo=facebook&colorB=3578E5
[facebook-url]: https://www.facebook.com/WhitespaceSystemsLtd/