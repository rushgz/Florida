# Florida

Follow [FRIDA](https://github.com/frida/frida) upstream to automatically apply Florida patches and build anti-detection Frida artifacts for Android and iPhoneOS.

跟随 FRIDA 上游自动应用 Florida 补丁，并为 Android 与 iPhoneOS 构建反检测 Frida 产物。

**Hint: Don't fork this repository**

## iPhoneOS build

The workflow builds Android and iPhoneOS artifacts separately. The iPhoneOS target is `ios-arm64`; `iphoneos-arm64` is a package architecture name, not a Frida `configure --host` value.

The iPhoneOS job uses ad-hoc signing for jailbroken devices, matching the `frida-patch` build flow. No Apple certificate or GitHub Actions secret is required. To enable it, create this repository variable:

- `BUILD_IOS=true`

The job configures Frida with `IOS_CERTID="-"` and installed assets, then publishes the ad-hoc-signed arm64 server and agent after checking their Mach-O type, iPhoneOS load command, exported `frida_agent_main` symbol, architecture, signature structure, and entitlements.

For local builds on macOS:

```sh
export IOS_CERTID="-"
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

An ad-hoc-signed build is intended for compatible jailbroken devices; it is not trusted by stock iOS. Deployment still depends on the device architecture, jailbreak/runtime, entitlements, and a compatible transport. Copy both files to the paths expected by the target package or installation layout, then verify the connection with `frida-ps` and an attach test.

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
