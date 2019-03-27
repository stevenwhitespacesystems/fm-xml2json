<!-- PROJECT SHIELDS -->
[![FileMaker Version][filemaker-shield]]()
[![FileMaker Platform][platform-shield]]()
[![Commit Shield][commit-shield]]()
[![Contributors][contributors-shield]]()
[![MIT License][license-shield]][license-url]
[![LinkedId][linkedin-shield]][linkedin-url]

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

<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About the Project](#about-the-project)
  * [Built With](#built-with)
* [Getting Started](#getting-started)
  * [Prerequisites](#prerequisites)
    * [Version](#version)
    * [Custom Functions](#custom-functions)
    * [Limitations](#limitations)
  * [Installation](#installation)
* [Usage](#usage)
* [Contributing](#contributing)
* [License](#license)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)

<!-- ABOUT THE PROJECT -->
## About The Project

[![Product Name Screen Shot][product-screenshot]](https://example.com)

As we delved into the world of REST APIs using FileMaker, we stumbled across an issue.

*How do we handle XML responses?*

When the REST response is a JSON object, we don't have any issue and can make use of the JSON functions introduced in FileMaker 16 to manipulate that response as we need.

However, we found out during our development of [fmapi-aws-s3](https://whitespacesystems.co.uk/portfolio-item/filemaker-aws-s3-integration/) that REST APIs can respond in XML.

This was an issue and with any issue, solutions are born.

We present [fmapi](https://whitespacesystems.co.uk/filemaker-3rd-party-api-integration/)-fm-xml2json.

A FileMaker script which when passed a **valid** XML string, will covert this string into a JSON object.

### Built With
* [FileMaker Pro](https://www.filemaker.com/)
* No 3rd party plugins.

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

*KB was determined using FileMaker's `Length ( field )` function.*

As stated above this script wasn't developed with the purpose of taking large XML data and converting it to JSON.

If you do have this need, we **strongly** suggest using a plugin or performing the conversion outside of FileMaker and then importing the result back into FileMaker.

##### FileMaker Text Parsing Functions

We have built the rules for identifying XML nodes in FileMaker using all the Text Functions available to us and from our testing it has handled all the testing XML data thrown at it.

However, there may be some fringe cases where this breaks down. If you discover such a case, please create a [Bug Report](https://github.com/stevenwhitespacesystems/fm-xml2json/issues) and we'll see if we can correct it. Unfortunately, there may be cases that can't be solved.

### Installation

1. Make sure that the [Custom Functions](#custom-functions) have been added to your solution.
2. Copy the `fm-xml2json` script to your solution.

<!-- USAGE EXAMPLES -->
## Usage

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
* [Computech IT Services](https://www.computech-it.co.uk)
* [Best-README-Template](https://github.com/othneildrew/Best-README-Template)

<!-- MARKDOWN LINKS & IMAGES -->
[filemaker-shield]: https://img.shields.io/badge/filemaker-%3E%3D%2016.0.0-green.svg?style=flat-square
[platform-shield]: https://img.shields.io/badge/platform-Pro%20%7C%20Go%20%7C%20Server%20%7C%20Webdirect%20%7C%20Cloud-purple.svg?style=flat-square
[contributors-shield]: https://img.shields.io/github/contributors/stevenwhitespacesystems/fm-xml2json.svg
[license-shield]: https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square
[commit-shield]: https://img.shields.io/github/last-commit/stevenwhitespacesystems/fm-xml2json.svg
[license-url]: https://choosealicense.com/licenses/mit
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=flat-square&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/stevenmcgill/
[product-screenshot]: logo.png