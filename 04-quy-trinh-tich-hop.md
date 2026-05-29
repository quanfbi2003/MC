# Tài liệu Quy trình Tích hợp Promon SHIELD

**Dự án:** Nghiên cứu & Đánh giá Giải pháp Bảo mật Ứng dụng Di động Promon SHIELD  
**Đơn vị thực hiện:** Phòng An ninh Thông tin – TSC (ANTT-TSC), VNPT Media  
**Phiên bản tài liệu:** 1.0  
**Ngày lập:** 2025  
**Yêu cầu liên quan:** Yêu cầu 4

---

## Mục lục

1. [Điều kiện tiên quyết môi trường](#1-điều-kiện-tiên-quyết-môi-trường)
2. [Quy trình post-build CLI cho Android APK](#2-quy-trình-post-build-cli-cho-android-apk)
3. [Quy trình post-build CLI cho Android AAB](#3-quy-trình-post-build-cli-cho-android-aab)
4. [Quy trình post-build CLI cho iOS IPA](#4-quy-trình-post-build-cli-cho-ios-ipa)
5. [Cấu trúc và giải thích file Blueprint JSON](#5-cấu-trúc-và-giải-thích-file-blueprint-json)
6. [Quy trình tích hợp CI/CD](#6-quy-trình-tích-hợp-cicd)
7. [Bước ký lại ứng dụng sau shielding](#7-bước-ký-lại-ứng-dụng-sau-shielding)
8. [Thay đổi backend cho App Verify](#8-thay-đổi-backend-cho-app-verify)
9. [Thay đổi mã nguồn ứng dụng](#9-thay-đổi-mã-nguồn-ứng-dụng)
10. [Đánh giá tác động đến dev team](#10-đánh-giá-tác-động-đến-dev-team)

---

## 1. Điều kiện tiên quyết môi trường

Trước khi thực hiện bất kỳ bước shielding nào, cần đảm bảo môi trường build đáp ứng đầy đủ các yêu cầu sau.

### 1.1 Yêu cầu chung (Android & iOS)

| Thành phần | Phiên bản tối thiểu | Ghi chú |
|---|---|---|
| **Java SE Development Kit (JDK)** | JDK 11 (LTS) | Shielder tool (`ai.digital.protect-android.jar`) chạy trên JVM. Khuyến nghị JDK 17 LTS cho môi trường production |
| **Bộ nhớ RAM** | 8 GB | Tối thiểu 4 GB dành riêng cho JVM heap (`-Xmx4g`) |
| **Dung lượng đĩa** | 10 GB trống | Bao gồm không gian làm việc tạm thời trong quá trình shielding |
| **Hệ điều hành** | Windows 10+, Ubuntu 18.04+, macOS 11+ | iOS shielding bắt buộc phải dùng macOS |

### 1.2 Yêu cầu cho Android

| Thành phần | Phiên bản tối thiểu | Ghi chú |
|---|---|---|
| **Android SDK** | API Level 21 (Android 5.0) | Build Tools phiên bản 30.0.3 trở lên |
| **Android Build Tools** | 30.0.3+ | Cần `zipalign`, `apksigner` trong PATH |
| **Signing Keystore** | JKS hoặc PKCS12 | Keystore dùng để ký lại APK/AAB sau shielding |
| **Shielder Tool** | protect-android 4.11.0+ | File `ai.digital.protect-android.jar` hoặc `protect-android.exe` |
| **Python** | 3.8+ | Tùy chọn — dùng cho script `generate_keepSignatures.py` và `deobfuscate.py` |

### 1.3 Yêu cầu cho iOS

| Thành phần | Phiên bản tối thiểu | Ghi chú |
|---|---|---|
| **macOS** | macOS 11 (Big Sur) | Bắt buộc — iOS shielding không hỗ trợ Windows/Linux |
| **Xcode** | Xcode 13+ | Cần `codesign`, `xcrun`, `altool` trong PATH |
| **iOS Deployment Target** | iOS 12.0 | Ứng dụng phải hỗ trợ iOS 12 trở lên |
| **Apple Developer Certificate** | Distribution Certificate | Provisioning Profile loại App Store hoặc Ad Hoc |
| **Provisioning Profile** | Hợp lệ, chưa hết hạn | Phải khớp với Bundle ID của ứng dụng |
| **Shielder Tool (iOS)** | Phiên bản tương ứng | Liên hệ Promon để nhận iOS Shielder |

### 1.4 Kiểm tra môi trường trước khi bắt đầu

```bash
# Kiểm tra Java
java -version
# Kết quả mong đợi: openjdk version "17.x.x" hoặc "11.x.x"

# Kiểm tra Android Build Tools (Windows)
%ANDROID_HOME%\build-tools\30.0.3\zipalign -version
%ANDROID_HOME%\build-tools\30.0.3\apksigner version

# Kiểm tra Xcode (macOS)
xcode-select --version
xcrun --version

# Kiểm tra Shielder tool
java -jar ai.digital.protect-android.jar --version
# Hoặc trên Windows:
protect-android.exe --version
```

---

## 2. Quy trình post-build CLI cho Android APK

Quy trình shielding APK gồm 7 bước tuần tự. Bước 1–3 là chuẩn bị, bước 4 là shielding chính, bước 5–7 là ký lại và kiểm tra.

### Bước 1: Build APK release (chưa ký hoặc đã ký debug)

```bash
# Trong thư mục dự án Android
./gradlew assembleRelease

# Kết quả: app/build/outputs/apk/release/app-release-unsigned.apk
# Hoặc nếu đã cấu hình signing trong build.gradle:
# app/build/outputs/apk/release/app-release.apk
```

> **Lưu ý:** Nên build APK chưa ký (unsigned) trước khi shield, vì Shielder sẽ yêu cầu ký lại sau khi hoàn tất. Nếu APK đã ký, Shielder sẽ tự động xóa chữ ký cũ.

### Bước 2: Chuẩn bị file Blueprint JSON

Tạo file `blueprint.json` cho ứng dụng (xem chi tiết cấu trúc tại [Mục 5](#5-cấu-trúc-và-giải-thích-file-blueprint-json)):

```bash
# Tạo thư mục làm việc
mkdir -p /opt/shielding/myapp
cd /opt/shielding/myapp

# Cấu trúc thư mục làm việc
/opt/shielding/myapp/
├── input/
│   └── app-release-unsigned.apk
├── output/          # Shielder sẽ tạo file output tại đây
├── blueprint.json   # File cấu hình shielding
└── proguard.txt     # (Tùy chọn) Danh sách signature cần giữ nguyên
```

Nội dung `blueprint.json` tối thiểu cho production:

```json
{
  "targets": {
    "input": "input/app-release-unsigned.apk",
    "output": "output/"
  },
  "globalConfiguration": {
    "verbosityLevel": { "global": 1 },
    "keepSignatures": "proguard.txt"
  },
  "guardConfiguration": {
    "renaming": { "disable": false, "renameInResources": true },
    "stringEncryption": { "disable": false },
    "debugInfoStrip": { "disable": false },
    "rootDetection": [{
      "disable": false,
      "name": "Root detection",
      "invocationLocations": [{ "type": "auto" }],
      "tamperAction": { "type": "fail" }
    }],
    "debuggerDetection": [{
      "disable": false,
      "name": "Debugger detection",
      "invocationLocations": [{ "type": "auto" }],
      "tamperAction": { "type": "fail" }
    }]
  }
}
```

### Bước 3: (Tùy chọn) Tạo file keepSignatures để tránh lỗi reflection

```bash
# Sử dụng script có sẵn để tạo keepSignatures từ ProGuard mapping
python3 generate_keepSignatures.py \
  --mapping app/build/outputs/mapping/release/mapping.txt \
  --output proguard.txt

# Hoặc tạo thủ công — liệt kê các method/class cần giữ nguyên tên
# (các class dùng reflection, interface với backend, v.v.)
```

### Bước 4: Thực thi Shielder tool (bước chính)

```bash
# Trên Linux/macOS
java -Xmx4g -jar /opt/protect-android/bin/ai.digital.protect-android.jar \
  --blueprint blueprint.json \
  --license /path/to/license.key

# Trên Windows
protect-android.exe \
  --blueprint blueprint.json \
  --license C:\promon\license.key

# Với verbose output để debug
java -Xmx4g -jar ai.digital.protect-android.jar \
  --blueprint blueprint.json \
  --license license.key \
  --verbose

# Kết quả: output/app-release-unsigned_protected.apk
```

> **Thời gian xử lý:** Tùy thuộc vào kích thước APK và số lượng guard được bật. APK ~20MB thường mất 2–5 phút.

### Bước 5: Căn chỉnh APK (zipalign)

```bash
# zipalign là bắt buộc trước khi ký APK
$ANDROID_HOME/build-tools/30.0.3/zipalign -v -p 4 \
  output/app-release-unsigned_protected.apk \
  output/app-release-protected-aligned.apk

# Kiểm tra kết quả zipalign
$ANDROID_HOME/build-tools/30.0.3/zipalign -c -v 4 \
  output/app-release-protected-aligned.apk
```

### Bước 6: Ký lại APK với keystore production

```bash
# Ký bằng apksigner (khuyến nghị — hỗ trợ APK Signature Scheme v2/v3/v4)
$ANDROID_HOME/build-tools/30.0.3/apksigner sign \
  --ks /path/to/release.keystore \
  --ks-key-alias myapp-key \
  --ks-pass pass:${KEYSTORE_PASSWORD} \
  --key-pass pass:${KEY_PASSWORD} \
  --out output/app-release-protected-signed.apk \
  output/app-release-protected-aligned.apk

# Xác minh chữ ký
$ANDROID_HOME/build-tools/30.0.3/apksigner verify \
  --verbose output/app-release-protected-signed.apk
```

### Bước 7: Kiểm tra và xác nhận

```bash
# Kiểm tra kích thước file trước và sau
ls -lh input/app-release-unsigned.apk
ls -lh output/app-release-protected-signed.apk

# Cài thử trên thiết bị test (không root)
adb install output/app-release-protected-signed.apk

# Kiểm tra log khởi động
adb logcat | grep -i "promon\|shield\|protect"

# Kiểm tra ứng dụng hoạt động bình thường
# (chạy smoke test tự động hoặc kiểm tra thủ công)
```

---

## 3. Quy trình post-build CLI cho Android AAB

Android App Bundle (AAB) là định dạng phân phối qua Google Play. Quy trình shielding AAB tương tự APK nhưng có một số điểm khác biệt quan trọng.

### Bước 1: Build AAB release

```bash
# Build AAB release
./gradlew bundleRelease

# Kết quả: app/build/outputs/bundle/release/app-release.aab
```

### Bước 2: Chuẩn bị Blueprint JSON cho AAB

Sử dụng file `blueprint-aab.json` riêng biệt (cấu trúc tương tự APK nhưng trỏ đến file `.aab`):

```json
{
  "targets": {
    "input": "input/app-release.aab",
    "output": "output/"
  },
  "globalConfiguration": {
    "verbosityLevel": { "global": 1 },
    "keepSignatures": "proguard.txt",
    "nativeGuardsComponent": {
      "disable": false,
      "supportedABIs": ["arm64-v8a", "armeabi-v7a", "x86", "x86_64"]
    }
  },
  "guardConfiguration": {
    "renaming": { "disable": false, "renameInResources": true },
    "stringEncryption": { "disable": false },
    "rootDetection": [{
      "disable": false,
      "name": "Root detection",
      "invocationLocations": [{ "type": "auto" }],
      "tamperAction": { "type": "fail" }
    }]
  }
}
```

> **Lưu ý AAB:** Đường dẫn resource trong AAB có prefix `base/` (ví dụ: `base/assets/fonts/font.ttf` thay vì `assets/fonts/font.ttf`). Cần điều chỉnh trong cấu hình `resourceVerification`.

### Bước 3: Thực thi Shielder tool cho AAB

```bash
java -Xmx4g -jar ai.digital.protect-android.jar \
  --blueprint blueprint-aab.json \
  --license license.key

# Kết quả: output/app-release_protected.aab
```

### Bước 4: Ký lại AAB

```bash
# Ký AAB bằng jarsigner (apksigner không hỗ trợ AAB)
jarsigner \
  -verbose \
  -sigalg SHA256withRSA \
  -digestalg SHA-256 \
  -keystore /path/to/release.keystore \
  -storepass ${KEYSTORE_PASSWORD} \
  -keypass ${KEY_PASSWORD} \
  output/app-release_protected.aab \
  myapp-key

# Xác minh
jarsigner -verify -verbose output/app-release_protected.aab
```

### Bước 5: Upload lên Google Play

```bash
# Sử dụng Google Play Developer API hoặc fastlane
fastlane supply \
  --aab output/app-release_protected.aab \
  --track internal \
  --json_key /path/to/google-play-key.json \
  --package_name com.vnptmedia.myapp

# Hoặc upload thủ công qua Google Play Console
```

> **Lưu ý quan trọng về AAB và Google Play App Signing:** Nếu sử dụng Google Play App Signing, Google sẽ ký lại AAB bằng key của họ sau khi upload. Trong trường hợp này, `signatureCheck` trong blueprint cần được cấu hình với public key SHA256 của Google Play signing key (lấy từ Play Console → Setup → App signing).

### Bước 6: Kiểm tra bằng bundletool

```bash
# Tải bundletool từ Google
# https://github.com/google/bundletool/releases

# Build APK set từ AAB để test local
java -jar bundletool.jar build-apks \
  --bundle=output/app-release_protected.aab \
  --output=output/app-release.apks \
  --ks=/path/to/release.keystore \
  --ks-key-alias=myapp-key \
  --ks-pass=pass:${KEYSTORE_PASSWORD}

# Cài trên thiết bị kết nối
java -jar bundletool.jar install-apks \
  --apks=output/app-release.apks
```

### Bước 7: Xác nhận hoạt động

```bash
# Kiểm tra log
adb logcat | grep -i "promon\|shield"

# Chạy smoke test
adb shell am start -n com.vnptmedia.myapp/.MainActivity
```

---

## 4. Quy trình post-build CLI cho iOS IPA

iOS shielding **bắt buộc** thực hiện trên macOS với Xcode đã cài đặt. Shielder tool cho iOS là công cụ riêng biệt (liên hệ Promon để nhận).

### Bước 1: Build IPA release từ Xcode

```bash
# Cách 1: Build từ command line với xcodebuild
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -configuration Release \
  -archivePath build/MyApp.xcarchive \
  archive

# Export IPA từ archive
xcodebuild \
  -exportArchive \
  -archivePath build/MyApp.xcarchive \
  -exportOptionsPlist ExportOptions.plist \
  -exportPath build/output/

# Kết quả: build/output/MyApp.ipa
```

Nội dung file `ExportOptions.plist` mẫu:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>method</key>
  <string>app-store</string>
  <!-- Hoặc: ad-hoc, enterprise, development -->
  <key>teamID</key>
  <string>XXXXXXXXXX</string>
  <key>signingStyle</key>
  <string>manual</string>
  <key>provisioningProfiles</key>
  <dict>
    <key>com.vnptmedia.myapp</key>
    <string>MyApp Distribution Profile</string>
  </dict>
</dict>
</plist>
```

### Bước 2: Chuẩn bị Blueprint JSON cho iOS

iOS Shielder sử dụng cấu trúc blueprint tương tự Android nhưng với các guard đặc thù iOS:

```json
{
  "targets": {
    "input": "input/MyApp.ipa",
    "output": "output/"
  },
  "globalConfiguration": {
    "verbosityLevel": { "global": 1 }
  },
  "guardConfiguration": {
    "jailbreakDetection": [{
      "disable": false,
      "name": "Jailbreak detection",
      "invocationLocations": [{ "type": "auto" }],
      "tamperAction": { "type": "fail" }
    }],
    "debuggerDetection": [{
      "disable": false,
      "name": "Debugger detection",
      "invocationLocations": [{ "type": "auto" }],
      "tamperAction": { "type": "fail" }
    }],
    "emulatorDetection": [{
      "disable": false,
      "name": "Simulator detection",
      "invocationLocations": [{ "type": "auto" }],
      "tamperAction": { "type": "fail" }
    }],
    "signatureCheck": [{
      "disable": false,
      "name": "Signature check",
      "invocationLocations": [{ "type": "auto" }],
      "tamperAction": { "type": "fail" }
    }],
    "stringEncryption": { "disable": false },
    "debugInfoStrip": { "disable": false }
  }
}
```

### Bước 3: Giải nén IPA và chuẩn bị

```bash
# IPA thực chất là file ZIP
mkdir -p work/ipa_extracted
cp input/MyApp.ipa work/MyApp.zip
unzip work/MyApp.zip -d work/ipa_extracted/

# Cấu trúc bên trong:
# work/ipa_extracted/Payload/MyApp.app/
```

### Bước 4: Thực thi iOS Shielder

```bash
# Chạy iOS Shielder (tên tool có thể khác tùy phiên bản)
./promon-shield-ios \
  --blueprint blueprint-ios.json \
  --license license.key

# Hoặc với Java wrapper nếu có
java -Xmx4g -jar promon-shield-ios.jar \
  --blueprint blueprint-ios.json \
  --license license.key

# Kết quả: output/MyApp_protected.ipa (hoặc thư mục .app)
```

### Bước 5: Ký lại IPA với Distribution Certificate

```bash
# Xóa chữ ký cũ
codesign --remove-signature output/ipa_extracted/Payload/MyApp.app

# Ký lại tất cả framework và extension trước
find output/ipa_extracted/Payload/MyApp.app/Frameworks -name "*.framework" | while read fw; do
  codesign --force --sign "iPhone Distribution: VNPT Media JSC (XXXXXXXXXX)" \
    --entitlements MyApp.entitlements "$fw"
done

# Ký lại app chính
codesign \
  --force \
  --sign "iPhone Distribution: VNPT Media JSC (XXXXXXXXXX)" \
  --entitlements MyApp.entitlements \
  --timestamp \
  output/ipa_extracted/Payload/MyApp.app

# Xác minh chữ ký
codesign --verify --verbose=4 output/ipa_extracted/Payload/MyApp.app
```

### Bước 6: Đóng gói lại thành IPA

```bash
# Đóng gói lại
cd output/ipa_extracted
zip -r ../MyApp_protected_signed.ipa Payload/

# Kiểm tra kích thước
ls -lh ../MyApp_protected_signed.ipa
```

### Bước 7: Kiểm tra và upload

```bash
# Validate IPA trước khi upload App Store
xcrun altool --validate-app \
  --file output/MyApp_protected_signed.ipa \
  --type ios \
  --apiKey ${APP_STORE_CONNECT_API_KEY} \
  --apiIssuer ${APP_STORE_CONNECT_ISSUER_ID}

# Upload lên App Store Connect
xcrun altool --upload-app \
  --file output/MyApp_protected_signed.ipa \
  --type ios \
  --apiKey ${APP_STORE_CONNECT_API_KEY} \
  --apiIssuer ${APP_STORE_CONNECT_ISSUER_ID}

# Hoặc dùng fastlane deliver
fastlane deliver \
  --ipa output/MyApp_protected_signed.ipa \
  --skip_screenshots \
  --skip_metadata
```

---

## 5. Cấu trúc và giải thích file Blueprint JSON

File Blueprint JSON là trung tâm của toàn bộ quy trình shielding — nó định nghĩa **những gì cần bảo vệ**, **cách bảo vệ**, và **phản ứng khi phát hiện tấn công**. Phân tích dựa trên file mẫu `blueprint.json` trong thư mục `samples/java/`.

### 5.1 Cấu trúc tổng thể

```
blueprint.json
├── targets                    # Đường dẫn input/output
├── globalConfiguration        # Cấu hình toàn cục
│   ├── seed                   # Seed cho randomization
│   ├── exclude[]              # Loại trừ khỏi tất cả guard
│   ├── include[]              # Ghi đè exclude
│   ├── verbosityLevel         # Mức độ log
│   ├── appAware               # Cấu hình Promon Insight (telemetry)
│   ├── keepSignatures         # File ProGuard để giữ nguyên tên
│   └── nativeGuardsComponent  # Cấu hình native library protection
└── guardConfiguration         # Cấu hình từng guard
    ├── [Obfuscation Guards]   # renaming, controlFlowFlattening, v.v.
    └── [Runtime Guards]       # rootDetection, debuggerDetection, v.v.
```

### 5.2 Phần `targets` — Đường dẫn file

```json
"targets": {
  "input": "input/app-release.apk",  // Đường dẫn tương đối đến APK/AAB/IPA
  "output": "output/"                 // Thư mục chứa file đã shield
}
```

### 5.3 Phần `globalConfiguration` — Cấu hình toàn cục

```json
"globalConfiguration": {
  // Seed cho quá trình randomization — đảm bảo reproducible builds
  // Thay đổi seed sẽ tạo ra binary khác nhau (tốt cho security)
  "seed": 12345,

  // Loại trừ class/method/field khỏi TẤT CẢ guard
  // Dùng cho: class dùng reflection, interface với thư viện bên thứ ba
  "exclude": [
    {
      "type": "class",           // Loại: class | method | field
      "name": "com.example.*"    // Hỗ trợ wildcard (*)
    },
    {
      "type": "method",
      "name": "com.example.MyClass.myMethod",
      "argumentTypes": ["android.content.Context"]  // Phân biệt overload
    }
  ],

  // Ghi đè exclude — các item này SẼ được bảo vệ dù nằm trong exclude
  "include": [
    { "type": "method", "name": "com.example.MyClass.sensitiveMethod" }
  ],

  // Mức độ verbose: 0=tắt, 1=thông thường, 2=chi tiết, 3=debug
  "verbosityLevel": { "global": 1 },

  // Cấu hình Promon Insight (telemetry analytics)
  "appAware": {
    "disable": false,
    "debug": false,                                    // true chỉ dùng khi test
    "endpointURL": "https://insight.vnptmedia.vn",    // URL Insight Agent
    "applicationToken": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "checkpoints": {
      "startupCheckpoint": {
        "invocationLocation": "com.vnptmedia.app.MainActivity.onCreate"
      }
    },
    "heartbeat": {
      "disable": false,
      "interval": 60,      // Gửi heartbeat mỗi 60 giây
      "initialDelay": 5    // Delay 5 giây sau khi khởi động
    }
  },

  // File chứa danh sách method signature cần giữ nguyên tên
  // (tránh lỗi reflection sau khi obfuscation)
  "keepSignatures": "proguard.txt",

  // Cấu hình bảo vệ native library (.so files)
  "nativeGuardsComponent": {
    "disable": false,
    "supportedABIs": ["arm64-v8a", "armeabi-v7a", "x86", "x86_64"]
  }
}
```

### 5.4 Phần `guardConfiguration` — Obfuscation Guards

| Guard | Mô tả | Rủi ro |
|---|---|---|
| `renaming` | Đổi tên class, method, field | Có thể gây lỗi reflection — cần `keepSignatures` |
| `controlFlowFlattening` | Làm phẳng luồng điều khiển | Tăng kích thước code nhẹ |
| `staticMemberShuffling` | Xáo trộn thứ tự static member | Thấp |
| `inlining` | Inline method nhỏ vào caller | Tăng kích thước code |
| `stringEncryption` | Mã hóa string literals | Thấp |
| `annotationEncryption` | Mã hóa annotation | Thấp |
| `operatorRemoval` | Ẩn operator | Thấp |
| `literalHiding` | Ẩn literal values | Thấp |
| `debugInfoStrip` | Xóa thông tin debug | Thấp |
| `deadCodeInjection` | Chèn code giả | Tăng kích thước APK |
| `callHiding` | Ẩn method call | Thấp |
| `classEncryption` | Mã hóa toàn bộ class | Tăng thời gian load |
| `logStrip` | Xóa log statement | Thấp |
| `resourceEncryption` | Mã hóa file asset | Thấp |

### 5.5 Phần `guardConfiguration` — Runtime Guards

Mỗi runtime guard có cấu trúc chung:

```json
"rootDetection": [
  {
    "name": "Root detection instance 1",  // Tên định danh (tùy ý)
    "disable": false,                      // false = bật guard
    "debug": false,                        // true = in log vào logcat (chỉ dùng khi test)
    "executeOnBackgroundThread": true,     // Chạy trên background thread

    // Vị trí kích hoạt guard
    "invocationLocations": [
      // Kích hoạt tại method cụ thể
      { "type": "method", "name": "com.example.MainActivity.onCreate" },
      // Kích hoạt tự động (Shielder chọn vị trí tối ưu)
      { "type": "auto" },
      // Kích hoạt định kỳ
      { "type": "periodic", "initialDelay": 30, "interval": 60, "probability": 80 },
      // Kích hoạt khi khởi động
      { "type": "startup", "initialDelay": 10 }
    ],

    // Hành động khi phát hiện tấn công (tamper)
    "tamperAction": {
      // Gọi method tùy chỉnh trong app
      "type": "method",
      "name": "com.example.SecurityHandler.onThreatDetected",
      "immediateAction": true   // true = thực thi ngay, false = sau khi method hiện tại kết thúc
      // Hoặc: "type": "fail"       — thoát ứng dụng ngay lập tức
      // Hoặc: "type": "doNothing"  — không làm gì (chỉ dùng khi test)
    },

    // Hành động khi KHÔNG phát hiện tấn công (bình thường)
    "nonTamperAction": {
      "type": "method",
      "name": "com.example.SecurityHandler.onEnvironmentClean"
      // Hoặc: "type": "doNothing"
    }
  }
]
```

**Danh sách runtime guard có sẵn:**

| Guard | Phát hiện |
|---|---|
| `rootDetection` | Root/Jailbreak, Magisk, SuperSU |
| `debuggerDetection` | Debugger attach, jdb, LLDB |
| `hookDetection` | Xposed, LSPosed, native hooks |
| `dynamicInstrumentationDetection` | Frida, GameGuardian |
| `emulatorDetection` | Android AVD, Genymotion, BlueStacks |
| `signatureCheck` | Repackaging, chữ ký không khớp |
| `checksum` | Tampering DEX/binary |
| `resourceVerification` | Tampering file asset/resource |
| `maliciousPackageDetection` | Malware đã biết (StrandHogg, Cerberus) |
| `virtualizationDetection` | Virtual Space, Parallel Space |
| `virtualControlDetection` | Accessibility Service abuse |
| `customGuard` | Logic tùy chỉnh do developer định nghĩa |

---

## 6. Quy trình tích hợp CI/CD

### 6.1 Tổng quan pipeline tích hợp

```
[Developer Push Code]
        │
        ▼
[Build Stage]          → Build APK/AAB/IPA release
        │
        ▼
[Shield Stage]         → Chạy Shielder CLI với blueprint.json
        │
        ▼
[Sign Stage]           → Ký lại với production keystore/certificate
        │
        ▼
[Test Stage]           → Smoke test trên thiết bị/emulator
        │
        ▼
[Deploy Stage]         → Upload lên Google Play / App Store Connect
```

### 6.2 Tích hợp Jenkins

```groovy
// Jenkinsfile
pipeline {
    agent { label 'android-build-agent' }

    environment {
        ANDROID_HOME = '/opt/android-sdk'
        SHIELDER_JAR = '/opt/promon/bin/ai.digital.protect-android.jar'
        PROMON_LICENSE = credentials('promon-license-key')
        KEYSTORE_FILE = credentials('android-release-keystore')
        KEYSTORE_PASSWORD = credentials('android-keystore-password')
        KEY_ALIAS = 'myapp-release'
        KEY_PASSWORD = credentials('android-key-password')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Release APK') {
            steps {
                sh './gradlew assembleRelease'
            }
        }

        stage('Shield APK') {
            steps {
                // Chuẩn bị thư mục làm việc
                sh 'mkdir -p shielding/input shielding/output'
                sh 'cp app/build/outputs/apk/release/app-release-unsigned.apk shielding/input/'

                // Chạy Shielder
                sh """
                    java -Xmx4g -jar ${SHIELDER_JAR} \\
                        --blueprint shielding/blueprint.json \\
                        --license ${PROMON_LICENSE}
                """
            }
        }

        stage('Sign APK') {
            steps {
                // Zipalign
                sh """
                    ${ANDROID_HOME}/build-tools/30.0.3/zipalign -v -p 4 \\
                        shielding/output/app-release-unsigned_protected.apk \\
                        shielding/output/app-release-protected-aligned.apk
                """

                // Sign
                sh """
                    ${ANDROID_HOME}/build-tools/30.0.3/apksigner sign \\
                        --ks ${KEYSTORE_FILE} \\
                        --ks-key-alias ${KEY_ALIAS} \\
                        --ks-pass pass:${KEYSTORE_PASSWORD} \\
                        --key-pass pass:${KEY_PASSWORD} \\
                        --out shielding/output/app-release-final.apk \\
                        shielding/output/app-release-protected-aligned.apk
                """
            }
        }

        stage('Smoke Test') {
            steps {
                // Cài và test trên emulator/thiết bị
                sh 'adb install -r shielding/output/app-release-final.apk'
                sh './gradlew connectedAndroidTest'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'shielding/output/app-release-final.apk'
            }
        }
    }

    post {
        always {
            // Dọn dẹp file tạm
            sh 'rm -rf shielding/input shielding/output'
        }
        failure {
            emailext(
                subject: "Shield Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Xem chi tiết tại: ${env.BUILD_URL}",
                to: 'antt-tsc@vnptmedia.vn'
            )
        }
    }
}
```

### 6.3 Tích hợp GitLab CI

```yaml
# .gitlab-ci.yml
stages:
  - build
  - shield
  - sign
  - test
  - deploy

variables:
  ANDROID_HOME: /opt/android-sdk
  SHIELDER_JAR: /opt/promon/bin/ai.digital.protect-android.jar

build_release:
  stage: build
  image: gradle:7.6-jdk17
  script:
    - ./gradlew assembleRelease
  artifacts:
    paths:
      - app/build/outputs/apk/release/app-release-unsigned.apk
    expire_in: 1 hour

shield_apk:
  stage: shield
  image: openjdk:17-slim
  dependencies:
    - build_release
  script:
    - mkdir -p shielding/input shielding/output
    - cp app/build/outputs/apk/release/app-release-unsigned.apk shielding/input/
    - |
      java -Xmx4g -jar ${SHIELDER_JAR} \
        --blueprint shielding/blueprint.json \
        --license ${PROMON_LICENSE_KEY}
  artifacts:
    paths:
      - shielding/output/
    expire_in: 1 hour

sign_apk:
  stage: sign
  image: android-build:latest
  dependencies:
    - shield_apk
  script:
    - echo "${KEYSTORE_BASE64}" | base64 -d > release.keystore
    - |
      ${ANDROID_HOME}/build-tools/30.0.3/zipalign -v -p 4 \
        shielding/output/app-release-unsigned_protected.apk \
        shielding/output/app-release-aligned.apk
    - |
      ${ANDROID_HOME}/build-tools/30.0.3/apksigner sign \
        --ks release.keystore \
        --ks-key-alias ${KEY_ALIAS} \
        --ks-pass pass:${KEYSTORE_PASSWORD} \
        --key-pass pass:${KEY_PASSWORD} \
        --out shielding/output/app-release-final.apk \
        shielding/output/app-release-aligned.apk
    - rm -f release.keystore
  artifacts:
    paths:
      - shielding/output/app-release-final.apk
    expire_in: 7 days

deploy_internal:
  stage: deploy
  script:
    - fastlane supply --aab shielding/output/app-release-final.apk --track internal
  only:
    - main
```

### 6.4 Tích hợp GitHub Actions

```yaml
# .github/workflows/shield-and-release.yml
name: Shield and Release

on:
  push:
    tags:
      - 'v*'

jobs:
  build-shield-sign:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Build Release APK
        run: ./gradlew assembleRelease

      - name: Download Shielder
        run: |
          # Download từ artifact storage nội bộ
          curl -H "Authorization: Bearer ${{ secrets.ARTIFACT_TOKEN }}" \
            -o shielder.jar \
            https://artifacts.vnptmedia.vn/promon/ai.digital.protect-android.jar

      - name: Shield APK
        run: |
          mkdir -p shielding/input shielding/output
          cp app/build/outputs/apk/release/app-release-unsigned.apk shielding/input/
          java -Xmx4g -jar shielder.jar \
            --blueprint shielding/blueprint.json \
            --license "${{ secrets.PROMON_LICENSE_KEY }}"

      - name: Sign APK
        run: |
          echo "${{ secrets.KEYSTORE_BASE64 }}" | base64 -d > release.keystore
          $ANDROID_HOME/build-tools/30.0.3/zipalign -v -p 4 \
            shielding/output/app-release-unsigned_protected.apk \
            shielding/output/app-release-aligned.apk
          $ANDROID_HOME/build-tools/30.0.3/apksigner sign \
            --ks release.keystore \
            --ks-key-alias "${{ secrets.KEY_ALIAS }}" \
            --ks-pass "pass:${{ secrets.KEYSTORE_PASSWORD }}" \
            --key-pass "pass:${{ secrets.KEY_PASSWORD }}" \
            --out shielding/output/app-release-final.apk \
            shielding/output/app-release-aligned.apk

      - name: Upload to Google Play
        uses: r0adkll/upload-google-play@v1
        with:
          serviceAccountJsonPlainText: ${{ secrets.GOOGLE_PLAY_SERVICE_ACCOUNT }}
          packageName: com.vnptmedia.myapp
          releaseFiles: shielding/output/app-release-final.apk
          track: internal
```

---

## 7. Bước ký lại ứng dụng sau shielding

Sau khi Shielder xử lý, ứng dụng **mất chữ ký cũ** (Shielder xóa để tránh chữ ký không hợp lệ). Bước ký lại là **bắt buộc** trước khi phân phối.

### 7.1 Android — Ký lại APK

#### Chuẩn bị keystore

```bash
# Tạo keystore mới (chỉ làm một lần)
keytool -genkey -v \
  -keystore release.keystore \
  -alias myapp-release \
  -keyalg RSA \
  -keysize 2048 \
  -validity 10000 \
  -dname "CN=VNPT Media, OU=Mobile, O=VNPT Media JSC, L=Hanoi, ST=Hanoi, C=VN"

# Xem thông tin keystore
keytool -list -v -keystore release.keystore

# Lấy SHA256 fingerprint (dùng cho signatureCheck trong blueprint)
keytool -list -v -keystore release.keystore | grep "SHA256:"
```

#### Quy trình ký APK (chi tiết)

```bash
# Bước 1: Zipalign (bắt buộc trước khi ký với apksigner)
$ANDROID_HOME/build-tools/30.0.3/zipalign \
  -v -p 4 \
  app_protected.apk \
  app_protected_aligned.apk

# Bước 2: Ký với apksigner (hỗ trợ v1/v2/v3/v4 signature scheme)
$ANDROID_HOME/build-tools/30.0.3/apksigner sign \
  --ks release.keystore \
  --ks-key-alias myapp-release \
  --ks-pass pass:${KEYSTORE_PASS} \
  --key-pass pass:${KEY_PASS} \
  --v1-signing-enabled true \
  --v2-signing-enabled true \
  --v3-signing-enabled true \
  --out app_protected_signed.apk \
  app_protected_aligned.apk

# Bước 3: Xác minh
$ANDROID_HOME/build-tools/30.0.3/apksigner verify \
  --verbose \
  --print-certs \
  app_protected_signed.apk
```

> **Lưu ý về Signature Scheme:** Android 11+ yêu cầu APK Signature Scheme v2 trở lên. Nên bật cả v1, v2, v3 để tương thích tối đa.

#### Lấy SHA256 public key để cấu hình signatureCheck

```bash
# Lấy SHA256 của public key từ APK đã ký
$ANDROID_HOME/build-tools/30.0.3/apksigner verify \
  --print-certs app_protected_signed.apk | grep "SHA-256"

# Hoặc từ keystore
keytool -list -v -keystore release.keystore -alias myapp-release | grep "SHA256"

# Giá trị này dùng trong blueprint.json:
# "signaturePublicKeySHA256": ["6654a6d1e55776c596f788aae85c15c22d268f7c5696c7397bc52e9393fe793f"]
```

### 7.2 Android — Ký lại AAB

```bash
# AAB dùng jarsigner (không dùng apksigner)
jarsigner \
  -verbose \
  -sigalg SHA256withRSA \
  -digestalg SHA-256 \
  -keystore release.keystore \
  -storepass ${KEYSTORE_PASS} \
  -keypass ${KEY_PASS} \
  app_protected.aab \
  myapp-release

# Xác minh
jarsigner -verify -verbose -certs app_protected.aab
```

### 7.3 iOS — Ký lại IPA

#### Chuẩn bị certificate và provisioning profile

```bash
# Import certificate vào Keychain
security import Distribution_Certificate.p12 \
  -k ~/Library/Keychains/login.keychain \
  -P ${CERT_PASSWORD} \
  -T /usr/bin/codesign

# Cài provisioning profile
cp MyApp_Distribution.mobileprovision \
  ~/Library/MobileDevice/Provisioning\ Profiles/

# Lấy UUID của provisioning profile
/usr/libexec/PlistBuddy -c 'Print :UUID' \
  /dev/stdin <<< $(security cms -D -i MyApp_Distribution.mobileprovision)
```

#### Quy trình ký lại IPA

```bash
# Bước 1: Giải nén IPA
mkdir -p work/Payload
cp app_protected.ipa work/app_protected.zip
unzip work/app_protected.zip -d work/

# Bước 2: Xóa chữ ký cũ
codesign --remove-signature work/Payload/MyApp.app

# Bước 3: Ký lại framework (nếu có)
for framework in work/Payload/MyApp.app/Frameworks/*.framework; do
  codesign \
    --force \
    --sign "iPhone Distribution: VNPT Media JSC (XXXXXXXXXX)" \
    --timestamp \
    "$framework"
done

# Bước 4: Ký lại app chính với entitlements
codesign \
  --force \
  --sign "iPhone Distribution: VNPT Media JSC (XXXXXXXXXX)" \
  --entitlements MyApp.entitlements \
  --timestamp \
  work/Payload/MyApp.app

# Bước 5: Xác minh
codesign --verify --verbose=4 work/Payload/MyApp.app
spctl --assess --verbose work/Payload/MyApp.app

# Bước 6: Đóng gói lại
cd work && zip -r ../app_protected_signed.ipa Payload/
```

#### Lấy entitlements từ provisioning profile

```bash
# Trích xuất entitlements từ provisioning profile
security cms -D -i MyApp_Distribution.mobileprovision | \
  /usr/libexec/PlistBuddy -c 'Print :Entitlements' /dev/stdin > MyApp.entitlements
```

---

## 8. Thay đổi backend cho App Verify

App Verify (Promon Verify™) yêu cầu thay đổi ở cả phía ứng dụng và phía backend server. Đây là module **duy nhất** trong Promon SHIELD yêu cầu thay đổi đáng kể ở backend.

### 8.1 Kiến trúc tổng thể App Verify

```
[Mobile App (đã Shield)]
        │
        │  1. Tạo Attestation Token
        │     (cryptographic proof: app gốc + thiết bị hợp lệ)
        │
        ▼
[API Request + X-Promon-Token: <token>]
        │
        ▼
[Backend API Server]
        │
        │  2. Validate Token
        │     (gọi Promon Verify SDK)
        │
        ├─── Token hợp lệ ──→ HTTP 200 (xử lý request bình thường)
        └─── Token không hợp lệ ──→ HTTP 401/403 (từ chối)
```

### 8.2 Thay đổi cần thiết ở backend

#### a) Thêm dependency Promon Verify SDK

```xml
<!-- Maven (pom.xml) -->
<dependency>
    <groupId>no.promon</groupId>
    <artifactId>verify-sdk</artifactId>
    <version>1.x.x</version>
</dependency>
```

```groovy
// Gradle (build.gradle)
implementation 'no.promon:verify-sdk:1.x.x'
```

```python
# Python (requirements.txt)
promon-verify-sdk==1.x.x
```

> **Lưu ý:** Liên hệ Promon để nhận SDK và phiên bản chính xác. SDK có sẵn cho Java/Kotlin, Python, Node.js, và .NET.

#### b) Endpoint validation — Middleware/Filter

**Cách 1: Middleware toàn cục (áp dụng cho tất cả API)**

```java
// Java Spring Boot — AppVerifyFilter.java
@Component
@Order(1)
public class AppVerifyFilter implements Filter {

    @Autowired
    private PromonVerifyService verifyService;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response,
                         FilterChain chain) throws IOException, ServletException {

        HttpServletRequest httpRequest = (HttpServletRequest) request;
        HttpServletResponse httpResponse = (HttpServletResponse) response;

        // Lấy token từ header
        String token = httpRequest.getHeader("X-Promon-Token");

        if (token == null || token.isEmpty()) {
            httpResponse.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
            httpResponse.getWriter().write("{\"error\": \"Missing attestation token\"}");
            return;
        }

        // Validate token với Promon Verify SDK
        VerificationResult result = verifyService.verify(token);

        if (!result.isValid()) {
            httpResponse.setStatus(HttpServletResponse.SC_FORBIDDEN);
            httpResponse.getWriter().write(
                String.format("{\"error\": \"Invalid attestation token\", \"reason\": \"%s\"}",
                    result.getFailureReason())
            );
            return;
        }

        // Token hợp lệ — tiếp tục xử lý request
        chain.doFilter(request, response);
    }
}
```

**Cách 2: Annotation-based (áp dụng cho endpoint cụ thể)**

```java
// Annotation tùy chỉnh
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface RequireAppVerify {}

// Aspect
@Aspect
@Component
public class AppVerifyAspect {

    @Autowired
    private PromonVerifyService verifyService;

    @Before("@annotation(RequireAppVerify)")
    public void checkAppVerify(JoinPoint joinPoint) {
        HttpServletRequest request = getCurrentRequest();
        String token = request.getHeader("X-Promon-Token");

        VerificationResult result = verifyService.verify(token);
        if (!result.isValid()) {
            throw new AppVerifyException("Attestation failed: " + result.getFailureReason());
        }
    }
}

// Sử dụng trong controller
@RestController
@RequestMapping("/api/v1")
public class PaymentController {

    @PostMapping("/payment")
    @RequireAppVerify  // Chỉ endpoint nhạy cảm mới cần verify
    public ResponseEntity<?> processPayment(@RequestBody PaymentRequest request) {
        // Xử lý thanh toán
    }
}
```

#### c) Cấu hình Promon Verify SDK

```java
// PromonVerifyConfig.java
@Configuration
public class PromonVerifyConfig {

    @Bean
    public PromonVerifyClient promonVerifyClient() {
        return PromonVerifyClient.builder()
            .apiKey(System.getenv("PROMON_VERIFY_API_KEY"))
            .applicationId("com.vnptmedia.myapp")
            // Endpoint Promon Verify service (cloud hoặc on-prem)
            .verifyEndpoint("https://verify.promon.no/v1/verify")
            // Timeout
            .connectionTimeout(Duration.ofSeconds(5))
            .readTimeout(Duration.ofSeconds(10))
            // Cache kết quả verify để giảm latency
            .cacheEnabled(true)
            .cacheTtl(Duration.ofMinutes(5))
            .build();
    }
}
```

#### d) Validation logic chi tiết

```java
// PromonVerifyService.java
@Service
public class PromonVerifyService {

    private final PromonVerifyClient client;
    private final MetricsService metrics;

    public VerificationResult verify(String token) {
        if (token == null || token.length() < 10) {
            metrics.increment("app_verify.missing_token");
            return VerificationResult.invalid("Token missing or malformed");
        }

        try {
            // Gọi Promon Verify API
            VerifyResponse response = client.verify(VerifyRequest.of(token));

            if (response.isValid()) {
                // Log thông tin thiết bị (non-PII)
                log.info("App Verify passed: appVersion={}, platform={}",
                    response.getAppVersion(), response.getPlatform());
                metrics.increment("app_verify.success");
                return VerificationResult.valid();
            } else {
                // Log lý do thất bại để phân tích bảo mật
                log.warn("App Verify failed: reason={}, deviceInfo={}",
                    response.getFailureReason(), response.getDeviceInfo());
                metrics.increment("app_verify.failed." + response.getFailureReason());
                return VerificationResult.invalid(response.getFailureReason());
            }

        } catch (PromonVerifyException e) {
            // Lỗi kết nối đến Promon Verify service
            log.error("App Verify service error: {}", e.getMessage());
            metrics.increment("app_verify.service_error");

            // Quyết định: fail-open (cho phép) hay fail-closed (từ chối)?
            // Khuyến nghị: fail-open trong giai đoạn rollout, fail-closed khi ổn định
            return VerificationResult.valid(); // fail-open
        }
    }
}
```

### 8.3 Endpoint và Header convention

| Thành phần | Giá trị | Ghi chú |
|---|---|---|
| **Header name** | `X-Promon-Token` | Tên header chứa attestation token |
| **Token format** | JWT hoặc opaque string | Tùy phiên bản SDK |
| **Verify endpoint (cloud)** | `https://verify.promon.no/v1/verify` | Promon-hosted |
| **Verify endpoint (on-prem)** | `https://verify.internal.vnptmedia.vn/v1/verify` | Tự triển khai |
| **HTTP method** | POST | Gửi token để validate |
| **Response code (valid)** | 200 OK | Token hợp lệ |
| **Response code (invalid)** | 401/403 | Token không hợp lệ hoặc thiếu |

### 8.4 Chiến lược rollout

Không nên bật App Verify cho tất cả endpoint ngay lập tức. Khuyến nghị rollout theo giai đoạn:

| Giai đoạn | Hành động | Mục tiêu |
|---|---|---|
| **Giai đoạn 1 (Monitor)** | Bật App Verify ở chế độ log-only (không từ chối request) | Thu thập baseline, phát hiện false positive |
| **Giai đoạn 2 (Soft enforce)** | Từ chối request không có token, cho phép token không hợp lệ | Đảm bảo app mới đã tích hợp SDK |
| **Giai đoạn 3 (Full enforce)** | Từ chối cả request không có token và token không hợp lệ | Bảo vệ đầy đủ |

---

## 9. Thay đổi mã nguồn ứng dụng

Promon SHIELD được thiết kế theo nguyên tắc **post-build, no source code change** cho phần lớn tính năng. Tuy nhiên, hai module **SLS** và **App Verify** yêu cầu thay đổi mã nguồn nhỏ.

### 9.1 Tổng quan thay đổi cần thiết

| Module | Yêu cầu thay đổi code | Mức độ thay đổi |
|---|---|---|
| **SHIELD Core RASP** | Không | Không cần |
| **Code Protection** | Không | Không cần |
| **SAROM** | Không | Không cần |
| **Promon Insight** | Không (cấu hình qua blueprint) | Không cần |
| **SLS (Secure Local Storage)** | Có — thay thế SharedPreferences/UserDefaults | Trung bình |
| **App Verify** | Có — thêm SDK và gửi token | Nhỏ |
| **Callback API (tamperAction)** | Có — implement handler | Nhỏ đến trung bình |

### 9.2 SLS API — Thay đổi mã nguồn

SLS yêu cầu thay thế các lời gọi lưu trữ thông thường bằng SLS API. Đây là thay đổi **có chủ đích** — chỉ áp dụng cho dữ liệu nhạy cảm.

#### Android (Java)

```java
// TRƯỚC: SharedPreferences thông thường (KHÔNG AN TOÀN)
SharedPreferences prefs = getSharedPreferences("app_prefs", MODE_PRIVATE);
prefs.edit().putString("session_token", token).apply();
String savedToken = prefs.getString("session_token", null);

// SAU: SLS API (AN TOÀN — White-Box Crypto + Device Binding)
import no.promon.sls.PromonSLS;
import no.promon.sls.SLSException;

// Khởi tạo (thường trong Application class hoặc DI container)
PromonSLS sls = new PromonSLS(context);

// Ghi dữ liệu
try {
    sls.put("session_token", token);
} catch (SLSException e) {
    Log.e("SLS", "Failed to store token: " + e.getMessage());
}

// Đọc dữ liệu
try {
    String savedToken = sls.get("session_token");
    if (savedToken != null) {
        // Sử dụng token
    }
} catch (SLSException e) {
    Log.e("SLS", "Failed to read token: " + e.getMessage());
}

// Xóa dữ liệu
try {
    sls.delete("session_token");
} catch (SLSException e) {
    Log.e("SLS", "Failed to delete token: " + e.getMessage());
}
```

#### Android (Kotlin)

```kotlin
import no.promon.sls.PromonSLS
import no.promon.sls.SLSException

class SecureStorageManager(context: Context) {

    private val sls = PromonSLS(context)

    fun saveSessionToken(token: String): Boolean {
        return try {
            sls.put("session_token", token)
            true
        } catch (e: SLSException) {
            Timber.e(e, "Failed to save session token")
            false
        }
    }

    fun getSessionToken(): String? {
        return try {
            sls.get("session_token")
        } catch (e: SLSException) {
            Timber.e(e, "Failed to read session token")
            null
        }
    }

    fun clearSessionToken() {
        try {
            sls.delete("session_token")
        } catch (e: SLSException) {
            Timber.e(e, "Failed to delete session token")
        }
    }
}
```

#### iOS (Swift)

```swift
import PromonSLS

// TRƯỚC: UserDefaults thông thường (KHÔNG AN TOÀN)
UserDefaults.standard.set(token, forKey: "session_token")
let savedToken = UserDefaults.standard.string(forKey: "session_token")

// SAU: SLS API (AN TOÀN)
class SecureStorageManager {

    private let sls = PromonSLS()

    func saveSessionToken(_ token: String) {
        do {
            try sls.put(key: "session_token", value: token)
        } catch {
            print("SLS Error: \(error)")
        }
    }

    func getSessionToken() -> String? {
        do {
            return try sls.get(key: "session_token")
        } catch {
            print("SLS Error: \(error)")
            return nil
        }
    }

    func clearSessionToken() {
        do {
            try sls.delete(key: "session_token")
        } catch {
            print("SLS Error: \(error)")
        }
    }
}
```

**Dữ liệu nên lưu qua SLS:**
- Session token / JWT / Refresh token
- Thông tin xác thực (credentials)
- PII (Personally Identifiable Information)
- API keys nhạy cảm
- Dữ liệu tài chính (số tài khoản, số thẻ đã mask)

**Dữ liệu KHÔNG cần SLS:**
- Cài đặt UI (theme, language)
- Cache không nhạy cảm
- Dữ liệu công khai

### 9.3 App Verify SDK — Thay đổi mã nguồn

#### Android (Java/Kotlin)

```java
// Thêm dependency vào build.gradle
// implementation 'no.promon:app-verify-sdk:1.x.x'

import no.promon.appverify.AppVerify;
import no.promon.appverify.AppVerifyCallback;
import no.promon.appverify.AttestationToken;

// Khởi tạo App Verify (thường trong Application.onCreate())
AppVerify.initialize(context, "YOUR_APP_VERIFY_API_KEY");

// Lấy token trước khi gọi API nhạy cảm
AppVerify.getToken(new AppVerifyCallback() {
    @Override
    public void onSuccess(AttestationToken token) {
        // Thêm token vào header của API request
        apiClient.addHeader("X-Promon-Token", token.getValue());
        // Thực hiện API call
        apiClient.callSensitiveEndpoint(requestData);
    }

    @Override
    public void onFailure(Exception e) {
        // Xử lý lỗi — không thể lấy token
        // Có thể do: app bị tamper, môi trường không an toàn
        Log.e("AppVerify", "Failed to get attestation token: " + e.getMessage());
        showSecurityWarning();
    }
});
```

#### Tích hợp với OkHttp Interceptor (Kotlin)

```kotlin
// AppVerifyInterceptor.kt
class AppVerifyInterceptor : Interceptor {

    override fun intercept(chain: Interceptor.Chain): Response {
        val originalRequest = chain.request()

        // Chỉ thêm token cho các endpoint nhạy cảm
        if (!originalRequest.url.encodedPath.startsWith("/api/v1/sensitive")) {
            return chain.proceed(originalRequest)
        }

        // Lấy token đồng bộ (blocking)
        val token = AppVerify.getTokenSync() ?: return chain.proceed(originalRequest)

        val newRequest = originalRequest.newBuilder()
            .header("X-Promon-Token", token.value)
            .build()

        return chain.proceed(newRequest)
    }
}

// Đăng ký interceptor
val okHttpClient = OkHttpClient.Builder()
    .addInterceptor(AppVerifyInterceptor())
    .build()
```

### 9.4 Callback API — Implement tamper handler

Khi cấu hình `tamperAction` với `type: "method"` trong blueprint, cần implement method handler trong app:

```java
// TamperActionHandler.java
public class TamperActionHandler {

    /**
     * Được gọi khi phát hiện root/jailbreak
     * Method signature phải khớp CHÍNH XÁC với tên trong blueprint.json
     */
    public static void onRootDetected() {
        // Ghi log bảo mật
        SecurityLogger.log(SecurityEvent.ROOT_DETECTED);

        // Thông báo cho người dùng
        runOnUiThread(() -> {
            new AlertDialog.Builder(currentActivity)
                .setTitle("Cảnh báo bảo mật")
                .setMessage("Thiết bị của bạn không đáp ứng yêu cầu bảo mật. " +
                            "Ứng dụng sẽ đóng.")
                .setPositiveButton("OK", (d, w) -> System.exit(0))
                .setCancelable(false)
                .show();
        });
    }

    /**
     * Được gọi khi phát hiện debugger
     */
    public static void onDebuggerDetected() {
        SecurityLogger.log(SecurityEvent.DEBUGGER_DETECTED);
        // Thoát ngay lập tức — không hiển thị dialog
        System.exit(0);
    }

    /**
     * Được gọi khi môi trường bình thường (không có mối đe dọa)
     */
    public static void onEnvironmentClean() {
        // Có thể dùng để unlock tính năng hoặc log trạng thái bình thường
        SecurityLogger.log(SecurityEvent.ENVIRONMENT_CLEAN);
    }
}
```

---

## 10. Đánh giá tác động đến dev team

### 10.1 Tóm tắt effort ước tính

| Hạng mục | Effort ước tính | Người thực hiện | Ghi chú |
|---|---|---|---|
| **Thiết lập môi trường build** | 1–2 ngày | DevOps/Build Engineer | Cài JDK, cấu hình PATH, test Shielder |
| **Tích hợp CI/CD pipeline** | 2–3 ngày | DevOps Engineer | Jenkins/GitLab CI/GitHub Actions |
| **Soạn thảo blueprint.json** | 1–2 ngày | Android Developer | Cần hiểu cấu trúc app để cấu hình đúng |
| **Tích hợp SLS API** | 3–5 ngày | Android + iOS Developer | Thay thế SharedPreferences/UserDefaults |
| **Tích hợp App Verify SDK** | 2–3 ngày | Android + iOS Developer | Thêm SDK, cấu hình interceptor |
| **Thay đổi backend (App Verify)** | 3–5 ngày | Backend Developer | Middleware, SDK, cấu hình |
| **Implement Callback API** | 1–2 ngày | Android + iOS Developer | Handler cho tamper action |
| **Testing & QA** | 5–7 ngày | QA Engineer | Regression test, security test |
| **Tài liệu hóa** | 1–2 ngày | Tech Lead | Runbook, troubleshooting guide |
| **TỔNG** | **~3–4 tuần** | Nhóm 4–5 người | Bao gồm buffer 20% |

### 10.2 Phân tích tác động theo nhóm

#### a) Android Developer

**Thay đổi cần thực hiện:**
- Thêm SLS SDK dependency vào `build.gradle`
- Refactor code lưu trữ dữ liệu nhạy cảm (SharedPreferences → SLS)
- Thêm App Verify SDK và OkHttp interceptor
- Implement `TamperActionHandler` class với các method callback
- Cập nhật ProGuard rules để giữ nguyên tên các method callback

**Rủi ro kỹ thuật:**
- Obfuscation có thể gây lỗi reflection nếu không cấu hình `keepSignatures` đúng
- Một số thư viện bên thứ ba có thể xung đột với renaming guard
- Cần test kỹ trên các phiên bản Android khác nhau (đặc biệt Android 5–7)

**Thời gian học tập:** 1–2 ngày để làm quen với blueprint JSON và SLS API

#### b) iOS Developer

**Thay đổi cần thực hiện:**
- Thêm SLS SDK vào CocoaPods/SPM
- Refactor code lưu trữ (UserDefaults → SLS)
- Thêm App Verify SDK
- Implement security callback handler trong Swift/Objective-C
- Cập nhật entitlements và provisioning profile nếu cần

**Rủi ro kỹ thuật:**
- Quy trình ký lại IPA phức tạp hơn Android
- Cần macOS build agent trong CI/CD
- Xcode version compatibility cần kiểm tra

**Thời gian học tập:** 1–2 ngày

#### c) Backend Developer

**Thay đổi cần thực hiện:**
- Thêm Promon Verify SDK vào project
- Implement middleware/filter validate token
- Cấu hình kết nối đến Promon Verify service
- Thêm monitoring/alerting cho App Verify failures
- Cập nhật API documentation

**Rủi ro kỹ thuật:**
- Latency tăng do gọi Promon Verify service (~50–100ms)
- Cần xử lý trường hợp Promon Verify service không khả dụng (fail-open vs fail-closed)
- Cần chiến lược rollout để tránh ảnh hưởng người dùng hiện tại

**Thời gian học tập:** 1 ngày

#### d) DevOps/Build Engineer

**Thay đổi cần thực hiện:**
- Cài đặt và cấu hình Shielder tool trên build agent
- Cập nhật CI/CD pipeline (thêm shield stage và sign stage)
- Quản lý secrets (license key, keystore, certificate)
- Thiết lập monitoring cho build pipeline
- Cập nhật runbook và documentation

**Rủi ro kỹ thuật:**
- Build time tăng 5–15 phút do bước shielding
- Cần quản lý Promon license key an toàn (không commit vào repo)
- Cần backup keystore/certificate ở nơi an toàn

**Thời gian học tập:** 1–2 ngày

### 10.3 Rủi ro và biện pháp giảm thiểu

| Rủi ro | Mức độ | Biện pháp giảm thiểu |
|---|---|---|
| **Lỗi reflection sau obfuscation** | Cao | Tạo `keepSignatures.txt` đầy đủ; test kỹ trên staging |
| **Tăng kích thước APK/IPA** | Trung bình | Tham khảo benchmark: +15–30% kích thước; thông báo cho người dùng nếu cần |
| **Tăng thời gian build** | Trung bình | Chạy shielding song song với các bước khác nếu có thể |
| **False positive (app bị block nhầm)** | Trung bình | Bắt đầu với `tamperAction: "doNothing"` + logging; chuyển sang `fail` sau khi ổn định |
| **Latency API tăng (App Verify)** | Thấp | Cache token; timeout hợp lý; fail-open trong giai đoạn đầu |
| **Vendor lock-in** | Trung bình | Thiết kế abstraction layer cho SLS API để dễ thay thế |
| **License key bị lộ** | Cao | Lưu trong secret manager (Vault, AWS Secrets Manager); không commit vào repo |
| **Xung đột với thư viện bên thứ ba** | Trung bình | Test kỹ với tất cả dependency; liên hệ Promon support nếu cần |

### 10.4 Lộ trình triển khai đề xuất

```
Tuần 1: Thiết lập & Chuẩn bị
├── Cài đặt môi trường build (DevOps)
├── Nghiên cứu blueprint JSON (Android Dev)
└── Thiết kế abstraction layer cho SLS (Tech Lead)

Tuần 2: Tích hợp cơ bản
├── Tích hợp SLS API cho Android (Android Dev)
├── Tích hợp SLS API cho iOS (iOS Dev)
├── Implement Callback API handler (Android + iOS Dev)
└── Cập nhật CI/CD pipeline — shield stage (DevOps)

Tuần 3: App Verify & Backend
├── Tích hợp App Verify SDK — mobile (Android + iOS Dev)
├── Implement backend middleware (Backend Dev)
├── Test tích hợp end-to-end (QA)
└── Bật App Verify ở chế độ monitor (Backend Dev)

Tuần 4: Testing & Hardening
├── Regression test toàn bộ (QA)
├── Security test (ANTT-TSC)
├── Performance test (QA + DevOps)
├── Chuyển App Verify sang enforce mode (Backend Dev)
└── Tài liệu hóa và runbook (Tech Lead)
```

### 10.5 Điểm cần lưu ý đặc biệt cho VNPT Media

1. **MyVNPT và DigiShop có kiến trúc khác nhau** — cần soạn blueprint.json riêng cho từng app, không dùng chung.

2. **React Native / Flutter apps** — nếu MyVNPT hoặc DigiShop dùng cross-platform framework, cần kiểm tra tương thích với JavaScript bundle obfuscation. Promon hỗ trợ nhưng cần cấu hình thêm.

3. **Staging environment** — bắt buộc phải có môi trường staging để test shielded app trước khi release production. Không test trực tiếp trên production.

4. **Rollback plan** — luôn giữ bản APK/IPA chưa shield để rollback nhanh nếu phát hiện lỗi nghiêm trọng sau release.

5. **Promon support** — đăng ký support contract để có thể liên hệ kỹ thuật khi gặp vấn đề tích hợp. Thời gian phản hồi thường 1–2 ngày làm việc.

---

## Tài liệu tham khảo

- `protect-android-4.11.0-userguide.pdf` — Tài liệu hướng dẫn tích hợp chính thức
- `samples/java/blueprint.json` — File blueprint mẫu cho APK
- `samples/java/blueprint-aab.json` — File blueprint mẫu cho AAB
- `bin/default_blueprint.json` — Blueprint mặc định tối giản
- `Promon SHIELD Introduction - 2020_NoDEMO.pdf` — Tài liệu giới thiệu tổng quan
- `Promon_Intro_short_2026 (1).pdf` — Tài liệu giới thiệu cập nhật 2026
- [OWASP MASVS v2.x](https://mas.owasp.org/MASVS/) — Tiêu chuẩn bảo mật ứng dụng di động
- [Android APK Signing](https://developer.android.com/studio/publish/app-signing) — Tài liệu ký APK chính thức
- [Apple Code Signing](https://developer.apple.com/documentation/security/code_signing_services) — Tài liệu ký iOS chính thức
