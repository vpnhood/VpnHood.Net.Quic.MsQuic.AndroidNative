# VpnHood.Net.Quic.MsQuic.AndroidNative

A **self-contained native QUIC package for Android**: [MsQuic](https://github.com/microsoft/msquic),
Microsoft's QUIC library, built from
[this fork](https://github.com/vpnhood/VpnHood.Net.Quic.MsQuic.AndroidNative) with OpenSSL for TLS, for
Android 7.0 (API 24) and later. It ships:

1. The **`libmsquic.so`** per ABI (`arm64-v8a`, `armeabi-v7a`, `x86_64`) — **built by CI and packed into the NuGet**
   (it flows into the consuming APK as `lib/<abi>/libmsquic.so`). The `.so` is **not committed**; it's
   git-ignored (`native/**/*.so`) and produced fresh on each build.
2. The **`Microsoft.Quic` C# P/Invoke bindings** — a committed, self-contained copy under `Bindings/`
   with **public** types, picked up by the SDK's default `.cs` globbing.

It has **no VpnHood dependencies**. Its whole job is to hide the native-linking complexity behind one
package reference.

## Consuming it

Reference the package at the version you tested, as `VpnHood.Net.Quic.Android` does:

```xml
<PackageReference Include="VpnHood.Net.Quic.MsQuic.AndroidNative" Version="x.y.z" />
```

Every push to this repository's `main` publishes a new native build, so a floating version would take one
nobody has run on a device.

A consumer gets both the bundled `.so` (transitively, into the APK) and the bindings. Because the binding
types are **public**, consumers use them directly — no `InternalsVisibleTo` needed.

## Publishing a new version

**Push to `main`** — GitHub Actions builds all ABIs, packs, and publishes `9.0.<run-number>` to
nuget.org (see [android/README.md](https://github.com/vpnhood/VpnHood.Net.Quic.MsQuic.AndroidNative/blob/main/android/README.md)).
No manual version bump and no local build needed.

For a **local** pack (rare): build the `.so` first with `android/build-android.ps1`; the
`RefreshMsQuicNative` MSBuild target copies it into `native/<abi>/` before packing. Without a freshly
built `.so` the build fails on purpose — there is no committed binary to fall back on.

`arm64-v8a`, `armeabi-v7a` (32-bit ARM, needed for Android TV devices) and `x86_64` are produced;
`x86` is intentionally excluded.

## License

MsQuic and its C# bindings are Microsoft's, under the MIT license, and the native library links OpenSSL,
under the Apache License 2.0. The package carries both texts in its `licenses` folder:
[LICENSE](https://github.com/vpnhood/VpnHood.Net.Quic.MsQuic.AndroidNative/blob/main/LICENSE) and
[THIRD-PARTY-NOTICES](https://github.com/vpnhood/VpnHood.Net.Quic.MsQuic.AndroidNative/blob/main/THIRD-PARTY-NOTICES).
