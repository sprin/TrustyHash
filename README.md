# Client-Side Hash Calculator

This is a small HTML5 application that computes SHA-256 hashes on both local
files and on remote URLs. It is targeted towards users who do not have or are
unable to use the hash utilities on their local systems.

[Use it here](https://sprin.github.io/client-side-hash-calculator/)

## Usage

Local files can be opened from a file select dialog, or dragged into the "drop
area". Remote URLs can be entered, and if the remote server allows cross-origin
GET requests via
[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS),
the file will be downloaded to the client, with the option of saving locally.

The application can be saved and used offline. To save from the browser, use
"Save Page" > "Web Page, HTML Only".

## Trust

This app does the hash calculation in the browser using the
[WebCryptoAPI](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/digest).
This means we can trust the hash calculation under the following assumptions:

 - The integrity of the application has been preserved when it executes on the
   client.
 - The browser can be trusted to correctly implement the WebCryptoAPI.
 - The browser does not have any extensions enabled which might modify the
   behavior of the application.

Because the application itself is a single file, integrity can be verified
by saving and computing the hash with a trusted hash utility.

TODO: Publish signed hashes for each release.

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

In the interests of standardization and keeping things simple for the user,
only SHA-256 will be shown. A possible addition to this project is to allow
the user to select other hash algorithms, with SHA-256 remaining the default.

## Limitations

When the application is retrieved on an HTTPS connection, the application
cannot fetch HTTP URLs due to restrictions against [mixed active
content](https://developer.mozilla.org/en-US/docs/Security/Mixed_content#Mixed_active_content]).
A workaround for this is to save the page locally and open the local copy in
the browser.


## License

MIT
