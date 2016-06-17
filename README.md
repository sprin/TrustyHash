# TrustyHash - A Trustable Hash Calculator

TrustyHash is a small HTML5 application that computes SHA-256 hash values on
both local files and on remote URLs, with a strong emphasis on a process that
will allow you to trust the results.

Project Homepage: https://github.com/sprin/client-side-hash-calculator

[Use it here](https://sprin.github.io/client-side-hash-calculator/)

## How is this useful?

Integrity: "We have in hand the same set of sequences of bits that came into
existence when the object was created" - [Lynch](http://www.clir.org/pubs/reports/pub92/lynch.html)

"Friends don't let friends use unverified downloads."

This fills a need for a verifiable, web-based hash calculator written in free
JavaScript. If you already use the terminal-based hash utilities on your
system, you should continue to use those. This is targeted towards users who do
not have or are unable to use the hash utilities on their local systems. While
universal terminal-literacy is a good goal, the concepts of file integrity and
and authenticity and the ability to use tools for verification are perhaps more
fundamental.

Integrity is the first link in secure systems, and key to determining
authenticity. If we trust the association between an author and the hash value
of a file they created, perhaps because we trust them and they gave us the
hash in person, we can authenticate whether a file we believe to be the same
really did come from them. We can achieve the same result if the author had
used a signing key, and signed and distributed a hash value along with the
file, and we could trust the association between a particular key and the
author - albeit with somewhat more complexity and caveats (eg, has the signing
key been kept private?).

In a few words, this tool aims to enable verification of integrity and
authenticity claims in an accessible way that depends only on a trusted hash
value and the correctness and integrity of the TrustyHash app and the browser
it executes in. See the section "Trust" below for recommendations on
how to verify integrity of this application.

## Usage

Local files can be opened from a file select dialog, or dragged into the "drop
area". Remote URLs can be entered, and if the remote server allows cross-origin
GET requests via
[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS),
the file will be downloaded to the client, with the option of saving locally.

It's recommended to save the application, verify the intengrity, and use the
saved copy from then on. To save from the browser, use "Save Page" > "Web Page,
HTML Only". To verify, read the section on "Trust" below.

## Trust

This app does the hash calculation in the browser using the
[WebCryptoAPI](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/digest).
This means we can trust the hash calculation under the following assumptions:

 - The integrity of the application has been preserved when it executes in the
   browser.
 - The browser and any extensions can be trusted.

### Integrity of the TrustyHash itself

Because the application itself is a single HTML file which can be saved locally,
there are various means for verifying integrity.

#### Trusted Hash Utility

The most reliable way to verify is to compute the hash of the HTML file with a
trusted hash utility on the local system, and compare against the values
published below.

#### Code Audit

Someone familiar with JavaScript can spend a few minutes reading the concise
source to be assured that the program does what it claims to.

#### No Hash Utility, Can't Audit Code?

Hmm, no trusted hash utility, can't audit the code... you just can't give up
and use your copy of TrustyHash without trusting it! While you could try some
half-measure like getting some kind of consensus on the hash value of
TrustyHash from untrusted hash utilities on the web, maybe other copies of
TrustyHash found elsewhere... ultimately if you really need to trust
TrustyHash, you've got to be a bit more rigorous.

Without knowing a thing about JavaScript until this moment, you can create a
very small, simple program in about 5 minutes, that while not as nice as
TrustyHash perhaps, will get the job of hashing a local file done. As long as
you can follow along as the following code is explained, and you can be pretty
confident the code is not doing anything fishy, you can use this to verify
TrustyHash itself. I'll show you the whole program up-front before I explain
it - see, 5 minutes, no more!

```
<!doctype html>
<html>
<body>
<script>
var fileinput = document.createElement('input')
fileinput.type = 'file'
fileinput.onchange = function(){
var reader = new FileReader()
reader.readAsArrayBuffer(this.files[0]);
reader.onload = function(){
crypto.subtle.digest("SHA-256", this.result)
.then(function(buffer) {
var hexCodes = []
var view = new DataView(buffer)
for (var i = 0; i < view.byteLength; i += 4) {
var stringValue = view.getUint32(i).toString(16)
var paddedValue = ('00000000' + stringValue).slice(-8)
hexCodes.push(paddedValue)}
alert(hexCodes.join(""))})}}
document.body.appendChild(fileinput)
</script>
</body>
</html>
```

JavaScript programmers may take offense with the lack of conventional
formatting above, but I'm trying to making this easy to re-type for someone who
shouldn't need to be concerned with formatting conventions.

Now a more-or-less line-by-line explanation:

```
<!doctype html>
<html>
<body>
<input type="file">
<script>
```

These lines declare an HTML document with a file input and a script. HTML
technically requires a `head` element, but I'm trying to save you a bit of
typing. Now let's get into the JavaScript itself:

```
document.querySelector('input').onchange = function(){
```

This says, when a file is selected via the file input...

```
var reader = new FileReader()
```

...create a reader to read the file.

```
reader.readAsArrayBuffer(this.files[0]);
```

Read the file into memory.

```
reader.onload = function(){
```

When the reader finishes...

```
crypto.subtle.digest("SHA-256", this.result)
```

Hash the buffer with SHA-256.

```
.then(function(buffer) {
```

When hashing finishes, we have an unprintable object...

```
var hexCodes = []
```

...that we want to turn in to printable hex codes, which is just a way to
represent a number using 0 to 9 and 'a' to 'f'. Hex codes are the standard
representation of SHA-256 hash values.


```
var view = new DataView(buffer)
```

We create a view so we can read the buffer in chunks.

```
for (var i = 0; i < view.byteLength; i += 4) {
```

With each chunk...

```
var stringValue = view.getUint32(i).toString(16)
```

...convert each chunk to a number and get a string, which is just something we
can print.

```
var paddedValue = ('00000000' + stringValue).slice(-8)
```

To correctly print the string as a hex code, we need to add zeroes to the front
in case the number is less than 8 digits.

```
hexCodes.push(paddedValue)}
```

Keep the string we just created before moving on to the next chunk.

```
alert(hexCodes.join(""))})}}
```

Join all the strings created from each chunk together and pop it up on the
screen. That's it!

```
</script>
</body>
</html>
```

Oh, and we need these lines to formally close the HTML document.

If you followed all that, put this code into a file called `simple.html` and
open it up in your browser. I recommend re-typing, rather than copy-pasting,
since there are a bunch of sneaky ways someone could trick you into
copy-pasting something besides what you see on a web page. If creating HTML
files by hand is a bit confusing, you can save [the file I created for
you](https://raw.githubusercontent.com/sprin/client-side-hash-calculator/master/simple.html)
as long as you promise you will make sure the code matches the above after you
have saved it. One way to do this is to open the file in a browser, right-click
and select "View Page Source".

Open the 'simple.html' file in your browser, click the file input button,
select the 'TrustyHash.html' you saved earlier. If the printed hex code matches
the published hash values, congratulations, you just wrote a program that
computes SHA-256 hashes *and* used it to validate TrustyHash!

### Hash Values

TODO: Publish hash value for 1.0.0

## Deployment

The entire application is packaged in a single, brief HTML file. Simply deploy
the file under the web server root directory.

## Why only SHA-256?

SHA-256 remains the de facto standard for verifying files via hash in 2016.
The following projects have standardized on SHA-256 for verifying release
materials:

 - [OpenBSD](http://man.openbsd.org/signify)
 - [FreeBSD](https://www.freebsd.org/releases/10.2R/signatures.html)
 - [Centos](http://mirror.centos.org/centos/7/isos/x86_64/sha256sum.txt)
 - [Fedora](https://getfedora.org/verify)

In the interests of standardization and keeping things simple, only SHA-256
will be shown. A possible addition to this project is to allow the user to
select other hash algorithms, with SHA-256 remaining the default.

## Limitations

When the application is retrieved on an HTTPS connection, the application
cannot fetch HTTP URLs due to restrictions against [mixed active
content](https://developer.mozilla.org/en-US/docs/Security/Mixed_content#Mixed_active_content]).
A workaround for this is to save the page locally and open the local copy in
the browser, as recommended anyway.

## License

MIT
