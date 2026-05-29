# Tài liệu Tổng quan Kiến trúc và Tính năng Promon SHIELD

**Dự án:** Nghiên cứu & Đánh giá Giải pháp Bảo mật Ứng dụng Di động Promon SHIELD  
**Đơn vị thực hiện:** Phòng An ninh Thông tin – TSC (ANTT-TSC), VNPT Media  
**Phiên bản tài liệu:** 1.0  
**Ngày lập:** 2025  
**Yêu cầu liên quan:** Yêu cầu 1

---

## Mục lục

1. [Giới thiệu về Promon AS và Promon SHIELD](#1-giới-thiệu)
2. [Kiến trúc tổng thể](#2-kiến-trúc-tổng-thể)
3. [Mô tả chi tiết 6 module](#3-mô-tả-chi-tiết-6-module)
4. [Mối đe dọa được bảo vệ và đối chiếu Checklist_VNPT](#4-mối-đe-dọa-và-checklist-vnpt)
5. [Nền tảng và Framework được hỗ trợ](#5-nền-tảng-và-framework)
6. [Chứng nhận tuân thủ](#6-chứng-nhận-tuân-thủ)
7. [Các Interface được bảo vệ](#7-các-interface-được-bảo-vệ)
8. [Tóm tắt và nhận xét sơ bộ](#8-tóm-tắt)

---

## 1. Giới thiệu về Promon AS và Promon SHIELD

### 1.1 Về Promon AS

Promon AS là công ty an ninh mạng thế hệ mới có trụ sở tại Oslo, Na Uy, được thành lập năm 2006 dựa trên chương trình nghiên cứu của Đại học Oslo và SINTEF (một trong những tổ chức nghiên cứu độc lập lớn nhất châu Âu). Công ty chuyên cung cấp giải pháp bảo mật ứng dụng di động hàng đầu thế giới với công nghệ RASP (Runtime Application Self-Protection) tiên tiến.

**Các mốc lịch sử quan trọng:**
- **2006:** Thành lập, ra mắt Application Shielding cho Windows
- **2009:** Ngân hàng Tier-1 đầu tiên sử dụng công nghệ Promon; bắt đầu phát triển cho iOS và Android; đăng ký bằng sáng chế đầu tiên về phát hiện và chặn mối đe dọa bảo mật trong ứng dụng
- **2012:** Mở rộng quốc tế, bắt đầu bán công nghệ qua đối tác
- **2015:** Ngân hàng Tier-1 toàn cầu đầu tiên sử dụng; triển khai quy mô lớn tại châu Á
- **2018:** Mở văn phòng Sales & Marketing tại Hong Kong; đăng ký bằng sáng chế Trusted Execution Zone (TEZ) cho Android

**Quy mô hiện tại:** Bảo vệ hàng trăm triệu người dùng cuối trên toàn thế giới, phục vụ các khách hàng Tier-1 trong lĩnh vực ngân hàng, thanh toán, y tế, gaming và chính phủ điện tử.

### 1.2 Định nghĩa Promon SHIELD

Promon SHIELD™ (nay được đặt tên lại là **Promon Shield for Mobile™**) là giải pháp bảo mật ứng dụng di động toàn diện, cung cấp bảo vệ đa lớp cho ứng dụng iOS và Android. Giải pháp hoạt động theo nguyên tắc **post-build integration** — tức là bảo vệ được áp dụng sau khi ứng dụng đã được biên dịch, không yêu cầu thay đổi mã nguồn.

**Triết lý thiết kế:** "Bảo vệ ứng dụng ngay cả khi thiết bị đã bị nhiễm mã độc" — ứng dụng trở thành **self-defending** (tự bảo vệ), không phụ thuộc vào bảo mật của hệ điều hành hay thiết bị.

---

## 2. Kiến trúc Tổng thể

### 2.1 Mô hình bảo vệ hai lớp

Promon SHIELD bảo vệ ứng dụng theo hai chiều:

```
┌─────────────────────────────────────────────────────────────┐
│                    PROMON SHIELD PLATFORM                    │
├─────────────────────────┬───────────────────────────────────┤
│     BẢO VỆ TẠI REST     │      BẢO VỆ TẠI RUNTIME          │
│  (AT REST / STATIC)     │      (AT RUNTIME / DYNAMIC)       │
├─────────────────────────┼───────────────────────────────────┤
│ • Code Protection       │ • SHIELD Core RASP Engine         │
│   (Binary Obfuscation)  │   (Runtime Attack Protection)     │
│ • SAROM                 │ • Promon Insight (Telemetry)      │
│   (Static Data Encrypt) │ • App Verify (API Attestation)    │
│ • SLS                   │                                   │
│   (Secure Local Storage)│                                   │
└─────────────────────────┴───────────────────────────────────┘
```

### 2.2 Quy trình tích hợp (Post-Build Workflow)

```
[Ứng dụng gốc (APK/AAB/IPA)]
         │
         ▼
[Shielder Tool (CLI)]  ←── [Config Blueprint JSON/XML]
         │
         ▼
[Ứng dụng đã được Shield]
         │
         ▼
[Ký lại ứng dụng (Re-sign)]
         │
         ▼
[Phát hành lên App Store / Google Play]
```

**Shielder Tool** là công cụ CLI (Command Line Interface) chạy trên Linux/macOS/Windows, nhận đầu vào là file APK/AAB/IPA và file cấu hình blueprint, xuất ra ứng dụng đã được bảo vệ. Công cụ này có thể tích hợp vào pipeline CI/CD.

### 2.3 Kiến trúc Runtime

Khi ứng dụng đã được shield chạy trên thiết bị người dùng:

```
┌──────────────────────────────────────────────────────┐
│                  THIẾT BỊ NGƯỜI DÙNG                 │
│  ┌────────────────────────────────────────────────┐  │
│  │           ỨNG DỤNG ĐÃ ĐƯỢC SHIELD              │  │
│  │  ┌──────────────────────────────────────────┐  │  │
│  │  │         SHIELD CORE RASP ENGINE          │  │  │
│  │  │  • Kiểm tra môi trường liên tục          │  │  │
│  │  │  • Phát hiện tấn công runtime            │  │  │
│  │  │  • Phản ứng theo cấu hình                │  │  │
│  │  └──────────────────────────────────────────┘  │  │
│  │  ┌──────────────┐  ┌──────────────────────────┐│  │
│  │  │  App Logic   │  │  Optional Modules:       ││  │
│  │  │  (Protected) │  │  SLS / SAROM / App Verify││  │
│  │  └──────────────┘  └──────────────────────────┘│  │
│  └────────────────────────────────────────────────┘  │
│                         │                            │
│                         ▼                            │
│              [Promon Insight Agent]                  │
│                         │                            │
└─────────────────────────┼────────────────────────────┘
                          │ Telemetry (JSON/Protobuf)
                          ▼
              [SIEM / Dashboard / Fraud System]
```

---

## 3. Mô tả Chi tiết 6 Module

### 3.1 SHIELD Core RASP (Runtime Application Self-Protection)

**Vai trò:** Module trung tâm, là nền tảng của toàn bộ giải pháp. Cung cấp bảo vệ runtime toàn diện cho ứng dụng.

**Cơ chế hoạt động:**
- Nhúng các "guard" (bộ bảo vệ) vào mã nhị phân của ứng dụng trong quá trình shielding
- Các guard chạy liên tục trong suốt vòng đời ứng dụng, kiểm tra tính toàn vẹn của môi trường thực thi
- Khi phát hiện tấn công, kích hoạt phản ứng được cấu hình sẵn

**Các nhóm bảo vệ chính:**

#### a) Environment Integrity (Tính toàn vẹn môi trường) — Android
| Tính năng | Mô tả |
|---|---|
| Root Detection | Phát hiện thiết bị đã root (SuperSU, Magisk, Kingroot, v.v.) |
| Unlock Bootloader | Phát hiện bootloader đã được mở khóa |
| Emulator Detection | Phát hiện môi trường giả lập (Android Studio AVD, Genymotion, v.v.) |
| Virtual Space Detection | Phát hiện ứng dụng chạy trong không gian ảo hóa |
| Private Space & Work Profiles | Phát hiện môi trường profile riêng tư/công việc |
| Task Hijacking | Phát hiện tấn công chiếm quyền task (StrandHogg, StrandHogg 2.0) |
| Android Developer Bridge (ADB) | Phát hiện kết nối ADB đang hoạt động |
| Developer Options | Phát hiện chế độ nhà phát triển được bật |

#### b) Environment Integrity — iOS
| Tính năng | Mô tả |
|---|---|
| Jailbreak Detection | Phát hiện thiết bị đã jailbreak (Liberty Lite, v.v.) |
| Emulator Detection | Phát hiện iOS Simulator |
| Developer Mode | Phát hiện chế độ nhà phát triển |
| Disabled SIP | Phát hiện System Integrity Protection bị tắt |

#### c) Anti Reverse Engineering (Chống dịch ngược)
| Tính năng | Mô tả |
|---|---|
| Debugger Detection | Phát hiện debugger đang attach vào tiến trình |
| Code Tracers | Phát hiện công cụ trace mã (strace, ltrace) |
| Hooking Frameworks | Phát hiện Frida, Xposed, LSPosed, Cydia, EdXposed, Riru |
| Native Code Hooks | Phát hiện hook ở tầng native code |
| Memory Scanning | Phát hiện quét bộ nhớ trái phép |
| Obfuscation (Java/Kotlin) | Làm rối mã nguồn Java/Kotlin: đổi tên class/method/field, mã hóa string, làm phẳng luồng điều khiển |

#### d) App Integrity (Tính toàn vẹn ứng dụng)
| Tính năng | Mô tả |
|---|---|
| Repackaging Detection | Phát hiện ứng dụng bị decompile, sửa đổi và đóng gói lại |
| Signature Check | Kiểm tra chữ ký số của APK/IPA |
| Checksum Verification | Tính và kiểm tra checksum của toàn bộ mã DEX |
| Resource Verification | Kiểm tra tính toàn vẹn của file tài nguyên (ảnh, icon, manifest) |
| App Binding | Ràng buộc ứng dụng với thiết bị cụ thể |

#### e) User Interaction Protection (Bảo vệ tương tác người dùng)
| Tính năng | Mô tả |
|---|---|
| Untrusted Keyboard (Keyloggers) | Phát hiện bàn phím không tin cậy/keylogger |
| Screenshot Protection | Ngăn chặn chụp màn hình trái phép |
| Screen Mirroring | Phát hiện chiếu màn hình (scrcpy, Miracast, AirPlay) |
| Screen Reader (Accessibility) | Phát hiện Accessibility Service đọc màn hình |
| Overlay Attacks | Phát hiện tấn công overlay/phishing window |

#### f) 3rd Party Apps
| Tính năng | Mô tả |
|---|---|
| Sideloaded App Detection | Phát hiện ứng dụng cài từ nguồn không rõ ràng |
| Malware Detection | Phát hiện malware đã biết (StrandHogg, Cerberus, Gustuff, v.v.) |

**Cơ chế phản ứng (Reaction):**
- **Callback API:** Gọi hàm callback tùy chỉnh trong ứng dụng để xử lý sự kiện
- **ExitOn:** Thoát ứng dụng ngay lập tức khi phát hiện mối đe dọa
- **ExitOnURL:** Thoát và gửi thông báo đến URL được cấu hình (kèm thông tin thiết bị, lý do, phiên bản OS)

**Ví dụ ExitOnURL:**
```
https://partners.promon.co/notifications/?path=AppName/reason=00/
manufacturer=Samsung/model=GT-I9000/Android-API=16/SecMod=2.3.0/
```

---

### 3.2 Promon Insight (Data Analytics & Dashboard)

**Vai trò:** Module thu thập telemetry và phân tích bảo mật, cung cấp khả năng quan sát (observability) cho ứng dụng đã được shield.

**Kiến trúc Insight:**
```
[Ứng dụng + Insight Client]
         │ Telemetry events
         ▼
[Insight Agent (on-prem / cloud / hybrid)]
         │
    ┌────┴────┐
    ▼         ▼
[SIEM/Fraud] [Promon Dashboard]
[JSON/Protobuf] [Non-PII Analytics]
```

**Dữ liệu thu thập (non-PII theo mặc định):**
- Metadata thiết bị và ứng dụng
- Sự kiện rooting/jailbreaking
- Phát hiện hooking (Frida, Xposed)
- Phát hiện emulator
- Phát hiện screen reader
- Phát hiện debugger
- Các bất thường runtime khác
- Mỗi sự kiện được gắn nhãn mức độ nghiêm trọng (severity level)

**Tính năng chính:**
- **Phát hiện tín hiệu gian lận ẩn:** Phát hiện rooting, emulation, hooking, screen reader và chuyển tiếp tín hiệu tin cậy đến SIEM hoặc hệ thống phát hiện gian lận
- **Điều tra sự cố nhanh hơn:** Telemetry có timestamp và severity tag từ thiết bị thực để tái tạo timeline sự cố
- **Đơn giản hóa tuân thủ:** Tạo bằng chứng pháp lý phù hợp với GDPR, CCPA và các yêu cầu kiểm toán
- **Tích hợp liền mạch:** Xuất dữ liệu dạng JSON hoặc Protobuf đến SIEM, hệ thống phát hiện gian lận, hoặc công cụ tuân thủ

**Mô hình triển khai:**
| Mô hình | Mô tả | Ưu điểm |
|---|---|---|
| **Cloud** | Dễ triển khai nhất, bao gồm Promon-hosted dashboard cho non-PII | Nhanh, không cần hạ tầng |
| **On-premises** | Toàn quyền kiểm soát telemetry, không có Promon dashboard | Kiểm soát dữ liệu hoàn toàn |
| **Hybrid** | PII ở on-prem, non-PII gửi lên Promon cloud để visualization | Cân bằng bảo mật và tiện lợi |

**Tích hợp với hệ thống hiện có:**
- Không yêu cầu SDK bổ sung (nhúng sẵn trong Shield)
- Hỗ trợ xuất dữ liệu: JSON, Protobuf
- Tương thích với SIEM, data lake, fraud platform
- Khách hàng có thể bổ sung metadata tùy chỉnh (session ID, user ID) trong khi vẫn kiểm soát PII

**Đối tượng sử dụng:**
- **SOC Teams:** Nhận telemetry có cấu trúc, severity-tagged để phản ứng nhanh hơn
- **Fraud Teams:** Bằng chứng về các nỗ lực thao túng
- **Compliance Officers:** Bằng chứng pháp lý về tính toàn vẹn
- **CISOs:** Bức tranh tổng thể về mối đe dọa trên mobile estate

---

### 3.3 Code Protection (Promon Code Protect™)

**Vai trò:** Module bảo vệ mã nhị phân và thư viện native, ngăn chặn dịch ngược (reverse engineering) và đánh cắp sở hữu trí tuệ.

**Tên sản phẩm hiện tại:** Promon Code Protect™ (trước đây gọi là Promon Jigsaw Binary Obfuscation Engine)

**Cơ chế bảo vệ:**

| Kỹ thuật | Mô tả |
|---|---|
| **Multi-layer Obfuscation** | Biến đổi cấu trúc mã (hàm, biến, luồng thực thi) để làm cho cả native và JavaScript logic không thể đọc được bởi công cụ dịch ngược hoặc AI |
| **Section Encryption** | Mã hóa các section nhạy cảm trong binary, ngăn static analysis hoặc trích xuất logic cốt lõi |
| **Control-Flow Abstraction** | Che giấu cách ứng dụng thực thi bằng cách định tuyến lại function call và phá vỡ liên kết giữa các khối mã |
| **Checksumming & Integrity Verification** | Tính hash và checksum trên các vùng mã để phát hiện thay đổi trái phép; kích hoạt safe failure nếu phát hiện tampering |
| **Advanced Data & Symbol Obfuscation** | Đổi tên symbol, class, biến; ẩn string, integer, operator; xáo trộn thứ tự hàm |
| **Anti-debugging & Anti-tampering** | Chèn các bẫy ngẫu nhiên và kiểm tra tính toàn vẹn để phá vỡ debugging và ngăn runtime inspection |
| **Debug Stripping & Surface Reduction** | Xóa toàn bộ thông tin debug, metadata và annotation không cần thiết |

**Ngôn ngữ và framework được hỗ trợ:**
- **Native:** C, C++, Swift, Rust, Objective-C
- **Hybrid/Cross-platform:** React Native, Cordova, Ionic, Flutter
- **JVM:** Java, Kotlin (Android)
- **JavaScript:** Obfuscation cho hybrid apps

**Nền tảng hỗ trợ:** Android, iOS, macOS, ChromeOS, HarmonyOS, Amazon FireOS

**Đặc điểm nổi bật:**
- Tích hợp post-compile, không thay đổi mã nguồn
- Không ảnh hưởng đến hiệu năng runtime (zero latency overhead)
- Tích hợp liền mạch vào CI/CD pipeline
- Bảo vệ cả third-party SDK tích hợp trong ứng dụng
- Bảo vệ AI model và logic on-device AI

**Khác biệt so với obfuscation truyền thống:**
- Không chỉ là obfuscation tĩnh — bổ sung runtime integrity check, section encryption và cryptographic binding
- Làm cho decompilation và tampering trở nên bất khả thi (infeasible), không chỉ khó khăn

---

### 3.4 SAROM (Secure App ROM / Static At-Rest Object Manager)

**Vai trò:** Module mã hóa dữ liệu tĩnh nhúng trong ứng dụng, bảo vệ các bí mật (secrets) được đóng gói cùng APK/IPA.

**Tên đầy đủ:** Secure Application ROM (SAROM) — còn gọi là Promon Data Protect™ (phần static)

**Vấn đề giải quyết:**
Các ứng dụng thường nhúng trực tiếp vào binary các thông tin nhạy cảm như: API keys, certificate pins, encryption keys, configuration secrets. Những dữ liệu này có thể bị trích xuất dễ dàng bằng công cụ static analysis (jadx, apktool, strings).

**Cơ chế hoạt động:**
```
[Quá trình Shielding]
  Asset/Secret trong APK
         │
         ▼
  SAROM tự động mã hóa
  (AES-256 GCM)
         │
         ▼
  Secret được lưu dưới dạng
  encrypted blob trong APK

[Khi ứng dụng chạy]
  Ứng dụng yêu cầu secret
         │
         ▼
  SAROM giải mã động tại runtime
  (chỉ khi cần thiết)
         │
         ▼
  Secret được cung cấp cho
  application code
```

**Đặc điểm kỹ thuật:**
- **Thuật toán mã hóa:** AES-256 GCM (Galois/Counter Mode)
- **Tự động hóa:** Assets được tự động mã hóa trong quá trình shielding — không cần thay đổi mã nguồn
- **Bảo vệ tĩnh:** Secrets không bao giờ có thể truy cập được bằng static analysis
- **Giải mã động:** Chỉ giải mã tại runtime khi application code cần sử dụng
- **Kết hợp với RASP:** Nếu môi trường bị compromise (root, hook), SAROM có thể từ chối giải mã

**Trường hợp sử dụng điển hình:**
- API keys nhúng trong ứng dụng
- Certificate pinning data
- Encryption keys cho local data
- Configuration secrets (server URLs, feature flags nhạy cảm)
- License keys

---

### 3.5 SLS (Secure Local Storage)

**Vai trò:** Module lưu trữ cục bộ an toàn, bảo vệ dữ liệu nhạy cảm được lưu trên thiết bị người dùng, kể cả khi thiết bị đã bị root hoặc jailbreak.

**Tên đầy đủ:** Secure Local Storage (SLS) — là một phần của Promon Data Protect™

**Vấn đề giải quyết:**
Dữ liệu lưu trong SharedPreferences (Android), UserDefaults (iOS), SQLite, hoặc file system thông thường có thể bị đọc bởi ứng dụng khác hoặc attacker trên thiết bị đã root/jailbreak.

**Cơ chế hoạt động:**

```
[Ứng dụng ghi dữ liệu]
  SLS.put("key", "sensitive_value")
         │
         ▼
  White-Box Cryptography
  (Mã hóa với key được bảo vệ)
         │
         ▼
  Device Binding
  (Gắn với Device ID duy nhất)
         │
         ▼
  Lưu vào encrypted storage

[Ứng dụng đọc dữ liệu]
  SLS.get("key")
         │
         ▼
  Kiểm tra Device ID
         │
         ▼
  Giải mã với White-Box Crypto
         │
         ▼
  Trả về "sensitive_value"
```

**Đặc điểm kỹ thuật:**
- **White-Box Cryptography:** Khóa mã hóa được bảo vệ ngay cả khi attacker có quyền truy cập đầy đủ vào bộ nhớ thiết bị
- **Device Binding:** Dữ liệu được ràng buộc với Device ID — không thể đọc được khi copy sang thiết bị khác
- **API đơn giản:** PUT → GET → DELETE (round-trip: ghi → đọc → so sánh)
- **Bảo vệ trên thiết bị root/jailbreak:** Dữ liệu vẫn được bảo vệ ngay cả khi thiết bị đã bị compromise

**API tích hợp (yêu cầu thay đổi mã nguồn nhỏ):**
```java
// Android (Java/Kotlin)
PromonSLS sls = new PromonSLS(context);
sls.put("session_token", tokenValue);
String token = sls.get("session_token");
sls.delete("session_token");
```

**Trường hợp sử dụng điển hình:**
- Session tokens và authentication credentials
- Personally Identifiable Information (PII)
- API keys và secrets
- Dữ liệu tài chính nhạy cảm
- Khóa mã hóa cho dữ liệu local

**So sánh với lưu trữ thông thường:**
| Phương thức | Bảo vệ trên thiết bị root | Device Binding | White-Box Crypto |
|---|---|---|---|
| SharedPreferences | ❌ | ❌ | ❌ |
| Android Keystore | ⚠️ Một phần | ✅ | ❌ |
| **SLS (Promon)** | **✅** | **✅** | **✅** |

---

### 3.6 App Verify (Promon Verify™ — API Attestation)

**Vai trò:** Module xác thực tính toàn vẹn của ứng dụng khi gọi API backend, đảm bảo chỉ ứng dụng gốc (chưa bị sửa đổi) mới có thể giao tiếp với server.

**Tên đầy đủ:** Promon Verify™ (trước đây gọi là App Verify / Promon App Attestation™)

**Vấn đề giải quyết:**
Các API backend thường không thể phân biệt request đến từ ứng dụng gốc hay từ công cụ tấn công (Burp Suite, Postman, script tự động). App Verify giải quyết vấn đề này bằng cơ chế attestation.

**Cơ chế hoạt động:**

```
[Ứng dụng gốc đã được Shield]
         │
         ▼
  Tạo Attestation Token
  (Chứng minh: app gốc + thiết bị hợp lệ
   + môi trường không bị compromise)
         │
         ▼
  Gửi API Request + Token
  ─────────────────────────────────►
                                   [Backend Server]
                                         │
                                         ▼
                                   Validate Token
                                   (Xác thực với
                                    Promon Verify SDK)
                                         │
                                   ┌─────┴─────┐
                                   ▼           ▼
                              Token hợp lệ  Token không hợp lệ
                              HTTP 200      HTTP 401/403
                              (Cho phép)    (Từ chối)
```

**Đặc điểm kỹ thuật:**
- **Cryptographic Attestation:** Token được tạo bằng mật mã, không thể giả mạo
- **Real-time Binding:** Ràng buộc tính xác thực của ứng dụng với API call trong thời gian thực
- **Phân biệt request hợp lệ:** Phân biệt request từ app gốc với request từ Burp Suite, Charles Proxy, Postman
- **Yêu cầu thay đổi backend:** Server cần tích hợp Promon Verify SDK để validate token

**Thay đổi cần thiết:**
- **Phía ứng dụng:** Tích hợp App Verify SDK (yêu cầu thay đổi mã nguồn nhỏ)
- **Phía backend:** Thêm endpoint validation logic, tích hợp Promon Verify SDK

**Giới hạn quan trọng:**
App Verify **không** cung cấp certificate pinning hay mutual TLS trực tiếp. Nó bảo vệ API bằng cách xác thực nguồn gốc của request, không phải bằng cách mã hóa kênh truyền thông. Để chống MiTM hoàn toàn, cần kết hợp với:
- Certificate Pinning (trong mã nguồn ứng dụng)
- Mutual TLS (mTLS) ở tầng network

**Trường hợp sử dụng điển hình:**
- Bảo vệ API thanh toán khỏi bot và script tự động
- Ngăn chặn API scraping
- Xác thực tính toàn vẹn ứng dụng trước khi cấp phép truy cập dữ liệu nhạy cảm
- Phát hiện ứng dụng bị repackage đang cố gắng gọi API

---

### 3.7 Tóm tắt 6 Module

| Module | Loại bảo vệ | Yêu cầu thay đổi code | Tùy chọn/Bắt buộc |
|---|---|---|---|
| **SHIELD Core RASP** | Runtime (At Runtime) | Không | Bắt buộc (core) |
| **Promon Insight** | Telemetry & Analytics | Không | Tùy chọn |
| **Code Protection** | Static (At Rest) | Không | Tùy chọn |
| **SAROM** | Static Data Encryption | Không | Tùy chọn |
| **SLS** | Dynamic Local Storage | Có (API nhỏ) | Tùy chọn |
| **App Verify** | API Attestation | Có (SDK + backend) | Tùy chọn |

---

## 4. Mối đe dọa được Bảo vệ và Đối chiếu Checklist_VNPT

### 4.1 Danh sách đầy đủ mối đe dọa Promon SHIELD tuyên bố bảo vệ

**Nhóm 1: Tấn công môi trường (Environment Attacks)**
- Root/Jailbreak (bao gồm root-hiding tools: Magiskhide, Suhide, Rootcloak)
- Emulator/Giả lập (Android AVD, Genymotion, BlueStacks)
- Virtual Space (Parallel Space, Dual Space)
- Bootloader unlock
- Developer Options/ADB enabled

**Nhóm 2: Tấn công dịch ngược (Reverse Engineering Attacks)**
- Debugger attachment (Android Studio debugger, jdb, LLDB)
- Code tracing (strace, ltrace)
- Hooking frameworks (Frida, Xposed, LSPosed, EdXposed, Riru, Cydia, Android DDI)
- Native code hooks
- Memory scanning

**Nhóm 3: Tấn công toàn vẹn ứng dụng (App Integrity Attacks)**
- Repackaging (decompile → modify → repack → resign)
- Code injection
- Resource tampering
- Task hijacking (StrandHogg, StrandHogg 2.0)
- Dynamic loading of malicious code

**Nhóm 4: Tấn công tương tác người dùng (User Interaction Attacks)**
- Keylogging (untrusted keyboards)
- Screenshot/Screen recording
- Screen mirroring (scrcpy, Miracast, AirPlay)
- Accessibility Service abuse (screen readers, overlay attacks)
- Overlay/Phishing window attacks

**Nhóm 5: Tấn công mạng và API (Network & API Attacks)**
- Man-in-the-Middle (gián tiếp qua App Verify)
- API request forgery (qua App Verify attestation)
- Unauthorized API access

**Nhóm 6: Tấn công dữ liệu (Data Attacks)**
- Static data extraction từ APK/IPA (qua SAROM)
- Local storage theft (qua SLS)
- API key exposure

---

### 4.2 Đối chiếu với Checklist_VNPT (10 tiêu chí)

| # | Tiêu chí Checklist_VNPT | Module Promon SHIELD | Cơ chế bảo vệ | Đánh giá sơ bộ |
|---|---|---|---|---|
| 1 | **Chống MiTM** (Man-in-the-Middle) | App Verify (Promon Verify™) | Attestation token xác thực nguồn gốc request; backend từ chối request không có token hợp lệ. Lưu ý: không phải certificate pinning trực tiếp | ⚠️ **Gián tiếp** — App Verify ngăn request giả mạo nhưng không mã hóa kênh truyền. Cần kết hợp cert pinning để đạt bảo vệ đầy đủ |
| 2 | **Chống Hook** | SHIELD Core RASP | Phát hiện Frida, Xposed, LSPosed, EdXposed, Riru, Cydia, Android DDI, native code hooks | ✅ **Trực tiếp** — Phát hiện và phản ứng với hooking frameworks |
| 3 | **Chống Debug** | SHIELD Core RASP | Debugger Detection guard phát hiện debugger attach; Code Tracer detection | ✅ **Trực tiếp** — Phát hiện và phản ứng với debugger |
| 4 | **Chống Repack** | SHIELD Core RASP | Signature Check + Checksum guard kiểm tra chữ ký và checksum DEX; Resource Verification | ✅ **Trực tiếp** — Từ chối khởi động nếu phát hiện repackaging |
| 5 | **Chống Dynamic Load APK** | SHIELD Core RASP | Phát hiện sideloaded apps; Virtual Space detection; kiểm tra nguồn gốc ứng dụng | ⚠️ **Một phần** — Phát hiện môi trường có sideloaded apps nhưng không block dynamic class loading trực tiếp |
| 6 | **Chống Screen Mirroring** | SHIELD Core RASP | Screen Mirroring detection (scrcpy, Miracast, AirPlay, external display) | ✅ **Trực tiếp** — Phát hiện và phản ứng với screen mirroring |
| 7 | **Chống Inject Camera** | SHIELD Core RASP | Overlay attack detection; Accessibility Service detection; Virtual Control Detection | ⚠️ **Gián tiếp** — Phát hiện overlay và accessibility abuse có thể dùng để inject camera feed; không có cơ chế chuyên biệt cho camera injection |
| 8 | **Root/Jailbreak Detection** | SHIELD Core RASP | Root Detection (SuperSU, Magisk, Kingroot, root-hiding tools); Jailbreak Detection (iOS) | ✅ **Trực tiếp** — Phát hiện cả root thông thường và root-hiding |
| 9 | **Emulator Detection** | SHIELD Core RASP | Emulator Detection guard; Virtual Space detection; Virtualization Detection | ✅ **Trực tiếp** — Phát hiện Android AVD, Genymotion, BlueStacks và các emulator khác |
| 10 | **Accessibility Abuse** | SHIELD Core RASP | Screen Reader detection; Accessibility Service detection; Overlay detection | ✅ **Trực tiếp** — Phát hiện Accessibility Service có khả năng đọc màn hình hoặc thực hiện thao tác tự động |

**Chú giải:**
- ✅ **Trực tiếp:** Promon SHIELD có cơ chế bảo vệ chuyên biệt, hiệu quả cao
- ⚠️ **Gián tiếp/Một phần:** Có bảo vệ nhưng không đầy đủ hoặc cần kết hợp biện pháp bổ sung
- ❌ **Không có:** Không có cơ chế bảo vệ

**Tóm tắt đánh giá sơ bộ:**
- **8/10 tiêu chí** được bảo vệ trực tiếp hoặc gián tiếp
- **Tiêu chí cần lưu ý:**
  - **Chống MiTM (tiêu chí 1):** App Verify cung cấp bảo vệ gián tiếp; cần bổ sung certificate pinning để đạt bảo vệ đầy đủ
  - **Chống Dynamic Load APK (tiêu chí 5):** Cần kiểm tra thực tế để xác nhận mức độ bảo vệ
  - **Chống Inject Camera (tiêu chí 7):** Không có cơ chế chuyên biệt; bảo vệ gián tiếp qua overlay/accessibility detection

> **Lưu ý:** Đánh giá trên dựa trên tài liệu nhà cung cấp. Kết quả thực tế sẽ được xác nhận trong Giai đoạn 2 (Thử nghiệm) của dự án.

---

## 5. Nền tảng và Framework được Hỗ trợ

### 5.1 Nền tảng hệ điều hành

| Nền tảng | Phiên bản hỗ trợ | Định dạng file | Ghi chú |
|---|---|---|---|
| **Android** | Android 5.0+ (API 21+) | APK, AAB | Hỗ trợ cả Java và Kotlin |
| **iOS** | iOS 12+ | IPA | Hỗ trợ Swift và Objective-C |

**Lưu ý về Android:**
- Hỗ trợ cả APK (Android Package) và AAB (Android App Bundle)
- Tương thích với các kiến trúc: ARM, ARM64, x86, x86_64
- Hỗ trợ ứng dụng có nhiều DEX file (multidex)

**Lưu ý về iOS:**
- Hỗ trợ cả thiết bị thực và simulator (để test)
- Tương thích với App Extensions
- Hỗ trợ File Integrity Check và App Extensions protection

### 5.2 Framework ứng dụng được hỗ trợ

| Framework | Android | iOS | Ghi chú |
|---|---|---|---|
| **Native (Java/Kotlin)** | ✅ | N/A | Hỗ trợ đầy đủ tất cả tính năng |
| **Native (Swift/Obj-C)** | N/A | ✅ | Hỗ trợ đầy đủ tất cả tính năng |
| **Native (C/C++)** | ✅ | ✅ | Code Protection hỗ trợ native libraries |
| **React Native** | ✅ | ✅ | JavaScript obfuscation + native protection |
| **Flutter** | ✅ | ✅ | Dart/native binary protection |
| **Cordova** | ✅ | ✅ | JavaScript obfuscation + WebView protection |
| **Ionic** | ✅ | ✅ | Tương tự Cordova |
| **Xamarin** | ✅ | ✅ | .NET/Mono binary protection |

**Đặc điểm tích hợp theo framework:**

**React Native:**
- JavaScript bundle được obfuscate bởi Code Protection
- Native modules được bảo vệ bởi SHIELD Core RASP
- Không yêu cầu thay đổi JavaScript code

**Flutter:**
- Dart code được biên dịch thành native binary — được bảo vệ bởi Code Protection
- SHIELD Core RASP bảo vệ runtime environment

**Cordova/Ionic:**
- JavaScript/HTML/CSS trong WebView được obfuscate
- Native wrapper được bảo vệ bởi SHIELD Core RASP

### 5.3 Môi trường build được hỗ trợ

| Môi trường | Hỗ trợ | Ghi chú |
|---|---|---|
| **Windows** | ✅ | Shielder tool chạy trên Windows |
| **Linux (Ubuntu)** | ✅ | Khuyến nghị cho CI/CD |
| **macOS** | ✅ | Bắt buộc cho iOS shielding |
| **Docker** | ✅ | Hỗ trợ non-interactive mode |
| **CI/CD (Jenkins, GitLab CI, GitHub Actions)** | ✅ | Tích hợp qua CLI |

**Yêu cầu môi trường tối thiểu:**
- Java SE (JDK) — phiên bản tối thiểu theo tài liệu nhà cung cấp
- Android SDK (cho Android shielding)
- Xcode (cho iOS shielding, chỉ trên macOS)
- Signing certificate (keystore cho Android, provisioning profile cho iOS)

---

## 6. Chứng nhận Tuân thủ

### 6.1 Danh sách chứng nhận và công nhận

| Chứng nhận/Công nhận | Loại | Ý nghĩa | Trạng thái |
|---|---|---|---|
| **ISO 27001** | Chứng nhận | Hệ thống quản lý an toàn thông tin (ISMS) — tiêu chuẩn quốc tế | ✅ Đã đạt |
| **SOC 2** | Chứng nhận | Kiểm soát bảo mật, tính khả dụng, tính toàn vẹn xử lý, bảo mật và quyền riêng tư | ✅ Đã đạt |
| **EMVCo SMBP 2025** | Chứng nhận | EMV® Secure Mobile Biometric Payment — tiêu chuẩn bảo mật thanh toán di động của EMVCo | ✅ Đã đạt (2025) |
| **EU DORA** | Tuân thủ | Digital Operational Resilience Act — quy định về khả năng phục hồi hoạt động kỹ thuật số của EU | ✅ Tuân thủ |
| **EU NIS2** | Tuân thủ | Network and Information Security Directive 2 | 🔄 Đang xử lý |
| **ISO 9001** | Chứng nhận | Hệ thống quản lý chất lượng | 🔄 Đang xử lý |
| **Gartner Hype Cycle for Application Security 2025** | Công nhận | Được Gartner đưa vào Hype Cycle for Application Security 2025 | ✅ Được công nhận |

### 6.2 Chi tiết các chứng nhận quan trọng

#### ISO 27001
- Chứng nhận quốc tế về Hệ thống Quản lý An toàn Thông tin (ISMS)
- Đảm bảo Promon AS có quy trình bảo mật nội bộ đạt chuẩn
- Quan trọng cho các tổ chức tài chính và chính phủ khi lựa chọn nhà cung cấp

#### SOC 2
- Báo cáo kiểm toán độc lập về các kiểm soát bảo mật của Promon
- Đặc biệt quan trọng cho khách hàng doanh nghiệp tại Mỹ và quốc tế
- Bao gồm các nguyên tắc: Security, Availability, Processing Integrity, Confidentiality, Privacy

#### EMVCo SMBP 2025 (Secure Mobile Biometric Payment)
- Chứng nhận từ EMVCo — tổ chức quản lý tiêu chuẩn thanh toán EMV toàn cầu
- Xác nhận Promon SHIELD đáp ứng các yêu cầu bảo mật nghiêm ngặt cho ứng dụng thanh toán di động
- Đặc biệt quan trọng cho ứng dụng mobile banking và payment wallet
- Các kỳ vọng SMBP ánh xạ tự nhiên với PSD3, PSR, DORA và GDPR

#### EU DORA (Digital Operational Resilience Act)
- Quy định của EU áp dụng cho ~22,000 tổ chức tài chính
- Yêu cầu khả năng phục hồi hoạt động kỹ thuật số, bao gồm bảo mật ứng dụng di động
- Promon SHIELD giúp tổ chức tài chính đáp ứng yêu cầu DORA về:
  - Bảo vệ mã nguồn (chống reverse engineering)
  - Mã hóa dữ liệu
  - Chiến lược bảo mật đa lớp
  - Khả năng phục hồi dưới tấn công

### 6.3 Tài nguyên xác minh

- Trust Center: https://trust.promon.io
- EMVCo SMBP Certification: https://promon.io/security-news/promon-achieves-emvco-sbmp-certification
- Gartner Hype Cycle: https://promon.io/resources/downloads/gartner-hype-cycle-for-application-security-2025

### 6.4 Ý nghĩa đối với VNPT Media

Các chứng nhận trên có ý nghĩa quan trọng trong bối cảnh VNPT Media:
- **ISO 27001 + SOC 2:** Đảm bảo nhà cung cấp có quy trình bảo mật đáng tin cậy — giảm rủi ro supply chain
- **EMVCo SMBP:** Nếu VNPT Media có ứng dụng thanh toán (DigiShop), chứng nhận này là lợi thế lớn
- **EU DORA:** Mặc dù VNPT Media không trực tiếp thuộc phạm vi DORA, việc nhà cung cấp tuân thủ DORA cho thấy mức độ nghiêm túc về bảo mật

---

## 7. Các Interface được Bảo vệ

### 7.1 Ứng dụng Di động (Mobile App Interface)

**Phạm vi bảo vệ:**
Promon SHIELD bảo vệ toàn bộ ứng dụng di động — từ mã nguồn đã biên dịch đến dữ liệu runtime.

| Thành phần | Module bảo vệ | Cơ chế |
|---|---|---|
| **Mã nhị phân (DEX/native)** | Code Protection, SHIELD Core | Obfuscation, encryption, integrity check |
| **Tài nguyên (assets, resources)** | SHIELD Core RASP | Resource Verification, Resource Encryption |
| **Luồng thực thi runtime** | SHIELD Core RASP | Guards nhúng trong code, kiểm tra liên tục |
| **Giao diện người dùng** | SHIELD Core RASP | Screen mirroring protection, overlay detection |
| **Input người dùng** | SHIELD Core RASP | Keylogger detection, untrusted keyboard detection |
| **Dữ liệu nhạy cảm nhúng** | SAROM | AES-256 GCM encryption tự động |

**Phản ứng khi phát hiện tấn công:**
- Thoát ứng dụng ngay lập tức (ExitOn)
- Gọi callback tùy chỉnh (Callback API) — cho phép hiển thị thông báo thân thiện trước khi thoát
- Gửi thông báo đến server (ExitOnURL)
- Ghi log sự kiện vào Promon Insight

### 7.2 API Backend (Backend API Interface)

**Phạm vi bảo vệ:**
Module App Verify (Promon Verify™) bảo vệ API backend khỏi các request không hợp lệ.

| Loại request | Kết quả với App Verify |
|---|---|
| Request từ ứng dụng gốc, thiết bị sạch | ✅ Token hợp lệ → HTTP 200 |
| Request từ ứng dụng bị repackage | ❌ Token không hợp lệ → HTTP 401/403 |
| Request từ Burp Suite/Postman | ❌ Không có token → HTTP 401/403 |
| Request từ ứng dụng trên thiết bị root | ❌ Token phản ánh môi trường compromise → Có thể từ chối |
| Request từ script tự động | ❌ Không có token → HTTP 401/403 |

**Luồng xác thực API:**
```
App → [Tạo Attestation Token] → API Request + Token → Backend
Backend → [Validate Token với Promon Verify SDK] → Cho phép/Từ chối
```

**Yêu cầu thay đổi backend:**
1. Tích hợp Promon Verify SDK vào backend service
2. Thêm middleware/interceptor để extract và validate token từ request header
3. Cấu hình policy: token không hợp lệ → HTTP 401/403

**Giới hạn:**
- App Verify không thay thế HTTPS/TLS
- Không cung cấp certificate pinning
- Cần kết hợp với biện pháp bảo mật network để chống MiTM hoàn toàn

### 7.3 Local Storage (Lưu trữ Cục bộ)

**Phạm vi bảo vệ:**
Hai module bảo vệ dữ liệu lưu trữ cục bộ theo hai cách khác nhau:

| Module | Loại dữ liệu | Cơ chế | Khi nào dùng |
|---|---|---|---|
| **SAROM** | Dữ liệu tĩnh nhúng trong APK/IPA | AES-256 GCM, tự động trong shielding | API keys, certs, config secrets đóng gói cùng app |
| **SLS** | Dữ liệu động lưu trên thiết bị | White-Box Crypto + Device Binding | Session tokens, user data, credentials lưu runtime |

**So sánh với các phương thức lưu trữ thông thường:**

| Phương thức | Bảo vệ static | Bảo vệ trên root | Device Binding | White-Box Crypto |
|---|---|---|---|---|
| SharedPreferences (Android) | ❌ | ❌ | ❌ | ❌ |
| UserDefaults (iOS) | ❌ | ❌ | ❌ | ❌ |
| SQLite (không mã hóa) | ❌ | ❌ | ❌ | ❌ |
| Android Keystore | N/A | ⚠️ | ✅ | ❌ |
| iOS Keychain | N/A | ⚠️ | ✅ | ❌ |
| **SAROM (Promon)** | **✅** | **✅** | **N/A** | **N/A** |
| **SLS (Promon)** | **N/A** | **✅** | **✅** | **✅** |

### 7.4 Tổng hợp Interface được bảo vệ

```
┌─────────────────────────────────────────────────────────────────┐
│                    PHẠM VI BẢO VỆ PROMON SHIELD                 │
│                                                                  │
│  ┌──────────────────┐    ┌──────────────────┐                   │
│  │   MOBILE APP     │    │   LOCAL STORAGE  │                   │
│  │                  │    │                  │                   │
│  │ • Binary code    │    │ • Static secrets │                   │
│  │ • Runtime env    │    │   (SAROM)        │                   │
│  │ • UI/UX layer    │    │ • Dynamic data   │                   │
│  │ • User input     │    │   (SLS)          │                   │
│  └────────┬─────────┘    └──────────────────┘                   │
│           │                                                      │
│           │ API calls + Attestation Token                        │
│           ▼                                                      │
│  ┌──────────────────┐                                           │
│  │   BACKEND API    │                                           │
│  │                  │                                           │
│  │ • App Verify     │                                           │
│  │   validation     │                                           │
│  │ • Request auth   │                                           │
│  └──────────────────┘                                           │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │              PROMON INSIGHT (Telemetry)                  │   │
│  │  Thu thập sự kiện bảo mật từ tất cả các interface       │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 8. Tóm tắt và Nhận xét Sơ bộ

### 8.1 Điểm mạnh nổi bật

1. **Kiến trúc post-build:** Không yêu cầu thay đổi mã nguồn cho phần lớn tính năng — giảm thiểu tác động đến đội phát triển
2. **Bảo vệ đa lớp:** Kết hợp bảo vệ tĩnh (at-rest) và động (runtime) — khó bypass hơn giải pháp đơn lớp
3. **Phạm vi bảo vệ rộng:** Bao phủ 8/10 tiêu chí Checklist_VNPT theo tuyên bố của nhà cung cấp
4. **Hỗ trợ đa nền tảng:** Android + iOS, Native + React Native + Flutter + Cordova
5. **Chứng nhận uy tín:** ISO 27001, SOC 2, EMVCo SMBP 2025 — đặc biệt quan trọng cho ứng dụng tài chính
6. **Tích hợp CI/CD:** Shielder tool có thể tự động hóa trong pipeline build
7. **Telemetry & Insight:** Khả năng quan sát (observability) tốt qua Promon Insight

### 8.2 Điểm cần lưu ý

1. **Chống MiTM:** App Verify cung cấp bảo vệ gián tiếp — cần kết hợp certificate pinning để đạt bảo vệ đầy đủ
2. **Chống Inject Camera:** Không có cơ chế chuyên biệt — cần xác nhận qua thử nghiệm thực tế
3. **SLS và App Verify yêu cầu thay đổi code:** Đội phát triển cần tích hợp API — cần đánh giá tác động
4. **Vendor lock-in:** Phụ thuộc vào Promon AS cho cập nhật bảo mật và hỗ trợ
5. **Chi phí:** Mô hình licensing thương mại — cần đánh giá chi phí trong Task 2

### 8.3 Câu hỏi cần xác nhận qua thử nghiệm

Các điểm sau cần được xác nhận trong Giai đoạn 2 (Thử nghiệm):
- [ ] Hiệu quả thực tế của Root Detection với Magisk + MagiskHide
- [ ] Hiệu quả thực tế của Hook Detection với Frida (non-default settings)
- [ ] Mức độ bảo vệ chống Dynamic Load APK
- [ ] Cơ chế App Verify có thực sự block Burp Suite không
- [ ] Tác động đến kích thước APK và thời gian khởi động
- [ ] Khả năng bypass của các kỹ thuật tấn công nâng cao

### 8.4 Liên kết với các Task tiếp theo

| Task | Nội dung | Phụ thuộc vào Task 1 |
|---|---|---|
| Task 2 | Phân tích ưu/nhược điểm | Dựa trên kiến trúc đã nghiên cứu |
| Task 3 | Ma trận so sánh | Dựa trên tính năng đã liệt kê |
| Task 4 | Quy trình tích hợp | Dựa trên kiến trúc Shielder |
| Task 5-12 | Thử nghiệm thực tế | Xác nhận các tuyên bố trong tài liệu này |

---

## Tài liệu tham khảo

1. **Promon SHIELD Introduction - 2020_NoDEMO.pdf** — Tài liệu giới thiệu Promon SHIELD (2020), có sẵn trong workspace
2. **Promon_Intro_short_2026 (1).pdf** — Tài liệu giới thiệu ngắn Promon 2026, có sẵn trong workspace
3. **protect-android-4.11.0-userguide.pdf** — Hướng dẫn sử dụng Digital.ai App Protection for Android (tham khảo kỹ thuật về guards), có sẵn trong workspace
4. **Promon Shield for Mobile™** — https://promon.io/software-security-solutions/app-shielding
5. **Promon Code Protect™** — https://promon.co/software-security-solutions/jigsaw-engine-binary-code-obfuscation
6. **Promon Insight for App Security™** — https://promon.io/products/promon-insight-analytics
7. **Promon Verify™ (App Attestation)** — https://promon.io/security-news/introducing-promon-shield-app-attestation-api-security
8. **SAROM Announcement** — https://promon.io/security-news/promon-announces-secure-application-rom-sarom
9. **EMVCo SMBP Certification** — https://promon.io/security-news/promon-achieves-emvco-sbmp-certification
10. **Promon Compliance & Certifications** — https://trust.promon.io

---

*Tài liệu này được lập bởi Nhóm_Nghiên_Cứu thuộc ANTT-TSC, VNPT Media.*  
*Phiên bản: 1.0 | Ngày: 2025 | Trạng thái: Hoàn thành (Giai đoạn 1 - Task 1)*
