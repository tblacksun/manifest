# 📦 LineageOS 14.1 Build Guide for Exynos 5410 (ja3gxx)

## 🔧 Initialize the Repository

```bash
repo init -u ssh://git@github.com/LineageOS/android.git -b cm-14.1
```

## 📁 Add Local Manifest

```bash
cp local_manifest.xml .repo/local_manifests/
```

## 🔄 Sync LineageOS 14.1 Sources

```bash
repo sync -j8
```

## 🩹 Apply Patches (Manually if Needed)

```bash
cd ../..

cd external/libselinux
git am ../../device/samsung/exynos5410-common/patches/external_libselinux/556a9e925053e0b62f495233e165112e12b5d869.patch

cd ../..
cd frameworks/base
git am ../../device/samsung/exynos5410-common/patches/frameworks_base/0001-DO-NOT-MERGE-PATCH-Zygote-Stop-breaking-the-entire-s.patch
git fetch https://github.com/LineageOS/android_frameworks_base refs/changes/45/169945/2 && git cherry-pick FETCH_HEAD

cd ../..
cd system/core
git am ../../device/samsung/exynos5410-common/patches/system_core/79ce3d6a96f3d381dc4db1aac45ccb788e1276ab.patch

cd ../..
```

## 🌍 Set Environment Variables

```bash
export LC_ALL=C
export LANG=C
```

## 🖼️ Fix Email App Drawable Issues

```bash
mkdir -p packages/apps/UnifiedEmail/res/drawable-mdpi-v19/
cp packages/apps/UnifiedEmail/res/drawable-mdpi/notification_bg_normal_pressed.9.png \
   packages/apps/UnifiedEmail/res/drawable-mdpi-v19/

mkdir -p packages/apps/UnifiedEmail/res/drawable-hdpi-v19/
cp packages/apps/UnifiedEmail/res/drawable-mdpi/notification_bg_normal_pressed.9.png \
   packages/apps/UnifiedEmail/res/drawable-hdpi-v19/

mkdir -p packages/apps/UnifiedEmail/res/drawable-xhdpi-v19/
cp packages/apps/UnifiedEmail/res/drawable-mdpi/notification_bg_normal_pressed.9.png \
   packages/apps/UnifiedEmail/res/drawable-xhdpi-v19/

mkdir -p packages/apps/UnifiedEmail/res/drawable-xxhdpi-v4/
cp packages/apps/UnifiedEmail/res/drawable-mdpi/notification_bg_normal_pressed.9.png \
   packages/apps/UnifiedEmail/res/drawable-xxhdpi-v4/

mkdir -p packages/apps/UnifiedEmail/res/drawable-xxhdpi-v19/
cp packages/apps/UnifiedEmail/res/drawable-mdpi/notification_bg_normal_pressed.9.png \
   packages/apps/UnifiedEmail/res/drawable-xxhdpi-v19/

rm packages/apps/UnifiedEmail/res/drawable-xxhdpi/notification_bg_normal_pressed.9.png
```

## ⚡ Enable CCache for Faster Builds

```bash
export USE_CCACHE=1
prebuilts/misc/linux-x86/ccache/ccache -M 50G
```

## 🛠️ Fix Jack Server SSL Error (Error 35)

Edit this file:

```bash
sudo nano /etc/java-8-openjdk/security/java.security
```

Find the line:

```properties
jdk.tls.disabledAlgorithms=...
```

Make sure **TLSv1** and **TLSv1.1** are not listed. Example:

```properties
jdk.tls.disabledAlgorithms=SSLv3, RC4, DES, MD5withRSA, \
DH keySize < 1024, EC keySize < 224, 3DES_EDE_CBC, anon, NULL, \
include jdk.disabled.namedCurves
```

## 🛠️ Fix Jack Server Error 58

```bash
cd prebuilts/sdk/tools
touch jack
./jack-admin kill-server
./jack-admin start-server
mm -j32 showcommands
./jack-admin install-server jack-launcher.jar jack-server-4.8.ALPHA.jar
```

## 🚀 Build the ROM

```bash
. build/envsetup.sh
lunch lineage_ja3gxx-userdebug
brunch lineage_ja3gxx-userdebug
```

## 🧪 Debugging the Build

```bash
brunch lineage_ja3gxx-userdebug | tee build.log
grep -A20 "FAILED:" build.log
```

## 🔁 Restart Jack Server (Optional)

```bash
./prebuilts/sdk/tools/jack-admin kill-server
./prebuilts/sdk/tools/jack-admin start-server
```
