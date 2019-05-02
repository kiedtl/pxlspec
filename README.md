## PXL Image Format

> A simple image format for simple displaying the compatible terminal emulators.

### Goals

PXL was designed to be easy to display in a commandline environment. 
Currently there exists many methods of doing this, and hundreds of
tools. This spec aims to standardize the format of these ASCII escape
sequences based images.


### Table of contents

- [Example](#user-content-example)
- [Spec](#user-content-spec)
- [Files](#user-content-files)
- [Pixel](#user-content-pixel)
- [Lines](#user-content-lines)
- [MIME Type](#user-content-mime-type)
- [License](#user-content-license)

### Example

```
# coming soon
```

### Spec

* PXL relies heavily on ASCII escape sequences.
* Escape (`\e`) is the  ESC (`0x1B` / `U+001B`)
* PXL newlines are an LF (`0x0A`).
* Each pixel character is the Unicode upper-half block (`0x2580`)
* ASCII escape code separator can be either `:` or `;` (or both).

### Files

- A PXL file can be either encoded as ASCII or UTF-8. However, UTF-8 
is strongly preferred.
- A PXL image file has the `.pxl` file extension.
- A PXL image file *may* contain a marker at the beginning of the file, 
  showing that it is a PXL file and what version it is:
  ```
  \eP\eX\eL\e[<version>
  ```
  E.g. if the image adheres to v1.2.3 of the PXL spec, the marker 
  should look like this:
  ```
  \eP\eX\eL[1.2.3
  ```

### Pixel

A single 'pixel' is actually two pixels in PXL, using the old 
Unicode half-block trick.

```
\e[48;2;RRR;GGG;BBBm\e[38;2;RRR;GGG;BBBm▀
```

#### Pixel anatomy
A single pixel is comprised of two SGR (Set Graphics Rendition) ASCII escape sequences.
- The first sets **background** color of the pixel.
  - This is comprised of a ESC char (`\e`), `[48;2`, and then a semicolon/colon delimited RGB value ended by an `m`.
    ```
    \e[48;2;RRR;GGG;BBBm
    ```
- The second sets the **foreground** color of the pixel.
  - This is comprised of the ESC, `[38;2`, a semicolon/colon delimited RGB value ended by an `m`, and the pixel char.
    ```
    \e[38;2;RRR;GGG;BBBm▀
    ```
    
See [this page](https://docs.microsoft.com/en-us/windows/console/console-virtual-terminal-sequences#extended-colors) for more information regarding the SGR ASCII escape sequences.

### Lines
Lines are comprised of multiple pixels joined without any 
spaces/delimiter. Lines always end with the following sequence:
```
\e[0m\e[B\e[0G
```
This sequence is required. It does three things:
- resets the color
- moves the cursor down one row
- moves the cursor to the first column in that row

Lines **may not** end in a newline (CR, CRLF, or LF).

### Viewing PXL images
PXL images may be viewed either with:
- the `cat` command (on Linux, macOS, BSD, MSYS2/Cygwin, PowerShell)
- the `type` command (on PowerShell and Windows CMD)
- with cURL, if the image is on the internet:
  ```
  curl -sSL https://blahblah.org/files/img/blah/blah24.pxl
  ```
- any other command-line program that reads a file and displays raw data

### MIME Type
When transferring PXL images on the internet, the appropriate MIME type is
`image/pxl`.

### License
- This repository is licensed under the AGPL0-v3.0 license.
