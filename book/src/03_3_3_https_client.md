# HTTPS CLIENT

TODO check with Espressif: HTTPS support seems buggy - in my (Anatol) tests I get some unexpected HTTP header data before the actual body

You will now make changes to your http client files.

To establish a secure, encrypted HTTPS connection, we first need to add some certificates so a server's identity can be verified.

✅ Enable basic TLS certificate support in your project's `sdkconfig.defaults` by deleting the existing `CONFIG_MBEDTLS...` lines and adding:
```cfg
CONFIG_MBEDTLS_CERTIFICATE_BUNDLE=y
CONFIG_MBEDTLS_CERTIFICATE_BUNDLE_DEFAULT_CMN=y
```

Now, we create a custom client configuration to use an `http::client::EspHttpClientConfiguration` which enables the use of these certificates and uses default values for everything else:

```rust
let client_config = EspHttpClientConfiguration {
    use_global_ca_store: true,
    crt_bundle_attach: Some(esp_idf_sys::esp_crt_bundle_attach),
    ..Default::default()
}
```

✅ Initialize your HTTP client with this new configuration and verify HTTPS works by downloading from a `https` resource e.g. `https://espressif.com/`

## Troubleshooting (repeated from previous section)

- `error: cannot find macro llvm_asm in this scope`: set `channel = "nightly-2021-11-18"` in `rust-toolchain.toml` (as of February 2022, nightly Rust and the RISC-V ecosystem are somewhat incompatible)
- `missing WiFi name/password`: ensure that you've configured `cfg.toml` according to `cfg.toml.example` - a common problem is that package name and config section name don't match. 

```toml
# Cargo.toml
#...
[package]
name = "http-client"
#...

# cfg.toml
[http-client]
wifi_ssid = "..."
wifi_psk = "..."
```