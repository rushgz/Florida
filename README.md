# Florida

Follow [FRIDA](https://github.com/frida/frida) upstream to automatically apply Florida patches and build anti-detection Frida artifacts for Android and iPhoneOS.

跟随 FRIDA 上游自动应用 Florida 补丁，并为 Android 与 iPhoneOS 构建反检测 Frida 产物。

**Hint: Don't fork this repository**

## iPhoneOS build

The workflow builds Android and iPhoneOS artifacts separately. The iPhoneOS target is `ios-arm64`; `iphoneos-arm64` is a package architecture name, not a Frida `configure --host` value.

The iPhoneOS job requires a macOS runner with Xcode and an Apple development signing identity. Configure these repository secrets before setting `BUILD_IOS` to `true`:

- `APPLE_CERTIFICATES_P12`: base64-encoded signing certificate and private key
- `APPLE_CERTIFICATES_PASSWORD`: password for the PKCS#12 file
- `APPLE_KEYCHAIN_PASSWORD`: temporary keychain password
- `IOS_CERTID`: optional signing identity hash; when omitted, the first Apple Development identity is selected

The job configures Frida with installed assets and publishes the signed arm64 server and agent after checking their Mach-O type, iPhoneOS load command, exported `frida_agent_main` symbol, architecture, signature, and entitlements.

For local builds on macOS:

```sh
security find-identity -v -p codesigning
export IOS_CERTID="<Apple Development identity hash>"
git clone --recurse-submodules --branch 17.17.0 https://github.com/frida/frida
cd frida
for path in ../Florida/patches/*; do
  name=$(basename "$path")
  (cd "subprojects/$name" && git am ../../../Florida/patches/"$name"/*.patch)
done
mkdir build-ios-arm64
cd build-ios-arm64
../configure --prefix=/usr --host=ios-arm64 -- -Dfrida-core:assets=installed -Dfrida-core:server=enabled
make
DESTDIR="$PWD/../ios-staging" make install
```

The installed files are:

```text
ios-staging/usr/bin/frida-server
ios-staging/usr/lib/frida-1.0/frida-agent.dylib
```

A signed build is not by itself proof that a device can run or inject with it. Deployment still depends on the device architecture, jailbreak/runtime, entitlements, and a compatible transport. For a jailbroken device, copy both files to the paths expected by the target package or installation layout, then verify the connection with `frida-ps` and an attach test.

## Download

[Latest Release](https://github.com/Ylarod/Florida/releases/latest)

## References

- [https://github.com/hluwa/Patchs](https://github.com/hluwa/Patchs)
- [https://github.com/feicong/strong-frida](https://github.com/feicong/strong-frida)
- [https://github.com/qtfreet00/AntiFrida](https://github.com/qtfreet00/AntiFrida)
- [https://t.zsxq.com/miIunQN](https://t.zsxq.com/miIunQN)
- [https://github.com/darvincisec/DetectFrida](https://github.com/darvincisec/DetectFrida)
- [https://github.com/b-mueller/frida-detection-demo](https://github.com/b-mueller/frida-detection-demo)

## Thanks

- [@hluwa](https://github.com/hluwa)
- [@feicong](https://github.com/feicong)
- [@r0ysue](https://github.com/r0ysue)
- [@hellodword](https://github.com/hellodword)
- [@qtfreet00](https://github.com/qtfreet00)
