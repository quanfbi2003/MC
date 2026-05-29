# BÁO CÁO ĐÁNH GIÁ GIẢI PHÁP BẢO MẬT ỨNG DỤNG DI ĐỘNG
# PROMON SHIELD — ĐÁNH GIÁ VÀ KHUYẾN NGHỊ TRIỂN KHAI

**Đơn vị thực hiện:** Phòng An ninh Thông tin – TSC (ANTT-TSC), VNPT Media  
**Phiên bản:** 1.0 — Bản cuối  
**Ngày lập:** 2025  
**Phân loại:** Nội bộ — Dành cho Ban Lãnh đạo  
**Yêu cầu liên quan:** Yêu cầu 11

---

## Mục lục

1. [Tóm tắt Điều hành](#phần-1--tóm-tắt-điều-hành)
2. [Nghiên cứu Công nghệ](#phần-2--nghiên-cứu-công-nghệ)
3. [Đánh giá Tiêu chuẩn](#phần-3--đánh-giá-tiêu-chuẩn)
4. [Khuyến nghị](#phần-4--khuyến-nghị)
5. [Lộ trình Triển khai](#phần-5--lộ-trình-triển-khai)

---

## PHẦN 1 — TÓM TẮT ĐIỀU HÀNH

> **Dành cho Ban Lãnh đạo — Đọc trong 5 phút**

### 1.1 Bối cảnh và Vấn đề

Hai ứng dụng di động chiến lược của VNPT Media — **MyVNPT** (hàng triệu người dùng) và **DigiShop** (ứng dụng thanh toán) — hiện đang ở mức bảo mật rất thấp theo Checklist_VNPT nội bộ. Điều này tạo ra rủi ro nghiêm trọng về lộ lọt dữ liệu người dùng, gian lận tài chính và mất uy tín thương hiệu.

Phòng ANTT-TSC đã thực hiện nghiên cứu toàn diện trong 7 tuần để đánh giá giải pháp **Promon SHIELD** — một nền tảng bảo mật ứng dụng di động thương mại của Promon AS (Na Uy).

### 1.2 Kết quả Đánh giá — Điểm số Trước/Sau

| Ứng dụng | Điểm bảo mật HIỆN TẠI | Điểm sau PROMON SHIELD | Điểm sau bổ sung đầy đủ | Cải thiện |
|---|:---:|:---:|:---:|:---:|
| **MyVNPT** | 2.0/10 | **8.5/10** | 9.5–10/10 | **+325%** |
| **DigiShop** | 1.5/10 | **8.5/10** | 9.5–10/10 | **+467%** |

*Thang điểm: Checklist_VNPT 10 tiêu chí bảo mật ứng dụng di động nội bộ*

### 1.3 Kết quả Đánh giá Theo Tiêu chuẩn Quốc tế (OWASP MASVS)

| Nhóm tiêu chuẩn | Mức đáp ứng của Promon SHIELD |
|---|:---:|
| **MASVS-RESILIENCE** (chống tấn công runtime) | **100%** — Xuất sắc |
| Tổng thể MASVS (39 controls) | 36% (toàn bộ) / 74% (trong phạm vi) |

> **Lưu ý:** 51% controls MASVS thuộc trách nhiệm developer (secure coding) — nằm ngoài phạm vi bất kỳ giải pháp bảo mật bổ sung nào.

### 1.4 Kết luận Điều hành

**Khuyến nghị: (c) TRIỂN KHAI CÓ ĐIỀU KIỆN**

Promon SHIELD là giải pháp phù hợp nhất cho VNPT Media, với điều kiện hoàn thành 3 bước tiên quyết:

1. **Hoàn thành POC nội bộ** (Tháng 1–3): Xác nhận tích hợp kỹ thuật với MyVNPT và DigiShop
2. **Đàm phán giá và SLA** (Tháng 1–3): Đảm bảo TCO phù hợp ngân sách và cam kết hỗ trợ
3. **Bổ sung Certificate Pinning** (Tháng 4–6): Nâng điểm MiTM từ 0.5 → 1.0, đạt 9.0/10

**Lý do kinh tế:** Chi phí vi phạm dữ liệu trung bình (IBM 2024): **$4.88 triệu USD**. Đầu tư Promon SHIELD: **$42,000–$133,000/năm đầu** — tỷ lệ bảo vệ/chi phí rất cao.

---

## PHẦN 2 — NGHIÊN CỨU CÔNG NGHỆ

*Tổng hợp từ Task 1–4. Chi tiết kỹ thuật đầy đủ xem tại các tài liệu riêng.*

### 2.1 Promon SHIELD là gì?

Promon SHIELD là nền tảng bảo mật ứng dụng di động thương mại của **Promon AS** (Na Uy, thành lập 2003). Giải pháp hoạt động theo mô hình **post-build** — tích hợp bảo vệ vào APK/IPA đã build xong mà **không cần thay đổi mã nguồn** ứng dụng.

**6 module chính:**

| Module | Chức năng | Ứng dụng |
|---|---|---|
| **SHIELD Core RASP** | Bảo vệ runtime: phát hiện root, hook, debug, repack, emulator, screen mirroring, accessibility abuse | Cốt lõi — bắt buộc |
| **Code Protection** | Obfuscation đa lớp: mã hóa string, section, control-flow | Chống reverse engineering |
| **SAROM** | Mã hóa dữ liệu tĩnh AES-256 GCM (API keys, secrets trong APK) | Bảo vệ secrets |
| **SLS** | Lưu trữ cục bộ an toàn với White-Box Crypto + Device Binding | Bảo vệ session token |
| **App Verify** | API Attestation — xác thực request đến từ app gốc | Bảo vệ backend API |
| **Promon Insight** | Telemetry & Dashboard — thu thập security events, tích hợp SIEM | Giám sát bảo mật |

**Nền tảng hỗ trợ:** Android (API 21+), iOS (12+), Native, React Native, Flutter, Cordova.

**Chứng nhận:** ISO 27001, SOC 2 Type II, **EMVCo SMBP 2025** (quan trọng cho DigiShop), EU DORA.

### 2.2 Ưu điểm Chính

- **Post-build integration:** Không thay đổi mã nguồn — giảm rủi ro và thời gian triển khai đáng kể
- **RASP runtime:** Bảo vệ chủ động khi app đang chạy — không thể bypass bằng static analysis
- **EMVCo SMBP 2025:** Chứng nhận thanh toán quan trọng — lợi thế cạnh tranh cho DigiShop
- **Hỗ trợ cả Android + iOS:** Một giải pháp cho cả hai nền tảng
- **Promon Insight:** Telemetry tích hợp SIEM — SOC team có thể giám sát real-time

### 2.3 Nhược điểm và Rủi ro

- **Chi phí:** TCO năm đầu $42,000–$133,000 — cần đàm phán và lập kế hoạch ngân sách
- **Vendor lock-in:** Phụ thuộc vào Promon AS — cần SLA rõ ràng và kế hoạch exit
- **Tăng kích thước APK:** ~15–30% (ước tính từ tài liệu nhà cung cấp) — cần kiểm tra thực tế
- **Bước ký lại bắt buộc:** Sau mỗi lần shield, phải ký lại APK/IPA — cần tích hợp vào CI/CD
- **SLS và App Verify:** Yêu cầu thay đổi mã nguồn nhỏ (tích hợp SDK) — cần effort từ dev team

### 2.4 So sánh với Giải pháp Thay thế

| Giải pháp | Điểm tổng hợp | Nhận xét |
|---|:---:|---|
| **🥇 Promon SHIELD** | **4.40/5** | Toàn diện nhất; dẫn đầu RASP, attestation, iOS, EMVCo SMBP |
| 🥈 Digital.ai | 4.05/5 | Cạnh tranh gần nhất; **thiếu EMVCo SMBP** — bất lợi cho DigiShop |
| 🥉 DexGuard | 2.55/5 | Obfuscation tốt nhưng **không có RASP**, không có iOS |
| R8 (Google) | 2.30/5 | Miễn phí, tích hợp sẵn nhưng bảo mật rất hạn chế |
| ProGuard | 2.20/5 | Tương tự R8, kém hơn về tối ưu |
| RASP tự xây | 1.45/5 | **Không khả thi:** 15–18 tháng phát triển, ~2.55 tỷ VND, chất lượng không đảm bảo |

*Trọng số đánh giá: RASP 20%, API Attestation 15%, Chi phí 15%, iOS 10%, Telemetry 10%, Obfuscation 10%, Tích hợp 10%, Hỗ trợ 10%*

### 2.5 Quy trình Tích hợp

**Thời gian ước tính:** 3–4 tuần (nhóm 4–5 người, bao gồm 1 Security Engineer, 2 Mobile Developer, 1 DevOps, 1 Backend Developer).

**Quy trình cốt lõi (5 bước):**
1. Chuẩn bị môi trường (JDK 17, Android SDK, Xcode, signing certificate)
2. Cấu hình Blueprint JSON (định nghĩa guards và phản ứng)
3. Chạy Shielder CLI trên APK/AAB/IPA đã build
4. **Ký lại bắt buộc** với production keystore/certificate
5. Tích hợp vào CI/CD pipeline (Jenkins/GitLab CI/GitHub Actions)

**Thay đổi mã nguồn cần thiết:**
- SHIELD Core RASP: **Không cần** thay đổi mã nguồn
- SLS (Secure Local Storage): Cần tích hợp SLS SDK (~1–2 ngày)
- App Verify: Cần tích hợp SDK + thay đổi backend (~3–5 ngày)

---

## PHẦN 3 — ĐÁNH GIÁ TIÊU CHUẨN

### 3.1 Đánh giá OWASP MASVS v2.x — Tóm tắt

OWASP MASVS (Mobile Application Security Verification Standard) là tiêu chuẩn bảo mật ứng dụng di động quốc tế với 39 controls chia thành 7 nhóm.

#### Bảng Tỷ lệ Đáp ứng theo Nhóm

| Nhóm MASVS | Tổng Controls | Promon SHIELD Đáp ứng | Tỷ lệ (toàn bộ) | Tỷ lệ (trong phạm vi) |
|---|:---:|---|:---:|:---:|
| **RESILIENCE** | 8 | ✅ 8/8 đầy đủ | **100%** | **100%** |
| STORAGE | 7 | ✅ 2 đầy đủ + ⚠️ 2 một phần | 43% | 75% |
| AUTH | 3 | ⚠️ 2 một phần | 33% | 50% |
| NETWORK | 3 | ⚠️ 1 một phần | 17% | 50% |
| CRYPTO | 6 | ⚠️ 1 một phần | 8% | 50% |
| PLATFORM | 8 | ⚠️ 1 một phần | 6% | 50% |
| CODE | 4 | ⚠️ 1 một phần + ❌ 1 không đáp ứng | 13% | 25% |
| **TỔNG CỘNG** | **39** | **10 đầy đủ + 8 một phần** | **36%** | **74%** |

> **Lưu ý quan trọng:** 20/39 controls (51%) thuộc trách nhiệm developer (secure coding practices) — nằm ngoài phạm vi bất kỳ giải pháp bảo mật bổ sung nào. Promon SHIELD đóng góp vào 19 controls còn lại với tỷ lệ 74%.

#### Mapping MASVS-RESILIENCE (Nhóm cốt lõi — 100% đáp ứng)

| Control | Mô tả | Phân loại | Module Promon SHIELD |
|---|---|:---:|---|
| RESILIENCE-1 | Xác thực tính toàn vẹn platform | ✅ Đầy đủ | SHIELD Core RASP (Root/Jailbreak/Emulator Detection) |
| RESILIENCE-2 | Chống giả mạo (anti-tampering) | ✅ Đầy đủ | SHIELD Core RASP + Code Protection |
| RESILIENCE-3 | Chống phân tích tĩnh | ✅ Đầy đủ | Code Protection (Section Encryption, Control-Flow) |
| RESILIENCE-4 | Chống phân tích động | ✅ Đầy đủ | SHIELD Core RASP (Frida/Xposed/Debugger Detection) |
| RESILIENCE-5 | Device binding | ✅ Đầy đủ | SLS (Device ID Binding) + RASP (App Binding) |
| RESILIENCE-6 | Kiểm soát truy cập bổ sung | ✅ Đầy đủ | App Verify (API Attestation) + SAROM |
| RESILIENCE-7 | Phát hiện và phản ứng với giả mạo | ✅ Đầy đủ | SHIELD Core RASP (Reaction) + Promon Insight |
| RESILIENCE-8 | Phát hiện Root/Jailbreak | ✅ Đầy đủ | SHIELD Core RASP (Root-hiding tools detection) |

---

### 3.2 Đánh giá Checklist_VNPT — 10 Tiêu chí

#### Bảng Đánh giá Chi tiết

| # | Tiêu chí | Kết quả | Điểm | Module Promon SHIELD | Ghi chú / Cần bổ sung |
|---|---|:---:|:---:|---|---|
| 1 | **Chống MiTM** | ⚠️ Một phần | 0.5 | App Verify (API Attestation) | Cần bổ sung **Certificate Pinning** |
| 2 | **Chống Hook** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | Phát hiện Frida, Xposed, LSPosed, Riru |
| 3 | **Chống Debug** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | Phát hiện cả Java và native debugger |
| 4 | **Chống Repack** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | Signature + DEX Checksum + Resource Verification |
| 5 | **Chống Dynamic Load APK** | ⚠️ Một phần | 0.5 | SHIELD Core RASP | Cần **code audit** + cấm DexClassLoader |
| 6 | **Chống Screen Mirroring** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | Phát hiện scrcpy, Miracast, AirPlay |
| 7 | **Chống Inject Camera** | ⚠️ Một phần | 0.5 | SHIELD Core RASP (gián tiếp) | Cần **Liveness Detection SDK** nếu dùng eKYC |
| 8 | **Root/Jailbreak Detection** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | Phát hiện cả root-hiding tools (Shamiko, Zygisk) |
| 9 | **Emulator Detection** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | Phát hiện AVD, Genymotion, BlueStacks |
| 10 | **Accessibility Abuse Detection** | ✅ Đáp ứng | 1.0 | SHIELD Core RASP | Phát hiện Screen Reader, Overlay Attack |
| | **TỔNG CỘNG** | | **8.5/10** | | |

#### Bảng So sánh Điểm số Trước/Sau

| Ứng dụng | Trước Promon SHIELD | Sau Promon SHIELD | Sau + Cert Pinning | Sau + Đầy đủ bổ sung |
|---|:---:|:---:|:---:|:---:|
| **MyVNPT** | 2.0/10 | **8.5/10** | 9.0/10 | **9.5–10/10** |
| **DigiShop** | 1.5/10 | **8.5/10** | 9.0/10 | **9.5–10/10** |
| **Cải thiện MyVNPT** | — | +6.5 điểm | +7.0 điểm | +7.5–8.0 điểm |
| **Cải thiện DigiShop** | — | +7.0 điểm | +7.5 điểm | +8.0–8.5 điểm |

#### Lộ trình Đạt Điểm Tối đa

```
Tháng 1–3:   Triển khai Promon SHIELD
             → MyVNPT: 2.0 → 8.5/10 | DigiShop: 1.5 → 8.5/10

Tháng 4–6:   Bổ sung Certificate Pinning
             → Cả hai ứng dụng: 8.5 → 9.0/10

Tháng 6–9:   Code Audit + Dynamic Loading Fix
             → Cả hai ứng dụng: 9.0 → 9.5/10

Tháng 9–12:  Liveness Detection (nếu cần eKYC)
             → Cả hai ứng dụng: 9.5 → 10/10
```

---

## PHẦN 4 — KHUYẾN NGHỊ

### 4.1 Kết luận: (c) TRIỂN KHAI CÓ ĐIỀU KIỆN

Sau khi phân tích toàn diện kỹ thuật, chi phí và rủi ro, Phòng ANTT-TSC **khuyến nghị triển khai Promon SHIELD** cho MyVNPT và DigiShop, với 3 điều kiện tiên quyết phải hoàn thành trước khi ký hợp đồng chính thức.

### 4.2 Ba Điều kiện Tiên quyết

#### Điều kiện 1: Hoàn thành POC nội bộ (Proof of Concept)

**Mục tiêu:** Xác nhận Promon SHIELD tích hợp thành công với kiến trúc thực tế của MyVNPT và DigiShop, không gây lỗi chức năng.

**Nội dung POC:**
- Tích hợp SHIELD Core RASP vào bản build thử nghiệm của DigiShop (Android + iOS)
- Kiểm tra các chức năng cốt lõi: đăng nhập, thanh toán, xem hóa đơn
- Đo lường tác động hiệu năng: kích thước APK, thời gian khởi động, CPU/RAM
- Kiểm tra tích hợp CI/CD pipeline hiện có của VNPT Media

**Tiêu chí thành công:** App hoạt động bình thường trên thiết bị thực không root; không có false positive block người dùng hợp lệ; tăng kích thước APK ≤ 30%.

**Timeline:** 4–6 tuần | **Nhân lực:** 2 Mobile Developer + 1 Security Engineer + 1 DevOps

#### Điều kiện 2: Đàm phán giá và SLA với Promon AS

**Mục tiêu:** Đảm bảo TCO phù hợp ngân sách VNPT Media và có cam kết hỗ trợ kỹ thuật rõ ràng.

**Các điểm cần đàm phán:**

| Hạng mục | Yêu cầu tối thiểu |
|---|---|
| **Mô hình license** | Per-app (không per-user) — phù hợp với quy mô VNPT Media |
| **Phạm vi license** | Bao gồm cả Android + iOS cho MyVNPT và DigiShop |
| **SLA hỗ trợ kỹ thuật** | Tối thiểu 8×5, phản hồi trong 4 giờ cho sự cố nghiêm trọng |
| **Cập nhật signature** | Cam kết cập nhật Frida/Magisk signature trong 72 giờ sau khi phiên bản mới ra |
| **Hỗ trợ Android/iOS mới** | Cam kết hỗ trợ major version mới trong 30 ngày sau khi phát hành |
| **Promon Insight** | Bao gồm trong license hoặc giá riêng rõ ràng |
| **Điều khoản exit** | Quyền nhận lại APK/IPA gốc (chưa shield) khi chấm dứt hợp đồng |

**Chi phí tham chiếu (ước tính thị trường):**
- TCO năm đầu: $42,000–$133,000 (bao gồm license, training, tích hợp, hạ tầng Insight)
- TCO từ năm 2+: $35,000–$113,000/năm

**Timeline:** Song song với POC (Tháng 1–3)

#### Điều kiện 3: Bổ sung Certificate Pinning

**Mục tiêu:** Nâng điểm tiêu chí Chống MiTM từ 0.5 → 1.0, đạt tổng điểm 9.0/10.

**Lý do bắt buộc:** App Verify của Promon SHIELD xác thực nguồn gốc request nhưng không bảo vệ kênh truyền thông. Attacker vẫn có thể intercept HTTPS traffic bằng cách cài CA giả vào thiết bị. Certificate Pinning là biện pháp bổ sung bắt buộc cho ứng dụng tài chính.

**Triển khai:**
- Android: OkHttp `CertificatePinner` hoặc Android Network Security Config
- iOS: TrustKit hoặc URLSession custom delegate
- Pin public key hash (SPKI) với ít nhất 2 backup pins
- Cập nhật pins trước khi certificate hết hạn (≥30 ngày)

**Effort:** 2–3 ngày developer | **Timeline:** Trong vòng 1 tháng sau khi triển khai Promon SHIELD

### 4.3 Phân tích Chi phí — Lợi ích

#### So sánh Chi phí 3 năm

| Kịch bản | Chi phí 3 năm (USD) | Mức bảo mật | Rủi ro |
|---|:---:|:---:|:---:|
| **Không làm gì** | $0 | 2/10 | **Rất cao** |
| ProGuard/R8 (miễn phí) | ~$5,000 | 3/10 | Cao |
| DexGuard | ~$45,000–$135,000 | 5/10 | Trung bình |
| **Promon SHIELD** | **~$105,000–$340,000** | **8.5–9.5/10** | **Thấp** |
| Digital.ai | ~$90,000–$300,000 | 8.5/10 | Thấp (thiếu EMVCo) |
| RASP tự xây | ~$300,000–$600,000 | 5–6/10 | Trung bình–Cao |

#### Phân tích ROI

**Chi phí vi phạm dữ liệu trung bình (IBM Cost of Data Breach Report 2024): $4.88 triệu USD**

Nếu xác suất vi phạm dữ liệu trong 3 năm là 10% (ước tính thận trọng):
- **Rủi ro kỳ vọng không bảo vệ:** $4.88M × 10% = **$488,000**
- **Chi phí Promon SHIELD 3 năm:** ~$105,000–$340,000
- **Tiết kiệm kỳ vọng:** $148,000–$383,000

Ngoài ra, chi phí gián tiếp khi vi phạm dữ liệu (mất uy tín thương hiệu, phạt theo quy định, mất khách hàng) thường gấp 2–3 lần chi phí trực tiếp.

**Kết luận:** Đầu tư vào Promon SHIELD có ROI dương rõ ràng ngay cả với xác suất vi phạm thấp.

#### Lý do chọn Promon SHIELD thay vì Digital.ai

Mặc dù Digital.ai có điểm tổng hợp gần tương đương (4.05/5 vs 4.40/5), Promon SHIELD được ưu tiên vì:

1. **EMVCo SMBP 2025:** Chứng nhận quan trọng cho DigiShop (ứng dụng thanh toán). Digital.ai không có chứng nhận này.
2. **Secure Local Storage (SLS) với White-Box Crypto:** Bảo vệ dữ liệu trên thiết bị root tốt hơn. Digital.ai không có module tương đương.
3. **Hỗ trợ địa phương:** Promon AS có đối tác tại châu Á — dễ tìm hỗ trợ kỹ thuật tại Việt Nam hơn Digital.ai.
4. **Gartner Hype Cycle 2025:** Được Gartner công nhận — tăng uy tín khi báo cáo lên ban lãnh đạo.

---

## PHẦN 5 — LỘ TRÌNH TRIỂN KHAI

### 5.1 Tổng quan Lộ trình

```
GIAI ĐOẠN 1          GIAI ĐOẠN 2          GIAI ĐOẠN 3
Tháng 1–3            Tháng 4–6            Tháng 7–12
─────────────────    ─────────────────    ─────────────────────
POC + Đàm phán  →   Triển khai DigiShop → Triển khai MyVNPT
                                           + Mở rộng
```

---

### 5.2 Giai đoạn 1: POC và Đàm phán (Tháng 1–3)

**Mục tiêu:** Xác nhận tính khả thi kỹ thuật và hoàn tất điều kiện tiên quyết trước khi cam kết đầu tư.

#### Ứng dụng ưu tiên: DigiShop (Android — bản thử nghiệm)

**Lý do chọn DigiShop trước:** Điểm bảo mật thấp hơn (1.5/10), rủi ro cao hơn do tính năng thanh toán, và cần chứng nhận EMVCo SMBP.

#### Điều kiện tiên quyết

| Điều kiện | Trách nhiệm | Deadline |
|---|---|---|
| Nhận license thử nghiệm từ Promon AS | ANTT-TSC | Tuần 1 |
| Chuẩn bị môi trường build (JDK 17, Android SDK) | DevOps | Tuần 1 |
| Cung cấp bản build DigiShop release (unsigned) | Mobile Dev Team | Tuần 2 |
| Chuẩn bị keystore thử nghiệm | Mobile Dev Team | Tuần 2 |
| Xác định CI/CD pipeline hiện có | DevOps | Tuần 1 |

#### Các hoạt động chính

**Tuần 1–2: Thiết lập môi trường**
- Cài đặt Shielder tool và kiểm tra license
- Chuẩn bị Blueprint JSON cơ bản (RASP cốt lõi)
- Build DigiShop release APK (unsigned)

**Tuần 3–4: Thực hiện POC**
- Chạy Shielder trên DigiShop APK
- Ký lại và cài thử trên thiết bị test
- Kiểm tra chức năng cốt lõi (đăng nhập, thanh toán, xem hóa đơn)
- Đo lường: kích thước APK, thời gian khởi động, CPU/RAM

**Tuần 5–6: Tích hợp CI/CD và đánh giá**
- Tích hợp bước shield vào pipeline Jenkins/GitLab CI
- Kiểm tra automated test suite sau khi shield
- Lập báo cáo POC với kết quả đo lường thực tế

**Song song (Tuần 1–12): Đàm phán thương mại**
- Gửi RFQ (Request for Quotation) cho Promon AS
- Đàm phán giá, mô hình license, SLA
- Review hợp đồng với bộ phận pháp lý
- Hoàn tất ký kết hợp đồng

#### Tiêu chí Hoàn thành Giai đoạn 1

- [ ] POC thành công: DigiShop hoạt động bình thường sau khi shield
- [ ] Không có false positive block người dùng hợp lệ
- [ ] Tăng kích thước APK ≤ 30%
- [ ] CI/CD pipeline tích hợp thành công
- [ ] Hợp đồng với Promon AS đã ký

---

### 5.3 Giai đoạn 2: Triển khai DigiShop (Tháng 4–6)

**Mục tiêu:** Triển khai Promon SHIELD đầy đủ cho DigiShop (Android + iOS), đưa điểm bảo mật từ 1.5/10 lên 8.5/10.

#### Ứng dụng ưu tiên: DigiShop (Android + iOS — Production)

**Lý do DigiShop trước MyVNPT:**
- Điểm bảo mật thấp hơn (1.5 vs 2.0)
- Xử lý giao dịch tài chính — rủi ro cao hơn
- Cần EMVCo SMBP — chứng nhận chỉ có ở Promon SHIELD

#### Điều kiện tiên quyết

| Điều kiện | Trách nhiệm | Deadline |
|---|---|---|
| Hoàn thành Giai đoạn 1 (POC + hợp đồng) | ANTT-TSC | Cuối Tháng 3 |
| Môi trường macOS cho iOS shielding | DevOps | Đầu Tháng 4 |
| Apple Distribution Certificate hợp lệ | Mobile Dev Team | Đầu Tháng 4 |
| Kế hoạch rollback nếu có sự cố | ANTT-TSC + DevOps | Đầu Tháng 4 |
| Thông báo cho QA team về quy trình test mới | PM | Đầu Tháng 4 |

#### Các hoạt động chính

**Tháng 4 — Triển khai Android DigiShop**
- Cấu hình Blueprint JSON production (đầy đủ guards)
- Tích hợp SLS SDK cho session token và credentials
- Tích hợp App Verify SDK + thay đổi backend API
- Triển khai Certificate Pinning (OkHttp CertificatePinner)
- Chạy full regression test
- Release lên Google Play (track internal → production)

**Tháng 5 — Triển khai iOS DigiShop**
- Cấu hình Blueprint JSON cho iOS
- Thiết lập môi trường macOS shielding
- Tích hợp SLS và App Verify cho iOS
- Triển khai Certificate Pinning (TrustKit)
- Chạy full regression test trên thiết bị iOS thực
- Submit lên App Store

**Tháng 6 — Ổn định và giám sát**
- Theo dõi Promon Insight dashboard
- Phân tích security events từ production
- Tích hợp Promon Insight với SIEM của VNPT Media
- Điều chỉnh cấu hình nếu có false positive
- Lập báo cáo kết quả Giai đoạn 2

#### Tiêu chí Hoàn thành Giai đoạn 2

- [ ] DigiShop Android đạt 8.5/10 Checklist_VNPT
- [ ] DigiShop iOS đạt 8.5/10 Checklist_VNPT
- [ ] Certificate Pinning triển khai thành công (điểm MiTM: 0.5 → 1.0)
- [ ] Promon Insight tích hợp với SIEM
- [ ] Không có sự cố production trong 2 tuần đầu sau release
- [ ] Tổng điểm DigiShop: 1.5/10 → **9.0/10**

---

### 5.4 Giai đoạn 3: Triển khai MyVNPT và Mở rộng (Tháng 7–12)

**Mục tiêu:** Triển khai Promon SHIELD cho MyVNPT, hoàn thiện bảo mật toàn diện, và lập kế hoạch mở rộng cho các ứng dụng khác.

#### Ứng dụng ưu tiên: MyVNPT (Android + iOS — Production)

#### Điều kiện tiên quyết

| Điều kiện | Trách nhiệm | Deadline |
|---|---|---|
| Hoàn thành Giai đoạn 2 thành công | ANTT-TSC | Cuối Tháng 6 |
| Bài học kinh nghiệm từ DigiShop đã được ghi nhận | ANTT-TSC | Đầu Tháng 7 |
| Blueprint JSON cho MyVNPT đã được chuẩn bị | ANTT-TSC | Đầu Tháng 7 |
| Code audit Dynamic Loading cho MyVNPT | Mobile Dev Team | Tháng 7–8 |

#### Các hoạt động chính

**Tháng 7–8 — Triển khai MyVNPT Android + iOS**
- Áp dụng kinh nghiệm từ DigiShop — quy trình đã được tối ưu
- Tích hợp đầy đủ: SHIELD Core RASP + SLS + App Verify + Certificate Pinning
- Code audit và fix Dynamic Loading (nâng điểm tiêu chí #5 từ 0.5 → 1.0)
- Release lên Google Play và App Store

**Tháng 9–10 — Hoàn thiện bảo mật**
- Triển khai Promon Insight đầy đủ cho cả MyVNPT và DigiShop
- Xây dựng Security Dashboard tích hợp SIEM
- Đào tạo SOC team về phân tích security events từ Promon Insight
- Cập nhật Checklist_VNPT lên 17 tiêu chí (bổ sung 7 tiêu chí mới từ MASVS)

**Tháng 11–12 — Đánh giá và Mở rộng**
- Đánh giá lại điểm bảo mật theo Checklist_VNPT 17 tiêu chí
- Lập kế hoạch triển khai cho các ứng dụng khác của VNPT Media (nếu có)
- Lập báo cáo tổng kết năm đầu triển khai
- Đàm phán gia hạn hợp đồng năm 2

#### Tiêu chí Hoàn thành Giai đoạn 3

- [ ] MyVNPT Android + iOS đạt 9.0/10 Checklist_VNPT
- [ ] Code audit Dynamic Loading hoàn thành (điểm tiêu chí #5: 0.5 → 1.0)
- [ ] Tổng điểm MyVNPT: 2.0/10 → **9.5/10**
- [ ] SOC team đã được đào tạo về Promon Insight
- [ ] Checklist_VNPT cập nhật lên 17 tiêu chí
- [ ] Kế hoạch mở rộng cho ứng dụng khác đã được lập

---

### 5.5 Tóm tắt Lộ trình và Nguồn lực

#### Timeline Tổng hợp

| Giai đoạn | Thời gian | Ứng dụng | Mục tiêu điểm | Ngân sách ước tính |
|---|---|---|:---:|---|
| **Giai đoạn 1** | Tháng 1–3 | DigiShop (POC) | Xác nhận kỹ thuật | $5,000–$15,000 (POC + đàm phán) |
| **Giai đoạn 2** | Tháng 4–6 | DigiShop (Production) | 1.5 → **9.0/10** | $42,000–$133,000 (license năm đầu) |
| **Giai đoạn 3** | Tháng 7–12 | MyVNPT (Production) | 2.0 → **9.5/10** | Bao gồm trong license năm đầu |

#### Nhân lực Cần thiết

| Vai trò | Giai đoạn 1 | Giai đoạn 2 | Giai đoạn 3 |
|---|:---:|:---:|:---:|
| Security Engineer (ANTT-TSC) | 1 FTE | 0.5 FTE | 0.5 FTE |
| Mobile Developer (Android) | 0.5 FTE | 1 FTE | 1 FTE |
| Mobile Developer (iOS) | 0.5 FTE | 1 FTE | 1 FTE |
| DevOps Engineer | 0.5 FTE | 0.5 FTE | 0.25 FTE |
| Backend Developer | 0.25 FTE | 0.5 FTE | 0.5 FTE |

#### Rủi ro và Biện pháp Giảm thiểu

| Rủi ro | Xác suất | Tác động | Biện pháp |
|---|:---:|:---:|---|
| False positive block người dùng hợp lệ | Trung bình | Cao | POC kỹ lưỡng; cấu hình Callback thay vì ExitOn cho một số guards |
| Tăng kích thước APK quá mức | Thấp | Trung bình | Đo lường trong POC; tắt guards không cần thiết |
| Xung đột với thư viện third-party | Trung bình | Trung bình | Cấu hình `keepSignatures`; test kỹ trước release |
| Vendor lock-in | Thấp | Cao | Đàm phán điều khoản exit; lưu trữ APK/IPA gốc |
| Frida bypass mới | Thấp | Trung bình | SLA cập nhật signature trong 72 giờ |

---

---

## PHỤ LỤC A — Danh sách Tài liệu Nghiên cứu

| Tài liệu | Mô tả | Task |
|---|---|---|
| `01-tong-quan-kien-truc-tinh-nang.md` | Tổng quan kiến trúc và 6 module Promon SHIELD | Task 1 |
| `02-uu-nhuoc-diem-phan-tich.md` | Phân tích ưu nhược điểm, chi phí TCO | Task 2 |
| `03-ma-tran-so-sanh.md` | Ma trận so sánh 6 giải pháp theo 10 tiêu chí | Task 3 |
| `04-quy-trinh-tich-hop.md` | Quy trình tích hợp Shielder CLI + CI/CD | Task 4 |
| `05-danh-gia-masvs.md` | Đánh giá 39 MASVS controls | Task 5 |
| `06-danh-gia-checklist-vnpt.md` | Đánh giá 10 tiêu chí Checklist_VNPT | Task 6 |
| `07-bao-cao-danh-gia-cuoi-cung.md` | Báo cáo tổng hợp (tài liệu này) | Task 7 |

---

## PHỤ LỤC B — Thuật ngữ

| Thuật ngữ | Định nghĩa |
|---|---|
| **RASP** | Runtime Application Self-Protection — bảo vệ ứng dụng tại thời điểm chạy |
| **Post-build** | Tích hợp bảo vệ sau khi build xong, không thay đổi mã nguồn |
| **Shielder** | Công cụ CLI của Promon dùng để tích hợp SHIELD vào APK/AAB/IPA |
| **Blueprint JSON** | File cấu hình định nghĩa guards và phản ứng cho Shielder |
| **App Verify** | Module API Attestation của Promon SHIELD |
| **SLS** | Secure Local Storage — lưu trữ cục bộ an toàn với White-Box Crypto |
| **SAROM** | Static At-Rest Object Manager — mã hóa dữ liệu tĩnh AES-256 GCM |
| **Promon Insight** | Dashboard telemetry và tích hợp SIEM của Promon SHIELD |
| **EMVCo SMBP** | EMV Co. Software-Based Mobile Payments — chứng nhận thanh toán di động |
| **MASVS** | OWASP Mobile Application Security Verification Standard |
| **TCO** | Total Cost of Ownership — tổng chi phí sở hữu |
| **False positive** | Phát hiện nhầm người dùng hợp lệ là tấn công |
| **Certificate Pinning** | Kỹ thuật ràng buộc ứng dụng với chứng chỉ SSL cụ thể |
| **White-Box Crypto** | Mật mã học bảo vệ khóa ngay cả khi attacker có full memory access |
| **Device Binding** | Ràng buộc dữ liệu/ứng dụng với thiết bị cụ thể |

---

## PHỤ LỤC C — Thông tin Liên hệ

| Đơn vị | Liên hệ |
|---|---|
| **Promon AS (Na Uy)** | https://promon.co | sales@promon.co |
| **ANTT-TSC, VNPT Media** | Phòng An ninh Thông tin – Trung tâm Dịch vụ Kỹ thuật |

---

*Báo cáo này được lập dựa trên nghiên cứu tài liệu kỹ thuật của Promon AS, tài liệu công khai, và phân tích nội bộ của ANTT-TSC. Kết quả thực tế cần được xác nhận qua POC và thử nghiệm trực tiếp.*

*Phiên bản: 1.0 — Bản cuối | Ngày: 2025 | Tác giả: Phòng ANTT-TSC, VNPT Media*

*Tài liệu liên quan: Task 1–6 trong thư mục `research/`*
