# QValues
> Utility classes for correctly parsing `Accept-Encodings` 

Utility classes for [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)
compliant parsing of the `Accept-Encoding`, `Accept-Charset` and `Accept-Language` header.

## Background
Dave Transom's blog post on [What's wrong with `Request.Headers["Accept-Encoding"].Contains("gzip")`?](http://www.singular.co.nz/2008/07/finding-preferred-accept-encoding-header-in-csharp/) 
goes into detail on why parsing `Accept-Encoding` with `String#contains` 
is a malpractice that has some downsides you should be aware of. 

His article also contains some nice utility classes that does what 
thousand of sites do wrong, which is what I have put up here, along
with some bug fixes.

## Typical usage
```cs
/// load encodings from header
QValueList encodings = new QValueList(Request.Headers["Accept-Encoding"]);

/// get the types we can handle, can be accepted and
/// in the defined client preference
QValue preferred = encodings.FindPreferred("gzip", "deflate", "identity");

/// if none of the preferred values were found, but the
/// client can accept wildcard encodings, we'll default
/// to Gzip.
if (preferred.IsEmpty && encodings.AcceptWildcard && encodings.Find("gzip").IsEmpty)
  preferred = new QValue("gzip");

// handle the preferred encoding
switch (preferred.Name)
{
  case "gzip":
      Response.AppendHeader("Content-Encoding", "gzip");
      Response.Filter = new GZipStream(Response.Filter, CompressionMode.Compress);
    break;
  case "deflate":
      Response.AppendHeader("Content-Encoding", "deflate");
      Response.Filter = new DeflateStream(Response.Filter, CompressionMode.Compress);
    break;
  case "identity":
  default:
    break;
}
```
