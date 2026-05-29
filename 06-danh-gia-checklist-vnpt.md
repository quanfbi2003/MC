# Đánh giá Promon SHIELD theo Checklist_VNPT 10 Tiêu chí

**Dự án:** Nghiên cứu & Đánh giá Giải pháp Bảo mật Ứng dụng Di động Promon SHIELD  
**Đơn vị thực hiện:** Phòng An ninh Thông tin – TSC (ANTT-TSC), VNPT Media  
**Phiên bản tài liệu:** 1.0  
**Ngày lập:** 2025  
**Yêu cầu liên quan:** Yêu cầu 9  
**Phụ thuộc:** Task 1 (`01-tong-quan-kien-truc-tinh-nang.md`), Task 5 (`05-danh-gia-masvs.md`)

---

## Mục lục

1. [Giới thiệu và Phạm vi Đánh giá](#1-giới-thiệu-và-phạm-vi-đánh-giá)
2. [Phương pháp Đánh giá](#2-phương-pháp-đánh-giá)
3. [Đánh giá Chi tiết 10 Tiêu chí](#3-đánh-giá-chi-tiết-10-tiêu-chí)
   - [Tiêu chí 1: Chống MiTM](#tiêu-chí-1-chống-mitm)
   - [Tiêu chí 2: Chống Hook](#tiêu-chí-2-chống-hook)
   - [Tiêu chí 3: Chống Debug](#tiêu-chí-3-chống-debug)
   - [Tiêu chí 4: Chống Repack](#tiêu-chí-4-chống-repack)
   - [Tiêu chí 5: Chống Dynamic Load APK](#tiêu-chí-5-chống-dynamic-load-apk)
   - [Tiêu chí 6: Chống Screen Mirroring](#tiêu-chí-6-chống-screen-mirroring)
   - [Tiêu chí 7: Chống Inject Camera](#tiêu-chí-7-chống-inject-camera)
   - [Tiêu chí 8: Root/Jailbreak Detection](#tiêu-chí-8-rootjailbreak-detection)
   - [Tiêu chí 9: Emulator Detection](#tiêu-chí-9-emulator-detection)
   - [Tiêu chí 10: Accessibility Abuse Detection](#tiêu-chí-10-accessibility-abuse-detection)
4. [Bảng Tổng hợp Điểm số](#4-bảng-tổng-hợp-điểm-số)
5. [Giải pháp Bổ sung cho Tiêu chí Chưa Đáp ứng Đầy đủ](#5-giải-pháp-bổ-sung)
6. [Đề xuất Cập nhật Checklist_VNPT](#6-đề-xuất-cập-nhật-checklist_vnpt)
7. [Kết luận và Khuyến nghị](#7-kết-luận-và-khuyến-nghị)

---

## 1. Giới thiệu và Phạm vi Đánh giá

### 1.1 Bối cảnh

Hai ứng dụng di động chiến lược của VNPT Media — **MyVNPT** và **DigiShop** — hiện đang đạt điểm bảo mật thấp theo Checklist_VNPT nội bộ:

| Ứng dụng | Điểm hiện tại | Tỷ lệ |
|---|---|---|
| **MyVNPT** | 2/10 | 20% |
| **DigiShop** | 1.5/10 | 15% |

Tài liệu này đánh giá mức độ cải thiện điểm số sau khi triển khai Promon SHIELD, dựa hoàn toàn trên nghiên cứu tài liệu kỹ thuật của nhà cung cấp và kết quả phân tích từ Task 1 và Task 5.

### 1.2 Checklist_VNPT — 10 Tiêu chí Bảo mật

| # | Tiêu chí | Mô tả ngắn |
|---|---|---|
| 1 | **Chống MiTM** | Ngăn chặn tấn công Man-in-the-Middle trên kênh truyền thông |
| 2 | **Chống Hook** | Phát hiện và ngăn chặn Frida, Xposed, LSPosed |
| 3 | **Chống Debug** | Phát hiện và ngăn chặn debugger attach vào tiến trình |
| 4 | **Chống Repack** | Phát hiện ứng dụng bị decompile → sửa đổi → repack → resign |
| 5 | **Chống Dynamic Load APK** | Ngăn sideloading và dynamic class loading độc hại |
| 6 | **Chống Screen Mirroring** | Phát hiện chiếu màn hình (scrcpy, Miracast, AirPlay) |
| 7 | **Chống Inject Camera** | Ngăn chặn giả mạo/inject camera feed |
| 8 | **Root/Jailbreak Detection** | Phát hiện thiết bị đã root (Android) hoặc jailbreak (iOS) |
| 9 | **Emulator Detection** | Phát hiện môi trường giả lập |
| 10 | **Accessibility Abuse Detection** | Phát hiện lạm dụng Accessibility Service |

### 1.3 Lưu ý Quan trọng

> **⚠️ Đánh giá này dựa trên tài liệu kỹ thuật nhà cung cấp — không phải kết quả kiểm tra thực tế.**  
> Kết quả thực tế có thể khác biệt và cần được xác nhận qua thử nghiệm trực tiếp.

---

## 2. Phương pháp Đánh giá

### 2.1 Thang điểm đánh giá

| Kết quả | Ký hiệu | Điểm | Định nghĩa |
|---|---|---|---|
| **Đáp ứng** | ✅ | 1.0 | Promon SHIELD có cơ chế bảo vệ trực tiếp, hiệu quả cao |
| **Một phần** | ⚠️ | 0.5 | Có bảo vệ nhưng không đầy đủ; cần kết hợp biện pháp bổ sung |
| **Không đáp ứng** | ❌ | 0.0 | Không có cơ chế bảo vệ liên quan |

### 2.2 Cấu trúc đánh giá từng tiêu chí

Mỗi tiêu chí được đánh giá theo cấu trúc:
1. **Mô tả mối đe dọa** — Tấn công cụ thể, kịch bản thực tế
2. **Cơ chế Promon SHIELD** — Module và tính năng liên quan
3. **Kết quả đánh giá** — Đáp ứng / Một phần / Không đáp ứng
4. **Lý giải kỹ thuật** — Phân tích chi tiết tại sao đạt/không đạt
5. **Giải pháp bổ sung** (nếu cần) — Biện pháp kết hợp để đạt bảo vệ đầy đủ

---

## 3. Đánh giá Chi tiết 10 Tiêu chí

---

### Tiêu chí 1: Chống MiTM (Man-in-the-Middle)

#### 3.1.1 Mô tả Mối đe dọa

Tấn công MiTM trên ứng dụng di động xảy ra khi kẻ tấn công đặt mình vào giữa kết nối giữa ứng dụng và backend server, cho phép:
- **Nghe lén (Eavesdropping):** Đọc toàn bộ dữ liệu truyền qua mạng (credentials, session tokens, dữ liệu cá nhân)
- **Sửa đổi dữ liệu (Tampering):** Thay đổi request/response trong quá trình truyền
- **Phân tích API (API Analysis):** Dùng Burp Suite, Charles Proxy để intercept và phân tích cấu trúc API

**Kịch bản tấn công điển hình:**
- Cài chứng chỉ CA giả vào thiết bị → intercept HTTPS traffic bằng Burp Suite
- Kết nối WiFi giả (Evil Twin AP) → intercept toàn bộ traffic
- ARP Spoofing trên mạng nội bộ → redirect traffic qua máy tấn công

**Công cụ phổ biến:** Burp Suite, Charles Proxy, mitmproxy, Wireshark, Ettercap

#### 3.1.2 Cơ chế Promon SHIELD

| Module | Tính năng | Mô tả |
|---|---|---|
| **App Verify (Promon Verify™)** | API Attestation | Tạo cryptographic attestation token chứng minh request đến từ ứng dụng gốc, chưa bị sửa đổi, chạy trên môi trường không bị compromise |
| **App Verify** | Backend Validation | Backend từ chối request không có token hợp lệ (HTTP 401/403) — ngăn request từ Burp Suite, Postman, script tự động |
| **SHIELD Core RASP** | Environment Check | Phát hiện môi trường bị compromise (root, hook) — gián tiếp ngăn attacker setup MiTM proxy |

**Giới hạn quan trọng của App Verify:**
- App Verify **không** thực hiện certificate pinning
- App Verify **không** mã hóa kênh truyền thông
- App Verify xác thực **nguồn gốc request** (app gốc hay không), không bảo vệ **kênh truyền thông**

#### 3.1.3 Kết quả Đánh giá

> **⚠️ MỘT PHẦN — Điểm: 0.5/1.0**

#### 3.1.4 Lý giải Kỹ thuật

App Verify cung cấp bảo vệ **gián tiếp** chống MiTM theo cơ chế sau:
- Nếu attacker intercept request và gửi lại (replay attack), token attestation sẽ không hợp lệ → backend từ chối
- Nếu attacker dùng ứng dụng bị repackage để gọi API, token sẽ không được tạo → backend từ chối
- Tuy nhiên, nếu attacker chỉ **nghe lén** (passive MiTM) mà không sửa đổi request, App Verify không ngăn được

**Khoảng trống bảo mật:**
- Attacker cài CA giả vào thiết bị → intercept HTTPS → đọc dữ liệu nhạy cảm: **KHÔNG BỊ NGĂN** bởi App Verify
- Cần **Certificate Pinning** để ngăn chặn hoàn toàn: ứng dụng từ chối kết nối nếu chứng chỉ server không khớp với pin đã cấu hình

**Kết luận:** Tiêu chí này chỉ được đáp ứng một phần. Cần bổ sung Certificate Pinning để đạt bảo vệ đầy đủ.

#### 3.1.5 Giải pháp Bổ sung

| Giải pháp | Mô tả | Ưu tiên |
|---|---|---|
| **Certificate Pinning** | Triển khai OkHttp `CertificatePinner` (Android) hoặc TrustKit (iOS). Pin public key hash (SPKI) thay vì certificate để dễ rotate. Cấu hình backup pins để tránh lockout | ⭐⭐⭐ Cao |
| **Mutual TLS (mTLS)** | Cả client và server xác thực lẫn nhau bằng certificate. Đặc biệt phù hợp cho API thanh toán DigiShop | ⭐⭐ Trung bình |
| **Network Security Config** | Cấu hình `android:usesCleartextTraffic="false"` và `<trust-anchors>` trong Android Network Security Config để chỉ tin tưởng CA cụ thể | ⭐⭐⭐ Cao |
| **App Verify + Cert Pinning** | Kết hợp cả hai: App Verify xác thực nguồn gốc request + Cert Pinning bảo vệ kênh truyền thông → bảo vệ 2 lớp toàn diện | ⭐⭐⭐ Cao |

---

### Tiêu chí 2: Chống Hook (Frida, Xposed, LSPosed)

#### 3.2.1 Mô tả Mối đe dọa

Hooking là kỹ thuật can thiệp vào luồng thực thi của ứng dụng tại runtime, cho phép:
- **Bypass xác thực:** Hook hàm kiểm tra mật khẩu/PIN để luôn trả về `true`
- **Đánh cắp dữ liệu:** Hook hàm mã hóa/giải mã để lấy plaintext
- **Phân tích logic:** Trace luồng thực thi để hiểu business logic
- **Bypass bảo vệ:** Hook hàm root detection để luôn trả về `false`

**Công cụ phổ biến:**
- **Frida:** Dynamic instrumentation toolkit — inject JavaScript vào tiến trình đang chạy
- **Xposed Framework:** Modify behavior của Android system và app mà không cần APK modification
- **LSPosed / EdXposed:** Phiên bản hiện đại của Xposed, hoạt động với Magisk
- **Riru / Zygisk:** Module injection framework cho Android

#### 3.2.2 Cơ chế Promon SHIELD

| Module | Tính năng | Công cụ bị phát hiện |
|---|---|---|
| **SHIELD Core RASP** | Hooking Framework Detection | Frida (tất cả phiên bản), Xposed, LSPosed, EdXposed, Riru, Cydia (iOS), Android DDI |
| **SHIELD Core RASP** | Native Code Hook Detection | Hook ở tầng native code (GOT/PLT hooking, inline hooking) |
| **SHIELD Core RASP** | Memory Scanning Detection | Phát hiện quét bộ nhớ trái phép để tìm hook points |
| **Code Protection** | Anti-debugging traps | Chèn bẫy ngẫu nhiên phá vỡ instrumentation |

**Cơ chế phát hiện Frida (đa phương pháp):**
- Phát hiện process name `frida-server`
- Phát hiện port mặc định Frida (27042)
- Phát hiện library injection (`frida-agent.so`)
- Phát hiện `/proc/self/maps` bất thường
- Phát hiện D-Bus socket của Frida

**Phản ứng:** ExitOn (thoát ngay) hoặc Callback API (xử lý tùy chỉnh)

#### 3.2.3 Kết quả Đánh giá

> **✅ ĐÁP ỨNG — Điểm: 1.0/1.0**

#### 3.2.4 Lý giải Kỹ thuật

Promon SHIELD có cơ chế phát hiện hooking framework **trực tiếp và toàn diện**:
- Phát hiện tất cả hooking framework phổ biến: Frida, Xposed, LSPosed, EdXposed, Riru, Cydia
- Sử dụng đa phương pháp phát hiện độc lập — tăng khả năng phát hiện kể cả khi attacker cố ẩn
- Phát hiện cả native code hooks (GOT/PLT hooking) — không chỉ Java-level hooks
- Phản ứng ngay lập tức khi phát hiện

**Lưu ý về giới hạn:**
- Frida với cấu hình non-default (custom gadget, no-spawn mode, custom port) có thể khó phát hiện hơn
- Đây là giới hạn chung của tất cả giải pháp RASP, không riêng Promon SHIELD
- Promon AS liên tục cập nhật signature để đối phó với kỹ thuật bypass mới

**Kết luận:** Tiêu chí này được đáp ứng đầy đủ. Phạm vi phát hiện rộng, bao gồm tất cả công cụ hooking phổ biến.

---

### Tiêu chí 3: Chống Debug (Debugger Attach)

#### 3.3.1 Mô tả Mối đe dọa

Debugging là kỹ thuật phân tích ứng dụng bằng cách attach debugger vào tiến trình đang chạy, cho phép:
- **Breakpoint:** Dừng thực thi tại điểm cụ thể để kiểm tra trạng thái
- **Step-through:** Thực thi từng dòng lệnh để hiểu logic
- **Memory inspection:** Đọc/ghi bộ nhớ tiến trình để lấy keys, tokens, credentials
- **Register manipulation:** Thay đổi giá trị register để bypass điều kiện kiểm tra

**Công cụ phổ biến:**
- **Android:** Android Studio Debugger, jdb (Java Debugger), LLDB, GDB
- **iOS:** Xcode Debugger, LLDB
- **Cross-platform:** IDA Pro, Ghidra (với debugging plugin), radare2

#### 3.3.2 Cơ chế Promon SHIELD

| Module | Tính năng | Mô tả |
|---|---|---|
| **SHIELD Core RASP** | Debugger Detection | Phát hiện debugger đang attach vào tiến trình (Android Studio debugger, jdb, LLDB, GDB) |
| **SHIELD Core RASP** | Code Tracer Detection | Phát hiện công cụ trace system calls (strace, ltrace, ptrace) |
| **Code Protection** | Anti-debugging traps | Chèn các bẫy ngẫu nhiên trong binary để phá vỡ debugging session |
| **Code Protection** | Debug Stripping | Xóa toàn bộ thông tin debug, metadata, annotation — giảm bề mặt tấn công |

**Cơ chế phát hiện debugger (đa phương pháp):**
- Kiểm tra `android:debuggable` flag trong AndroidManifest
- Kiểm tra `/proc/self/status` cho `TracerPid` (khác 0 = đang bị trace)
- Kiểm tra `ptrace(PTRACE_TRACEME)` — nếu thất bại = đang bị debug
- Kiểm tra timing (debugger làm chậm thực thi đáng kể)
- Phát hiện JDWP (Java Debug Wire Protocol) port đang mở

#### 3.3.3 Kết quả Đánh giá

> **✅ ĐÁP ỨNG — Điểm: 1.0/1.0**

#### 3.3.4 Lý giải Kỹ thuật

Promon SHIELD có cơ chế phát hiện debugger **trực tiếp và đa lớp**:
- Phát hiện cả Java-level debugging (JDWP) và native-level debugging (ptrace/LLDB)
- Sử dụng nhiều phương pháp phát hiện độc lập — khó bypass tất cả cùng lúc
- Code Protection thêm anti-debugging traps ngẫu nhiên — làm cho debugging trở nên không ổn định
- Debug Stripping loại bỏ thông tin debug — giảm đáng kể hiệu quả của debugger

**Kết luận:** Tiêu chí này được đáp ứng đầy đủ. Bảo vệ đa lớp chống cả Java và native debugging.

---

### Tiêu chí 4: Chống Repack (Decompile → Modify → Repack → Resign)

#### 3.4.1 Mô tả Mối đe dọa

Repackaging là quy trình tấn công phổ biến nhất trên Android:
1. **Decompile:** Dùng apktool/jadx để decompile APK thành smali/Java code
2. **Modify:** Sửa đổi logic (bypass license check, inject malware, thêm quảng cáo)
3. **Repack:** Đóng gói lại thành APK mới
4. **Resign:** Ký lại với chứng chỉ của attacker
5. **Distribute:** Phân phối qua store giả, phishing, sideloading

**Hậu quả:**
- Ứng dụng giả mạo đánh cắp credentials người dùng
- Inject malware/spyware vào ứng dụng hợp lệ
- Bypass kiểm tra bản quyền, in-app purchase
- Phân phối phiên bản độc hại mang thương hiệu VNPT

#### 3.4.2 Cơ chế Promon SHIELD

| Module | Tính năng | Mô tả |
|---|---|---|
| **SHIELD Core RASP** | Signature Check | Kiểm tra chữ ký số APK/IPA tại runtime — phát hiện ngay khi chữ ký khác với bản gốc |
| **SHIELD Core RASP** | DEX Checksum Verification | Tính và kiểm tra checksum toàn bộ mã DEX — phát hiện bất kỳ thay đổi bytecode nào |
| **SHIELD Core RASP** | Resource Verification | Kiểm tra tính toàn vẹn file tài nguyên (ảnh, icon, AndroidManifest.xml) |
| **SHIELD Core RASP** | Repackaging Detection | Phát hiện toàn bộ quy trình repack — kết hợp signature + checksum + resource |
| **Code Protection** | Checksumming & Integrity | Hash và checksum trên các vùng mã nhị phân — phát hiện thay đổi trái phép trong native code |

**Kịch bản bảo vệ:**
- Attacker decompile → sửa logic → repack → ký lại với chữ ký khác → cài lại: **BỊ PHÁT HIỆN** (Signature Check)
- Attacker inject code vào DEX mà không thay đổi chữ ký (patch binary): **BỊ PHÁT HIỆN** (DEX Checksum)
- Attacker thay đổi resource files (icon, strings.xml): **BỊ PHÁT HIỆN** (Resource Verification)
- Attacker sửa đổi native library (.so): **BỊ PHÁT HIỆN** (Code Protection Checksumming)

#### 3.4.3 Kết quả Đánh giá

> **✅ ĐÁP ỨNG — Điểm: 1.0/1.0**

#### 3.4.4 Lý giải Kỹ thuật

Promon SHIELD cung cấp bảo vệ chống repackaging **toàn diện và đa lớp**:
- Kiểm tra chữ ký số — lớp bảo vệ đầu tiên, phát hiện resign
- DEX checksum — phát hiện thay đổi bytecode dù không resign
- Resource verification — phát hiện thay đổi tài nguyên
- Native code checksumming — phát hiện thay đổi trong .so files

Kết hợp 4 lớp kiểm tra độc lập làm cho việc repackage mà không bị phát hiện trở nên cực kỳ khó khăn.

**Kết luận:** Tiêu chí này được đáp ứng đầy đủ. Bảo vệ toàn diện chống mọi hình thức repackaging.

---

### Tiêu chí 5: Chống Dynamic Load APK (Sideloading, Dynamic Class Loading)

#### 3.5.1 Mô tả Mối đe dọa

Dynamic loading là kỹ thuật tải và thực thi code từ nguồn bên ngoài tại runtime:
- **Sideloading:** Cài ứng dụng từ nguồn không rõ ràng (không qua Google Play/App Store)
- **Dynamic DEX Loading:** Tải file DEX/JAR từ server hoặc storage và thực thi
- **Plugin Framework:** Dùng framework như RePlugin, VirtualAPK để load plugin động
- **Malicious Update:** Tải và thực thi code độc hại giả dạng bản cập nhật

**Rủi ro:**
- Ứng dụng sideloaded có thể là phiên bản giả mạo chứa malware
- Dynamic class loading có thể inject code độc hại vào ứng dụng hợp lệ
- Bypass kiểm tra bảo mật của app store

#### 3.5.2 Cơ chế Promon SHIELD

| Module | Tính năng | Mô tả |
|---|---|---|
| **SHIELD Core RASP** | Sideloaded App Detection | Phát hiện ứng dụng được cài từ nguồn không rõ ràng (không qua official store) |
| **SHIELD Core RASP** | Virtual Space Detection | Phát hiện ứng dụng chạy trong không gian ảo hóa (Parallel Space, Dual Space, VirtualXposed) — môi trường thường dùng để chạy sideloaded apps |
| **SHIELD Core RASP** | Malware Detection | Phát hiện malware đã biết (StrandHogg, Cerberus, Gustuff) có thể được sideload |
| **SHIELD Core RASP** | App Integrity | Kiểm tra tính toàn vẹn của ứng dụng đang chạy — phát hiện nếu code bị inject từ nguồn ngoài |

**Giới hạn quan trọng:**
- Promon SHIELD **không có cơ chế chuyên biệt** để block dynamic class loading (`DexClassLoader`, `PathClassLoader`)
- Phát hiện sideloaded apps là phát hiện **môi trường** (có apps sideloaded trên thiết bị), không phải block hành vi dynamic loading trong ứng dụng được bảo vệ
- Không có API để kiểm soát `ClassLoader` của ứng dụng

#### 3.5.3 Kết quả Đánh giá

> **⚠️ MỘT PHẦN — Điểm: 0.5/1.0**

#### 3.5.4 Lý giải Kỹ thuật

Promon SHIELD bảo vệ **gián tiếp** chống Dynamic Load APK:
- **Phát hiện môi trường có sideloaded apps:** Cảnh báo khi thiết bị có ứng dụng từ nguồn không rõ ràng
- **Virtual Space Detection:** Ngăn ứng dụng chạy trong môi trường ảo hóa — nơi dynamic loading thường được thực hiện
- **App Integrity:** Phát hiện nếu code của ứng dụng bị thay đổi do dynamic injection

**Khoảng trống bảo mật:**
- Không block trực tiếp `DexClassLoader` trong ứng dụng được bảo vệ
- Không kiểm soát được việc ứng dụng tự tải DEX từ server (nếu developer cố ý làm vậy)
- Không phát hiện dynamic loading từ các nguồn hợp lệ (ví dụ: plugin framework)

#### 3.5.5 Giải pháp Bổ sung

| Giải pháp | Mô tả | Ưu tiên |
|---|---|---|
| **Cấm Dynamic Class Loading** | Không sử dụng `DexClassLoader` hoặc `PathClassLoader` với nguồn bên ngoài trong mã nguồn ứng dụng | ⭐⭐⭐ Cao |
| **Code Review** | Audit toàn bộ mã nguồn để đảm bảo không có dynamic loading từ nguồn không tin cậy | ⭐⭐⭐ Cao |
| **App Verify** | Kết hợp App Verify để đảm bảo chỉ ứng dụng gốc mới gọi được API — ngăn ứng dụng sideloaded giả mạo | ⭐⭐ Trung bình |
| **Google Play Integrity API** | Sử dụng Google Play Integrity API để xác nhận ứng dụng được cài từ Google Play | ⭐⭐ Trung bình |

---

### Tiêu chí 6: Chống Screen Mirroring (scrcpy, Miracast, AirPlay)

#### 3.6.1 Mô tả Mối đe dọa

Screen Mirroring cho phép chiếu màn hình thiết bị di động lên màn hình khác, tạo ra rủi ro:
- **Lộ thông tin nhạy cảm:** Mật khẩu, OTP, số tài khoản, thông tin cá nhân hiển thị trên màn hình chiếu
- **Ghi lại phiên làm việc:** Attacker ghi lại màn hình chiếu để phân tích sau
- **Shoulder surfing từ xa:** Quan sát hoạt động người dùng từ xa qua màn hình chiếu

**Công cụ và giao thức phổ biến:**
- **scrcpy:** Công cụ mã nguồn mở chiếu màn hình Android qua ADB/USB/WiFi
- **Miracast:** Chuẩn chiếu màn hình không dây của Wi-Fi Alliance
- **AirPlay:** Giao thức chiếu màn hình của Apple (iOS → Apple TV, Mac)
- **Google Cast:** Chiếu màn hình Android lên Chromecast
- **External Display:** Kết nối màn hình ngoài qua HDMI/USB-C

#### 3.6.2 Cơ chế Promon SHIELD

| Module | Tính năng | Mô tả |
|---|---|---|
| **SHIELD Core RASP** | Screen Mirroring Detection | Phát hiện chiếu màn hình qua scrcpy, Miracast, AirPlay, external display |
| **SHIELD Core RASP** | Screenshot Protection | Ngăn chặn chụp màn hình trái phép (FLAG_SECURE) |
| **SHIELD Core RASP** | Screen Recording Detection | Phát hiện ứng dụng đang ghi lại màn hình |

**Cơ chế phát hiện:**
- Kiểm tra `DisplayManager` API để phát hiện external display đang kết nối
- Phát hiện Miracast/WFD (Wi-Fi Direct Display) session đang hoạt động
- Phát hiện AirPlay session trên iOS
- Phát hiện scrcpy qua ADB connection và virtual display

**Phản ứng:** Callback API (ứng dụng có thể ẩn nội dung nhạy cảm hoặc hiển thị cảnh báo) hoặc ExitOn

#### 3.6.3 Kết quả Đánh giá

> **✅ ĐÁP ỨNG — Điểm: 1.0/1.0**

#### 3.6.4 Lý giải Kỹ thuật

Promon SHIELD có cơ chế phát hiện Screen Mirroring **trực tiếp và toàn diện**:
- Phát hiện tất cả phương thức chiếu màn hình phổ biến: scrcpy, Miracast, AirPlay, external display
- Kết hợp Screenshot Protection để ngăn chụp màn hình
- Phản ứng linh hoạt: có thể ẩn nội dung nhạy cảm thay vì thoát ứng dụng — UX thân thiện hơn

**Đây là tính năng mà developer khó tự triển khai** — yêu cầu kiến thức sâu về Android/iOS display APIs và các giao thức chiếu màn hình.

**Kết luận:** Tiêu chí này được đáp ứng đầy đủ. Phát hiện toàn diện các phương thức screen mirroring.

---

### Tiêu chí 7: Chống Inject Camera (Camera Feed Injection)

#### 3.7.1 Mô tả Mối đe dọa

Camera injection là kỹ thuật thay thế hoặc giả mạo luồng video từ camera thiết bị:
- **Virtual Camera:** Tạo camera ảo cung cấp video giả (ảnh tĩnh, video pre-recorded) thay vì camera thực
- **Camera Feed Hijacking:** Hook API camera để intercept và thay thế frame video
- **Deepfake Injection:** Inject video deepfake vào luồng camera để bypass xác thực khuôn mặt (Face ID, liveness detection)
- **Screen Capture as Camera:** Dùng màn hình capture làm nguồn camera giả

**Kịch bản tấn công điển hình:**
- Bypass eKYC (electronic Know Your Customer) bằng cách inject ảnh/video của người khác
- Bypass liveness detection trong xác thực sinh trắc học
- Giả mạo danh tính trong video call banking

#### 3.7.2 Cơ chế Promon SHIELD

| Module | Tính năng | Liên quan đến Camera Injection |
|---|---|---|
| **SHIELD Core RASP** | Overlay Attack Detection | Phát hiện overlay window che phủ camera preview — có thể dùng để inject fake camera feed |
| **SHIELD Core RASP** | Accessibility Service Detection | Phát hiện Accessibility Service có thể dùng để điều khiển camera |
| **SHIELD Core RASP** | Hooking Framework Detection | Phát hiện Frida/Xposed dùng để hook Camera API |
| **SHIELD Core RASP** | Virtual Space Detection | Phát hiện môi trường ảo hóa — nơi virtual camera thường được tạo |
| **SHIELD Core RASP** | Root Detection | Thiết bị root là điều kiện cần để inject virtual camera ở system level |

**Giới hạn quan trọng:**
- Promon SHIELD **không có module chuyên biệt** cho camera injection detection
- Không có API để xác thực tính xác thực của camera feed
- Không phát hiện được virtual camera được tạo ở system level (không cần root trên một số thiết bị)
- Không phát hiện được camera injection thông qua hardware-level manipulation

#### 3.7.3 Kết quả Đánh giá

> **⚠️ MỘT PHẦN — Điểm: 0.5/1.0**

#### 3.7.4 Lý giải Kỹ thuật

Promon SHIELD bảo vệ **gián tiếp** chống Camera Injection:
- **Phát hiện hooking:** Nếu attacker dùng Frida để hook Camera API → bị phát hiện bởi Hooking Framework Detection
- **Phát hiện root:** Camera injection ở system level thường yêu cầu root → bị phát hiện bởi Root Detection
- **Phát hiện overlay:** Overlay attack dùng để che camera preview → bị phát hiện bởi Overlay Detection
- **Phát hiện virtual space:** Virtual camera thường chạy trong virtual space → bị phát hiện

**Khoảng trống bảo mật:**
- Không phát hiện được virtual camera app hợp lệ (ví dụ: DroidCam, EpocCam) được cài trực tiếp
- Không có cơ chế xác thực tính xác thực của camera frame (liveness detection)
- Không phát hiện được camera injection không cần root và không dùng hooking

#### 3.7.5 Giải pháp Bổ sung

| Giải pháp | Mô tả | Ưu tiên |
|---|---|---|
| **Liveness Detection SDK** | Tích hợp SDK liveness detection chuyên biệt (FaceTec, iProov, Onfido) để phát hiện deepfake và replay attack | ⭐⭐⭐ Cao (nếu dùng eKYC) |
| **Camera Source Validation** | Kiểm tra `CameraCharacteristics` để xác nhận camera là hardware camera thực, không phải virtual | ⭐⭐ Trung bình |
| **Frame Integrity Check** | Phân tích metadata của camera frame (timestamp, sensor data) để phát hiện bất thường | ⭐⭐ Trung bình |
| **Kết hợp Root + Hook Detection** | Đảm bảo Root Detection và Hook Detection được bật — ngăn phần lớn camera injection attacks | ⭐⭐⭐ Cao |

---

### Tiêu chí 8: Root/Jailbreak Detection

#### 3.8.1 Mô tả Mối đe dọa

Root (Android) và Jailbreak (iOS) là quá trình leo thang đặc quyền trên thiết bị di động:
- **Root Android:** Cấp quyền superuser (root) cho ứng dụng — bypass sandbox Android
- **Jailbreak iOS:** Loại bỏ hạn chế của Apple — cho phép cài ứng dụng ngoài App Store, truy cập filesystem
- **Hậu quả:** Attacker có thể đọc/ghi bộ nhớ ứng dụng, bypass bảo mật hệ điều hành, cài malware ở system level

**Công cụ root phổ biến:**
- **Android:** Magisk (phổ biến nhất), SuperSU, Kingroot, KingoRoot
- **Root-hiding:** Magisk Hide, Shamiko, Zygisk (ẩn root khỏi detection)
- **iOS:** checkra1n, unc0ver, Palera1n (jailbreak tools)

#### 3.8.2 Cơ chế Promon SHIELD

| Module | Tính năng | Mô tả |
|---|---|---|
| **SHIELD Core RASP** | Root Detection (Android) | Phát hiện SuperSU, Magisk, Kingroot, KingoRoot; kiểm tra binary su, busybox; kiểm tra mount points bất thường |
| **SHIELD Core RASP** | Root-hiding Detection | Phát hiện Magisk Hide, Suhide, Rootcloak, Shamiko, Zygisk — các công cụ ẩn root |
| **SHIELD Core RASP** | Bootloader Unlock Detection | Phát hiện bootloader đã được mở khóa (dấu hiệu thiết bị có thể bị root) |
| **SHIELD Core RASP** | Jailbreak Detection (iOS) | Phát hiện Cydia, Sileo, Zebra; phát hiện Liberty Lite; kiểm tra file hệ thống bất thường (/Applications/Cydia.app, /bin/bash) |
| **SHIELD Core RASP** | Sandbox Escape Detection (iOS) | Phát hiện sandbox escape — dấu hiệu jailbreak |

**Phương pháp phát hiện root (đa lớp):**
- Kiểm tra sự tồn tại của binary su (`/system/bin/su`, `/system/xbin/su`)
- Kiểm tra package manager cho Magisk Manager, SuperSU
- Kiểm tra mount points bất thường (`/system` writable)
- Kiểm tra build tags (`ro.build.tags = test-keys`)
- Kiểm tra `/proc/self/maps` cho thư viện root
- Kiểm tra hành vi hệ thống bất thường

#### 3.8.3 Kết quả Đánh giá

> **✅ ĐÁP ỨNG — Điểm: 1.0/1.0**

#### 3.8.4 Lý giải Kỹ thuật

Promon SHIELD có cơ chế phát hiện Root/Jailbreak **trực tiếp, toàn diện và đa lớp**:
- Phát hiện cả root thông thường VÀ root-hiding tools (Shamiko, Zygisk) — đây là điểm khác biệt quan trọng
- Phát hiện bootloader unlock — cảnh báo sớm trước khi root
- Phát hiện jailbreak iOS với nhiều phương pháp
- Sử dụng nhiều phương pháp phát hiện độc lập — khó bypass tất cả cùng lúc

**Lưu ý về giới hạn:**
- Magisk với Shamiko/Zygisk cấu hình tiên tiến có thể qua mặt một số detection methods
- Không có giải pháp nào đảm bảo 100% với root-hiding tiên tiến
- Promon AS liên tục cập nhật để đối phó với kỹ thuật root-hiding mới

**Kết luận:** Tiêu chí này được đáp ứng đầy đủ. Phạm vi phát hiện rộng nhất trong các giải pháp thương mại.

---

### Tiêu chí 9: Emulator Detection

#### 3.9.1 Mô tả Mối đe dọa

Emulator là môi trường giả lập thiết bị di động trên máy tính, thường được dùng để:
- **Phân tích ứng dụng:** Chạy ứng dụng trong môi trường kiểm soát để phân tích hành vi
- **Automation testing:** Tự động hóa tấn công (credential stuffing, account takeover)
- **Bypass bảo mật:** Một số bảo vệ không hoạt động đúng trong emulator
- **Fraud:** Tạo nhiều tài khoản giả từ một máy tính

**Emulator phổ biến:**
- **Android:** Android Studio AVD (Android Virtual Device), Genymotion, BlueStacks, NoxPlayer, LDPlayer, MEmu
- **iOS:** Xcode iOS Simulator
- **Cloud:** AWS Device Farm, Firebase Test Lab (emulator-based)

#### 3.9.2 Cơ chế Promon SHIELD

| Module | Tính năng | Mô tả |
|---|---|---|
| **SHIELD Core RASP** | Emulator Detection (Android) | Phát hiện Android Studio AVD, Genymotion, BlueStacks, NoxPlayer và các emulator khác |
| **SHIELD Core RASP** | Emulator Detection (iOS) | Phát hiện Xcode iOS Simulator |
| **SHIELD Core RASP** | Virtual Space Detection | Phát hiện ứng dụng chạy trong không gian ảo hóa (Parallel Space, Dual Space) |
| **SHIELD Core RASP** | Virtualization Detection | Phát hiện môi trường ảo hóa ở tầng thấp hơn |

**Phương pháp phát hiện emulator (đa lớp):**
- Kiểm tra hardware properties (`ro.hardware`, `ro.product.model`, `ro.product.manufacturer`)
- Kiểm tra IMEI, IMSI, phone number (emulator thường có giá trị mặc định)
- Kiểm tra sensor data (emulator thiếu accelerometer, gyroscope thực)
- Kiểm tra CPU architecture và features
- Kiểm tra network interfaces bất thường
- Kiểm tra `/dev/qemu_pipe` (QEMU emulator)
- Kiểm tra build fingerprint

#### 3.9.3 Kết quả Đánh giá

> **✅ ĐÁP ỨNG — Điểm: 1.0/1.0**

#### 3.9.4 Lý giải Kỹ thuật

Promon SHIELD có cơ chế phát hiện Emulator **trực tiếp và toàn diện**:
- Phát hiện tất cả emulator phổ biến: AVD, Genymotion, BlueStacks, NoxPlayer
- Sử dụng nhiều phương pháp phát hiện độc lập — khó giả mạo tất cả cùng lúc
- Phát hiện cả iOS Simulator — quan trọng cho testing security
- Virtual Space Detection bổ sung thêm lớp phát hiện

**Kết luận:** Tiêu chí này được đáp ứng đầy đủ. Phát hiện toàn diện các emulator phổ biến.

---

### Tiêu chí 10: Accessibility Abuse Detection

#### 3.10.1 Mô tả Mối đe dọa

Accessibility Service là tính năng Android/iOS hỗ trợ người dùng khuyết tật, nhưng thường bị lạm dụng:
- **Keylogging:** Đọc tất cả text được nhập vào bất kỳ ứng dụng nào (mật khẩu, OTP, số thẻ)
- **Screen Reading:** Đọc nội dung màn hình của ứng dụng khác (số dư tài khoản, thông tin cá nhân)
- **Auto-click:** Tự động thực hiện thao tác (chuyển tiền, xác nhận giao dịch) mà không cần người dùng
- **Overlay Attack:** Hiển thị overlay giả mạo lên trên ứng dụng hợp lệ để đánh cắp credentials
- **Banking Trojan:** Nhiều banking trojan (Anubis, Cerberus, Gustuff) lạm dụng Accessibility Service

**Kịch bản tấn công điển hình:**
- Malware yêu cầu quyền Accessibility → đọc OTP từ SMS app → tự động xác nhận giao dịch
- Overlay attack: hiển thị form đăng nhập giả lên trên MyVNPT → đánh cắp credentials

#### 3.10.2 Cơ chế Promon SHIELD

| Module | Tính năng | Mô tả |
|---|---|---|
| **SHIELD Core RASP** | Screen Reader Detection | Phát hiện Accessibility Service đang đọc màn hình của ứng dụng |
| **SHIELD Core RASP** | Accessibility Service Detection | Phát hiện Accessibility Service có khả năng thực hiện thao tác tự động |
| **SHIELD Core RASP** | Overlay Attack Detection | Phát hiện overlay window hiển thị lên trên ứng dụng (phishing overlay) |
| **SHIELD Core RASP** | Untrusted Keyboard Detection | Phát hiện bàn phím không tin cậy/keylogger |

**Cơ chế phát hiện:**
- Kiểm tra danh sách Accessibility Services đang hoạt động
- Phân tích quyền của Accessibility Service (có quyền đọc màn hình không?)
- Phát hiện overlay window với `TYPE_APPLICATION_OVERLAY` hoặc `TYPE_SYSTEM_ALERT`
- Kiểm tra Input Method (IME) có phải bàn phím tin cậy không

**Phản ứng:** Callback API (ứng dụng có thể hiển thị cảnh báo và yêu cầu người dùng tắt Accessibility Service) hoặc ExitOn

#### 3.10.3 Kết quả Đánh giá

> **✅ ĐÁP ỨNG — Điểm: 1.0/1.0**

#### 3.10.4 Lý giải Kỹ thuật

Promon SHIELD có cơ chế phát hiện Accessibility Abuse **trực tiếp và toàn diện**:
- Phát hiện Screen Reader và Accessibility Service đang hoạt động
- Phát hiện Overlay Attack — bảo vệ chống phishing overlay
- Phát hiện Untrusted Keyboard — bảo vệ chống keylogging
- Phản ứng linh hoạt: có thể cảnh báo người dùng thay vì thoát ngay — UX thân thiện hơn

**Đây là tính năng đặc biệt quan trọng** cho ứng dụng tài chính như MyVNPT và DigiShop, nơi banking trojan thường lạm dụng Accessibility Service để đánh cắp credentials và tự động thực hiện giao dịch.

**Kết luận:** Tiêu chí này được đáp ứng đầy đủ. Bảo vệ toàn diện chống Accessibility Abuse.

---

## 4. Bảng Tổng hợp Điểm số

### 4.1 Bảng Đánh giá Tổng hợp 10 Tiêu chí

| # | Tiêu chí | Kết quả | Điểm | Module Promon SHIELD | Ghi chú |
|---|---|---|---|---|---|
| 1 | **Chống MiTM** | ⚠️ Một phần | 0.5 | App Verify (API Attestation) | Cần bổ sung Certificate Pinning |
| 2 | **Chống Hook** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP (Hooking Detection) | Phát hiện Frida, Xposed, LSPosed, Riru |
| 3 | **Chống Debug** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP (Debugger Detection) | Phát hiện cả Java và native debugger |
| 4 | **Chống Repack** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP (App Integrity) | Signature + DEX Checksum + Resource Verification |
| 5 | **Chống Dynamic Load APK** | ⚠️ Một phần | 0.5 | SHIELD Core RASP (Sideload + Virtual Space) | Không block DexClassLoader trực tiếp |
| 6 | **Chống Screen Mirroring** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP (Screen Mirroring Detection) | Phát hiện scrcpy, Miracast, AirPlay |
| 7 | **Chống Inject Camera** | ⚠️ Một phần | 0.5 | SHIELD Core RASP (Overlay + Hook Detection) | Không có module chuyên biệt cho camera |
| 8 | **Root/Jailbreak Detection** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP (Root/Jailbreak Detection) | Phát hiện cả root-hiding tools |
| 9 | **Emulator Detection** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP (Emulator Detection) | Phát hiện AVD, Genymotion, BlueStacks |
| 10 | **Accessibility Abuse Detection** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP (Accessibility Detection) | Phát hiện Screen Reader, Overlay Attack |
| | **TỔNG CỘNG** | | **8.5/10** | | |

### 4.2 Phân tích Kết quả

| Phân loại | Số tiêu chí | Tiêu chí |
|---|---|---|
| ✅ **Đáp ứng đầy đủ** | 7 | #2, #3, #4, #6, #8, #9, #10 |
| ⚠️ **Đáp ứng một phần** | 3 | #1 (MiTM), #5 (Dynamic Load), #7 (Camera) |
| ❌ **Không đáp ứng** | 0 | — |

### 4.3 Bảng So sánh Điểm số: Trước và Sau Promon SHIELD

#### Phương pháp tính điểm

Điểm số Checklist_VNPT được tính theo thang điểm 10, trong đó mỗi tiêu chí có trọng số bằng nhau (1 điểm/tiêu chí).

**Điểm ban đầu (trước Promon SHIELD):**
- MyVNPT: 2/10 (đáp ứng 2 tiêu chí)
- DigiShop: 1.5/10 (đáp ứng 1.5 tiêu chí)

**Điểm sau khi triển khai Promon SHIELD:**

Promon SHIELD đóng góp điểm cho các tiêu chí mà ứng dụng chưa đáp ứng. Giả định rằng điểm ban đầu của cả hai ứng dụng đến từ các tiêu chí khác nhau, và Promon SHIELD bổ sung bảo vệ cho các tiêu chí còn lại.

| Tiêu chí | Điểm Promon SHIELD | Ghi chú |
|---|---|---|
| Chống MiTM | 0.5 | Một phần — cần bổ sung Cert Pinning |
| Chống Hook | 1.0 | Đầy đủ |
| Chống Debug | 1.0 | Đầy đủ |
| Chống Repack | 1.0 | Đầy đủ |
| Chống Dynamic Load APK | 0.5 | Một phần — cần bổ sung code review |
| Chống Screen Mirroring | 1.0 | Đầy đủ |
| Chống Inject Camera | 0.5 | Một phần — cần bổ sung liveness detection |
| Root/Jailbreak Detection | 1.0 | Đầy đủ |
| Emulator Detection | 1.0 | Đầy đủ |
| Accessibility Abuse Detection | 1.0 | Đầy đủ |
| **Tổng đóng góp Promon SHIELD** | **8.5/10** | |

#### Bảng So sánh Điểm số Tổng hợp

| Ứng dụng | Điểm ban đầu | Điểm sau Promon SHIELD | Cải thiện | Tỷ lệ cải thiện |
|---|---|---|---|---|
| **MyVNPT** | 2.0/10 | **8.5/10** | +6.5 điểm | +325% |
| **DigiShop** | 1.5/10 | **8.5/10** | +7.0 điểm | +467% |

> **Lưu ý về phương pháp tính:** Điểm sau Promon SHIELD (8.5/10) phản ánh mức độ đáp ứng của Promon SHIELD đối với 10 tiêu chí Checklist_VNPT. Điểm thực tế có thể cao hơn nếu ứng dụng đã đáp ứng một số tiêu chí trước khi triển khai (ví dụ: MyVNPT đã đạt 2/10 ban đầu). Điểm tối đa có thể đạt được là **9.5–10/10** nếu bổ sung thêm Certificate Pinning, giải pháp Dynamic Load APK, và Liveness Detection.

#### Kịch bản điểm số chi tiết

**Kịch bản 1 — Chỉ triển khai Promon SHIELD (không bổ sung):**
- Điểm đạt được: **8.5/10**
- MyVNPT: 2.0 → 8.5 (+6.5)
- DigiShop: 1.5 → 8.5 (+7.0)

**Kịch bản 2 — Promon SHIELD + Certificate Pinning:**
- Tiêu chí MiTM: 0.5 → 1.0 (+0.5)
- Điểm đạt được: **9.0/10**

**Kịch bản 3 — Promon SHIELD + Cert Pinning + Dynamic Load Fix + Liveness Detection:**
- Tất cả tiêu chí đạt đầy đủ
- Điểm đạt được: **10/10**

---

## 5. Giải pháp Bổ sung cho Tiêu chí Chưa Đáp ứng Đầy đủ

### 5.1 Tiêu chí 1: Chống MiTM — Giải pháp Bổ sung

**Vấn đề:** App Verify xác thực nguồn gốc request nhưng không bảo vệ kênh truyền thông. Attacker vẫn có thể intercept HTTPS traffic bằng cách cài CA giả.

#### Giải pháp A: Certificate Pinning (Ưu tiên cao nhất)

**Mô tả kỹ thuật:**
```java
// Android — OkHttp CertificatePinner
OkHttpClient client = new OkHttpClient.Builder()
    .certificatePinner(new CertificatePinner.Builder()
        .add("api.myvnpt.vn", "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=")
        .add("api.myvnpt.vn", "sha256/BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB=") // backup pin
        .build())
    .build();
```

**Lưu ý triển khai:**
- Pin **public key hash (SPKI)** thay vì certificate hash — dễ rotate hơn
- Luôn có ít nhất **2 pins** (1 active + 1 backup) để tránh lockout khi rotate
- Cập nhật pins trước khi certificate hết hạn (ít nhất 30 ngày trước)
- Test kỹ trên cả Android và iOS trước khi release

**Công cụ hỗ trợ:**
- **Android:** OkHttp `CertificatePinner`, Android Network Security Config
- **iOS:** TrustKit, URLSession với custom `URLSessionDelegate`
- **Cross-platform:** TrustKit (hỗ trợ cả Android và iOS)

**Effort:** Trung bình (2–3 ngày developer) | **Ưu tiên:** ⭐⭐⭐ Cao

#### Giải pháp B: Mutual TLS (mTLS) — Cho API nhạy cảm

**Mô tả:** Cả client (ứng dụng) và server đều xác thực lẫn nhau bằng certificate. Đặc biệt phù hợp cho API thanh toán DigiShop.

**Effort:** Cao (cần thay đổi cả backend và ứng dụng) | **Ưu tiên:** ⭐⭐ Trung bình

#### Giải pháp C: Network Security Config (Android)

```xml
<!-- res/xml/network_security_config.xml -->
<network-security-config>
    <domain-config>
        <domain includeSubdomains="true">api.myvnpt.vn</domain>
        <pin-set expiration="2026-01-01">
            <pin digest="SHA-256">AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=</pin>
            <pin digest="SHA-256">BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB=</pin>
        </pin-set>
    </domain-config>
</network-security-config>
```

**Effort:** Thấp (1 ngày) | **Ưu tiên:** ⭐⭐⭐ Cao

---

### 5.2 Tiêu chí 5: Chống Dynamic Load APK — Giải pháp Bổ sung

**Vấn đề:** Promon SHIELD phát hiện môi trường có sideloaded apps nhưng không block dynamic class loading trực tiếp trong ứng dụng.

#### Giải pháp A: Cấm Dynamic Class Loading trong mã nguồn

**Mô tả:** Audit toàn bộ mã nguồn để đảm bảo không sử dụng `DexClassLoader` hoặc `PathClassLoader` với nguồn bên ngoài.

```java
// KHÔNG làm thế này:
DexClassLoader loader = new DexClassLoader(
    "/sdcard/malicious.dex",  // Nguồn bên ngoài — nguy hiểm!
    context.getCacheDir().getAbsolutePath(),
    null,
    context.getClassLoader()
);

// Chỉ load từ assets hoặc resources nội bộ nếu cần:
// Hoặc tốt nhất là không dùng dynamic loading
```

**Effort:** Thấp–Trung bình (tùy mức độ sử dụng dynamic loading hiện tại) | **Ưu tiên:** ⭐⭐⭐ Cao

#### Giải pháp B: Google Play Integrity API

**Mô tả:** Sử dụng Google Play Integrity API để xác nhận ứng dụng được cài từ Google Play và chạy trên thiết bị không bị compromise.

```java
// Kiểm tra Play Integrity khi khởi động
IntegrityManager integrityManager = IntegrityManagerFactory.create(context);
Task<IntegrityTokenResponse> integrityTokenResponse = integrityManager
    .requestIntegrityToken(IntegrityTokenRequest.builder()
        .setNonce(generateNonce())
        .build());
```

**Effort:** Trung bình (2–3 ngày) | **Ưu tiên:** ⭐⭐ Trung bình

#### Giải pháp C: Kết hợp App Verify + Sideload Detection

**Mô tả:** Kết hợp App Verify (xác thực ứng dụng gốc) với Sideload Detection của Promon SHIELD để tạo lớp bảo vệ kép.

**Effort:** Thấp (đã có sẵn trong Promon SHIELD) | **Ưu tiên:** ⭐⭐⭐ Cao

---

### 5.3 Tiêu chí 7: Chống Inject Camera — Giải pháp Bổ sung

**Vấn đề:** Promon SHIELD không có module chuyên biệt cho camera injection detection. Bảo vệ chỉ gián tiếp qua overlay/hook/root detection.

#### Giải pháp A: Liveness Detection SDK (Quan trọng nhất cho eKYC)

**Mô tả:** Tích hợp SDK liveness detection chuyên biệt để phát hiện deepfake, replay attack và camera injection.

**Các SDK phổ biến:**
| SDK | Nhà cung cấp | Tính năng | Chi phí |
|---|---|---|---|
| **FaceTec** | FaceTec Inc. | 3D liveness, deepfake detection, iBeta Level 2 certified | Thương mại |
| **iProov** | iProov Ltd. | Genuine Presence Assurance, chống deepfake | Thương mại |
| **Onfido** | Onfido | Document + face verification, liveness | Thương mại |
| **AWS Rekognition** | Amazon | Face liveness detection | Pay-per-use |

**Effort:** Cao (1–2 tuần tích hợp) | **Ưu tiên:** ⭐⭐⭐ Cao (nếu ứng dụng dùng eKYC/face auth)

#### Giải pháp B: Camera Source Validation

**Mô tả:** Kiểm tra `CameraCharacteristics` để xác nhận camera là hardware camera thực.

```java
CameraManager cameraManager = (CameraManager) getSystemService(CAMERA_SERVICE);
for (String cameraId : cameraManager.getCameraIdList()) {
    CameraCharacteristics characteristics = cameraManager.getCameraCharacteristics(cameraId);
    Integer facing = characteristics.get(CameraCharacteristics.LENS_FACING);
    // Kiểm tra các đặc tính hardware để phát hiện virtual camera
    Integer hardwareLevel = characteristics.get(
        CameraCharacteristics.INFO_SUPPORTED_HARDWARE_LEVEL);
    // LEGACY level thường là dấu hiệu của virtual camera
}
```

**Effort:** Trung bình (2–3 ngày) | **Ưu tiên:** ⭐⭐ Trung bình

#### Giải pháp C: Đảm bảo Root + Hook Detection được bật

**Mô tả:** Phần lớn camera injection attacks yêu cầu root hoặc hooking. Đảm bảo Root Detection và Hook Detection của Promon SHIELD được bật với phản ứng ExitOn.

**Effort:** Rất thấp (cấu hình blueprint) | **Ưu tiên:** ⭐⭐⭐ Cao

---

### 5.4 Tóm tắt Giải pháp Bổ sung theo Mức độ Ưu tiên

| # | Giải pháp | Tiêu chí | Effort | Ưu tiên | Tác động |
|---|---|---|---|---|---|
| 1 | **Certificate Pinning** (OkHttp/TrustKit) | MiTM (#1) | Trung bình | ⭐⭐⭐ | Đưa tiêu chí #1 từ 0.5 → 1.0 |
| 2 | **Network Security Config** (Android) | MiTM (#1) | Thấp | ⭐⭐⭐ | Bổ sung bảo vệ TLS |
| 3 | **Cấm Dynamic Class Loading** (code audit) | Dynamic Load (#5) | Thấp–TB | ⭐⭐⭐ | Đưa tiêu chí #5 từ 0.5 → 1.0 |
| 4 | **Root + Hook Detection bật ExitOn** | Camera (#7) | Rất thấp | ⭐⭐⭐ | Tăng bảo vệ gián tiếp cho #7 |
| 5 | **Liveness Detection SDK** | Camera (#7) | Cao | ⭐⭐⭐ | Đưa tiêu chí #7 từ 0.5 → 1.0 (nếu dùng eKYC) |
| 6 | **Google Play Integrity API** | Dynamic Load (#5) | Trung bình | ⭐⭐ | Bổ sung xác thực nguồn gốc app |
| 7 | **Mutual TLS (mTLS)** | MiTM (#1) | Cao | ⭐⭐ | Bảo vệ API thanh toán DigiShop |
| 8 | **Camera Source Validation** | Camera (#7) | Trung bình | ⭐⭐ | Phát hiện virtual camera |

---

## 6. Đề xuất Cập nhật Checklist_VNPT

### 6.1 Phân tích Khoảng trống giữa Checklist_VNPT và MASVS v2.x

Checklist_VNPT hiện tại tập trung vào **MASVS-RESILIENCE** (chống tấn công runtime và dịch ngược). Tuy nhiên, OWASP MASVS v2.x bao gồm nhiều nhóm bảo mật quan trọng khác chưa được đề cập trong Checklist_VNPT.

**Mapping Checklist_VNPT → MASVS:**

| Tiêu chí Checklist_VNPT | MASVS Control tương ứng |
|---|---|
| Chống MiTM | NETWORK-3, RESILIENCE-6 |
| Chống Hook | RESILIENCE-4 |
| Chống Debug | RESILIENCE-4 |
| Chống Repack | RESILIENCE-2 |
| Chống Dynamic Load APK | RESILIENCE-2, CODE-3 |
| Chống Screen Mirroring | STORAGE-7 |
| Chống Inject Camera | (Chưa có MASVS control trực tiếp) |
| Root/Jailbreak Detection | RESILIENCE-1, RESILIENCE-8 |
| Emulator Detection | RESILIENCE-1 |
| Accessibility Abuse Detection | PLATFORM-4 |

**Nhận xét:** Checklist_VNPT bao phủ tốt nhóm RESILIENCE nhưng **thiếu hoàn toàn** các nhóm STORAGE, CRYPTO, AUTH, NETWORK, CODE.

---

### 6.2 Đề xuất Tiêu chí Mới từ MASVS chưa có trong Checklist_VNPT

#### Nhóm A: Tiêu chí Bảo mật Dữ liệu (MASVS-STORAGE)

**Tiêu chí đề xuất 11: Bảo mật Lưu trữ Cục bộ**

| Hạng mục | Chi tiết |
|---|---|
| **Mô tả** | Dữ liệu nhạy cảm (session token, credentials, PII) được mã hóa khi lưu trên thiết bị |
| **MASVS Control** | STORAGE-1, STORAGE-2 |
| **Lý do thêm** | SharedPreferences và SQLite không mã hóa — dễ bị đọc trên thiết bị root. Đây là lỗ hổng phổ biến nhất trong ứng dụng mobile banking |
| **Cách kiểm tra** | Kiểm tra SharedPreferences, SQLite, files trong app data directory trên thiết bị root |
| **Promon SHIELD** | SLS (Secure Local Storage) đáp ứng đầy đủ |
| **Ưu tiên** | ⭐⭐⭐ Cao |

**Tiêu chí đề xuất 12: Ngăn chặn Backup Dữ liệu Nhạy cảm**

| Hạng mục | Chi tiết |
|---|---|
| **Mô tả** | Ứng dụng không cho phép backup dữ liệu nhạy cảm qua Android Auto Backup hoặc iTunes Backup |
| **MASVS Control** | STORAGE-4 |
| **Lý do thêm** | Backup có thể bị truy cập bởi attacker có quyền truy cập vào tài khoản Google/Apple của người dùng |
| **Cách kiểm tra** | Kiểm tra `android:allowBackup` trong AndroidManifest; kiểm tra iOS Data Protection class |
| **Promon SHIELD** | Không đáp ứng — trách nhiệm developer |
| **Ưu tiên** | ⭐⭐⭐ Cao |

---

#### Nhóm B: Tiêu chí Bảo mật Mạng (MASVS-NETWORK)

**Tiêu chí đề xuất 13: Cấu hình TLS Đúng chuẩn**

| Hạng mục | Chi tiết |
|---|---|
| **Mô tả** | Ứng dụng chỉ sử dụng TLS 1.2+ với cipher suite mạnh; không có HTTP fallback |
| **MASVS Control** | NETWORK-1, NETWORK-2 |
| **Lý do thêm** | TLS 1.0/1.1 có lỗ hổng đã biết (POODLE, BEAST). Nhiều ứng dụng vẫn cho phép HTTP fallback |
| **Cách kiểm tra** | Dùng Burp Suite để kiểm tra TLS version và cipher suites; kiểm tra `android:usesCleartextTraffic` |
| **Promon SHIELD** | Không đáp ứng — trách nhiệm developer/DevOps |
| **Ưu tiên** | ⭐⭐⭐ Cao |

---

#### Nhóm C: Tiêu chí Bảo mật Mã nguồn (MASVS-CODE)

**Tiêu chí đề xuất 14: Cơ chế Bắt buộc Cập nhật Ứng dụng**

| Hạng mục | Chi tiết |
|---|---|
| **Mô tả** | Ứng dụng có cơ chế phát hiện và bắt buộc cập nhật khi phiên bản hiện tại có lỗ hổng bảo mật nghiêm trọng |
| **MASVS Control** | CODE-2 |
| **Lý do thêm** | Người dùng tiếp tục dùng phiên bản cũ có lỗ hổng đã biết — rủi ro bảo mật cao |
| **Cách kiểm tra** | Kiểm tra xem ứng dụng có kiểm tra version khi khởi động không; kiểm tra In-App Update API |
| **Promon SHIELD** | Không đáp ứng — cần triển khai riêng |
| **Ưu tiên** | ⭐⭐⭐ Cao |

**Tiêu chí đề xuất 15: Quản lý Dependency và Lỗ hổng Đã biết**

| Hạng mục | Chi tiết |
|---|---|
| **Mô tả** | Ứng dụng không sử dụng thư viện third-party có lỗ hổng bảo mật đã biết (CVE) |
| **MASVS Control** | CODE-3 |
| **Lý do thêm** | Nhiều ứng dụng dùng thư viện cũ có CVE nghiêm trọng (Log4Shell, Spring4Shell, v.v.) |
| **Cách kiểm tra** | Chạy OWASP Dependency-Check hoặc Snyk trên dependencies |
| **Promon SHIELD** | Một phần (Code Protection bảo vệ third-party SDK khỏi phân tích) |
| **Ưu tiên** | ⭐⭐⭐ Cao |

---

#### Nhóm D: Tiêu chí Bảo mật Xác thực (MASVS-AUTH)

**Tiêu chí đề xuất 16: Bảo mật Xác thực và Phiên làm việc**

| Hạng mục | Chi tiết |
|---|---|
| **Mô tả** | Ứng dụng sử dụng giao thức xác thực an toàn (OAuth 2.0 + PKCE), quản lý session đúng chuẩn (timeout, invalidation) |
| **MASVS Control** | AUTH-1, AUTH-3 |
| **Lý do thêm** | Session token không hết hạn, JWT với HS256, thiếu refresh token rotation là lỗ hổng phổ biến |
| **Cách kiểm tra** | Kiểm tra JWT algorithm, session timeout, token invalidation khi logout |
| **Promon SHIELD** | Một phần (App Verify bổ sung lớp xác thực API) |
| **Ưu tiên** | ⭐⭐⭐ Cao |

---

#### Nhóm E: Tiêu chí Bảo mật Mật mã (MASVS-CRYPTO)

**Tiêu chí đề xuất 17: Triển khai Mật mã Đúng chuẩn**

| Hạng mục | Chi tiết |
|---|---|
| **Mô tả** | Ứng dụng không sử dụng thuật toán mật mã lỗi thời (MD5, SHA-1, DES, RC4); sử dụng AES-256-GCM, ECDSA-256 |
| **MASVS Control** | CRYPTO-1, CRYPTO-4 |
| **Lý do thêm** | MD5 và SHA-1 đã bị phá vỡ; DES/3DES không đủ mạnh cho dữ liệu tài chính |
| **Cách kiểm tra** | Static analysis với MobSF; kiểm tra code sử dụng `MessageDigest.getInstance("MD5")` |
| **Promon SHIELD** | Một phần (SAROM dùng AES-256-GCM; SLS dùng White-Box Crypto) |
| **Ưu tiên** | ⭐⭐⭐ Cao |

---

### 6.3 Checklist_VNPT Cập nhật — Đề xuất 17 Tiêu chí

| # | Tiêu chí | Nhóm MASVS | Trạng thái | Promon SHIELD |
|---|---|---|---|---|
| 1 | Chống MiTM | NETWORK-3 | Hiện có | ⚠️ Một phần |
| 2 | Chống Hook | RESILIENCE-4 | Hiện có | ✅ Đầy đủ |
| 3 | Chống Debug | RESILIENCE-4 | Hiện có | ✅ Đầy đủ |
| 4 | Chống Repack | RESILIENCE-2 | Hiện có | ✅ Đầy đủ |
| 5 | Chống Dynamic Load APK | RESILIENCE-2 | Hiện có | ⚠️ Một phần |
| 6 | Chống Screen Mirroring | STORAGE-7 | Hiện có | ✅ Đầy đủ |
| 7 | Chống Inject Camera | — | Hiện có | ⚠️ Một phần |
| 8 | Root/Jailbreak Detection | RESILIENCE-1, 8 | Hiện có | ✅ Đầy đủ |
| 9 | Emulator Detection | RESILIENCE-1 | Hiện có | ✅ Đầy đủ |
| 10 | Accessibility Abuse Detection | PLATFORM-4 | Hiện có | ✅ Đầy đủ |
| **11** | **Bảo mật Lưu trữ Cục bộ** | STORAGE-1, 2 | **Đề xuất thêm** | ✅ SLS |
| **12** | **Ngăn chặn Backup Dữ liệu Nhạy cảm** | STORAGE-4 | **Đề xuất thêm** | ❌ Developer |
| **13** | **Cấu hình TLS Đúng chuẩn** | NETWORK-1, 2 | **Đề xuất thêm** | ❌ Developer |
| **14** | **Cơ chế Bắt buộc Cập nhật** | CODE-2 | **Đề xuất thêm** | ❌ Developer |
| **15** | **Quản lý Dependency/CVE** | CODE-3 | **Đề xuất thêm** | ⚠️ Một phần |
| **16** | **Bảo mật Xác thực và Session** | AUTH-1, 3 | **Đề xuất thêm** | ⚠️ Một phần |
| **17** | **Triển khai Mật mã Đúng chuẩn** | CRYPTO-1, 4 | **Đề xuất thêm** | ⚠️ Một phần |

**Lý do đề xuất 7 tiêu chí mới:**
- Các tiêu chí 11–17 đều có trong OWASP MASVS v2.x và là yêu cầu bảo mật cơ bản
- Checklist_VNPT hiện tại tập trung quá nhiều vào RESILIENCE, bỏ qua các lỗ hổng phổ biến hơn (insecure storage, weak TLS, outdated dependencies)
- Các tiêu chí mới phù hợp với bối cảnh ứng dụng tài chính (MyVNPT, DigiShop)

---

## 7. Kết luận và Khuyến nghị

### 7.1 Tóm tắt Kết quả Đánh giá

#### Kết quả đánh giá Promon SHIELD theo Checklist_VNPT 10 tiêu chí:

| Chỉ số | Giá trị |
|---|---|
| **Tiêu chí đáp ứng đầy đủ** | 7/10 (70%) |
| **Tiêu chí đáp ứng một phần** | 3/10 (30%) |
| **Tiêu chí không đáp ứng** | 0/10 (0%) |
| **Điểm tổng Promon SHIELD** | **8.5/10** |

#### Cải thiện điểm số sau triển khai Promon SHIELD:

| Ứng dụng | Trước | Sau (Promon SHIELD) | Sau (+ Giải pháp bổ sung) |
|---|---|---|---|
| **MyVNPT** | 2.0/10 | **8.5/10** | **9.5–10/10** |
| **DigiShop** | 1.5/10 | **8.5/10** | **9.5–10/10** |

### 7.2 Điểm mạnh của Promon SHIELD trong bối cảnh Checklist_VNPT

**1. Bảo vệ toàn diện nhóm RESILIENCE (7/10 tiêu chí):**
- Chống Hook, Debug, Repack, Screen Mirroring, Root/Jailbreak, Emulator, Accessibility Abuse — tất cả được đáp ứng đầy đủ
- Đây là nhóm tiêu chí khó nhất để tự triển khai — Promon SHIELD giải quyết hoàn toàn

**2. Không yêu cầu thay đổi mã nguồn cho 7/10 tiêu chí:**
- Đội phát triển MyVNPT và DigiShop không cần refactor code để đạt 7 tiêu chí
- Giảm đáng kể thời gian và rủi ro triển khai

**3. Bảo vệ đa lớp (defense-in-depth):**
- Mỗi tiêu chí được bảo vệ bởi nhiều cơ chế độc lập
- Attacker phải bypass nhiều lớp bảo vệ cùng lúc — tăng chi phí tấn công đáng kể

**4. Phản ứng linh hoạt:**
- ExitOn, Callback API, ExitOnURL — cho phép UX thân thiện
- Có thể cảnh báo người dùng thay vì thoát ngay — phù hợp với ứng dụng dịch vụ

### 7.3 Giới hạn và Khoảng trống

**1. Tiêu chí MiTM (chỉ đạt 0.5/1.0):**
- App Verify bảo vệ gián tiếp nhưng không thay thế Certificate Pinning
- Cần bổ sung Certificate Pinning để đạt bảo vệ đầy đủ

**2. Tiêu chí Dynamic Load APK (chỉ đạt 0.5/1.0):**
- Không block DexClassLoader trực tiếp
- Cần code audit và cấm dynamic loading từ nguồn bên ngoài

**3. Tiêu chí Inject Camera (chỉ đạt 0.5/1.0):**
- Không có module chuyên biệt cho camera injection
- Cần Liveness Detection SDK nếu ứng dụng dùng eKYC/face authentication

**4. Checklist_VNPT thiếu 7 tiêu chí quan trọng từ MASVS:**
- Bảo mật lưu trữ cục bộ, TLS configuration, forced update, dependency management, authentication security, cryptography — đều chưa có trong checklist

### 7.4 Khuyến nghị Hành động

#### Khuyến nghị 1: Triển khai Promon SHIELD ngay lập tức

**Lý do:** Promon SHIELD đưa điểm bảo mật từ 2/10 (MyVNPT) và 1.5/10 (DigiShop) lên **8.5/10** — cải thiện 325–467% chỉ với một giải pháp duy nhất, không cần thay đổi mã nguồn cho 7/10 tiêu chí.

**Ưu tiên triển khai:**
1. **Giai đoạn 1 (Tháng 1–2):** Tích hợp Promon SHIELD vào DigiShop trước (điểm thấp hơn, rủi ro cao hơn do tính năng thanh toán)
2. **Giai đoạn 2 (Tháng 2–3):** Tích hợp vào MyVNPT
3. **Cấu hình tối thiểu bắt buộc:** Root Detection (ExitOn), Repackaging Detection (ExitOn), Debugger Detection (ExitOn), Hooking Detection (ExitOn)
4. **Cấu hình khuyến nghị thêm:** Emulator Detection, Screen Mirroring Detection, Accessibility Detection (Callback)

#### Khuyến nghị 2: Bổ sung Certificate Pinning (Ưu tiên cao)

**Lý do:** Đưa tiêu chí MiTM từ 0.5 → 1.0, tổng điểm từ 8.5 → 9.0/10.

**Thực hiện:**
- Triển khai OkHttp `CertificatePinner` cho Android
- Triển khai TrustKit cho iOS
- Pin public key hash (SPKI) với backup pins
- **Timeline:** Trong vòng 1 tháng sau khi triển khai Promon SHIELD

#### Khuyến nghị 3: Code Audit cho Dynamic Loading (Ưu tiên cao)

**Lý do:** Đưa tiêu chí Dynamic Load APK từ 0.5 → 1.0, tổng điểm từ 9.0 → 9.5/10.

**Thực hiện:**
- Audit toàn bộ mã nguồn MyVNPT và DigiShop
- Loại bỏ hoặc kiểm soát chặt chẽ mọi dynamic class loading
- **Timeline:** Trong vòng 2 tháng

#### Khuyến nghị 4: Cập nhật Checklist_VNPT lên 17 tiêu chí

**Lý do:** Checklist hiện tại bỏ qua nhiều lỗ hổng phổ biến và nghiêm trọng (insecure storage, weak TLS, outdated dependencies).

**Thực hiện:**
- Thêm 7 tiêu chí mới (tiêu chí 11–17) vào Checklist_VNPT
- Đánh giá lại MyVNPT và DigiShop theo checklist 17 tiêu chí
- **Timeline:** Trong vòng 1 tháng (chỉ cần cập nhật tài liệu)

#### Khuyến nghị 5: Liveness Detection cho DigiShop (Nếu dùng eKYC)

**Lý do:** Đưa tiêu chí Inject Camera từ 0.5 → 1.0, tổng điểm từ 9.5 → 10/10.

**Thực hiện:**
- Đánh giá nhu cầu eKYC/face authentication của DigiShop
- Nếu có: tích hợp Liveness Detection SDK (FaceTec, iProov, hoặc AWS Rekognition)
- **Timeline:** Trong vòng 3 tháng (tùy mức độ ưu tiên)

---

### 7.5 Lộ trình Đạt Điểm Tối đa Checklist_VNPT

```
Tháng 1–2: Triển khai Promon SHIELD
  → Điểm: 2.0 → 8.5/10 (MyVNPT), 1.5 → 8.5/10 (DigiShop)

Tháng 2–3: Certificate Pinning + Network Security Config
  → Điểm: 8.5 → 9.0/10

Tháng 3–4: Code Audit + Dynamic Loading Fix
  → Điểm: 9.0 → 9.5/10

Tháng 4–6: Liveness Detection (nếu cần) + Checklist Update
  → Điểm: 9.5 → 10/10

Tháng 6+: Triển khai 7 tiêu chí mới (Checklist 17 tiêu chí)
  → Đánh giá lại theo checklist mở rộng
```

### 7.6 Kết luận Cuối cùng

> **Promon SHIELD là giải pháp hiệu quả nhất để cải thiện điểm bảo mật Checklist_VNPT trong thời gian ngắn nhất.**

Với một giải pháp duy nhất, không cần thay đổi mã nguồn cho phần lớn tính năng, Promon SHIELD đưa điểm bảo mật của MyVNPT và DigiShop từ mức **"Rất thấp" (2/10, 1.5/10)** lên mức **"Tốt" (8.5/10)** — đáp ứng 85% yêu cầu Checklist_VNPT.

Kết hợp với các giải pháp bổ sung đơn giản (Certificate Pinning, code audit), điểm số có thể đạt **9.5–10/10** trong vòng 3–4 tháng.

Đồng thời, việc cập nhật Checklist_VNPT lên 17 tiêu chí sẽ giúp VNPT Media có bức tranh bảo mật toàn diện hơn, phù hợp với tiêu chuẩn OWASP MASVS v2.x và thực tiễn bảo mật ứng dụng di động hiện đại.

---

## Phụ lục A: Bảng Tóm tắt Nhanh — 10 Tiêu chí Checklist_VNPT

| # | Tiêu chí | Kết quả | Điểm | Module chính | Cần bổ sung |
|---|---|---|---|---|---|
| 1 | Chống MiTM | ⚠️ Một phần | 0.5 | App Verify | Certificate Pinning |
| 2 | Chống Hook | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | — |
| 3 | Chống Debug | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | — |
| 4 | Chống Repack | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | — |
| 5 | Chống Dynamic Load APK | ⚠️ Một phần | 0.5 | SHIELD Core RASP | Code audit, cấm DexClassLoader |
| 6 | Chống Screen Mirroring | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | — |
| 7 | Chống Inject Camera | ⚠️ Một phần | 0.5 | SHIELD Core RASP (gián tiếp) | Liveness Detection SDK |
| 8 | Root/Jailbreak Detection | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | — |
| 9 | Emulator Detection | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | — |
| 10 | Accessibility Abuse Detection | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | — |
| | **TỔNG** | | **8.5/10** | | |

---

## Phụ lục B: So sánh Điểm số Trực quan

```
Điểm bảo mật Checklist_VNPT (thang 10):

MyVNPT:
  Trước Promon SHIELD:  ██░░░░░░░░  2.0/10
  Sau Promon SHIELD:    █████████░  8.5/10
  Sau bổ sung đầy đủ:  ██████████  9.5–10/10

DigiShop:
  Trước Promon SHIELD:  █░░░░░░░░░  1.5/10
  Sau Promon SHIELD:    █████████░  8.5/10
  Sau bổ sung đầy đủ:  ██████████  9.5–10/10
```

---

## Phụ lục C: Tham chiếu Tài liệu

| Tài liệu | Mô tả |
|---|---|
| `01-tong-quan-kien-truc-tinh-nang.md` | Tổng quan kiến trúc và tính năng Promon SHIELD |
| `02-uu-nhuoc-diem-phan-tich.md` | Phân tích ưu nhược điểm |
| `03-so-sanh-giai-phap.md` | Ma trận so sánh với giải pháp thay thế |
| `04-quy-trinh-tich-hop.md` | Quy trình tích hợp Shielder |
| `05-danh-gia-masvs.md` | Đánh giá theo OWASP MASVS v2.x |
| OWASP MASVS v2.x | https://mas.owasp.org/MASVS/ |
| OWASP MASTG | https://mas.owasp.org/MASTG/ |
| Promon SHIELD Technical Docs | Tài liệu kỹ thuật nhà cung cấp |

---

*Tài liệu này được lập dựa trên nghiên cứu tài liệu kỹ thuật của Promon AS và OWASP MASVS v2.x. Kết quả thực tế cần được xác nhận qua thử nghiệm trực tiếp.*

*Phiên bản: 1.0 | Ngày: 2025 | Tác giả: ANTT-TSC, VNPT Media*
