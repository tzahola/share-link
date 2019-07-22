# share-link
Expiring links for file sharing. Low-tech. Supports downloading whole folders. Can be used with any CGI-compliant web server, like [lighttpd](https://www.lighttpd.net). 

## Dependencies

- `date` from GNU coreutils
- `openssl` for HMAC calculation
- `base64` from GNU coreutils
- [`jq` for JWT handling](https://stedolan.github.io/jq)
- [`uconv` from icu-devtools](https://manpages.debian.org/jessie/icu-devtools/uconv.1.en.html) for ASCIIfying the downloaded filename
- [`zip` via InfoZIP](https://packages.debian.org/buster/zip) for on-the-fly folder downloads

## Installation

- clone repo
- make the `get` CGI script publicly available via your webserver
- set the `SHARE_LINK_PREFIX` environment variable to the URL of the `get` CGI script (e.g. `export SHARE_LINK_PREFIX="http://example.com/get/"`)
- `share-link` will generate a 256-bit key on first use; **make sure this is NOT available publicly**
- for best results disable CGI response buffering on your webserver, e.g. [for lighttpd set `server.stream-response-body = 2`](https://redmine.lighttpd.net/projects/lighttpd/wiki/Server_stream-response-bodyDetails)

## Usage

Use `share-link` to generate expiring links for your folders/files.

### Example:

```
$ share-link --expiry tomorrow --link-prefix http://example.com -- /home/foo/bar/document.txt
http://example.com/get/eyJ0eXAiOiJKV1QiLCJleHAiOjE1NjM4NzUxOTJ9Cg.eyJwYXRoIjoiL2hvbWUvZm9vL2Jhci9kb2N1bWVudC50eHQifQo.KHN0ZGluKT0gMjMzZWQ4MzRkMWI3OTIxNWMwNTUxYjExOWRmMzZmNzQyNTliYzQwYjYzMzlkNjc2OTkzZTZjNmVmM2NkZjQ0NAo
```

### Arguments:

* Expiry date (`--expiry | -e`): can be any human-readable string that `date` accepts, like *"tomorrow", "+1day", "+1week", etc.*
* Link prefix (`--link-prefix | -l`): the final link will be this prefix + `/get/[token]`. If omitted then the `SHARE_LINK_PREFIX` environment variable will be used.

## Invalidate links in case of emergency

If you remove the `key` file next to `get` and `share-link` (generated by `share-link` on first use), all existing links will be invalidated. 
