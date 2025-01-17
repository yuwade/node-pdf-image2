# pdf-image2

Provides an interface to convert PDF's pages to png files in Node.js
by using ImageMagick.

## Installation

    npm install pdf-image2

Ensure you have `convert`, `gs`, and `pdfinfo` (part of poppler) commands.

### Ubuntu

    sudo apt-get install imagemagick ghostscript poppler-utils

### OSX (Yosemite)

    brew install imagemagick ghostscript poppler

### Windows
   
    choco install imagemagick

if you don't have choco, goto https://chocolatey.org/install

- Windows 7+ / Windows Server 2003+
- PowerShell v2+ (minimum is v3 for install from this website due to TLS 1.2 requirement)
+ .NET Framework 4+ (the installation will attempt to install .NET 4.0 if you do not have it installed)(minimum is 4.5 for install from this website due to TLS 1.2 requirement)

powershell

```shell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```
![src=/md/choco.png](md/choco.png)

## Usage

#### Convert single page:
```javascript
var PDFImage = require("pdf-image2").PDFImage;

var pdfImage = new PDFImage("/tmp/slide.pdf");
pdfImage.convertPage(0).then(function (imagePath) {
  // 0-th page (first page) of the slide.pdf is available as slide-0.png
  fs.existsSync("/tmp/slide-0.png") // => true
});
```

#### Convert full file
```javascript
var PDFImage = require("pdf-image2").PDFImage;

var pdfImage = new PDFImage("/tmp/slide.pdf");
pdfImage.convertFile().then(function (imagePaths) {
  // [ /tmp/slide-0.png, /tmp/slide-1.png ]
});


```
#### Convert full file and merge result into single image
```javascript
var PDFImage = require("pdf-image2").PDFImage;
var pdfImage = new PDFImage("/tmp/slide.pdf", {
  combinedImage: true
});

pdfImage.convertFile().then(function (imagePaths) {
   // /tmp/slide.png 
});
```

## Express

Following example shows an example of pdf-image2 in Express, which gives
URLs for each pages of a PDF like
`http://example.com:3000/tmp/slide.pdf/0.png`.

```javascript
  app.get(/(.*\.pdf)\/([0-9]+).png$/i, function (req, res) {
    var pdfPath = req.params[0];
    var pageNumber = req.params[1];

    var PDFImage = require("pdf-image2").PDFImage;
    var pdfImage = new PDFImage(pdfPath);

    pdfImage.convertPage(pageNumber).then(function (imagePath) {
      res.sendFile(imagePath);
    }, function (err) {
      res.send(err, 500);
    });
  });
```

## Options

Following example shows an example of how to add imagemagick command-line options (you can find the complete list here -> http://www.imagemagick.org/script/convert.php):

```javascript
var pdfImage = new PDFImage(pdfPath, {
  convertOptions: {
    "-resize": "2000x2000",
    "-quality": "75"
  }
});
```
