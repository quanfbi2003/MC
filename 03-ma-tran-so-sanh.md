# Ma trận So sánh Giải pháp Bảo mật Ứng dụng Di động

**Dự án:** Nghiên cứu & Đánh giá Giải pháp Bảo mật Ứng dụng Di động Promon SHIELD  
**Đơn vị thực hiện:** Phòng An ninh Thông tin – TSC (ANTT-TSC), VNPT Media  
**Phiên bản tài liệu:** 1.0  
**Ngày lập:** 2025  
**Yêu cầu liên quan:** Yêu cầu 3  
**Phụ thuộc:** Task 1, Task 2 — Tài liệu tổng quan và phân tích ưu nhược điểm

---

## Mục lục

1. [Tổng quan các giải pháp được so sánh](#1-tổng-quan-các-giải-pháp)
2. [Ma trận so sánh kỹ thuật đầy đủ](#2-ma-trận-so-sánh-kỹ-thuật)
3. [Tính năng Promon SHIELD có mà ProGuard/R8 không có](#3-tính-năng-độc-quyền-promon-shield)
4. [Phân tích chi tiết từng giải pháp](#4-phân-tích-chi-tiết)
5. [Đánh giá tính khả thi RASP tự xây dựng](#5-rasp-tự-xây-dựng)
6. [Điểm số tổng hợp và xếp hạng](#6-điểm-số-tổng-hợp)
7. [Khuyến nghị có lý giải kỹ thuật](#7-khuyến-nghị)
8. [Kết luận](#8-kết-luận)

---

## 1. Tổng quan các Giải pháp được So sánh

### 1.1 Danh sách giải pháp

| Giải pháp | Nhà cung cấp | Loại | Mô hình |
|---|---|---|---|
| **Promon SHIELD** | Promon AS (Na Uy) | Thương mại | Post-build, RASP + Obfuscation + Attestation |
| **ProGuard** | Guardsquare (mã nguồn mở) | Miễn phí | Build-time obfuscation |
| **R8** | Google (tích hợp Android Gradle) | Miễn phí | Build-time shrinking + obfuscation |
| **DexGuard** | Guardsquare (Bỉ) | Thương mại | Build-time obfuscation nâng cao |
| **Digital.ai App Protection** | Digital.ai (Mỹ, trước đây là Arxan) | Thương mại | Post-build, RASP + Obfuscation |
| **RASP tự xây dựng** | Nội bộ VNPT Media | Tự phát triển | Runtime protection tùy chỉnh |

### 1.2 Phạm vi so sánh

Tài liệu này so sánh 6 giải pháp theo **10 tiêu chí kỹ thuật** bao gồm:
1. RASP Runtime Protection
2. Code Obfuscation
3. API Attestation
4. Telemetry & Dashboard
5. Hỗ trợ iOS
6. Chi phí (TCO)
7. Độ phức tạp tích hợp
8. Mức độ hỗ trợ kỹ thuật
9. Hỗ trợ CI/CD
10. Tuân thủ tiêu chuẩn

---

## 2. Ma trận So sánh Kỹ thuật Đầy đủ

### 2.1 Ma trận tổng quan (Có/Không/Một phần)

| Tiêu chí | Promon SHIELD | ProGuard | R8 | DexGuard | Digital.ai | RASP Tự xây |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| **RASP Runtime Protection** | ✅ Đầy đủ | ❌ Không | ❌ Không | ⚠️ Hạn chế | ✅ Đầy đủ | ⚠️ Tùy chỉnh |
| **Code Obfuscation** | ✅ Đầy đủ | ✅ Cơ bản | ✅ Cơ bản | ✅ Nâng cao | ✅ Nâng cao | ❌ Không |
| **API Attestation** | ✅ App Verify | ❌ Không | ❌ Không | ❌ Không | ✅ Có | ⚠️ Tùy chỉnh |
| **Telemetry & Dashboard** | ✅ Promon Insight | ❌ Không | ❌ Không | ❌ Không | ✅ Có | ❌ Không |
| **Hỗ trợ iOS** | ✅ iOS 12+ | ❌ Không | ❌ Không | ❌ Không | ✅ Có | ⚠️ Cần phát triển |
| **Root/Jailbreak Detection** | ✅ Đầy đủ | ❌ Không | ❌ Không | ⚠️ Hạn chế | ✅ Có | ⚠️ Tùy chỉnh |
| **Anti-Frida/Xposed** | ✅ Đầy đủ | ❌ Không | ❌ Không | ⚠️ Hạn chế | ✅ Có | ⚠️ Tùy chỉnh |
| **Anti-Debug** | ✅ Đầy đủ | ❌ Không | ❌ Không | ⚠️ Hạn chế | ✅ Có | ⚠️ Tùy chỉnh |
| **Anti-Repackaging** | ✅ Đầy đủ | ❌ Không | ❌ Không | ⚠️ Hạn chế | ✅ Có | ⚠️ Tùy chỉnh |
| **Screen Mirroring Detection** | ✅ Có | ❌ Không | ❌ Không | ❌ Không | ⚠️ Hạn chế | ⚠️ Tùy chỉnh |
| **Accessibility Abuse Detection** | ✅ Có | ❌ Không | ❌ Không | ❌ Không | ⚠️ Hạn chế | ⚠️ Tùy chỉnh |
| **Secure Local Storage** | ✅ SLS (WBC) | ❌ Không | ❌ Không | ❌ Không | ⚠️ Hạn chế | ❌ Không |
| **Static Data Encryption** | ✅ SAROM | ❌ Không | ❌ Không | ❌ Không | ⚠️ Hạn chế | ❌ Không |
| **Post-build Integration** | ✅ Có | ❌ Không | ❌ Không | ✅ Có | ✅ Có | ❌ Không |
| **CI/CD Support** | ✅ Đầy đủ | ✅ Tích hợp sẵn | ✅ Tích hợp sẵn | ✅ Có | ✅ Có | ⚠️ Cần xây dựng |
| **Chứng nhận EMVCo/ISO** | ✅ Đầy đủ | ❌ Không | ❌ Không | ⚠️ Một phần | ✅ Có | ❌ Không |


### 2.2 Ma trận chi tiết theo 10 tiêu chí kỹ thuật

#### Tiêu chí 1: RASP Runtime Protection

| Giải pháp | Mức độ | Chi tiết |
|---|---|---|
| **Promon SHIELD** | ⭐⭐⭐⭐⭐ Xuất sắc | SHIELD Core RASP: phát hiện root, jailbreak, emulator, debugger, Frida, Xposed, repackaging, screen mirroring, accessibility abuse, overlay, keylogger, ADB, developer options. Phản ứng: ExitOn, Callback, ExitOnURL |
| **ProGuard** | ❌ Không có | Chỉ là công cụ obfuscation tĩnh, không có bất kỳ cơ chế runtime nào |
| **R8** | ❌ Không có | Chỉ là trình biên dịch/thu nhỏ, không có runtime protection |
| **DexGuard** | ⭐⭐ Hạn chế | Có một số kiểm tra runtime cơ bản (root detection, tamper detection) nhưng không toàn diện như Promon SHIELD |
| **Digital.ai** | ⭐⭐⭐⭐ Tốt | RASP đầy đủ: root/jailbreak, debugger, hooking, repackaging, emulator. Tương đương Promon SHIELD về phạm vi |
| **RASP Tự xây** | ⭐⭐ Tùy chỉnh | Phụ thuộc hoàn toàn vào năng lực đội ngũ; thường chỉ đạt 3-5 tiêu chí trong năm đầu |

#### Tiêu chí 2: Code Obfuscation

| Giải pháp | Mức độ | Chi tiết |
|---|---|---|
| **Promon SHIELD** | ⭐⭐⭐⭐⭐ Xuất sắc | Multi-layer obfuscation: đổi tên, mã hóa string, control-flow flattening, section encryption, anti-debugging traps. Hỗ trợ Java/Kotlin, C/C++, Swift, JS |
| **ProGuard** | ⭐⭐ Cơ bản | Đổi tên class/method/field, loại bỏ code không dùng. Dễ bị decompile bằng jadx |
| **R8** | ⭐⭐ Cơ bản | Tương tự ProGuard + tối ưu bytecode. Không có string encryption hay control-flow obfuscation |
| **DexGuard** | ⭐⭐⭐⭐ Tốt | String encryption, class encryption, reflection obfuscation, resource encryption. Mạnh hơn ProGuard/R8 đáng kể |
| **Digital.ai** | ⭐⭐⭐⭐ Tốt | Obfuscation nâng cao cho Java/Kotlin và native code. Tương đương DexGuard |
| **RASP Tự xây** | ❌ Không có | RASP tự xây thường không bao gồm obfuscation; cần kết hợp ProGuard/R8 riêng |

#### Tiêu chí 3: API Attestation

| Giải pháp | Mức độ | Chi tiết |
|---|---|---|
| **Promon SHIELD** | ⭐⭐⭐⭐⭐ Xuất sắc | App Verify (Promon Verify™): cryptographic attestation token, phân biệt app gốc vs. Burp Suite/Postman, yêu cầu tích hợp backend SDK |
| **ProGuard** | ❌ Không có | Không có cơ chế attestation |
| **R8** | ❌ Không có | Không có cơ chế attestation |
| **DexGuard** | ❌ Không có | Không có module attestation tích hợp |
| **Digital.ai** | ⭐⭐⭐⭐ Tốt | Có API attestation tương tự App Verify; tích hợp với backend |
| **RASP Tự xây** | ⭐⭐ Tùy chỉnh | Có thể tự xây dựng attestation nhưng phức tạp và dễ có lỗ hổng |


#### Tiêu chí 4: Telemetry & Dashboard

| Giải pháp | Mức độ | Chi tiết |
|---|---|---|
| **Promon SHIELD** | ⭐⭐⭐⭐⭐ Xuất sắc | Promon Insight: thu thập security events có timestamp và severity, xuất JSON/Protobuf, tích hợp SIEM, hỗ trợ on-premise/cloud/hybrid |
| **ProGuard** | ❌ Không có | Không có telemetry |
| **R8** | ❌ Không có | Không có telemetry |
| **DexGuard** | ❌ Không có | Không có dashboard hay telemetry tích hợp |
| **Digital.ai** | ⭐⭐⭐⭐ Tốt | Có dashboard và telemetry; hỗ trợ SIEM integration. Tương đương Promon Insight |
| **RASP Tự xây** | ❌ Không có | Cần xây dựng riêng; chi phí và thời gian đáng kể |

#### Tiêu chí 5: Hỗ trợ iOS

| Giải pháp | Mức độ | Chi tiết |
|---|---|---|
| **Promon SHIELD** | ⭐⭐⭐⭐⭐ Xuất sắc | iOS 12+, Swift/Obj-C, jailbreak detection, emulator detection, RASP đầy đủ. Yêu cầu macOS để shield |
| **ProGuard** | ❌ Không có | Chỉ dành cho Android/JVM |
| **R8** | ❌ Không có | Chỉ dành cho Android |
| **DexGuard** | ❌ Không có | Chỉ dành cho Android. Guardsquare có iXGuard riêng cho iOS (sản phẩm khác) |
| **Digital.ai** | ⭐⭐⭐⭐⭐ Xuất sắc | Hỗ trợ iOS đầy đủ, tương đương Promon SHIELD |
| **RASP Tự xây** | ⭐ Rất khó | iOS có nhiều hạn chế về runtime hooking; cần kiến thức chuyên sâu về iOS internals |

#### Tiêu chí 6: Chi phí (TCO — Total Cost of Ownership)

| Giải pháp | Chi phí năm đầu (USD) | Chi phí từ năm 2+ (USD/năm) | Ghi chú |
|---|---|---|---|
| **Promon SHIELD** | $42,000–$133,000 | $35,000–$113,000 | License + training + integration + infra Insight |
| **ProGuard** | $0–$5,000 | $0–$2,000 | Miễn phí; chi phí chỉ là nhân công tích hợp |
| **R8** | $0–$3,000 | $0–$1,000 | Tích hợp sẵn trong Android Gradle; gần như miễn phí |
| **DexGuard** | $15,000–$50,000 | $12,000–$45,000 | License per-app; rẻ hơn Promon SHIELD |
| **Digital.ai** | $35,000–$120,000 | $30,000–$100,000 | Tương đương Promon SHIELD; có thể cao hơn với enterprise |
| **RASP Tự xây** | $150,000–$400,000 | $80,000–$200,000 | Chi phí nhân lực cao; xem phân tích chi tiết ở Mục 5 |

*Lưu ý: Tất cả chi phí là ước tính dựa trên thông tin thị trường. Cần yêu cầu báo giá chính thức.*

#### Tiêu chí 7: Độ phức tạp Tích hợp

| Giải pháp | Mức độ phức tạp | Thời gian tích hợp ước tính | Chi tiết |
|---|---|---|---|
| **Promon SHIELD** | ⭐⭐⭐ Trung bình | 2–4 tuần | Post-build CLI; cần cấu hình blueprint JSON, re-sign, CI/CD pipeline. SLS/App Verify cần thay đổi code |
| **ProGuard** | ⭐ Thấp | 1–3 ngày | Tích hợp sẵn trong Android build; chỉ cần cấu hình rules file |
| **R8** | ⭐ Thấp | 1–2 ngày | Tích hợp sẵn trong Android Gradle Plugin; bật bằng 1 dòng config |
| **DexGuard** | ⭐⭐ Thấp-Trung | 3–7 ngày | Thay thế ProGuard/R8; cấu hình tương tự nhưng có thêm tính năng |
| **Digital.ai** | ⭐⭐⭐ Trung bình | 2–4 tuần | Tương tự Promon SHIELD; post-build với blueprint config |
| **RASP Tự xây** | ⭐⭐⭐⭐⭐ Rất cao | 6–18 tháng | Cần thiết kế, phát triển, test toàn bộ từ đầu |


#### Tiêu chí 8: Mức độ Hỗ trợ Kỹ thuật

| Giải pháp | Mức độ | Chi tiết |
|---|---|---|
| **Promon SHIELD** | ⭐⭐⭐⭐ Tốt | Hỗ trợ kỹ thuật chuyên sâu; cập nhật signature Frida/Xposed mới; hỗ trợ Android/iOS version mới nhanh; ISO 27001, SOC 2 |
| **ProGuard** | ⭐⭐ Cộng đồng | Mã nguồn mở; hỗ trợ qua community forum và Stack Overflow; không có SLA |
| **R8** | ⭐⭐⭐ Tốt | Google hỗ trợ qua Android issue tracker; tài liệu đầy đủ; cập nhật theo Android Gradle Plugin |
| **DexGuard** | ⭐⭐⭐⭐ Tốt | Guardsquare cung cấp hỗ trợ thương mại; tài liệu kỹ thuật đầy đủ; cập nhật thường xuyên |
| **Digital.ai** | ⭐⭐⭐⭐ Tốt | Hỗ trợ enterprise; tài liệu đầy đủ; cập nhật thường xuyên. Tương đương Promon SHIELD |
| **RASP Tự xây** | ⭐ Nội bộ | Phụ thuộc hoàn toàn vào đội ngũ nội bộ; không có SLA; rủi ro khi nhân sự nghỉ việc |

#### Tiêu chí 9: Hỗ trợ CI/CD Pipeline

| Giải pháp | Mức độ | Chi tiết |
|---|---|---|
| **Promon SHIELD** | ⭐⭐⭐⭐ Tốt | CLI thuần túy, Docker support, non-interactive mode; tích hợp Jenkins/GitLab CI/GitHub Actions/Azure DevOps |
| **ProGuard** | ⭐⭐⭐⭐⭐ Xuất sắc | Tích hợp sẵn trong Android Gradle; không cần cấu hình thêm |
| **R8** | ⭐⭐⭐⭐⭐ Xuất sắc | Tích hợp sẵn trong Android Gradle Plugin; zero configuration |
| **DexGuard** | ⭐⭐⭐⭐ Tốt | Tích hợp vào Gradle build; tương tự ProGuard nhưng có thêm bước cấu hình |
| **Digital.ai** | ⭐⭐⭐⭐ Tốt | CLI support; tích hợp CI/CD; tương đương Promon SHIELD |
| **RASP Tự xây** | ⭐⭐ Thấp | Cần xây dựng CI/CD integration riêng; tốn thêm thời gian phát triển |

#### Tiêu chí 10: Tuân thủ Tiêu chuẩn

| Giải pháp | Chứng nhận | Chi tiết |
|---|---|---|
| **Promon SHIELD** | ISO 27001, SOC 2, EMVCo SMBP 2025, EU DORA | Chứng nhận đầy đủ nhất; đặc biệt EMVCo SMBP quan trọng cho DigiShop |
| **ProGuard** | Không có | Công cụ mã nguồn mở; không có chứng nhận bảo mật |
| **R8** | Không có | Công cụ Google; không có chứng nhận bảo mật riêng |
| **DexGuard** | ISO 27001 (Guardsquare) | Guardsquare có ISO 27001; DexGuard không có chứng nhận riêng cho sản phẩm |
| **Digital.ai** | ISO 27001, SOC 2 | Chứng nhận tốt nhưng không có EMVCo SMBP |
| **RASP Tự xây** | Không có | Không có chứng nhận; cần audit độc lập nếu muốn đạt chuẩn |

---

## 3. Tính năng Promon SHIELD có mà ProGuard/R8 không có

Đây là phân tích quan trọng vì ProGuard và R8 là các công cụ miễn phí phổ biến nhất trong hệ sinh thái Android. Nhiều tổ chức nhầm tưởng rằng ProGuard/R8 đã đủ để bảo vệ ứng dụng.

### 3.1 Bảng so sánh tính năng độc quyền

| # | Tính năng | Promon SHIELD | ProGuard | R8 | Lý do quan trọng |
|---|---|:---:|:---:|:---:|---|
| 1 | **RASP Runtime Protection** | ✅ | ❌ | ❌ | ProGuard/R8 chỉ bảo vệ tĩnh; không có bảo vệ khi app đang chạy |
| 2 | **Root/Jailbreak Detection** | ✅ | ❌ | ❌ | Thiết bị root có thể bypass mọi bảo vệ tĩnh |
| 3 | **Anti-Frida/Xposed Detection** | ✅ | ❌ | ❌ | Frida có thể hook bất kỳ hàm nào dù đã obfuscate |
| 4 | **Anti-Debug Detection** | ✅ | ❌ | ❌ | Debugger có thể đọc memory và bypass logic |
| 5 | **Anti-Repackaging** | ✅ | ❌ | ❌ | App obfuscate vẫn có thể bị decompile, sửa, repack |
| 6 | **Emulator Detection** | ✅ | ❌ | ❌ | Emulator dễ dàng bypass obfuscation |
| 7 | **Screen Mirroring Detection** | ✅ | ❌ | ❌ | Không liên quan đến obfuscation |
| 8 | **Accessibility Abuse Detection** | ✅ | ❌ | ❌ | Không liên quan đến obfuscation |
| 9 | **API Attestation (App Verify)** | ✅ | ❌ | ❌ | ProGuard/R8 không bảo vệ API backend |
| 10 | **Telemetry & Security Dashboard** | ✅ | ❌ | ❌ | Không có khả năng quan sát security events |
| 11 | **Secure Local Storage (SLS)** | ✅ | ❌ | ❌ | SharedPreferences vẫn bị đọc trên thiết bị root |
| 12 | **Static Data Encryption (SAROM)** | ✅ | ❌ | ❌ | API keys trong APK vẫn bị trích xuất bằng jadx |
| 13 | **iOS Support** | ✅ | ❌ | ❌ | ProGuard/R8 chỉ dành cho Android/JVM |
| 14 | **Post-build Integration** | ✅ | ❌ | ❌ | ProGuard/R8 yêu cầu tích hợp vào build process |
| 15 | **String Encryption** | ✅ | ❌ | ❌ | ProGuard/R8 không mã hóa string literals |
| 16 | **Control-Flow Obfuscation** | ✅ | ❌ | ❌ | ProGuard/R8 chỉ đổi tên, không làm phức tạp luồng |
| 17 | **Section Encryption** | ✅ | ❌ | ❌ | ProGuard/R8 không mã hóa binary sections |
| 18 | **EMVCo SMBP Certification** | ✅ | ❌ | ❌ | Quan trọng cho ứng dụng thanh toán DigiShop |

### 3.2 Phân tích khoảng cách bảo mật

**Kết luận quan trọng:** ProGuard và R8 chỉ cung cấp **bảo vệ tĩnh** (static protection) — làm cho code khó đọc hơn khi phân tích offline. Chúng **không có bất kỳ cơ chế bảo vệ runtime nào**.

Điều này có nghĩa là:
- Một attacker với thiết bị đã root và Frida có thể **bypass hoàn toàn** ProGuard/R8 obfuscation tại runtime
- API keys trong APK vẫn có thể bị trích xuất bằng `jadx` hoặc `strings` command
- Ứng dụng bị repackage vẫn có thể hoạt động bình thường
- Không có cách nào phát hiện khi ứng dụng đang bị tấn công

**Ví dụ minh họa:**
```
Tình huống: Attacker muốn đánh cắp API key từ MyVNPT

Với ProGuard/R8:
  jadx -d output/ MyVNPT.apk
  grep -r "api_key\|secret\|token" output/
  → Tìm thấy API key trong vài phút

Với Promon SHIELD (SAROM):
  jadx -d output/ MyVNPT_shielded.apk
  → API key đã được mã hóa AES-256 GCM
  → Không thể đọc được bằng static analysis
  → Chỉ giải mã tại runtime trong môi trường hợp lệ
```

---

## 4. Phân tích Chi tiết Từng Giải pháp

### 4.1 ProGuard

**Tổng quan:** Công cụ mã nguồn mở do Guardsquare phát triển, tích hợp sẵn trong Android build system từ năm 2002. Là công cụ obfuscation tiêu chuẩn cho Android.

**Ưu điểm:**
- Hoàn toàn miễn phí, tích hợp sẵn trong Android Gradle
- Giảm kích thước APK bằng cách loại bỏ code không dùng
- Cộng đồng lớn, tài liệu phong phú
- Không ảnh hưởng đến hiệu năng runtime

**Nhược điểm:**
- Chỉ đổi tên class/method/field — dễ bị decompile bằng jadx
- Không có string encryption, control-flow obfuscation
- Không có bất kỳ runtime protection nào
- Không hỗ trợ iOS
- Không bảo vệ native libraries (.so files)

**Phù hợp với:** Ứng dụng không có yêu cầu bảo mật cao; giảm kích thước APK; bước đầu tiên trong chiến lược bảo mật nhiều lớp.

**Không phù hợp với:** Ứng dụng tài chính, thanh toán, hoặc bất kỳ ứng dụng nào cần bảo vệ thực sự.

---

### 4.2 R8

**Tổng quan:** Trình biên dịch/thu nhỏ của Google, thay thế ProGuard từ Android Gradle Plugin 3.4. Tích hợp sẵn và bật mặc định trong release builds.

**Ưu điểm:**
- Tích hợp sẵn, zero configuration
- Tối ưu bytecode tốt hơn ProGuard
- Giảm kích thước APK hiệu quả hơn
- Hỗ trợ Kotlin coroutines tốt hơn ProGuard

**Nhược điểm:**
- Tương tự ProGuard về mặt bảo mật — chỉ là obfuscation cơ bản
- Không có string encryption hay control-flow obfuscation
- Không có runtime protection
- Không hỗ trợ iOS

**Phù hợp với:** Tất cả Android apps như một baseline; kết hợp với giải pháp bảo mật khác.

**Không phù hợp với:** Dùng độc lập cho ứng dụng có yêu cầu bảo mật cao.

---

### 4.3 DexGuard

**Tổng quan:** Sản phẩm thương mại của Guardsquare (cùng công ty phát triển ProGuard), cung cấp obfuscation nâng cao cho Android.

**Ưu điểm:**
- String encryption — bảo vệ tốt hơn ProGuard/R8
- Class encryption — mã hóa toàn bộ class
- Resource encryption — bảo vệ file tài nguyên
- Reflection obfuscation — làm phức tạp dynamic class loading
- Tích hợp vào Gradle build — quy trình quen thuộc
- Chi phí thấp hơn Promon SHIELD và Digital.ai

**Nhược điểm:**
- **Không có RASP runtime protection** — đây là hạn chế lớn nhất
- Không có API attestation
- Không có telemetry/dashboard
- **Không hỗ trợ iOS** (cần mua iXGuard riêng — sản phẩm khác)
- Không có Secure Local Storage
- Không có chứng nhận EMVCo SMBP

**Phù hợp với:** Ứng dụng Android cần obfuscation mạnh hơn ProGuard/R8 nhưng không cần RASP; ngân sách hạn chế.

**Không phù hợp với:** Ứng dụng cần bảo vệ runtime, iOS support, hoặc API attestation.

---

### 4.4 Digital.ai Application Protection (formerly Arxan)

**Tổng quan:** Giải pháp bảo mật ứng dụng di động toàn diện của Digital.ai (Mỹ), trước đây là Arxan Technologies. Cạnh tranh trực tiếp với Promon SHIELD.

**Ưu điểm:**
- RASP đầy đủ: root/jailbreak, debugger, hooking, repackaging, emulator
- Obfuscation nâng cao cho Java/Kotlin và native code
- API attestation tương tự App Verify
- Telemetry và dashboard
- Hỗ trợ iOS đầy đủ
- Post-build integration
- Chứng nhận ISO 27001, SOC 2

**Nhược điểm:**
- Chi phí tương đương hoặc cao hơn Promon SHIELD
- Không có chứng nhận EMVCo SMBP 2025 (Promon SHIELD có)
- Ít được biết đến tại thị trường Việt Nam — khó tìm đối tác hỗ trợ địa phương
- Tài liệu kỹ thuật ít chi tiết hơn Promon SHIELD
- Không có Secure Local Storage tương đương SLS với White-Box Crypto

**Phù hợp với:** Tổ chức đã có quan hệ với Digital.ai hoặc cần giải pháp thay thế Promon SHIELD.

**Không phù hợp với:** Ứng dụng thanh toán cần EMVCo SMBP; tổ chức ưu tiên hỗ trợ địa phương.

---

## 5. Đánh giá Tính khả thi RASP Tự xây dựng

### 5.1 Phạm vi công việc cần thực hiện

Để xây dựng một giải pháp RASP tương đương Promon SHIELD, đội ngũ nội bộ cần phát triển các module sau:

| Module | Mô tả | Độ phức tạp |
|---|---|---|
| Root/Jailbreak Detection | Phát hiện Magisk, SuperSU, Kingroot, root-hiding tools | Cao |
| Emulator Detection | Phát hiện AVD, Genymotion, BlueStacks | Trung bình |
| Debugger Detection | Phát hiện JDWP, ptrace, lldb | Cao |
| Anti-Frida | Phát hiện frida-server, gadget injection | Rất cao |
| Anti-Xposed/LSPosed | Phát hiện Xposed framework và modules | Cao |
| Anti-Repackaging | Signature verification, checksum | Trung bình |
| Screen Mirroring Detection | Phát hiện scrcpy, Miracast, AirPlay | Cao |
| Accessibility Detection | Phát hiện Accessibility Service abuse | Trung bình |
| iOS RASP | Jailbreak detection, anti-debug cho iOS | Rất cao |
| CI/CD Integration | Build pipeline, blueprint config | Trung bình |
| Telemetry System | Thu thập và gửi security events | Cao |
| Dashboard/SIEM | Hiển thị và phân tích events | Cao |
| Maintenance & Updates | Cập nhật khi Frida/Magisk ra version mới | Liên tục |

### 5.2 Ước tính Nhân lực (Người-Tháng)

#### Giai đoạn 1: Nghiên cứu và Thiết kế (3 tháng)

| Vai trò | Số người | Thời gian | Người-Tháng |
|---|---|---|---|
| Security Researcher (Android/iOS internals) | 2 | 3 tháng | 6 |
| Mobile Security Engineer | 2 | 3 tháng | 6 |
| Architect | 1 | 3 tháng | 3 |
| **Tổng Giai đoạn 1** | **5** | **3 tháng** | **15** |

#### Giai đoạn 2: Phát triển Android RASP (6 tháng)

| Module | Người-Tháng | Ghi chú |
|---|---|---|
| Root/Jailbreak Detection | 2 | Bao gồm root-hiding bypass |
| Emulator Detection | 1.5 | |
| Debugger + Anti-Frida | 4 | Frida detection rất phức tạp |
| Anti-Xposed/LSPosed | 2 | |
| Anti-Repackaging | 1.5 | |
| Screen Mirroring + Accessibility | 2 | |
| CI/CD Integration | 1 | |
| Testing & QA | 3 | |
| **Tổng Giai đoạn 2** | **17** | |

#### Giai đoạn 3: Phát triển iOS RASP (6 tháng)

| Module | Người-Tháng | Ghi chú |
|---|---|---|
| Jailbreak Detection (iOS) | 3 | iOS internals phức tạp hơn Android |
| Anti-Debug iOS | 2 | |
| Anti-Frida iOS | 3 | Frida trên iOS khác Android |
| Anti-Repackaging iOS | 1.5 | |
| Testing & QA iOS | 2.5 | Cần nhiều thiết bị iOS |
| **Tổng Giai đoạn 3** | **12** | |

#### Giai đoạn 4: Telemetry & Dashboard (3 tháng)

| Module | Người-Tháng | Ghi chú |
|---|---|---|
| Telemetry Client (Android + iOS) | 2 | |
| Backend Telemetry Server | 2 | |
| Dashboard UI | 2 | |
| SIEM Integration | 1 | |
| **Tổng Giai đoạn 4** | **7** | |

#### Tổng hợp Nhân lực

| Giai đoạn | Thời gian | Người-Tháng | Chi phí nhân lực (VND) |
|---|---|---|---|
| Giai đoạn 1: Nghiên cứu & Thiết kế | 3 tháng | 15 | ~750 triệu |
| Giai đoạn 2: Android RASP | 6 tháng | 17 | ~850 triệu |
| Giai đoạn 3: iOS RASP | 6 tháng | 12 | ~600 triệu |
| Giai đoạn 4: Telemetry & Dashboard | 3 tháng | 7 | ~350 triệu |
| **Tổng phát triển ban đầu** | **~15–18 tháng** | **51** | **~2.55 tỷ VND** |

*Giả định: Lương trung bình Security Engineer tại Việt Nam = 50 triệu VND/tháng (bao gồm overhead)*


### 5.3 Timeline Phát triển

```
Tháng 1-3:   [Nghiên cứu & Thiết kế]
              ├── Nghiên cứu Android/iOS internals
              ├── Thiết kế kiến trúc RASP
              └── Proof of Concept

Tháng 4-9:   [Phát triển Android RASP]
              ├── Root/Jailbreak/Emulator detection
              ├── Anti-Frida/Xposed
              ├── Anti-Repackaging
              └── Screen Mirroring/Accessibility

Tháng 10-15: [Phát triển iOS RASP]
              ├── Jailbreak detection
              ├── Anti-debug iOS
              └── Anti-Frida iOS

Tháng 16-18: [Telemetry & Dashboard]
              ├── Telemetry client
              ├── Backend server
              └── Dashboard UI

Tháng 19-21: [Testing, QA, Security Audit]
              ├── Penetration testing
              ├── Bug fixes
              └── Documentation

Tháng 22+:   [Maintenance & Updates]
              ├── Cập nhật khi Frida ra version mới
              ├── Hỗ trợ Android/iOS version mới
              └── Vá lỗ hổng mới phát hiện
```

**Thực tế:** Timeline 15–18 tháng là ước tính lạc quan. Nhiều tổ chức lớn đã thử và mất 2–3 năm để đạt mức bảo vệ tương đương giải pháp thương mại.

### 5.4 Chi phí Duy trì Hàng năm

| Hạng mục | Chi phí/năm (VND) | Ghi chú |
|---|---|---|
| **Nhân lực duy trì** | 1.2–2.0 tỷ | 2–3 Security Engineers full-time |
| **Cập nhật Frida signatures** | Bao gồm trong nhân lực | Frida ra version mới ~mỗi 2–4 tuần |
| **Hỗ trợ Android version mới** | Bao gồm trong nhân lực | Android ra major version mỗi năm |
| **Hỗ trợ iOS version mới** | Bao gồm trong nhân lực | iOS ra major version mỗi năm |
| **Security audit độc lập** | 200–500 triệu | Cần audit hàng năm để đảm bảo chất lượng |
| **Hạ tầng telemetry** | 100–200 triệu | Server, bandwidth, storage |
| **Tổng chi phí duy trì/năm** | **1.5–2.7 tỷ VND** | **~$60,000–$110,000 USD** |

### 5.5 Rủi ro của RASP Tự xây dựng

| Rủi ro | Mức độ | Mô tả |
|---|---|---|
| **Chất lượng bảo mật thấp hơn** | Rất cao | Đội ngũ nội bộ khó đạt mức chuyên môn của Promon AS (20 năm kinh nghiệm) |
| **False positive cao** | Cao | Phát hiện sai dẫn đến block người dùng hợp lệ |
| **Dễ bypass hơn** | Cao | Attacker có thể nghiên cứu và bypass giải pháp tự xây dễ hơn |
| **Rủi ro nhân sự** | Cao | Nếu Security Engineer nghỉ việc, kiến thức bị mất |
| **Không có chứng nhận** | Trung bình | Không thể đạt EMVCo SMBP hay SOC 2 |
| **Thời gian phản ứng chậm** | Cao | Khi Frida ra version mới, cần thời gian để cập nhật |
| **Chi phí ẩn** | Cao | Chi phí thực tế thường cao hơn ước tính 50–100% |

### 5.6 Kết luận về RASP Tự xây dựng

**RASP tự xây dựng KHÔNG khả thi** cho VNPT Media vì các lý do sau:

1. **Chi phí cao hơn giải pháp thương mại:** Chi phí phát triển ban đầu ~2.55 tỷ VND + duy trì ~1.5–2.7 tỷ/năm, tổng 3 năm đầu = ~7–10 tỷ VND. So với Promon SHIELD ~1–3 tỷ VND/3 năm.

2. **Thời gian quá dài:** 15–18 tháng để có sản phẩm cơ bản, trong khi MyVNPT và DigiShop cần bảo vệ ngay.

3. **Chất lượng không đảm bảo:** Promon AS có 20 năm kinh nghiệm và đội ngũ nghiên cứu chuyên sâu. Đội ngũ nội bộ không thể đạt mức tương đương trong thời gian ngắn.

4. **Không đạt chứng nhận:** Không thể đạt EMVCo SMBP 2025 — quan trọng cho DigiShop.

5. **Rủi ro nhân sự:** Phụ thuộc vào 2–3 người có kiến thức chuyên sâu — rủi ro cao khi họ nghỉ việc.

---

## 6. Điểm số Tổng hợp và Xếp hạng

### 6.1 Phương pháp tính điểm

Mỗi tiêu chí được chấm theo thang 1–5 và nhân với trọng số phản ánh mức độ quan trọng với VNPT Media:

| Tiêu chí | Trọng số | Lý do |
|---|---|---|
| RASP Runtime Protection | 20% | Yêu cầu cốt lõi của Checklist_VNPT |
| Code Obfuscation | 10% | Quan trọng nhưng không đủ một mình |
| API Attestation | 15% | Quan trọng cho DigiShop (thanh toán) |
| Telemetry & Dashboard | 10% | Cần thiết cho SOC team |
| Hỗ trợ iOS | 10% | MyVNPT và DigiShop có cả iOS |
| Chi phí (TCO) | 15% | Ngân sách là yếu tố thực tế |
| Độ phức tạp tích hợp | 10% | Ảnh hưởng đến timeline triển khai |
| Mức độ hỗ trợ | 10% | Quan trọng cho vận hành dài hạn |

### 6.2 Bảng điểm chi tiết

| Tiêu chí | Trọng số | Promon SHIELD | ProGuard | R8 | DexGuard | Digital.ai | RASP Tự xây |
|---|---|---|---|---|---|---|---|
| RASP Runtime | 20% | 5 (1.00) | 1 (0.20) | 1 (0.20) | 2 (0.40) | 5 (1.00) | 2 (0.40) |
| Code Obfuscation | 10% | 5 (0.50) | 2 (0.20) | 2 (0.20) | 4 (0.40) | 4 (0.40) | 1 (0.10) |
| API Attestation | 15% | 5 (0.75) | 1 (0.15) | 1 (0.15) | 1 (0.15) | 4 (0.60) | 2 (0.30) |
| Telemetry | 10% | 5 (0.50) | 1 (0.10) | 1 (0.10) | 1 (0.10) | 4 (0.40) | 1 (0.10) |
| Hỗ trợ iOS | 10% | 5 (0.50) | 1 (0.10) | 1 (0.10) | 1 (0.10) | 5 (0.50) | 2 (0.20) |
| Chi phí (TCO) | 15% | 3 (0.45) | 5 (0.75) | 5 (0.75) | 4 (0.60) | 3 (0.45) | 1 (0.15) |
| Độ phức tạp | 10% | 3 (0.30) | 5 (0.50) | 5 (0.50) | 4 (0.40) | 3 (0.30) | 1 (0.10) |
| Mức độ hỗ trợ | 10% | 4 (0.40) | 2 (0.20) | 3 (0.30) | 4 (0.40) | 4 (0.40) | 1 (0.10) |
| **Tổng điểm** | **100%** | **4.40** | **2.20** | **2.30** | **2.55** | **4.05** | **1.45** |

*Điểm trong ngoặc = điểm thô × trọng số*

### 6.3 Xếp hạng tổng hợp

| Hạng | Giải pháp | Điểm | Nhận xét |
|---|---|---|---|
| 🥇 1 | **Promon SHIELD** | **4.40/5.00** | Giải pháp toàn diện nhất; dẫn đầu về RASP, attestation, telemetry, iOS |
| 🥈 2 | **Digital.ai** | **4.05/5.00** | Cạnh tranh gần nhất với Promon SHIELD; thiếu EMVCo SMBP |
| 🥉 3 | **DexGuard** | **2.55/5.00** | Obfuscation tốt nhưng thiếu RASP và iOS |
| 4 | **R8** | **2.30/5.00** | Miễn phí, tích hợp sẵn nhưng bảo mật hạn chế |
| 5 | **ProGuard** | **2.20/5.00** | Tương tự R8 nhưng kém hơn về tối ưu |
| 6 | **RASP Tự xây** | **1.45/5.00** | Chi phí cao nhất, thời gian dài nhất, chất lượng thấp nhất |

---

## 7. Khuyến nghị có Lý giải Kỹ thuật

### 7.1 Khuyến nghị chính

> **Khuyến nghị: Triển khai Promon SHIELD cho MyVNPT và DigiShop, kết hợp với R8 như một lớp bảo vệ bổ sung.**

### 7.2 Lý giải kỹ thuật chi tiết

#### Lý do 1: Khoảng cách bảo mật hiện tại quá lớn

MyVNPT hiện đạt **2/10** và DigiShop đạt **1.5/10** tiêu chí Checklist_VNPT. Đây là mức bảo mật rất thấp cho ứng dụng phục vụ hàng triệu người dùng và xử lý giao dịch tài chính.

Promon SHIELD có thể nâng điểm lên **8–9/10** (dựa trên phân tích Task 1), giải quyết 8/10 tiêu chí Checklist_VNPT một cách trực tiếp.

#### Lý do 2: Promon SHIELD là giải pháp duy nhất đáp ứng đầy đủ yêu cầu

Phân tích cho thấy chỉ có Promon SHIELD và Digital.ai đáp ứng đầy đủ các yêu cầu kỹ thuật cốt lõi:

| Yêu cầu cốt lõi | Promon SHIELD | Digital.ai | Các giải pháp khác |
|---|:---:|:---:|:---:|
| RASP đầy đủ (8+ tiêu chí) | ✅ | ✅ | ❌ |
| Hỗ trợ cả Android và iOS | ✅ | ✅ | ❌ (ProGuard/R8/DexGuard) |
| API Attestation | ✅ | ✅ | ❌ |
| Telemetry/SIEM | ✅ | ✅ | ❌ |
| EMVCo SMBP (cho DigiShop) | ✅ | ❌ | ❌ |
| Post-build (không thay đổi code) | ✅ | ✅ | ❌ (ProGuard/R8) |

#### Lý do 3: Promon SHIELD vượt trội Digital.ai ở điểm quan trọng

Mặc dù Digital.ai là đối thủ cạnh tranh gần nhất, Promon SHIELD có lợi thế:

- **EMVCo SMBP 2025:** Chứng nhận quan trọng cho DigiShop (ứng dụng thanh toán). Digital.ai không có chứng nhận này.
- **Secure Local Storage (SLS) với White-Box Crypto:** Bảo vệ dữ liệu trên thiết bị root tốt hơn. Digital.ai không có module tương đương.
- **Gartner Hype Cycle 2025:** Được Gartner công nhận — tăng uy tín khi báo cáo lên ban lãnh đạo.
- **Hỗ trợ địa phương:** Promon AS có đối tác tại châu Á; dễ tìm hỗ trợ hơn Digital.ai tại Việt Nam.

#### Lý do 4: Chi phí hợp lý so với rủi ro

Phân tích chi phí-lợi ích:

| Kịch bản | Chi phí 3 năm | Rủi ro bảo mật |
|---|---|---|
| Không làm gì | $0 | Rất cao — vi phạm dữ liệu, mất uy tín |
| ProGuard/R8 | ~$5,000 | Cao — không có runtime protection |
| DexGuard | ~$45,000–$135,000 | Trung bình — thiếu RASP và iOS |
| **Promon SHIELD** | **~$105,000–$340,000** | **Thấp — bảo vệ toàn diện** |
| Digital.ai | ~$90,000–$300,000 | Thấp — nhưng thiếu EMVCo SMBP |
| RASP Tự xây | ~$300,000–$600,000 | Trung bình-Cao — chất lượng không đảm bảo |

**Chi phí vi phạm dữ liệu trung bình (IBM Cost of Data Breach 2024): $4.88 triệu USD.** Đầu tư vào Promon SHIELD là hợp lý về mặt kinh tế.

#### Lý do 5: Phù hợp với lộ trình kỹ thuật của VNPT Media

- **Post-build integration:** Không yêu cầu thay đổi mã nguồn MyVNPT/DigiShop — giảm rủi ro và thời gian triển khai
- **CI/CD support:** Tích hợp vào pipeline Jenkins/GitLab CI hiện có của VNPT Media
- **Hỗ trợ React Native/Flutter:** Nếu VNPT Media chuyển sang cross-platform trong tương lai
- **On-premise Insight:** Dữ liệu telemetry không rời khỏi hạ tầng VNPT Media — tuân thủ quy định bảo vệ dữ liệu

### 7.3 Khuyến nghị bổ sung

Promon SHIELD không phải là giải pháp hoàn hảo cho mọi vấn đề. Cần kết hợp với:

| Biện pháp bổ sung | Lý do | Ưu tiên |
|---|---|---|
| **R8 (bật mặc định)** | Giảm kích thước APK, obfuscation cơ bản miễn phí | Ngay lập tức |
| **Certificate Pinning** | Promon SHIELD không có cert pinning trực tiếp | Cao |
| **Network Security Config** | Ngăn cleartext traffic trên Android | Cao |
| **Mutual TLS (mTLS)** | Bảo vệ API nhạy cảm của DigiShop | Trung bình |
| **Secure Coding Training** | Promon SHIELD không thể bảo vệ logic lỗi | Trung bình |

### 7.4 Lộ trình triển khai đề xuất

**Giai đoạn 1 (Tháng 1–3): POC và Đàm phán**
- Thực hiện POC với memory-game.apk (Task 5–12)
- Yêu cầu báo giá chính thức từ Promon AS
- Đàm phán điều khoản hợp đồng (SLA, escrow, thời hạn)
- Quyết định triển khai

**Giai đoạn 2 (Tháng 4–6): Tích hợp DigiShop**
- DigiShop ưu tiên vì xử lý thanh toán (rủi ro cao hơn)
- Tích hợp SHIELD Core RASP + Code Protection + SAROM
- Tích hợp App Verify cho API thanh toán
- Triển khai Promon Insight on-premise

**Giai đoạn 3 (Tháng 7–9): Tích hợp MyVNPT**
- Áp dụng kinh nghiệm từ DigiShop
- Tích hợp SLS cho session tokens và PII
- Mở rộng Insight dashboard

**Giai đoạn 4 (Tháng 10+): Tối ưu và Mở rộng**
- Đánh giá kết quả thực tế
- Tối ưu cấu hình guard
- Xem xét mở rộng cho các ứng dụng khác

---

## 8. Kết luận

### 8.1 Tóm tắt so sánh

| Giải pháp | Điểm | Phù hợp với VNPT Media | Khuyến nghị |
|---|---|---|---|
| **Promon SHIELD** | **4.40/5** | ✅ Rất phù hợp | **Triển khai** |
| **Digital.ai** | 4.05/5 | ✅ Phù hợp | Phương án dự phòng |
| **DexGuard** | 2.55/5 | ⚠️ Một phần | Chỉ nếu ngân sách rất hạn chế |
| **R8** | 2.30/5 | ✅ Bổ sung | Kết hợp với Promon SHIELD |
| **ProGuard** | 2.20/5 | ✅ Bổ sung | Thay bằng R8 |
| **RASP Tự xây** | 1.45/5 | ❌ Không phù hợp | Không khuyến nghị |

### 8.2 Kết luận cuối cùng

**Promon SHIELD là giải pháp phù hợp nhất** cho VNPT Media vì:

1. **Toàn diện nhất:** Duy nhất đáp ứng đầy đủ 8/10 tiêu chí Checklist_VNPT
2. **Hỗ trợ cả Android và iOS:** Phủ toàn bộ portfolio ứng dụng của VNPT Media
3. **EMVCo SMBP 2025:** Chứng nhận quan trọng cho DigiShop
4. **Post-build:** Không yêu cầu thay đổi mã nguồn — triển khai nhanh
5. **Chi phí hợp lý:** Thấp hơn RASP tự xây; tương đương Digital.ai nhưng có thêm EMVCo SMBP
6. **Hỗ trợ kỹ thuật:** Promon AS có 20 năm kinh nghiệm và đội ngũ nghiên cứu chuyên sâu

**Điều kiện tiên quyết để triển khai:**
- Hoàn thành POC (Task 5–12) để xác nhận hiệu quả thực tế
- Đàm phán giá hợp lý với Promon AS (hoặc đối tác phân phối tại Việt Nam)
- Đảm bảo SLA rõ ràng về thời gian hỗ trợ OS mới và cập nhật signature
- Lập kế hoạch quản lý keystore và certificate an toàn

---

## Phụ lục A: Nguồn tham khảo

| Nguồn | Loại | Ghi chú |
|---|---|---|
| Tài liệu kỹ thuật Promon AS | Nhà cung cấp | Tổng quan kiến trúc và tính năng |
| Task 1: 01-tong-quan-kien-truc-tinh-nang.md | Nội bộ | Phân tích chi tiết 6 module |
| Task 2: 02-uu-nhuoc-diem-phan-tich.md | Nội bộ | Phân tích ưu nhược điểm |
| Digital.ai Application Protection (formerly Arxan).pdf | Nhà cung cấp | Thông tin Digital.ai |
| PB-AS-2022-digitalai-application-security.pdf | Nhà cung cấp | Product brief Digital.ai |
| Guardsquare DexGuard documentation | Nhà cung cấp | Thông tin DexGuard |
| Android Developer Documentation (ProGuard/R8) | Google | Thông tin ProGuard/R8 |
| IBM Cost of Data Breach Report 2024 | Nghiên cứu | Chi phí vi phạm dữ liệu |
| Gartner Hype Cycle for Application Security 2025 | Gartner | Công nhận Promon SHIELD |
| "Honey, I Shrunk Your App Security" (ResearchGate, 2018) | Học thuật | Nghiên cứu bypass Promon SHIELD |

## Phụ lục B: Thuật ngữ

| Thuật ngữ | Định nghĩa |
|---|---|
| RASP | Runtime Application Self-Protection |
| TCO | Total Cost of Ownership — Tổng chi phí sở hữu |
| WBC | White-Box Cryptography — Mật mã hộp trắng |
| EMVCo SMBP | EMV Secure Mobile Biometric Payment |
| SLA | Service Level Agreement — Thỏa thuận mức dịch vụ |
| MAU | Monthly Active Users — Người dùng hoạt động hàng tháng |
| POC | Proof of Concept — Thử nghiệm khái niệm |
| SIEM | Security Information and Event Management |

---

*Tài liệu này được lập bởi Phòng An ninh Thông tin – TSC (ANTT-TSC), VNPT Media.*  
*Phiên bản 1.0 — 2025*  
*Phụ thuộc: Task 1 (01-tong-quan-kien-truc-tinh-nang.md), Task 2 (02-uu-nhuoc-diem-phan-tich.md)*
