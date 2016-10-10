# QValues
> Dave Transom's QValue lib for accepted encodings stored for easier retrieval

See [Dave's original article](http://www.singular.co.nz/2008/07/finding-preferred-accept-encoding-header-in-csharp/) 
on why parsing `Accept-Encoding` with `String#contains` 
is a malpractice that has some downsides you should be aware of. 

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
