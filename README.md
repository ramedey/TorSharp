# TorSharp

Use Tor for your C# HTTP clients. Tor + Privoxy = :heart:

All you need is client code that can use a simple HTTP proxy.

## Details

- Uses Privoxy to redirect HTTP proxy traffic to Tor.
- Uses virtual desktops to manage Tor and Privoxy processes.
- Optionally downloads the latest version of Tor and Privoxy.

## Install

```
Install-Package Knapcode.TorSharp
```

## Example

```csharp
// configure
var settings = new TorSharpSettings
{
    ZippedToolsDirectory = Path.Combine(Path.GetTempPath(), "TorZipped"),
    ExtractedToolsDirectory = Path.Combine(Path.GetTempPath(), "TorExtracted"),
    PrivoxyPort = 1337,
    TorSocksPort = 1338,
    TorControlPort = 1339,
    TorControlPassword = "foobar"
};

// download tools
await new TorSharpToolFetcher(settings, new HttpClient()).FetchAsync();

// execute
var proxy = new TorSharpProxy(settings);
var handler = new HttpClientHandler
{
    Proxy = new WebProxy(new Uri("http://localhost:" + settings.PrivoxyPort))
};
var httpClient = new HttpClient(handler);
await proxy.ConfigureAndStartAsync();
Console.WriteLine(await httpClient.GetStringAsync("http://icanhazip.com"));
await proxy.GetNewIdentityAsync();
Console.WriteLine(await httpClient.GetStringAsync("http://icanhazip.com"));
proxy.Stop();
```