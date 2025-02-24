# rquest

[![CI](https://github.com/0x676e67/rquest/actions/workflows/ci.yml/badge.svg)](https://github.com/0x676e67/rquest/actions/workflows/ci.yml)
[![Crates.io License](https://img.shields.io/crates/l/rquest)](./LICENSE)
[![crates.io](https://img.shields.io/crates/v/rquest.svg)](https://crates.io/crates/rquest)
[![Crates.io Total Downloads](https://img.shields.io/crates/d/rquest)](https://crates.io/crates/rquest)

> 🚀 Help me work seamlessly with open source sharing by [sponsoring me on GitHub](https://github.com/0x676e67/0x676e67/blob/main/SPONSOR.md)

An ergonomic, all-in-one HTTP client for spoofing any browser with TLS, JA3/JA4, and HTTP2 fingerprints.

> <ins>**This project is intended for personal learning purposes only. Use at your own risk.**</ins>

## Features

- Plain, JSON, urlencoded, multipart bodies
- Header Order
- Redirect Policy
- Cookie Store
- HTTP Proxies
- WebSocket Upgrade
- HTTPS via BoringSSL
- Perfectly Chrome, Safari, and Firefox

## Example

This asynchronous example uses [Tokio](https://tokio.rs) and enables some optional features. Your `Cargo.toml` could look like this:

```toml
[dependencies]
tokio = { version = "1", features = ["full"] }
rquest = "2.2.0"
```

And then the code:

```rust,no_run
use rquest::{Client, Impersonate};

#[tokio::main]
async fn main() -> Result<(), rquest::Error> {
    // Build a client to impersonate Firefox135
    let client = Client::builder()
        .impersonate(Impersonate::Firefox135)
        .build()?;

    // Use the API you're already familiar with
    let resp = client.get("https://tls.peet.ws/api/all").send().await?;
    println!("{}", resp.text().await?);

    Ok(())
}
```

## Overview

This project is a fork of [reqwest](https://github.com/seanmonstar/reqwest), similar to how [BoringSSL](https://github.com/cloudflare/boring) is a fork of OpenSSL. It optimizes commonly used APIs and enhances compatibility with connection pools, making it easier to switch proxies, IP addresses, and interfaces. Projects using reqwest can be migrated to rquest with minimal changes.

Overall, **`rquest`** is a superset of reqwest, offering simpler and more practical APIs while also fixing HTTP version negotiation [issues](https://github.com/seanmonstar/reqwest/issues/2116).

## Performance

`BoringSSL` is a fork of `OpenSSL` designed for security and efficiency, used by Google Chrome, Android, and Cloudflare. We haven't encountered serious issues with `BoringSSL` related to the Golang [utls issue](https://github.com/refraction-networking/utls/issues/274).

By default, `HTTP2` tracing is turned off, reducing performance overhead by 15%. For more information, see issue: <https://github.com/hyperium/h2/issues/713>

## Connection Pool

The client can configure the maximum number of connection pools. Request manages connections based on `Host` and `Proxy`/`IP`/`Interface`, and can flexibly switch between them.
- `Interface` refers to the network interface of the device, such as `wlan0` or `eth0`.

## Root Certificate

By default, `rquest` uses Mozilla's root certificates through the `webpki-roots` crate. This static root certificate bundle is not automatically updated and ignores any root certificates installed on the host. You can disable `default-features` to use the system's default certificate path. Additionally, `rquest` provides a certificate store for users to customize and update certificates.

## Fingerprint

- **HTTP/2 over TLS**

  **JA3**/**JA4**/**Akamai** fingerprints cannot accurately simulate browser fingerprints due to the sophistication of TLS encryption and the popularity of HTTP/2. `rquest` does not plan to support parsing these fingerprint strings for simulation. Users are encouraged to customize the configuration according to their own needs.

- **Default Fingerprint**

  In fact, most device models have the same `TLS`/`HTTP2` configuration, except that the `User-Agent` is changed.

    <details>

    <summary>Default device emulation types</summary>

  | **Browser**   | **Versions**                                                                                     |
  |---------------|--------------------------------------------------------------------------------------------------|
  | **Chrome**    | `Chrome100`, `Chrome101`, `Chrome104`, `Chrome105`, `Chrome106`, `Chrome107`, `Chrome108`, `Chrome109`, `Chrome114`, `Chrome116`, `Chrome117`, `Chrome118`, `Chrome119`, `Chrome120`, `Chrome123`, `Chrome124`, `Chrome126`, `Chrome127`, `Chrome128`, `Chrome129`, `Chrome130`, `Chrome131`, `Chrome133` |
  | **Edge**      | `Edge101`, `Edge122`, `Edge127`, `Edge131`                                                       |
  | **Safari**    | `SafariIos17_2`, `SafariIos17_4_1`, `SafariIos16_5`, `Safari15_3`, `Safari15_5`, `Safari15_6_1`, `Safari16`, `Safari16_5`, `Safari17_0`, `Safari17_2_1`, `Safari17_4_1`, `Safari17_5`, `Safari18`, `SafariIPad18`, `Safari18_2`, `Safari18_1_1` |
  | **OkHttp**    | `OkHttp3_9`, `OkHttp3_11`, `OkHttp3_13`, `OkHttp3_14`, `OkHttp4_9`, `OkHttp4_10`, `OkHttp5`         |
  | **Firefox**   | `Firefox109`, `Firefox117`, `Firefox128`, `Firefox133`, `Firefox135`, `FirefoxPrivate135`, `FirefoxAndroid135` |

    </details>

## Requirements

Do not compile with packages that depend on `openssl-sys`; it links with the same prefix symbol as `boring-sys`, which can cause [link failures](https://github.com/cloudflare/boring/issues/197) and other problems. Even if compilation succeeds, using both `openssl-sys` and `boring-sys` as dependencies can cause memory segmentation faults.

## Building

Install the dependencies required to build [BoringSSL](https://github.com/google/boringssl/blob/master/BUILDING.md#build-prerequisites)

```shell
sudo apt-get install build-essential cmake perl pkg-config libclang-dev musl-tools -y

cargo build --release
```

This GitHub Actions [workflow](.github/compilation-guide/build.yml) can be used to compile the project on **Linux**, **Windows**, and **macOS**.

## License

Released under the [Apache-2.0](./LICENSE) License.

## Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted for inclusion in the work by you, as defined in the [Apache-2.0](./LICENSE) license, shall be licensed as above, without any additional terms or conditions.

## Sponsors

Support this project by becoming a [sponsor](https://github.com/0x676e67/0x676e67/blob/main/SPONSOR.md).

## Accolades

The project is based on a fork of [reqwest](https://github.com/seanmonstar/reqwest).
