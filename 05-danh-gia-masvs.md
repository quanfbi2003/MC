# Đánh giá Promon SHIELD theo OWASP MASVS v2.x

**Dự án:** Nghiên cứu & Đánh giá Giải pháp Bảo mật Ứng dụng Di động Promon SHIELD  
**Đơn vị thực hiện:** Phòng An ninh Thông tin – TSC (ANTT-TSC), VNPT Media  
**Phiên bản tài liệu:** 1.0  
**Ngày lập:** 2025  
**Yêu cầu liên quan:** Yêu cầu 8  
**Phụ thuộc:** Task 1 (`01-tong-quan-kien-truc-tinh-nang.md`), Task 2 (`02-uu-nhuoc-diem-phan-tich.md`)

---

## Mục lục

1. [Giới thiệu và Phạm vi Đánh giá](#1-giới-thiệu-và-phạm-vi-đánh-giá)
2. [Phương pháp Đánh giá](#2-phương-pháp-đánh-giá)
3. [Bảng Mapping Tổng thể — 39 MASVS Controls](#3-bảng-mapping-tổng-thể)
4. [Phân tích Chi tiết theo Nhóm](#4-phân-tích-chi-tiết-theo-nhóm)
5. [Phân tích Chuyên sâu MASVS-RESILIENCE (R1–R8)](#5-phân-tích-chuyên-sâu-masvs-resilience)
6. [Bảng Tỷ lệ % Đáp ứng theo Nhóm](#6-bảng-tỷ-lệ-đáp-ứng)
7. [Controls Không Đáp ứng và Biện pháp Bổ sung](#7-controls-không-đáp-ứng-và-biện-pháp-bổ-sung)
8. [Kết luận về Mức độ Đáp ứng MASVS Level 1](#8-kết-luận)

---

## 1. Giới thiệu và Phạm vi Đánh giá

### 1.1 Mục tiêu

Tài liệu này đánh giá mức độ đáp ứng của **Promon SHIELD** đối với 39 controls trong **OWASP MASVS v2.x** (Mobile Application Security Verification Standard), bao gồm 7 nhóm: STORAGE, CRYPTO, AUTH, NETWORK, PLATFORM, CODE và RESILIENCE.

### 1.2 Lưu ý Quan trọng về Phạm vi

> **⚠️ Promon SHIELD là giải pháp bảo mật bổ sung (add-on security layer), KHÔNG phải giải pháp thay thế secure coding.**

Điều này có nghĩa là:
- **Nhiều MASVS controls** (đặc biệt STORAGE, CRYPTO, AUTH, NETWORK, PLATFORM, CODE) thuộc **trách nhiệm của developer** khi viết mã nguồn ứng dụng.
- Promon SHIELD **không thể** tự động đáp ứng các controls yêu cầu thiết kế kiến trúc bảo mật đúng đắn.
- Promon SHIELD **đóng góp mạnh nhất** vào nhóm **MASVS-RESILIENCE** — nhóm liên quan đến chống dịch ngược, chống giả mạo và phát hiện môi trường bị compromise.
- Đánh giá này dựa trên **tài liệu nhà cung cấp** — không phải kết quả kiểm tra thực tế.

### 1.3 Phân loại Đánh giá

| Ký hiệu | Phân loại | Định nghĩa |
|---|---|---|
| ✅ | **Đáp ứng đầy đủ** | Promon SHIELD có cơ chế trực tiếp đáp ứng control này |
| ⚠️ | **Đáp ứng một phần** | Promon SHIELD hỗ trợ một phần; cần bổ sung biện pháp khác |
| ❌ | **Không đáp ứng** | Promon SHIELD không có cơ chế liên quan đến control này |
| 🔵 | **N/A — Trách nhiệm Developer** | Control này thuộc trách nhiệm của developer, ngoài phạm vi Promon SHIELD |

---

## 2. Phương pháp Đánh giá

### 2.1 Nguồn tham chiếu

- **OWASP MASVS v2.x:** https://mas.owasp.org/MASVS/ (39 controls, 7 nhóm)
- **Tài liệu Promon SHIELD:** Tài liệu kỹ thuật nhà cung cấp, tổng hợp trong `01-tong-quan-kien-truc-tinh-nang.md`
- **OWASP MASTG:** https://mas.owasp.org/MASTG/ (test cases tham chiếu)

### 2.2 Tiêu chí Đánh giá

Mỗi MASVS control được đánh giá theo 3 câu hỏi:

1. **Promon SHIELD có tính năng nào liên quan trực tiếp đến control này không?**
2. **Tính năng đó có đủ để đáp ứng hoàn toàn yêu cầu của control không?**
3. **Nếu không đủ, cần bổ sung biện pháp gì từ phía developer/kiến trúc?**

### 2.3 Mapping Module Promon SHIELD → Nhóm MASVS

| Module Promon SHIELD | Nhóm MASVS liên quan chính |
|---|---|
| SHIELD Core RASP (Environment Integrity) | RESILIENCE-1, RESILIENCE-8 |
| SHIELD Core RASP (Anti Reverse Engineering) | RESILIENCE-3, RESILIENCE-4 |
| SHIELD Core RASP (App Integrity) | RESILIENCE-2, RESILIENCE-7 |
| SHIELD Core RASP (User Interaction Protection) | STORAGE-7, PLATFORM-4 |
| Code Protection (Obfuscation) | RESILIENCE-3 |
| SAROM (Static Data Encryption) | STORAGE-2, CRYPTO-1 |
| SLS (Secure Local Storage) | STORAGE-1, STORAGE-2, STORAGE-5 |
| App Verify (API Attestation) | RESILIENCE-5, RESILIENCE-6 |
| Promon Insight (Telemetry) | RESILIENCE-7 |

---

## 3. Bảng Mapping Tổng thể — 39 MASVS Controls

### 3.1 MASVS-STORAGE (7 controls)

| Control ID | Mô tả | Phân loại | Module Promon SHIELD | Ghi chú |
|---|---|---|---|---|
| **STORAGE-1** | Dữ liệu nhạy cảm chỉ được lưu cục bộ khi thực sự cần thiết | ⚠️ Đáp ứng một phần | SLS (Secure Local Storage) | SLS bảo vệ dữ liệu đã lưu, nhưng quyết định "có nên lưu không" là trách nhiệm developer |
| **STORAGE-2** | Dữ liệu nhạy cảm được bảo vệ khi lưu ngoài sandbox ứng dụng | ✅ Đáp ứng đầy đủ | SLS + SAROM | SLS mã hóa White-Box + Device Binding; SAROM mã hóa AES-256 GCM cho static assets |
| **STORAGE-3** | Ứng dụng không chia sẻ dữ liệu nhạy cảm với bên thứ ba không cần thiết | 🔵 N/A — Developer | — | Kiểm soát luồng dữ liệu là trách nhiệm kiến trúc ứng dụng |
| **STORAGE-4** | Ứng dụng không lưu dữ liệu nhạy cảm trong file backup | 🔵 N/A — Developer | — | Cần cấu hình `android:allowBackup=false` và iOS Data Protection class |
| **STORAGE-5** | Ứng dụng không giữ dữ liệu nhạy cảm trong bộ nhớ lâu hơn cần thiết | ⚠️ Đáp ứng một phần | SLS | SLS giải mã động tại runtime (chỉ khi cần); nhưng quản lý vòng đời bộ nhớ là trách nhiệm developer |
| **STORAGE-6** | Ứng dụng không lộ dữ liệu nhạy cảm qua cơ chế IPC | 🔵 N/A — Developer | — | Cần kiểm soát exported components, Content Provider permissions trong mã nguồn |
| **STORAGE-7** | Dữ liệu nhạy cảm không bị lộ qua giao diện người dùng hoặc screenshot | ✅ Đáp ứng đầy đủ | SHIELD Core RASP (Screenshot Protection) | Ngăn chặn chụp màn hình trái phép; phát hiện screen mirroring và screen recording |

### 3.2 MASVS-CRYPTO (6 controls)

| Control ID | Mô tả | Phân loại | Module Promon SHIELD | Ghi chú |
|---|---|---|---|---|
| **CRYPTO-1** | Ứng dụng sử dụng mật mã mạnh hiện đại theo best practices | ⚠️ Đáp ứng một phần | SAROM (AES-256 GCM), SLS (White-Box Crypto) | Promon dùng AES-256 GCM cho SAROM và White-Box Crypto cho SLS — đây là best practice. Tuy nhiên, mật mã trong logic ứng dụng là trách nhiệm developer |
| **CRYPTO-2** | Ứng dụng thực hiện các thao tác mật mã bằng các implementation đã được kiểm chứng | 🔵 N/A — Developer | — | Lựa chọn thư viện mật mã (BouncyCastle, OpenSSL, platform crypto) là trách nhiệm developer |
| **CRYPTO-3** | Ứng dụng sử dụng mật mã phù hợp với use-case | 🔵 N/A — Developer | — | Thiết kế cryptographic scheme là trách nhiệm kiến trúc ứng dụng |
| **CRYPTO-4** | Ứng dụng không sử dụng các giao thức/thuật toán mật mã đã lỗi thời | 🔵 N/A — Developer | — | Tránh MD5, SHA-1, DES, RC4 là trách nhiệm developer |
| **CRYPTO-5** | Ứng dụng không tái sử dụng cùng một khóa mật mã cho nhiều mục đích | 🔵 N/A — Developer | — | Key management design là trách nhiệm kiến trúc ứng dụng |
| **CRYPTO-6** | Tất cả giá trị ngẫu nhiên được tạo bằng CSPRNG đủ mạnh | 🔵 N/A — Developer | — | Sử dụng `SecureRandom` (Android) / `SecRandomCopyBytes` (iOS) là trách nhiệm developer |

### 3.3 MASVS-AUTH (3 controls)

| Control ID | Mô tả | Phân loại | Module Promon SHIELD | Ghi chú |
|---|---|---|---|---|
| **AUTH-1** | Ứng dụng sử dụng giao thức xác thực và phân quyền an toàn theo best practices | 🔵 N/A — Developer | — | Triển khai OAuth 2.0, OpenID Connect, JWT là trách nhiệm developer/backend |
| **AUTH-2** | Ứng dụng thực hiện xác thực cục bộ an toàn theo platform best practices | ⚠️ Đáp ứng một phần | SHIELD Core RASP (Environment Integrity) | RASP phát hiện môi trường bị compromise (root, hook) có thể ảnh hưởng đến biometric auth. Tuy nhiên, triển khai BiometricPrompt/LocalAuthentication là trách nhiệm developer |
| **AUTH-3** | Ứng dụng bảo vệ các thao tác nhạy cảm bằng xác thực bổ sung | ⚠️ Đáp ứng một phần | App Verify + SHIELD Core RASP | App Verify xác thực tính toàn vẹn ứng dụng trước khi cho phép thao tác nhạy cảm. Tuy nhiên, step-up authentication (re-auth, OTP) là trách nhiệm developer |

### 3.4 MASVS-NETWORK (3 controls)

| Control ID | Mô tả | Phân loại | Module Promon SHIELD | Ghi chú |
|---|---|---|---|---|
| **NETWORK-1** | Dữ liệu được mã hóa trên mạng bằng TLS | 🔵 N/A — Developer | — | Cấu hình HTTPS/TLS trong ứng dụng và backend là trách nhiệm developer |
| **NETWORK-2** | Cấu hình TLS phù hợp với best practices hiện tại | 🔵 N/A — Developer | — | TLS 1.2+, cipher suite selection là trách nhiệm developer/DevOps |
| **NETWORK-3** | Ứng dụng xác minh chứng chỉ X.509 của endpoint từ xa | ⚠️ Đáp ứng một phần | App Verify (API Attestation) | App Verify xác thực nguồn gốc request (app → server), nhưng không thực hiện certificate pinning. Cần bổ sung certificate pinning trong mã nguồn để đáp ứng đầy đủ |

### 3.5 MASVS-PLATFORM (8 controls)

| Control ID | Mô tả | Phân loại | Module Promon SHIELD | Ghi chú |
|---|---|---|---|---|
| **PLATFORM-1** | Ứng dụng chỉ yêu cầu quyền tối thiểu cần thiết | 🔵 N/A — Developer | — | Khai báo permissions trong AndroidManifest/Info.plist là trách nhiệm developer |
| **PLATFORM-2** | Tất cả đầu vào từ nguồn ngoài và người dùng được validate và sanitize | 🔵 N/A — Developer | — | Input validation là trách nhiệm developer trong mã nguồn ứng dụng |
| **PLATFORM-3** | Ứng dụng không export chức năng nhạy cảm qua custom URL schemes | 🔵 N/A — Developer | — | Kiểm soát URL scheme handling là trách nhiệm developer |
| **PLATFORM-4** | Ứng dụng không export chức năng nhạy cảm qua IPC | ⚠️ Đáp ứng một phần | SHIELD Core RASP (Task Hijacking Detection) | RASP phát hiện Task Hijacking (StrandHogg). Tuy nhiên, kiểm soát exported Activities/Services/Providers là trách nhiệm developer |
| **PLATFORM-5** | JavaScript bị tắt trong WebViews trừ khi thực sự cần thiết | 🔵 N/A — Developer | — | Cấu hình WebView settings là trách nhiệm developer |
| **PLATFORM-6** | WebViews được cấu hình chỉ cho phép tập hợp protocol handlers tối thiểu | 🔵 N/A — Developer | — | WebView protocol handler configuration là trách nhiệm developer |
| **PLATFORM-7** | Ứng dụng không sử dụng deprecated APIs | 🔵 N/A — Developer | — | Code review và API migration là trách nhiệm developer |
| **PLATFORM-8** | Ứng dụng chỉ sử dụng các APIs cập nhật | 🔵 N/A — Developer | — | Cập nhật target SDK và API usage là trách nhiệm developer |

### 3.6 MASVS-CODE (4 controls)

| Control ID | Mô tả | Phân loại | Module Promon SHIELD | Ghi chú |
|---|---|---|---|---|
| **CODE-1** | Ứng dụng yêu cầu phiên bản platform cập nhật | 🔵 N/A — Developer | — | Cấu hình `minSdkVersion` / `MinimumOSVersion` là trách nhiệm developer |
| **CODE-2** | Ứng dụng có cơ chế bắt buộc cập nhật ứng dụng | ❌ Không đáp ứng | — | Promon SHIELD không cung cấp forced update mechanism. Cần triển khai riêng (Firebase Remote Config, In-App Update API) |
| **CODE-3** | Ứng dụng chỉ sử dụng các thành phần phần mềm không có lỗ hổng đã biết | ⚠️ Đáp ứng một phần | Code Protection + SHIELD Core RASP | Code Protection bảo vệ third-party SDK khỏi bị phân tích. Tuy nhiên, việc kiểm tra và cập nhật dependencies là trách nhiệm developer (SCA tools) |
| **CODE-4** | Ứng dụng chỉ sử dụng các thành phần phần mềm được duy trì tích cực | 🔵 N/A — Developer | — | Dependency management và lifecycle tracking là trách nhiệm developer |

### 3.7 MASVS-RESILIENCE (8 controls)

| Control ID | Mô tả | Phân loại | Module Promon SHIELD | Ghi chú |
|---|---|---|---|---|
| **RESILIENCE-1** | Ứng dụng xác thực tính toàn vẹn của platform | ✅ Đáp ứng đầy đủ | SHIELD Core RASP (Environment Integrity) | Root Detection, Jailbreak Detection, Bootloader Unlock Detection, Emulator Detection, Virtual Space Detection, ADB Detection, Developer Options Detection |
| **RESILIENCE-2** | Ứng dụng triển khai cơ chế chống giả mạo (anti-tampering) | ✅ Đáp ứng đầy đủ | SHIELD Core RASP (App Integrity) + Code Protection | Signature Check, Checksum Verification (DEX), Resource Verification, Repackaging Detection; Code Protection với Checksumming & Integrity Verification |
| **RESILIENCE-3** | Ứng dụng triển khai biện pháp chống phân tích tĩnh | ✅ Đáp ứng đầy đủ | Code Protection (Promon Code Protect™) | Multi-layer Obfuscation, Section Encryption, Control-Flow Abstraction, Advanced Data & Symbol Obfuscation, Debug Stripping; hỗ trợ Java/Kotlin/Swift/C/C++/JS |
| **RESILIENCE-4** | Ứng dụng triển khai biện pháp chống phân tích động và chống instrumentation | ✅ Đáp ứng đầy đủ | SHIELD Core RASP (Anti Reverse Engineering) | Debugger Detection, Code Tracer Detection, Hooking Framework Detection (Frida, Xposed, LSPosed, EdXposed, Riru, Cydia), Native Code Hook Detection, Memory Scanning Detection |
| **RESILIENCE-5** | Ứng dụng triển khai chức năng 'device binding' | ✅ Đáp ứng đầy đủ | SLS (Device Binding) + SHIELD Core RASP (App Binding) | SLS ràng buộc dữ liệu với Device ID duy nhất; App Binding trong RASP ràng buộc ứng dụng với thiết bị cụ thể |
| **RESILIENCE-6** | Ứng dụng triển khai các kiểm soát bổ sung để hạn chế truy cập vào chức năng/dữ liệu nhạy cảm | ✅ Đáp ứng đầy đủ | App Verify (API Attestation) + SAROM | App Verify từ chối API request không có attestation token hợp lệ; SAROM từ chối giải mã khi môi trường bị compromise |
| **RESILIENCE-7** | Ứng dụng triển khai các kiểm soát bổ sung để phát hiện và phản ứng với giả mạo hoặc thao túng môi trường | ✅ Đáp ứng đầy đủ | SHIELD Core RASP (Reaction) + Promon Insight | ExitOn/ExitOnURL/Callback API phản ứng theo cấu hình; Promon Insight thu thập telemetry sự kiện bảo mật có timestamp và severity |
| **RESILIENCE-8** | Ứng dụng phát hiện và phản ứng khi chạy trên thiết bị đã root hoặc jailbreak | ✅ Đáp ứng đầy đủ | SHIELD Core RASP (Root/Jailbreak Detection) | Root Detection (SuperSU, Magisk, Kingroot, root-hiding tools: Magiskhide, Shamiko, Zygisk); Jailbreak Detection (Liberty Lite, v.v.); phản ứng ExitOn hoặc Callback |

---

## 4. Phân tích Chi tiết theo Nhóm

### 4.1 MASVS-STORAGE — Phân tích

**Tổng quan:** Promon SHIELD đóng góp trực tiếp vào 2/7 controls (STORAGE-2 và STORAGE-7), hỗ trợ một phần 2/7 controls (STORAGE-1 và STORAGE-5). Phần lớn controls còn lại thuộc trách nhiệm developer.

**Điểm mạnh:**
- **STORAGE-2:** SLS với White-Box Cryptography và Device Binding là giải pháp vượt trội so với Android Keystore trên thiết bị root. Dữ liệu được bảo vệ ngay cả khi attacker có full memory access.
- **STORAGE-7:** Screenshot Protection và Screen Mirroring Detection ngăn chặn lộ dữ liệu qua giao diện người dùng — đây là tính năng mà developer khó tự triển khai.

**Điểm cần lưu ý:**
- STORAGE-3, STORAGE-4, STORAGE-6 hoàn toàn phụ thuộc vào thiết kế ứng dụng. Developer cần cấu hình `android:allowBackup="false"`, kiểm soát Content Provider permissions, và hạn chế chia sẻ dữ liệu với third-party SDK.

### 4.2 MASVS-CRYPTO — Phân tích

**Tổng quan:** Promon SHIELD đóng góp gián tiếp vào CRYPTO-1 thông qua việc sử dụng AES-256 GCM (SAROM) và White-Box Crypto (SLS). Tuy nhiên, 5/6 controls còn lại hoàn toàn thuộc trách nhiệm developer.

**Điểm mạnh:**
- SAROM sử dụng AES-256 GCM — thuật toán mã hóa xác thực (AEAD) hiện đại, đáp ứng CRYPTO-1 cho phần dữ liệu tĩnh nhúng trong APK.
- White-Box Cryptography trong SLS bảo vệ khóa mã hóa ngay cả khi attacker có quyền truy cập bộ nhớ đầy đủ.

**Điểm cần lưu ý:**
- Developer phải đảm bảo logic mật mã trong ứng dụng (CRYPTO-2 đến CRYPTO-6) sử dụng các thư viện và thuật toán đúng chuẩn. Promon SHIELD không can thiệp vào logic mật mã của ứng dụng.

### 4.3 MASVS-AUTH — Phân tích

**Tổng quan:** Promon SHIELD hỗ trợ gián tiếp AUTH-2 và AUTH-3 thông qua RASP và App Verify. AUTH-1 hoàn toàn thuộc trách nhiệm developer.

**Điểm mạnh:**
- RASP phát hiện môi trường bị compromise (root, hook, emulator) — ngăn chặn bypass biometric authentication trên thiết bị bị tấn công.
- App Verify cung cấp lớp xác thực bổ sung ở tầng API, đảm bảo chỉ ứng dụng gốc mới có thể thực hiện thao tác nhạy cảm.

**Điểm cần lưu ý:**
- Triển khai OAuth 2.0, PKCE, session management, và step-up authentication là trách nhiệm hoàn toàn của developer và backend team.

### 4.4 MASVS-NETWORK — Phân tích

**Tổng quan:** Promon SHIELD chỉ đóng góp một phần vào NETWORK-3 thông qua App Verify. NETWORK-1 và NETWORK-2 hoàn toàn thuộc trách nhiệm developer/DevOps.

**Điểm quan trọng:**
- App Verify **không phải** certificate pinning. Nó xác thực nguồn gốc của request (app gốc hay không), không mã hóa kênh truyền thông.
- Để đáp ứng đầy đủ NETWORK-3, cần triển khai certificate pinning trong mã nguồn ứng dụng (OkHttp CertificatePinner, TrustKit, hoặc Network Security Config).
- Kết hợp App Verify + Certificate Pinning sẽ cung cấp bảo vệ toàn diện: xác thực nguồn gốc request VÀ bảo vệ kênh truyền thông.

### 4.5 MASVS-PLATFORM — Phân tích

**Tổng quan:** Promon SHIELD chỉ đóng góp một phần vào PLATFORM-4 thông qua Task Hijacking Detection. 7/8 controls còn lại thuộc trách nhiệm developer.

**Điểm mạnh:**
- Task Hijacking Detection (StrandHogg, StrandHogg 2.0) là tính năng quan trọng mà developer khó tự triển khai.

**Điểm cần lưu ý:**
- Các controls PLATFORM-1 đến PLATFORM-8 liên quan đến thiết kế ứng dụng, cấu hình AndroidManifest/Info.plist, và coding practices. Developer phải tuân thủ Android/iOS security guidelines.

### 4.6 MASVS-CODE — Phân tích

**Tổng quan:** Promon SHIELD không đáp ứng CODE-2 (forced update). CODE-3 được hỗ trợ một phần. CODE-1 và CODE-4 thuộc trách nhiệm developer.

**Điểm cần lưu ý:**
- CODE-2 (forced update mechanism) là gap quan trọng. Cần triển khai riêng bằng Android In-App Update API hoặc Firebase Remote Config.
- CODE-3 (no known vulnerabilities): Code Protection bảo vệ third-party SDK khỏi bị phân tích, nhưng không tự động phát hiện CVE trong dependencies. Cần dùng SCA tools (OWASP Dependency-Check, Snyk).

---

## 5. Phân tích Chuyên sâu MASVS-RESILIENCE (R1–R8)

Đây là nhóm Promon SHIELD đáp ứng tốt nhất — tất cả 8/8 controls được đáp ứng đầy đủ.

### 5.1 RESILIENCE-1: Xác thực Tính toàn vẹn Platform

**Yêu cầu MASVS:** Ứng dụng xác thực tính toàn vẹn của platform (phát hiện thiết bị bị compromise).

**Cơ chế Promon SHIELD:**

| Tính năng | Android | iOS | Mức độ |
|---|---|---|---|
| Root Detection (SuperSU, Magisk, Kingroot) | ✅ | N/A | Đầy đủ |
| Root-hiding Detection (Magiskhide, Shamiko, Zygisk) | ✅ | N/A | Đầy đủ |
| Jailbreak Detection (Liberty Lite, v.v.) | N/A | ✅ | Đầy đủ |
| Bootloader Unlock Detection | ✅ | N/A | Đầy đủ |
| Emulator Detection (AVD, Genymotion, BlueStacks) | ✅ | ✅ | Đầy đủ |
| Virtual Space Detection (Parallel Space, Dual Space) | ✅ | N/A | Đầy đủ |
| ADB Detection | ✅ | N/A | Đầy đủ |
| Developer Options Detection | ✅ | ✅ | Đầy đủ |
| Disabled SIP Detection (iOS) | N/A | ✅ | Đầy đủ |

**Phản ứng:** ExitOn (thoát ngay), ExitOnURL (thoát + gửi thông báo), Callback API (xử lý tùy chỉnh).

**Đánh giá MASTG:** Tương ứng với MASTG-TEST-0007 (Testing Root Detection), MASTG-TEST-0008 (Testing Emulator Detection). Promon SHIELD đáp ứng đầy đủ cả hai test cases.

**Kết luận:** ✅ **Đáp ứng đầy đủ** — Phạm vi phát hiện rộng, bao gồm cả root-hiding tools tiên tiến.

---

### 5.2 RESILIENCE-2: Cơ chế Chống Giả mạo (Anti-Tampering)

**Yêu cầu MASVS:** Ứng dụng phát hiện và phản ứng khi bị sửa đổi (repackaging, code injection, resource tampering).

**Cơ chế Promon SHIELD:**

| Tính năng | Mô tả | Hiệu quả |
|---|---|---|
| **Signature Check** | Kiểm tra chữ ký số APK/IPA tại runtime | Phát hiện repackaging với chữ ký khác |
| **DEX Checksum Verification** | Tính và kiểm tra checksum toàn bộ mã DEX | Phát hiện thay đổi bytecode |
| **Resource Verification** | Kiểm tra tính toàn vẹn file tài nguyên (ảnh, icon, manifest) | Phát hiện resource tampering |
| **Repackaging Detection** | Phát hiện ứng dụng bị decompile, sửa đổi và đóng gói lại | Phát hiện toàn bộ quy trình repack |
| **Code Protection Checksumming** | Hash và checksum trên các vùng mã nhị phân | Phát hiện thay đổi trái phép trong binary |

**Kịch bản tấn công được bảo vệ:**
- Attacker decompile APK → sửa đổi logic → repack → ký lại với chữ ký khác → cài lại: **BỊ PHÁT HIỆN** (Signature Check + Checksum)
- Attacker inject code vào DEX tại runtime: **BỊ PHÁT HIỆN** (DEX Checksum)
- Attacker thay đổi resource files (icon, strings): **BỊ PHÁT HIỆN** (Resource Verification)

**Đánh giá MASTG:** Tương ứng với MASTG-TEST-0038 (Testing Anti-Tampering). Promon SHIELD đáp ứng đầy đủ.

**Kết luận:** ✅ **Đáp ứng đầy đủ** — Bảo vệ toàn diện chống repackaging và code injection.

---

### 5.3 RESILIENCE-3: Biện pháp Chống Phân tích Tĩnh

**Yêu cầu MASVS:** Ứng dụng triển khai biện pháp ngăn chặn static analysis (decompilation, reverse engineering).

**Cơ chế Promon SHIELD (Code Protection — Promon Code Protect™):**

| Kỹ thuật | Mô tả | Hiệu quả chống Static Analysis |
|---|---|---|
| **Multi-layer Obfuscation** | Biến đổi cấu trúc mã, hàm, biến, luồng thực thi | Làm cho decompiled code không thể đọc được |
| **Section Encryption** | Mã hóa các section nhạy cảm trong binary | Ngăn static extraction của logic cốt lõi |
| **Control-Flow Abstraction** | Định tuyến lại function call, phá vỡ liên kết giữa các khối mã | Làm rối cấu trúc luồng thực thi |
| **Advanced Symbol Obfuscation** | Đổi tên class/method/field; ẩn string, integer, operator | Loại bỏ thông tin ngữ nghĩa |
| **Debug Stripping** | Xóa toàn bộ thông tin debug, metadata, annotation | Giảm bề mặt tấn công static analysis |
| **String Encryption** | Mã hóa string literals trong mã nguồn | Ngăn trích xuất API keys, URLs, secrets bằng `strings` command |

**Ngôn ngữ được hỗ trợ:** Java, Kotlin, Swift, Objective-C, C, C++, JavaScript (React Native, Cordova), Dart (Flutter).

**So sánh với ProGuard/R8:**
- ProGuard/R8: Chỉ rename symbols và remove unused code — dễ dàng bị reverse với jadx/apktool
- Promon Code Protect: Section encryption + control-flow abstraction + runtime integrity check — làm cho decompilation trở nên bất khả thi (infeasible)

**Đánh giá MASTG:** Tương ứng với MASTG-TEST-0034 (Testing Obfuscation). Promon SHIELD vượt yêu cầu tối thiểu.

**Kết luận:** ✅ **Đáp ứng đầy đủ** — Mức độ bảo vệ vượt trội so với obfuscation truyền thống.

---

### 5.4 RESILIENCE-4: Biện pháp Chống Phân tích Động và Chống Instrumentation

**Yêu cầu MASVS:** Ứng dụng phát hiện và phản ứng với dynamic analysis tools và instrumentation frameworks.

**Cơ chế Promon SHIELD:**

| Tính năng | Công cụ bị phát hiện | Mức độ |
|---|---|---|
| **Debugger Detection** | Android Studio debugger, jdb, LLDB, GDB | ✅ Đầy đủ |
| **Code Tracer Detection** | strace, ltrace, ptrace | ✅ Đầy đủ |
| **Hooking Framework Detection** | Frida, Xposed, LSPosed, EdXposed, Riru, Cydia, Android DDI | ✅ Đầy đủ |
| **Native Code Hook Detection** | Hook ở tầng native code (GOT/PLT hooking) | ✅ Đầy đủ |
| **Memory Scanning Detection** | Quét bộ nhớ trái phép | ✅ Đầy đủ |
| **Anti-debugging traps** | Bẫy ngẫu nhiên trong Code Protection | ✅ Đầy đủ |

**Kịch bản tấn công được bảo vệ:**
- Attacker dùng Frida để hook hàm xác thực: **BỊ PHÁT HIỆN** (Hooking Framework Detection)
- Attacker attach debugger để trace logic: **BỊ PHÁT HIỆN** (Debugger Detection)
- Attacker dùng strace để theo dõi system calls: **BỊ PHÁT HIỆN** (Code Tracer Detection)
- Attacker scan bộ nhớ để tìm keys/tokens: **BỊ PHÁT HIỆN** (Memory Scanning Detection)

**Lưu ý về Frida:** Promon SHIELD phát hiện Frida theo nhiều phương pháp (process name, port, library injection). Tuy nhiên, Frida với cấu hình non-default (custom gadget, no-spawn) có thể khó phát hiện hơn — đây là giới hạn chung của tất cả giải pháp RASP.

**Đánh giá MASTG:** Tương ứng với MASTG-TEST-0031 (Testing Anti-Debugging), MASTG-TEST-0033 (Testing Runtime Integrity Checks). Promon SHIELD đáp ứng đầy đủ.

**Kết luận:** ✅ **Đáp ứng đầy đủ** — Phạm vi phát hiện rộng, bao gồm các công cụ phổ biến nhất.

---

### 5.5 RESILIENCE-5: Device Binding

**Yêu cầu MASVS:** Ứng dụng ràng buộc chức năng hoặc dữ liệu với thiết bị cụ thể.

**Cơ chế Promon SHIELD:**

| Module | Cơ chế Device Binding | Mô tả |
|---|---|---|
| **SLS (Secure Local Storage)** | Device ID binding | Dữ liệu được mã hóa với khóa gắn với Device ID duy nhất. Copy dữ liệu sang thiết bị khác → không thể giải mã |
| **SHIELD Core RASP (App Binding)** | App-to-device binding | Ràng buộc ứng dụng với thiết bị cụ thể — ngăn chạy ứng dụng trên thiết bị không được phép |

**Ví dụ thực tế:**
- Session token lưu trong SLS trên thiết bị A → copy sang thiết bị B → **KHÔNG THỂ ĐỌC** (Device ID khác)
- Ứng dụng được bind với thiết bị A → cài trên thiết bị B → **TỪ CHỐI KHỞI ĐỘNG**

**Đánh giá MASTG:** Tương ứng với MASTG-TEST-0041 (Testing Device Binding). Promon SHIELD đáp ứng đầy đủ với cả hai lớp binding.

**Kết luận:** ✅ **Đáp ứng đầy đủ** — Device binding ở cả tầng dữ liệu (SLS) và tầng ứng dụng (RASP).

---

### 5.6 RESILIENCE-6: Kiểm soát Bổ sung Hạn chế Truy cập

**Yêu cầu MASVS:** Ứng dụng triển khai các kiểm soát bổ sung để hạn chế truy cập vào chức năng hoặc dữ liệu nhạy cảm.

**Cơ chế Promon SHIELD:**

| Module | Kiểm soát | Mô tả |
|---|---|---|
| **App Verify (Promon Verify™)** | API Attestation | Backend từ chối request không có attestation token hợp lệ. Chỉ ứng dụng gốc (chưa bị sửa đổi) mới tạo được token hợp lệ |
| **SAROM** | Conditional Decryption | Từ chối giải mã secrets khi môi trường bị compromise (root, hook) |
| **SHIELD Core RASP** | Environment-based Access Control | Từ chối khởi động hoặc thực thi chức năng nhạy cảm khi phát hiện môi trường không an toàn |

**Kịch bản bảo vệ:**
- Ứng dụng bị repackage cố gọi API thanh toán: **BỊ TỪ CHỐI** (App Verify token không hợp lệ)
- Script tự động (Postman, curl) cố gọi API: **BỊ TỪ CHỐI** (không có attestation token)
- Ứng dụng chạy trên thiết bị root cố đọc API key từ SAROM: **BỊ TỪ CHỐI** (SAROM từ chối giải mã)

**Đánh giá MASTG:** Tương ứng với MASTG-TEST-0039 (Testing Additional Security Controls). Promon SHIELD đáp ứng đầy đủ.

**Kết luận:** ✅ **Đáp ứng đầy đủ** — Kiểm soát đa lớp từ tầng API đến tầng dữ liệu.

---

### 5.7 RESILIENCE-7: Phát hiện và Phản ứng với Giả mạo/Thao túng Môi trường

**Yêu cầu MASVS:** Ứng dụng phát hiện và phản ứng với tampering hoặc environmental manipulation.

**Cơ chế Promon SHIELD:**

| Thành phần | Vai trò |
|---|---|
| **SHIELD Core RASP (Reaction Engine)** | Phản ứng theo cấu hình: ExitOn (thoát ngay), ExitOnURL (thoát + gửi thông báo kèm thông tin thiết bị), Callback API (xử lý tùy chỉnh trong ứng dụng) |
| **Promon Insight** | Thu thập telemetry sự kiện bảo mật có timestamp và severity tag; xuất JSON/Protobuf đến SIEM; phát hiện tín hiệu gian lận ẩn |
| **Code Protection (Anti-tampering traps)** | Chèn bẫy ngẫu nhiên phát hiện runtime inspection và tampering |

**Ví dụ ExitOnURL payload:**
```
https://partners.promon.co/notifications/?path=AppName/reason=00/
manufacturer=Samsung/model=SM-G998B/Android-API=33/SecMod=2.3.0/
```

**Tích hợp với SOC/SIEM:**
- Promon Insight gửi sự kiện bảo mật có cấu trúc đến SIEM của VNPT Media
- SOC team nhận cảnh báo real-time khi phát hiện tấn công trên thiết bị người dùng
- Hỗ trợ điều tra sự cố với timeline đầy đủ

**Đánh giá MASTG:** Tương ứng với MASTG-TEST-0040 (Testing Tampering Detection). Promon SHIELD đáp ứng đầy đủ.

**Kết luận:** ✅ **Đáp ứng đầy đủ** — Phản ứng linh hoạt + telemetry cho SOC.

---

### 5.8 RESILIENCE-8: Phát hiện Root/Jailbreak

**Yêu cầu MASVS:** Ứng dụng phát hiện và phản ứng khi chạy trên thiết bị đã root hoặc jailbreak.

**Cơ chế Promon SHIELD:**

**Android Root Detection:**
- Phát hiện binary su, busybox, SuperSU, Magisk, Kingroot
- Phát hiện root-hiding tools: Magiskhide, Suhide, Rootcloak, Shamiko, Zygisk
- Phát hiện Bootloader Unlock (dấu hiệu thiết bị có thể bị root)
- Phát hiện hệ thống file bất thường (mount points, writable /system)

**iOS Jailbreak Detection:**
- Phát hiện Cydia, Sileo, Zebra (package managers)
- Phát hiện Liberty Lite và các jailbreak bypass tools
- Phát hiện file hệ thống bất thường (/Applications/Cydia.app, /bin/bash)
- Phát hiện sandbox escape

**Phản ứng:** ExitOn (thoát ngay lập tức) hoặc Callback API (cho phép ứng dụng xử lý tùy chỉnh — ví dụ: hiển thị cảnh báo, giới hạn chức năng).

**Lưu ý về root-hiding:** Magisk với Shamiko/Zygisk có thể ẩn root khỏi một số detection methods. Promon SHIELD sử dụng nhiều phương pháp phát hiện độc lập để tăng khả năng phát hiện, nhưng không có giải pháp nào đảm bảo 100% với root-hiding tiên tiến.

**Đánh giá MASTG:** Tương ứng với MASTG-TEST-0007 (Testing Root Detection). Promon SHIELD đáp ứng đầy đủ với phạm vi phát hiện rộng.

**Kết luận:** ✅ **Đáp ứng đầy đủ** — Phát hiện cả root thông thường và root-hiding tools.

---

### 5.9 Tóm tắt MASVS-RESILIENCE

| Control | Tên | Phân loại | Điểm nổi bật |
|---|---|---|---|
| RESILIENCE-1 | Platform Integrity | ✅ Đầy đủ | 9 loại kiểm tra môi trường |
| RESILIENCE-2 | Anti-Tampering | ✅ Đầy đủ | Signature + DEX Checksum + Resource Verification |
| RESILIENCE-3 | Anti-Static Analysis | ✅ Đầy đủ | Section Encryption + Control-Flow Abstraction |
| RESILIENCE-4 | Anti-Dynamic Analysis | ✅ Đầy đủ | Frida/Xposed/Debugger/Tracer Detection |
| RESILIENCE-5 | Device Binding | ✅ Đầy đủ | SLS Device ID Binding + App Binding |
| RESILIENCE-6 | Additional Access Controls | ✅ Đầy đủ | App Verify Attestation + SAROM Conditional Decrypt |
| RESILIENCE-7 | Tampering Response | ✅ Đầy đủ | ExitOn/Callback + Promon Insight Telemetry |
| RESILIENCE-8 | Root/Jailbreak Detection | ✅ Đầy đủ | Root-hiding tools detection |

**Tỷ lệ đáp ứng RESILIENCE: 8/8 = 100%**

---

## 6. Bảng Tỷ lệ % Đáp ứng theo Nhóm

### 6.1 Phương pháp Tính

Để tính tỷ lệ đáp ứng một cách công bằng, áp dụng hai cách tính:

**Cách 1 — Tính trên toàn bộ controls (bao gồm N/A):**
- ✅ Đáp ứng đầy đủ = 1.0 điểm
- ⚠️ Đáp ứng một phần = 0.5 điểm
- ❌ Không đáp ứng = 0.0 điểm
- 🔵 N/A (Developer) = 0.0 điểm (Promon SHIELD không đóng góp)

**Cách 2 — Tính trên controls trong phạm vi Promon SHIELD (loại trừ N/A):**
- Chỉ tính các controls mà Promon SHIELD có thể đóng góp (✅, ⚠️, ❌)

### 6.2 Bảng Chi tiết theo Nhóm

#### MASVS-STORAGE (7 controls)

| Control | Phân loại | Điểm |
|---|---|---|
| STORAGE-1 | ⚠️ Một phần | 0.5 |
| STORAGE-2 | ✅ Đầy đủ | 1.0 |
| STORAGE-3 | 🔵 N/A | 0.0 |
| STORAGE-4 | 🔵 N/A | 0.0 |
| STORAGE-5 | ⚠️ Một phần | 0.5 |
| STORAGE-6 | 🔵 N/A | 0.0 |
| STORAGE-7 | ✅ Đầy đủ | 1.0 |
| **Tổng** | | **3.0 / 7** |
| **Tỷ lệ (toàn bộ)** | | **43%** |
| **Tỷ lệ (trong phạm vi)** | 4 controls có thể đóng góp | **3.0/4 = 75%** |

#### MASVS-CRYPTO (6 controls)

| Control | Phân loại | Điểm |
|---|---|---|
| CRYPTO-1 | ⚠️ Một phần | 0.5 |
| CRYPTO-2 | 🔵 N/A | 0.0 |
| CRYPTO-3 | 🔵 N/A | 0.0 |
| CRYPTO-4 | 🔵 N/A | 0.0 |
| CRYPTO-5 | 🔵 N/A | 0.0 |
| CRYPTO-6 | 🔵 N/A | 0.0 |
| **Tổng** | | **0.5 / 6** |
| **Tỷ lệ (toàn bộ)** | | **8%** |
| **Tỷ lệ (trong phạm vi)** | 1 control có thể đóng góp | **0.5/1 = 50%** |

#### MASVS-AUTH (3 controls)

| Control | Phân loại | Điểm |
|---|---|---|
| AUTH-1 | 🔵 N/A | 0.0 |
| AUTH-2 | ⚠️ Một phần | 0.5 |
| AUTH-3 | ⚠️ Một phần | 0.5 |
| **Tổng** | | **1.0 / 3** |
| **Tỷ lệ (toàn bộ)** | | **33%** |
| **Tỷ lệ (trong phạm vi)** | 2 controls có thể đóng góp | **1.0/2 = 50%** |

#### MASVS-NETWORK (3 controls)

| Control | Phân loại | Điểm |
|---|---|---|
| NETWORK-1 | 🔵 N/A | 0.0 |
| NETWORK-2 | 🔵 N/A | 0.0 |
| NETWORK-3 | ⚠️ Một phần | 0.5 |
| **Tổng** | | **0.5 / 3** |
| **Tỷ lệ (toàn bộ)** | | **17%** |
| **Tỷ lệ (trong phạm vi)** | 1 control có thể đóng góp | **0.5/1 = 50%** |

#### MASVS-PLATFORM (8 controls)

| Control | Phân loại | Điểm |
|---|---|---|
| PLATFORM-1 | 🔵 N/A | 0.0 |
| PLATFORM-2 | 🔵 N/A | 0.0 |
| PLATFORM-3 | 🔵 N/A | 0.0 |
| PLATFORM-4 | ⚠️ Một phần | 0.5 |
| PLATFORM-5 | 🔵 N/A | 0.0 |
| PLATFORM-6 | 🔵 N/A | 0.0 |
| PLATFORM-7 | 🔵 N/A | 0.0 |
| PLATFORM-8 | 🔵 N/A | 0.0 |
| **Tổng** | | **0.5 / 8** |
| **Tỷ lệ (toàn bộ)** | | **6%** |
| **Tỷ lệ (trong phạm vi)** | 1 control có thể đóng góp | **0.5/1 = 50%** |

#### MASVS-CODE (4 controls)

| Control | Phân loại | Điểm |
|---|---|---|
| CODE-1 | 🔵 N/A | 0.0 |
| CODE-2 | ❌ Không đáp ứng | 0.0 |
| CODE-3 | ⚠️ Một phần | 0.5 |
| CODE-4 | 🔵 N/A | 0.0 |
| **Tổng** | | **0.5 / 4** |
| **Tỷ lệ (toàn bộ)** | | **13%** |
| **Tỷ lệ (trong phạm vi)** | 2 controls có thể đóng góp | **0.5/2 = 25%** |

#### MASVS-RESILIENCE (8 controls)

| Control | Phân loại | Điểm |
|---|---|---|
| RESILIENCE-1 | ✅ Đầy đủ | 1.0 |
| RESILIENCE-2 | ✅ Đầy đủ | 1.0 |
| RESILIENCE-3 | ✅ Đầy đủ | 1.0 |
| RESILIENCE-4 | ✅ Đầy đủ | 1.0 |
| RESILIENCE-5 | ✅ Đầy đủ | 1.0 |
| RESILIENCE-6 | ✅ Đầy đủ | 1.0 |
| RESILIENCE-7 | ✅ Đầy đủ | 1.0 |
| RESILIENCE-8 | ✅ Đầy đủ | 1.0 |
| **Tổng** | | **8.0 / 8** |
| **Tỷ lệ (toàn bộ)** | | **100%** |
| **Tỷ lệ (trong phạm vi)** | 8 controls | **100%** |

### 6.3 Bảng Tổng hợp Tỷ lệ Đáp ứng

| Nhóm | Tổng Controls | ✅ Đầy đủ | ⚠️ Một phần | ❌ Không đáp ứng | 🔵 N/A | Tỷ lệ (toàn bộ) | Tỷ lệ (trong phạm vi) |
|---|---|---|---|---|---|---|---|
| **STORAGE** | 7 | 2 | 2 | 0 | 3 | **43%** | **75%** |
| **CRYPTO** | 6 | 0 | 1 | 0 | 5 | **8%** | **50%** |
| **AUTH** | 3 | 0 | 2 | 0 | 1 | **33%** | **50%** |
| **NETWORK** | 3 | 0 | 1 | 0 | 2 | **17%** | **50%** |
| **PLATFORM** | 8 | 0 | 1 | 0 | 7 | **6%** | **50%** |
| **CODE** | 4 | 0 | 1 | 1 | 2 | **13%** | **25%** |
| **RESILIENCE** | 8 | 8 | 0 | 0 | 0 | **100%** | **100%** |
| **TỔNG CỘNG** | **39** | **10** | **8** | **1** | **20** | **36%** | **64%** |

**Ghi chú tính toán tổng:**
- Tổng điểm = 10×1.0 + 8×0.5 + 1×0.0 + 20×0.0 = **14.0 / 39 = 36%** (tính trên toàn bộ)
- Tổng điểm trong phạm vi = 14.0 / (39-20) = 14.0 / 19 = **74%** (loại trừ N/A)

### 6.4 Biểu đồ Tỷ lệ Đáp ứng (Text)

```
Tỷ lệ đáp ứng theo nhóm (tính trên toàn bộ controls):

RESILIENCE  ████████████████████████████████████████ 100%
STORAGE     █████████████████░░░░░░░░░░░░░░░░░░░░░░  43%
AUTH        █████████████░░░░░░░░░░░░░░░░░░░░░░░░░░  33%
NETWORK     ███████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  17%
CODE        █████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  13%
CRYPTO      ███░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░   8%
PLATFORM    ██░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░   6%

TỔNG CỘNG  ██████████████░░░░░░░░░░░░░░░░░░░░░░░░░  36%
(trong phạm vi Promon SHIELD)  ████████████████████████████░░░░  74%
```

---

## 7. Controls Không Đáp ứng và Biện pháp Bổ sung

### 7.1 Danh sách Controls Cần Biện pháp Bổ sung

Để đạt **MASVS Level 1** (mức bảo mật cơ bản), ứng dụng cần đáp ứng TẤT CẢ 39 controls. Phần này liệt kê các controls mà Promon SHIELD không đáp ứng hoặc chỉ đáp ứng một phần, kèm biện pháp bổ sung cụ thể.

---

#### 7.1.1 STORAGE Controls — Biện pháp Bổ sung

**STORAGE-3: Không chia sẻ dữ liệu nhạy cảm với bên thứ ba không cần thiết**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | 🔵 N/A — Trách nhiệm Developer |
| **Rủi ro** | Third-party SDK (analytics, ads, crash reporting) có thể thu thập dữ liệu nhạy cảm |
| **Biện pháp bổ sung** | 1. Audit tất cả third-party SDK được tích hợp; 2. Cấu hình data minimization cho Firebase Analytics, Crashlytics; 3. Không truyền PII vào SDK analytics; 4. Sử dụng Privacy Manifest (iOS 17+) để khai báo data usage |
| **Công cụ hỗ trợ** | MobSF (static analysis), Wireshark (network traffic analysis) |

**STORAGE-4: Không lưu dữ liệu nhạy cảm trong backup**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | 🔵 N/A — Trách nhiệm Developer |
| **Rủi ro** | Android Auto Backup và iTunes Backup có thể sao lưu dữ liệu nhạy cảm |
| **Biện pháp bổ sung** | **Android:** Cấu hình `android:allowBackup="false"` trong AndroidManifest.xml, hoặc dùng `<data-extraction-rules>` để loại trừ file nhạy cảm; **iOS:** Sử dụng `NSFileProtectionComplete` và đặt `NSURLIsExcludedFromBackupKey=true` cho file nhạy cảm |
| **Ưu tiên** | ⭐⭐⭐ Cao — Dễ triển khai, tác động lớn |

**STORAGE-6: Không lộ dữ liệu nhạy cảm qua IPC**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | 🔵 N/A — Trách nhiệm Developer |
| **Rủi ro** | Exported Activities, Services, Content Providers có thể bị ứng dụng khác truy cập |
| **Biện pháp bổ sung** | 1. Đặt `android:exported="false"` cho tất cả components không cần export; 2. Thêm `android:permission` cho Content Providers; 3. Validate Intent data trước khi xử lý; 4. Sử dụng `FLAG_SECURE` cho Activities chứa dữ liệu nhạy cảm |
| **Ưu tiên** | ⭐⭐⭐ Cao |

---

#### 7.1.2 CRYPTO Controls — Biện pháp Bổ sung

**CRYPTO-2 đến CRYPTO-6: Triển khai mật mã đúng chuẩn**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | 🔵 N/A — Trách nhiệm Developer |
| **Rủi ro** | Sử dụng thuật toán lỗi thời (MD5, SHA-1, DES), tái sử dụng khóa, PRNG yếu |
| **Biện pháp bổ sung** | 1. **CRYPTO-2:** Sử dụng Android Keystore System / iOS Secure Enclave cho key storage; 2. **CRYPTO-3:** AES-256-GCM cho symmetric, RSA-2048/ECDSA-256 cho asymmetric; 3. **CRYPTO-4:** Loại bỏ MD5, SHA-1, DES, 3DES, RC4; 4. **CRYPTO-5:** Mỗi khóa chỉ dùng cho một mục đích (encryption key ≠ signing key); 5. **CRYPTO-6:** Dùng `SecureRandom` (Android) / `SecRandomCopyBytes` (iOS) |
| **Công cụ kiểm tra** | MobSF, jadx (kiểm tra static), MASTG test cases |
| **Ưu tiên** | ⭐⭐⭐ Cao |

---

#### 7.1.3 AUTH Controls — Biện pháp Bổ sung

**AUTH-1: Giao thức xác thực và phân quyền an toàn**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | 🔵 N/A — Trách nhiệm Developer |
| **Biện pháp bổ sung** | 1. Triển khai OAuth 2.0 với PKCE (không dùng Implicit Flow); 2. JWT với RS256/ES256 (không dùng HS256 với shared secret); 3. Refresh token rotation; 4. Session timeout và invalidation; 5. Rate limiting trên backend |
| **Ưu tiên** | ⭐⭐⭐ Cao |

**AUTH-2: Xác thực cục bộ an toàn**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | ⚠️ Một phần (Promon SHIELD hỗ trợ gián tiếp) |
| **Biện pháp bổ sung** | 1. Sử dụng `BiometricPrompt` API (Android) / `LocalAuthentication` (iOS) — không tự triển khai biometric; 2. Lưu biometric-protected key trong Android Keystore / iOS Secure Enclave; 3. Không dùng biometric trên thiết bị root (RASP đã phát hiện — cần xử lý trong callback) |
| **Ưu tiên** | ⭐⭐ Trung bình |

---

#### 7.1.4 NETWORK Controls — Biện pháp Bổ sung

**NETWORK-1 & NETWORK-2: TLS đúng chuẩn**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | 🔵 N/A — Trách nhiệm Developer |
| **Biện pháp bổ sung** | 1. Bắt buộc HTTPS cho tất cả kết nối (không có HTTP fallback); 2. Cấu hình `android:usesCleartextTraffic="false"` trong AndroidManifest; 3. TLS 1.2+ (tắt TLS 1.0, 1.1); 4. Cipher suites: TLS_AES_128_GCM_SHA256, TLS_AES_256_GCM_SHA384 (TLS 1.3); 5. Cấu hình Network Security Config (Android) |
| **Ưu tiên** | ⭐⭐⭐ Cao |

**NETWORK-3: Certificate Verification / Certificate Pinning**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | ⚠️ Một phần (App Verify hỗ trợ gián tiếp) |
| **Biện pháp bổ sung** | 1. Triển khai **Certificate Pinning** trong mã nguồn: OkHttp `CertificatePinner` (Android), TrustKit (iOS/Android); 2. Pin public key hash (SPKI) thay vì certificate để dễ rotate; 3. Cấu hình backup pins để tránh lockout khi rotate certificate; 4. Kết hợp với App Verify để có bảo vệ 2 lớp |
| **Ưu tiên** | ⭐⭐⭐ Cao — Đặc biệt quan trọng cho ứng dụng tài chính |

---

#### 7.1.5 PLATFORM Controls — Biện pháp Bổ sung

**PLATFORM-1 đến PLATFORM-8: Bảo mật Platform**

| Control | Biện pháp Bổ sung | Ưu tiên |
|---|---|---|
| **PLATFORM-1** (Minimum Permissions) | Audit AndroidManifest.xml; loại bỏ permissions không dùng; dùng runtime permissions đúng cách | ⭐⭐⭐ Cao |
| **PLATFORM-2** (Input Validation) | Validate tất cả Intent extras, URL parameters, user input; dùng parameterized queries cho SQLite | ⭐⭐⭐ Cao |
| **PLATFORM-3** (URL Scheme Security) | Validate URL scheme parameters; không expose sensitive actions qua deep links; dùng App Links (Android) / Universal Links (iOS) | ⭐⭐ Trung bình |
| **PLATFORM-4** (IPC Security) | `android:exported="false"` cho components không cần export; validate Intent data | ⭐⭐⭐ Cao |
| **PLATFORM-5** (WebView JavaScript) | `webView.settings.javaScriptEnabled = false` trừ khi cần; không dùng `addJavascriptInterface` với untrusted content | ⭐⭐⭐ Cao |
| **PLATFORM-6** (WebView Protocol Handlers) | Whitelist allowed URL schemes trong WebView; block `file://`, `javascript:`, `data:` | ⭐⭐ Trung bình |
| **PLATFORM-7 & 8** (API Versions) | Cập nhật `targetSdkVersion` lên Android 14+ (API 34+); loại bỏ deprecated API calls | ⭐⭐ Trung bình |

---

#### 7.1.6 CODE Controls — Biện pháp Bổ sung

**CODE-1: Yêu cầu Platform Version Cập nhật**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | 🔵 N/A — Trách nhiệm Developer |
| **Biện pháp bổ sung** | Đặt `minSdkVersion >= 26` (Android 8.0) để loại bỏ thiết bị quá cũ; iOS `MinimumOSVersion >= 14.0` |
| **Ưu tiên** | ⭐⭐ Trung bình |

**CODE-2: Cơ chế Bắt buộc Cập nhật Ứng dụng** ❌ *Gap quan trọng nhất*

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | ❌ Không đáp ứng — Promon SHIELD không cung cấp tính năng này |
| **Rủi ro** | Người dùng tiếp tục dùng phiên bản cũ có lỗ hổng bảo mật đã biết |
| **Biện pháp bổ sung** | **Android:** Triển khai [In-App Update API](https://developer.android.com/guide/playcore/in-app-updates) (Immediate Update cho critical security patches); **iOS:** Kiểm tra version qua API backend khi app khởi động; **Cross-platform:** Firebase Remote Config để kiểm soát minimum version; Backend từ chối request từ phiên bản cũ hơn minimum version |
| **Ưu tiên** | ⭐⭐⭐ Cao |

**CODE-3: Không có Lỗ hổng Đã biết trong Dependencies**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | ⚠️ Một phần |
| **Biện pháp bổ sung** | 1. Tích hợp **SCA (Software Composition Analysis)** vào CI/CD: OWASP Dependency-Check, Snyk, GitHub Dependabot; 2. Cập nhật dependencies thường xuyên; 3. Theo dõi CVE database cho các thư viện đang dùng |
| **Ưu tiên** | ⭐⭐⭐ Cao |

**CODE-4: Chỉ dùng Components được Duy trì Tích cực**

| Hạng mục | Chi tiết |
|---|---|
| **Trạng thái** | 🔵 N/A — Trách nhiệm Developer |
| **Biện pháp bổ sung** | 1. Audit tất cả dependencies; loại bỏ thư viện không còn được maintain; 2. Ưu tiên thư viện có active maintainer và release gần đây; 3. Xem xét fork hoặc thay thế thư viện abandoned |
| **Ưu tiên** | ⭐⭐ Trung bình |

---

### 7.2 Tóm tắt Biện pháp Bổ sung theo Mức độ Ưu tiên

#### Ưu tiên Cao (⭐⭐⭐) — Cần triển khai ngay

| # | Biện pháp | Controls liên quan | Effort |
|---|---|---|---|
| 1 | **Certificate Pinning** (OkHttp/TrustKit) | NETWORK-3 | Trung bình |
| 2 | **Forced App Update** (In-App Update API) | CODE-2 | Thấp |
| 3 | **Backup Exclusion** (`allowBackup=false`) | STORAGE-4 | Rất thấp |
| 4 | **TLS Configuration** (Network Security Config) | NETWORK-1, NETWORK-2 | Thấp |
| 5 | **IPC Security** (exported=false, Intent validation) | STORAGE-6, PLATFORM-4 | Thấp |
| 6 | **Input Validation** (Intent extras, user input) | PLATFORM-2 | Trung bình |
| 7 | **WebView Security** (JS disabled, protocol whitelist) | PLATFORM-5, PLATFORM-6 | Thấp |
| 8 | **SCA Integration** (Dependency-Check, Snyk) | CODE-3 | Thấp |
| 9 | **Cryptography Best Practices** (AES-256-GCM, ECDSA) | CRYPTO-2 đến CRYPTO-6 | Cao |
| 10 | **OAuth 2.0 + PKCE** (Authentication Protocol) | AUTH-1 | Cao |

#### Ưu tiên Trung bình (⭐⭐) — Triển khai trong 3–6 tháng

| # | Biện pháp | Controls liên quan | Effort |
|---|---|---|---|
| 11 | **BiometricPrompt API** (Local Authentication) | AUTH-2 | Trung bình |
| 12 | **Minimum SDK Version** (API 26+) | CODE-1 | Thấp |
| 13 | **Permission Audit** (Minimum permissions) | PLATFORM-1 | Thấp |
| 14 | **URL Scheme Validation** (Deep link security) | PLATFORM-3 | Thấp |
| 15 | **Dependency Lifecycle Management** | CODE-4 | Thấp |
| 16 | **Third-party SDK Audit** (Data sharing) | STORAGE-3 | Trung bình |

---

## 8. Kết luận về Mức độ Đáp ứng MASVS Level 1

### 8.1 Định nghĩa MASVS Level 1

**MASVS Level 1** (L1 — Standard Security) là mức bảo mật cơ bản, yêu cầu ứng dụng đáp ứng tất cả 39 controls trong MASVS v2.x. Đây là mức tối thiểu được khuyến nghị cho mọi ứng dụng di động.

> *Lưu ý: MASVS v2.x không còn phân chia L1/L2/R như phiên bản cũ. Thay vào đó, tất cả controls đều áp dụng, với MASVS-RESILIENCE là nhóm tùy chọn cho ứng dụng yêu cầu bảo vệ cao hơn (banking, fintech). Tuy nhiên, trong bối cảnh đánh giá này, chúng tôi sử dụng khái niệm "MASVS Level 1" để chỉ mức đáp ứng toàn bộ 39 controls.*

### 8.2 Đánh giá Tổng thể

#### Kết quả Đánh giá Promon SHIELD

| Chỉ số | Giá trị |
|---|---|
| **Tổng controls MASVS v2.x** | 39 |
| **Controls Promon SHIELD đáp ứng đầy đủ (✅)** | 10 (26%) |
| **Controls Promon SHIELD đáp ứng một phần (⚠️)** | 8 (20%) |
| **Controls Promon SHIELD không đáp ứng (❌)** | 1 (3%) |
| **Controls ngoài phạm vi Promon SHIELD (🔵 N/A)** | 20 (51%) |
| **Tỷ lệ đóng góp của Promon SHIELD** | 36% (toàn bộ) / 74% (trong phạm vi) |

#### Nhận xét Quan trọng

**Promon SHIELD KHÔNG thể đơn độc đưa ứng dụng đạt MASVS Level 1.** Lý do:

1. **51% controls (20/39)** thuộc trách nhiệm developer — Promon SHIELD không can thiệp vào thiết kế kiến trúc, coding practices, hay cấu hình platform.

2. **Promon SHIELD là lớp bảo vệ bổ sung**, không phải giải pháp thay thế secure development lifecycle (SDLC).

3. Để đạt MASVS Level 1, cần **kết hợp** Promon SHIELD với:
   - Secure coding practices (CRYPTO, AUTH, NETWORK, PLATFORM, CODE)
   - Security testing (SAST, DAST, SCA)
   - Secure architecture design

### 8.3 Đánh giá theo Từng Nhóm

| Nhóm | Đánh giá | Nhận xét |
|---|---|---|
| **RESILIENCE** | ✅ **Xuất sắc (100%)** | Promon SHIELD đáp ứng hoàn toàn. Đây là nhóm cốt lõi của giải pháp. |
| **STORAGE** | ⚠️ **Tốt (75% trong phạm vi)** | SLS và SAROM đóng góp đáng kể. Cần developer bổ sung backup exclusion và IPC security. |
| **AUTH** | ⚠️ **Trung bình (50% trong phạm vi)** | RASP và App Verify hỗ trợ gián tiếp. Developer cần triển khai OAuth 2.0 và biometric auth đúng chuẩn. |
| **NETWORK** | ⚠️ **Trung bình (50% trong phạm vi)** | App Verify hỗ trợ một phần. Cần bổ sung certificate pinning và TLS configuration. |
| **CRYPTO** | ⚠️ **Trung bình (50% trong phạm vi)** | SAROM/SLS dùng mật mã tốt. Developer cần đảm bảo logic mật mã trong ứng dụng đúng chuẩn. |
| **PLATFORM** | ⚠️ **Trung bình (50% trong phạm vi)** | Task Hijacking Detection hỗ trợ một phần. Phần lớn là trách nhiệm developer. |
| **CODE** | ❌ **Yếu (25% trong phạm vi)** | CODE-2 (forced update) là gap quan trọng. Cần triển khai riêng. |

### 8.4 Lộ trình Đạt MASVS Level 1

Để đạt MASVS Level 1 với Promon SHIELD, VNPT Media cần thực hiện theo lộ trình sau:

#### Giai đoạn 1 — Triển khai Promon SHIELD (Tháng 1–2)
- Tích hợp Promon SHIELD vào MyVNPT và DigiShop
- Bật đầy đủ SHIELD Core RASP (Root, Jailbreak, Emulator, Debugger, Hooking, Repackaging)
- Tích hợp SLS cho session tokens và credentials
- Tích hợp SAROM cho API keys và secrets
- **Kết quả:** Đáp ứng hoàn toàn RESILIENCE (8/8) + một phần STORAGE, AUTH

#### Giai đoạn 2 — Secure Coding Fixes (Tháng 2–4)
- Triển khai Certificate Pinning (NETWORK-3)
- Cấu hình `allowBackup=false` và Data Protection (STORAGE-4)
- Triển khai In-App Update API (CODE-2)
- Cấu hình Network Security Config, TLS 1.2+ (NETWORK-1, NETWORK-2)
- Audit và fix IPC security (PLATFORM-4, STORAGE-6)
- Tích hợp SCA tools vào CI/CD (CODE-3)
- **Kết quả:** Đáp ứng thêm NETWORK, CODE, một phần PLATFORM

#### Giai đoạn 3 — Architecture & Crypto Review (Tháng 4–6)
- Review và cập nhật cryptographic implementation (CRYPTO-2 đến CRYPTO-6)
- Triển khai OAuth 2.0 + PKCE (AUTH-1)
- Triển khai BiometricPrompt đúng chuẩn (AUTH-2)
- Audit third-party SDK data sharing (STORAGE-3)
- WebView security hardening (PLATFORM-5, PLATFORM-6)
- **Kết quả:** Đáp ứng CRYPTO, AUTH, PLATFORM

#### Kết quả Dự kiến sau 6 tháng

| Nhóm | Trước | Sau (ước tính) |
|---|---|---|
| STORAGE | ~30% | ~85% |
| CRYPTO | ~8% | ~80% |
| AUTH | ~33% | ~85% |
| NETWORK | ~17% | ~90% |
| PLATFORM | ~6% | ~75% |
| CODE | ~13% | ~80% |
| RESILIENCE | ~0% | **100%** |
| **TỔNG** | **~15%** | **~85%** |

### 8.5 Kết luận Cuối cùng

> **Promon SHIELD là giải pháp không thể thiếu để đạt MASVS-RESILIENCE, nhưng cần kết hợp với secure development practices để đạt MASVS Level 1 toàn diện.**

**Điểm mạnh của Promon SHIELD trong bối cảnh MASVS:**
1. **Đáp ứng 100% MASVS-RESILIENCE** — nhóm khó nhất và quan trọng nhất cho ứng dụng tài chính/banking
2. **Đóng góp đáng kể vào STORAGE** thông qua SLS và SAROM
3. **Tăng cường AUTH và NETWORK** thông qua RASP và App Verify
4. **Giảm đáng kể effort** cho developer trong việc triển khai anti-tampering, anti-debugging, device binding

**Giới hạn của Promon SHIELD:**
1. Không thể thay thế secure coding practices cho CRYPTO, AUTH, NETWORK, PLATFORM, CODE
2. CODE-2 (forced update) là gap duy nhất mà Promon SHIELD hoàn toàn không đóng góp
3. Hiệu quả phụ thuộc vào cấu hình blueprint đúng đắn

**Khuyến nghị:**
- **Triển khai Promon SHIELD** để đạt MASVS-RESILIENCE ngay lập tức
- **Song song** thực hiện secure coding review theo lộ trình 3 giai đoạn
- **Mục tiêu 6 tháng:** Đạt ~85% MASVS Level 1 compliance
- **Mục tiêu 12 tháng:** Đạt 95%+ MASVS Level 1 compliance (sau khi hoàn thành architecture review)

---

## Phụ lục A: Bảng Mapping Đầy đủ — Tóm tắt Nhanh

| Control | Nhóm | Phân loại | Module Promon SHIELD |
|---|---|---|---|
| STORAGE-1 | STORAGE | ⚠️ Một phần | SLS |
| STORAGE-2 | STORAGE | ✅ Đầy đủ | SLS + SAROM |
| STORAGE-3 | STORAGE | 🔵 N/A | — |
| STORAGE-4 | STORAGE | 🔵 N/A | — |
| STORAGE-5 | STORAGE | ⚠️ Một phần | SLS |
| STORAGE-6 | STORAGE | 🔵 N/A | — |
| STORAGE-7 | STORAGE | ✅ Đầy đủ | SHIELD Core RASP |
| CRYPTO-1 | CRYPTO | ⚠️ Một phần | SAROM + SLS |
| CRYPTO-2 | CRYPTO | 🔵 N/A | — |
| CRYPTO-3 | CRYPTO | 🔵 N/A | — |
| CRYPTO-4 | CRYPTO | 🔵 N/A | — |
| CRYPTO-5 | CRYPTO | 🔵 N/A | — |
| CRYPTO-6 | CRYPTO | 🔵 N/A | — |
| AUTH-1 | AUTH | 🔵 N/A | — |
| AUTH-2 | AUTH | ⚠️ Một phần | SHIELD Core RASP |
| AUTH-3 | AUTH | ⚠️ Một phần | App Verify + RASP |
| NETWORK-1 | NETWORK | 🔵 N/A | — |
| NETWORK-2 | NETWORK | 🔵 N/A | — |
| NETWORK-3 | NETWORK | ⚠️ Một phần | App Verify |
| PLATFORM-1 | PLATFORM | 🔵 N/A | — |
| PLATFORM-2 | PLATFORM | 🔵 N/A | — |
| PLATFORM-3 | PLATFORM | 🔵 N/A | — |
| PLATFORM-4 | PLATFORM | ⚠️ Một phần | SHIELD Core RASP |
| PLATFORM-5 | PLATFORM | 🔵 N/A | — |
| PLATFORM-6 | PLATFORM | 🔵 N/A | — |
| PLATFORM-7 | PLATFORM | 🔵 N/A | — |
| PLATFORM-8 | PLATFORM | 🔵 N/A | — |
| CODE-1 | CODE | 🔵 N/A | — |
| CODE-2 | CODE | ❌ Không đáp ứng | — |
| CODE-3 | CODE | ⚠️ Một phần | Code Protection |
| CODE-4 | CODE | 🔵 N/A | — |
| RESILIENCE-1 | RESILIENCE | ✅ Đầy đủ | SHIELD Core RASP |
| RESILIENCE-2 | RESILIENCE | ✅ Đầy đủ | SHIELD Core RASP + Code Protection |
| RESILIENCE-3 | RESILIENCE | ✅ Đầy đủ | Code Protection |
| RESILIENCE-4 | RESILIENCE | ✅ Đầy đủ | SHIELD Core RASP |
| RESILIENCE-5 | RESILIENCE | ✅ Đầy đủ | SLS + SHIELD Core RASP |
| RESILIENCE-6 | RESILIENCE | ✅ Đầy đủ | App Verify + SAROM |
| RESILIENCE-7 | RESILIENCE | ✅ Đầy đủ | SHIELD Core RASP + Promon Insight |
| RESILIENCE-8 | RESILIENCE | ✅ Đầy đủ | SHIELD Core RASP |

---

## Phụ lục B: Tham chiếu MASTG Test Cases

| MASVS Control | MASTG Test Case | Mô tả |
|---|---|---|
| STORAGE-1, 2 | MASTG-TEST-0001 | Testing Local Data Storage |
| STORAGE-7 | MASTG-TEST-0006 | Testing for Sensitive Data in the Keyboard Cache |
| CRYPTO-1 to 6 | MASTG-TEST-0013 to 0018 | Testing Cryptographic APIs |
| AUTH-1 to 3 | MASTG-TEST-0019 to 0021 | Testing Authentication |
| NETWORK-1 to 3 | MASTG-TEST-0022 to 0024 | Testing Network Communication |
| PLATFORM-1 to 8 | MASTG-TEST-0025 to 0032 | Testing Platform Interaction |
| RESILIENCE-1 | MASTG-TEST-0007, 0008 | Testing Root/Emulator Detection |
| RESILIENCE-2 | MASTG-TEST-0038 | Testing Anti-Tampering |
| RESILIENCE-3 | MASTG-TEST-0034 | Testing Obfuscation |
| RESILIENCE-4 | MASTG-TEST-0031, 0033 | Testing Anti-Debugging, Runtime Integrity |
| RESILIENCE-5 | MASTG-TEST-0041 | Testing Device Binding |
| RESILIENCE-8 | MASTG-TEST-0007 | Testing Root Detection |

---

*Tài liệu này được lập dựa trên nghiên cứu tài liệu kỹ thuật của Promon AS và OWASP MASVS v2.x. Kết quả thực tế sẽ được xác nhận trong giai đoạn thử nghiệm (Task 5–7 của dự án).*

*Phiên bản: 1.0 | Ngày: 2025 | Tác giả: ANTT-TSC, VNPT Media*
