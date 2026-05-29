# Phân tích Ưu điểm và Nhược điểm của Promon SHIELD

**Dự án:** Nghiên cứu & Đánh giá Giải pháp Bảo mật Ứng dụng Di động Promon SHIELD  
**Đơn vị thực hiện:** Phòng An ninh Thông tin – TSC (ANTT-TSC), VNPT Media  
**Phiên bản tài liệu:** 1.0  
**Ngày lập:** 2025  
**Yêu cầu liên quan:** Yêu cầu 2  
**Phụ thuộc:** Task 1 — Tài liệu tổng quan kiến trúc và tính năng (`01-tong-quan-kien-truc-tinh-nang.md`)

---

## Mục lục

1. [Ưu điểm kỹ thuật](#1-ưu-điểm-kỹ-thuật)
2. [Nhược điểm và Rủi ro](#2-nhược-điểm-và-rủi-ro)
3. [Tác động đến kích thước APK/IPA](#3-tác-động-đến-kích-thước-apkipa)
4. [Tác động đến thời gian khởi động](#4-tác-động-đến-thời-gian-khởi-động)
5. [Yêu cầu môi trường và cấu hình hệ thống](#5-yêu-cầu-môi-trường-và-cấu-hình-hệ-thống)
6. [Phân tích mô hình Licensing](#6-phân-tích-mô-hình-licensing)
7. [Đánh giá tổng thể](#7-đánh-giá-tổng-thể)
8. [Kết luận và Khuyến nghị sơ bộ](#8-kết-luận-và-khuyến-nghị-sơ-bộ)

---

## 1. Ưu điểm Kỹ thuật

### 1.1 Tích hợp Post-Build — Không yêu cầu thay đổi mã nguồn

**Mô tả:** Promon SHIELD hoạt động theo mô hình post-build integration: công cụ Shielder CLI nhận đầu vào là file APK/AAB/IPA đã biên dịch và áp dụng bảo vệ mà không cần can thiệp vào mã nguồn gốc.

**Lợi ích kỹ thuật:**
- Đội phát triển không cần học API bảo mật mới hay thay đổi kiến trúc ứng dụng
- Có thể áp dụng cho ứng dụng legacy mà không cần refactor
- Giảm thiểu rủi ro lỗi do tích hợp sai (integration bugs)
- Tách biệt hoàn toàn giữa chu kỳ phát triển và chu kỳ bảo mật
- Cho phép áp dụng bảo vệ cho third-party SDK mà không có mã nguồn

**Ngoại lệ:** SLS (Secure Local Storage) và App Verify yêu cầu tích hợp API nhỏ vào mã nguồn, nhưng đây là các module tùy chọn.

**Điểm đánh giá: 9/10** — Đây là ưu điểm cạnh tranh lớn nhất so với các giải pháp yêu cầu tích hợp SDK sâu.

---

### 1.2 RASP Runtime — Bảo vệ Chủ động tại Thời điểm Chạy

**Mô tả:** SHIELD Core RASP nhúng các "guard" vào binary ứng dụng, chạy liên tục trong suốt vòng đời ứng dụng để phát hiện và phản ứng với tấn công theo thời gian thực.

**Lợi ích kỹ thuật:**
- Bảo vệ ngay cả khi thiết bị đã bị compromise (root, jailbreak)
- Phát hiện tấn công động (Frida, debugger) mà static analysis không thể ngăn chặn
- Phản ứng linh hoạt: ExitOn, Callback API, ExitOnURL — cho phép UX thân thiện
- Không phụ thuộc vào bảo mật của hệ điều hành hay thiết bị
- Bảo vệ theo nguyên tắc "defense in depth" — nhiều lớp kiểm tra độc lập

**Phạm vi phát hiện:** Root/Jailbreak, Emulator, Debugger, Frida/Xposed, Repackaging, Screen Mirroring, Accessibility Abuse, Overlay Attack, Keylogger, ADB, Developer Options.

**Điểm đánh giá: 9/10** — Phạm vi bảo vệ runtime rộng, bao phủ 8/10 tiêu chí Checklist_VNPT.

---

### 1.3 Hỗ trợ CI/CD Pipeline — Tự động hóa Quy trình Bảo mật

**Mô tả:** Shielder tool là CLI thuần túy, hỗ trợ non-interactive mode và Docker, cho phép tích hợp liền mạch vào pipeline CI/CD hiện có.

**Lợi ích kỹ thuật:**
- Tích hợp với Jenkins, GitLab CI, GitHub Actions, Azure DevOps, CircleCI
- Cấu hình blueprint JSON/XML có thể version-control cùng mã nguồn
- Hỗ trợ Docker container — đảm bảo môi trường build nhất quán
- Shielding tự động sau mỗi build thành công — không cần can thiệp thủ công
- Có thể cấu hình khác nhau cho môi trường dev/staging/production

**Điểm đánh giá: 8/10** — Tích hợp CI/CD tốt, nhưng cần thêm bước ký lại (re-sign) sau shielding, làm phức tạp pipeline một chút.

---

### 1.4 Bảo vệ Đa lớp (Multi-Layer Protection)

**Mô tả:** Promon SHIELD kết hợp bảo vệ tĩnh (at-rest) và động (runtime) trong một giải pháp tích hợp, tạo ra nhiều lớp phòng thủ độc lập.

| Lớp bảo vệ | Module | Loại |
|---|---|---|
| Obfuscation mã nhị phân | Code Protection | Static |
| Mã hóa dữ liệu nhúng | SAROM (AES-256 GCM) | Static |
| Lưu trữ an toàn | SLS (White-Box Crypto) | Dynamic |
| Bảo vệ runtime | SHIELD Core RASP | Dynamic |
| Xác thực API | App Verify | Dynamic |
| Telemetry & Giám sát | Promon Insight | Monitoring |

**Lợi ích:** Attacker phải vượt qua nhiều lớp bảo vệ độc lập — tăng đáng kể chi phí tấn công. Ngay cả khi một lớp bị bypass, các lớp còn lại vẫn hoạt động.

**Điểm đánh giá: 9/10** — Kiến trúc defense-in-depth vượt trội so với giải pháp đơn lớp.

---

### 1.5 Hỗ trợ Đa nền tảng và Đa framework

**Mô tả:** Promon SHIELD hỗ trợ đồng thời Android và iOS, cùng với các framework cross-platform phổ biến.

**Nền tảng hỗ trợ:**
- Android 5.0+ (API 21+): APK, AAB; kiến trúc ARM, ARM64, x86, x86_64
- iOS 12+: IPA; Swift, Objective-C

**Framework hỗ trợ:** Native Java/Kotlin, Native Swift/Obj-C, React Native, Flutter, Cordova, Ionic, Xamarin, C/C++

**Lợi ích cho VNPT Media:**
- MyVNPT và DigiShop có thể được bảo vệ bằng cùng một giải pháp
- Không cần mua riêng giải pháp cho Android và iOS
- Hỗ trợ React Native/Flutter nếu VNPT Media chuyển sang cross-platform trong tương lai

**Điểm đánh giá: 8/10** — Phủ rộng, nhưng cần xác nhận thực tế với framework cụ thể của MyVNPT/DigiShop.

---

### 1.6 Chứng nhận Tuân thủ Quốc tế

**Mô tả:** Promon AS đã đạt các chứng nhận bảo mật uy tín, đặc biệt quan trọng cho ứng dụng tài chính.

| Chứng nhận | Ý nghĩa | Liên quan đến VNPT Media |
|---|---|---|
| ISO 27001 | ISMS đạt chuẩn quốc tế | Giảm rủi ro supply chain |
| SOC 2 | Kiểm toán độc lập về bảo mật | Tin cậy cho doanh nghiệp |
| EMVCo SMBP 2025 | Tiêu chuẩn thanh toán di động | Quan trọng cho DigiShop |
| EU DORA | Khả năng phục hồi kỹ thuật số | Thể hiện mức độ nghiêm túc |
| Gartner Hype Cycle 2025 | Được công nhận bởi Gartner | Uy tín thị trường |

**Điểm đánh giá: 8/10** — Chứng nhận EMVCo SMBP đặc biệt có giá trị cho ứng dụng thanh toán DigiShop.

---

### 1.7 Telemetry và Khả năng Giám sát (Promon Insight)

**Mô tả:** Promon Insight cung cấp khả năng quan sát (observability) toàn diện về các mối đe dọa bảo mật trên mobile estate.

**Lợi ích kỹ thuật:**
- Thu thập sự kiện bảo mật có timestamp và severity tag từ thiết bị thực
- Xuất dữ liệu JSON/Protobuf — tích hợp với SIEM hiện có của VNPT Media
- Hỗ trợ on-premise deployment — dữ liệu không rời khỏi hạ tầng VNPT Media
- Phát hiện tín hiệu gian lận ẩn (rooting, emulation, hooking) để chuyển tiếp đến hệ thống fraud detection
- Không yêu cầu SDK bổ sung — Insight được nhúng sẵn trong Shield

**Điểm đánh giá: 7/10** — Tính năng tốt, nhưng cần đánh giá thêm về yêu cầu hạ tầng on-premise.

---

### 1.8 Bảo vệ Dữ liệu Nhạy cảm (SAROM + SLS)

**Mô tả:** Hai module SAROM và SLS giải quyết vấn đề bảo vệ dữ liệu nhạy cảm ở cả hai trạng thái: tĩnh (nhúng trong APK) và động (lưu trên thiết bị).

**SAROM — Bảo vệ dữ liệu tĩnh:**
- Tự động mã hóa AES-256 GCM trong quá trình shielding
- Không cần thay đổi mã nguồn
- Ngăn trích xuất API keys, certificate pins, config secrets bằng jadx/apktool

**SLS — Bảo vệ dữ liệu động:**
- White-Box Cryptography: khóa được bảo vệ ngay cả khi attacker có full memory access
- Device Binding: dữ liệu không thể đọc khi copy sang thiết bị khác
- Bảo vệ session tokens, PII, credentials ngay cả trên thiết bị đã root

**Điểm đánh giá: 8/10** — Giải pháp toàn diện cho bảo vệ dữ liệu, đặc biệt SLS vượt trội so với Android Keystore trên thiết bị root.

---

### 1.9 Xác thực Tính toàn vẹn API (App Verify)

**Mô tả:** App Verify (Promon Verify™) cho phép backend phân biệt request từ ứng dụng gốc với request từ công cụ tấn công.

**Lợi ích:**
- Ngăn API scraping và bot automation
- Phát hiện ứng dụng bị repackage đang cố gọi API
- Cryptographic attestation — không thể giả mạo token
- Bảo vệ API thanh toán của DigiShop khỏi request giả mạo

**Điểm đánh giá: 7/10** — Hiệu quả cao nhưng yêu cầu thay đổi cả phía app và backend.

---

### 1.10 Hỗ trợ Kỹ thuật và Cập nhật Bảo mật

**Mô tả:** Promon AS cung cấp hỗ trợ kỹ thuật chuyên sâu và cập nhật thường xuyên để đối phó với các kỹ thuật tấn công mới.

**Lợi ích:**
- Cập nhật signature phát hiện hooking framework mới (Frida versions, LSPosed variants)
- Hỗ trợ Android/iOS version mới nhanh chóng (ví dụ: Android 15 với 16KB page size)
- Đội ngũ nghiên cứu bảo mật chuyên sâu — phát hiện và vá lỗ hổng mới
- Tài liệu kỹ thuật đầy đủ và hỗ trợ tích hợp

**Điểm đánh giá: 7/10** — Phụ thuộc vào chất lượng hỗ trợ thực tế và SLA được cam kết trong hợp đồng.

---

### Tóm tắt Ưu điểm

| # | Ưu điểm | Điểm (1-10) | Mức độ quan trọng |
|---|---|---|---|
| 1 | Post-build integration, không thay đổi mã nguồn | 9 | ⭐⭐⭐ Rất cao |
| 2 | RASP runtime bảo vệ chủ động | 9 | ⭐⭐⭐ Rất cao |
| 3 | Hỗ trợ CI/CD pipeline | 8 | ⭐⭐⭐ Rất cao |
| 4 | Bảo vệ đa lớp (defense-in-depth) | 9 | ⭐⭐⭐ Rất cao |
| 5 | Đa nền tảng và đa framework | 8 | ⭐⭐ Cao |
| 6 | Chứng nhận tuân thủ quốc tế | 8 | ⭐⭐ Cao |
| 7 | Telemetry và giám sát (Insight) | 7 | ⭐⭐ Cao |
| 8 | Bảo vệ dữ liệu nhạy cảm (SAROM + SLS) | 8 | ⭐⭐⭐ Rất cao |
| 9 | Xác thực tính toàn vẹn API (App Verify) | 7 | ⭐⭐ Cao |
| 10 | Hỗ trợ kỹ thuật và cập nhật bảo mật | 7 | ⭐⭐ Cao |

---

## 2. Nhược điểm và Rủi ro

### 2.1 Chi phí Bản quyền — Không Minh bạch và Có thể Cao

**Mô tả:** Promon SHIELD là giải pháp thương mại với mô hình licensing không công khai giá. Chi phí được thương lượng trực tiếp với nhà cung cấp và phụ thuộc vào nhiều yếu tố.

**Rủi ro cụ thể:**
- Không có bảng giá công khai — khó lập ngân sách trước khi đàm phán
- Chi phí có thể tăng đáng kể khi số lượng ứng dụng hoặc người dùng tăng
- Phí gia hạn hàng năm có thể tăng sau khi đã phụ thuộc vào giải pháp
- Chi phí ẩn: phí hỗ trợ kỹ thuật, phí cập nhật major version, phí training
- Tỷ giá ngoại tệ (USD/EUR) ảnh hưởng đến chi phí thực tế tại Việt Nam

**Ước tính chi phí (dựa trên thông tin thị trường):**
- Giải pháp tương đương (DexGuard, Digital.ai): $15,000–$80,000/năm/ứng dụng
- Promon SHIELD: ước tính $20,000–$100,000+/năm tùy quy mô và tính năng
- *Lưu ý: Đây là ước tính thị trường, cần xác nhận qua báo giá chính thức từ Promon AS*

**Điểm rủi ro: 7/10** — Chi phí là rào cản lớn nhất cho quyết định triển khai.

---

### 2.2 Vendor Lock-in — Phụ thuộc Nhà cung cấp

**Mô tả:** Sau khi triển khai Promon SHIELD, tổ chức sẽ phụ thuộc sâu vào Promon AS cho các cập nhật bảo mật, hỗ trợ kỹ thuật và khả năng tương thích với OS mới.

**Rủi ro cụ thể:**
- **Phụ thuộc cập nhật:** Khi Android/iOS ra phiên bản mới, cần chờ Promon cập nhật Shielder tương thích
- **Phụ thuộc signature:** Các signature phát hiện Frida/Xposed mới phải được Promon cung cấp
- **Khó chuyển đổi:** Sau khi tích hợp SLS và App Verify vào mã nguồn, việc chuyển sang giải pháp khác đòi hỏi refactor đáng kể
- **Rủi ro kinh doanh:** Nếu Promon AS ngừng hoạt động hoặc bị mua lại, tương lai sản phẩm không chắc chắn
- **Phụ thuộc hạ tầng:** Promon Insight (cloud mode) phụ thuộc vào hạ tầng của Promon

**Biện pháp giảm thiểu:**
- Đàm phán điều khoản escrow mã nguồn trong hợp đồng
- Yêu cầu SLA rõ ràng về thời gian hỗ trợ OS mới
- Hạn chế tích hợp SLS/App Verify ở mức tối thiểu cần thiết

**Điểm rủi ro: 7/10** — Rủi ro trung bình-cao, cần điều khoản hợp đồng chặt chẽ.

---

### 2.3 Tác động Hiệu năng — Overhead Runtime

**Mô tả:** Mặc dù Promon tuyên bố "no performance loss" cho Code Protection, SHIELD Core RASP chạy các guard liên tục trong runtime, tạo ra overhead nhất định.

**Rủi ro cụ thể:**
- **CPU overhead:** Các guard kiểm tra môi trường định kỳ tiêu thụ CPU cycles
- **Memory overhead:** RASP engine và các guard chiếm thêm RAM
- **Startup time:** Quá trình khởi tạo RASP engine tăng thời gian cold start
- **Battery drain:** Kiểm tra liên tục có thể ảnh hưởng đến thời lượng pin trên thiết bị cũ
- **Thiết bị low-end:** Tác động hiệu năng rõ rệt hơn trên thiết bị Android cấu hình thấp (phổ biến tại Việt Nam)

**Dữ liệu benchmark (từ nghiên cứu độc lập và tài liệu nhà cung cấp):**
- Promon tuyên bố: "zero latency overhead" cho Code Protection (static obfuscation)
- RASP runtime overhead: ước tính 1–5% CPU tùy mức độ bảo vệ được bật
- Startup time tăng: ước tính 100–500ms tùy kích thước app và số guard được bật
- *Kết quả thực tế sẽ được đo trong Task 6 (thử nghiệm thực tế)*

**Điểm rủi ro: 5/10** — Overhead thường chấp nhận được, nhưng cần đo thực tế trên thiết bị target.

---

### 2.4 Độ phức tạp Tích hợp — Quy trình Build Phức tạp hơn

**Mô tả:** Việc thêm bước shielding vào quy trình build làm tăng độ phức tạp của pipeline CI/CD và yêu cầu kiến thức kỹ thuật mới.

**Rủi ro cụ thể:**
- **Bước ký lại (re-sign):** Sau shielding, APK/IPA phải được ký lại — cần quản lý keystore/certificate cẩn thận
- **Cấu hình blueprint:** File JSON/XML cấu hình phức tạp, dễ cấu hình sai dẫn đến false positive hoặc bảo vệ không đầy đủ
- **Debug khó khăn:** Ứng dụng đã shield khó debug hơn — cần cấu hình riêng cho môi trường dev
- **Thời gian build tăng:** Bước shielding thêm 2–10 phút vào pipeline tùy kích thước app
- **Tương thích thư viện:** Một số third-party SDK có thể xung đột với SHIELD guards
- **Yêu cầu macOS cho iOS:** Shielding iOS bắt buộc phải chạy trên macOS — hạn chế nếu CI/CD chạy trên Linux

**Điểm rủi ro: 6/10** — Độ phức tạp tăng đáng kể, cần đào tạo đội DevOps.

---

### 2.5 Khả năng Bypass — Không có Bảo vệ Tuyệt đối

**Mô tả:** Nghiên cứu học thuật đã chứng minh rằng các giải pháp RASP, bao gồm Promon SHIELD, có thể bị bypass bởi attacker có kỹ năng cao.

**Bằng chứng từ nghiên cứu:**
- Bài báo "Honey, I Shrunk Your App Security" (ResearchGate, 2018) mô tả hai kỹ thuật tấn công Promon Shield:
  1. **Static removal:** Loại bỏ toàn bộ cơ chế bảo vệ khỏi APK bằng phân tích tĩnh
  2. **Dynamic disabling:** Vô hiệu hóa tất cả biện pháp bảo mật tại runtime
- Promon đã phản hồi và cập nhật bảo vệ sau nghiên cứu này
- Các kỹ thuật bypass mới liên tục được phát triển bởi cộng đồng security research

**Rủi ro cụ thể:**
- Attacker có kỹ năng cao (APT, nation-state) có thể bypass với đủ thời gian và nguồn lực
- Frida với cấu hình non-default có thể tránh được một số detection
- Magisk với các module ẩn root (Shamiko, Zygisk) có thể qua mặt root detection
- Bảo vệ là "security through obscurity" một phần — không phải bảo mật tuyệt đối

**Quan điểm thực tế:** Promon SHIELD tăng đáng kể chi phí tấn công, đủ để ngăn chặn attacker thông thường. Không có giải pháp nào bảo vệ tuyệt đối trước attacker có kỹ năng cao và đủ nguồn lực.

**Điểm rủi ro: 6/10** — Rủi ro thực tế ở mức trung bình; phù hợp với mô hình threat của VNPT Media.

---

### 2.6 Yêu cầu Thay đổi Backend cho App Verify

**Mô tả:** Module App Verify yêu cầu thay đổi đáng kể ở phía backend, không chỉ là tích hợp phía ứng dụng.

**Rủi ro cụ thể:**
- Backend phải tích hợp Promon Verify SDK — thêm dependency vào server
- Cần thêm middleware/interceptor để validate attestation token trên mọi API endpoint cần bảo vệ
- Tăng latency API do bước validation token (ước tính 10–50ms)
- Cần xử lý graceful degradation khi Promon Verify service không khả dụng
- Phụ thuộc vào Promon infrastructure cho token validation (nếu dùng cloud mode)

**Điểm rủi ro: 6/10** — Tác động đến backend team đáng kể, cần lập kế hoạch kỹ.

---

### 2.7 Giới hạn Bảo vệ Chống MiTM

**Mô tả:** App Verify cung cấp bảo vệ gián tiếp chống MiTM, không phải bảo vệ trực tiếp qua certificate pinning hay mTLS.

**Rủi ro cụ thể:**
- App Verify xác thực nguồn gốc request, không mã hóa kênh truyền thông
- Attacker có thể vẫn intercept traffic nếu cài được CA certificate giả vào thiết bị
- Không có cơ chế certificate pinning tích hợp sẵn
- Tiêu chí "Chống MiTM" trong Checklist_VNPT chỉ được đáp ứng gián tiếp

**Biện pháp bổ sung cần thiết:**
- Triển khai certificate pinning trong mã nguồn ứng dụng
- Xem xét mutual TLS (mTLS) cho các API nhạy cảm
- Kết hợp App Verify với Network Security Config (Android)

**Điểm rủi ro: 6/10** — Gap bảo mật quan trọng cần được bổ sung.

---

### Tóm tắt Nhược điểm và Rủi ro

| # | Nhược điểm/Rủi ro | Điểm rủi ro (1-10) | Mức độ ảnh hưởng |
|---|---|---|---|
| 1 | Chi phí bản quyền cao, không minh bạch | 7 | ⭐⭐⭐ Cao |
| 2 | Vendor lock-in — phụ thuộc nhà cung cấp | 7 | ⭐⭐⭐ Cao |
| 3 | Overhead hiệu năng runtime | 5 | ⭐⭐ Trung bình |
| 4 | Độ phức tạp tích hợp và quy trình build | 6 | ⭐⭐ Trung bình |
| 5 | Khả năng bypass bởi attacker kỹ năng cao | 6 | ⭐⭐ Trung bình |
| 6 | Yêu cầu thay đổi backend cho App Verify | 6 | ⭐⭐ Trung bình |
| 7 | Giới hạn bảo vệ chống MiTM | 6 | ⭐⭐ Trung bình |

---

## 3. Tác động đến Kích thước APK/IPA

### 3.1 Phương pháp đánh giá

Dữ liệu trong phần này được tổng hợp từ:
1. Tài liệu kỹ thuật của Promon AS
2. Benchmark công khai từ nghiên cứu học thuật và cộng đồng security
3. So sánh với các giải pháp tương đương (DexGuard, Digital.ai, OneSpan)
4. *Kết quả thực tế sẽ được đo trong Task 6 với memory-game.apk*

### 3.2 Các yếu tố ảnh hưởng đến kích thước APK/IPA

Khi Promon SHIELD được áp dụng, kích thước file tăng do các thành phần sau được thêm vào:

| Thành phần thêm vào | Ước tính kích thước | Ghi chú |
|---|---|---|
| SHIELD Core RASP engine (native libs) | 1.5–3.0 MB | Thêm .so files cho ARM, ARM64 |
| Guard code nhúng vào DEX | 0.3–0.8 MB | Tùy số guard được bật |
| Code Protection overhead | 0–20% kích thước DEX gốc | Obfuscation có thể tăng hoặc giảm |
| SAROM encrypted assets | ~0% overhead | Mã hóa không tăng kích thước đáng kể |
| SLS library (nếu dùng) | 0.2–0.5 MB | Thêm native library |
| App Verify SDK (nếu dùng) | 0.3–0.6 MB | Thêm native library |

### 3.3 Bảng tác động kích thước APK — Ước tính theo loại ứng dụng

| Loại ứng dụng | Kích thước gốc (MB) | Kích thước sau Shield (MB) | Tăng tuyệt đối (MB) | Tăng tương đối (%) |
|---|---|---|---|---|
| App nhỏ (simple utility) | 5–10 MB | 8–14 MB | 3–4 MB | 30–60% |
| App trung bình (banking/fintech) | 20–40 MB | 23–45 MB | 3–5 MB | 10–25% |
| App lớn (e-commerce, media) | 50–100 MB | 54–106 MB | 4–6 MB | 5–12% |
| App rất lớn (game, streaming) | 100–500 MB | 104–506 MB | 4–6 MB | 1–6% |

**Nhận xét:** Mức tăng tuyệt đối khá ổn định (3–6 MB) do phần lớn là RASP engine native libraries. Tỷ lệ phần trăm tăng cao hơn với app nhỏ nhưng ít ảnh hưởng thực tế hơn với app lớn.

### 3.4 So sánh với các giải pháp tương đương

| Giải pháp | Tăng kích thước APK (ước tính) | Nguồn |
|---|---|---|
| **Promon SHIELD** | **3–6 MB (5–25%)** | Ước tính từ tài liệu kỹ thuật |
| DexGuard (Guardsquare) | 2–5 MB (5–20%) | Tài liệu Guardsquare |
| Digital.ai App Protection | 3–7 MB (5–30%) | Tài liệu Digital.ai |
| OneSpan App Shielding | 2–5 MB (5–20%) | Tài liệu OneSpan |
| ProGuard/R8 (obfuscation only) | -10% đến +5% | Thường giảm kích thước |

### 3.5 Tác động đến iOS IPA

Đối với iOS, tác động tương tự nhưng có một số điểm khác biệt:
- IPA thường nhỏ hơn APK tương đương do nén tốt hơn
- Promon SHIELD thêm ~2–5 MB cho iOS IPA
- App Store có giới hạn tải qua cellular: 200 MB (iOS 13+) — ít ảnh hưởng với app thông thường
- Bitcode không còn được Apple hỗ trợ từ Xcode 14 — không ảnh hưởng đến shielding

### 3.6 Đánh giá tác động thực tế với MyVNPT và DigiShop

| Ứng dụng | Kích thước ước tính hiện tại | Kích thước sau Shield (ước tính) | Tăng | Đánh giá |
|---|---|---|---|---|
| MyVNPT Android | ~30–50 MB | ~34–56 MB | ~4–6 MB | ✅ Chấp nhận được |
| MyVNPT iOS | ~25–40 MB | ~28–45 MB | ~3–5 MB | ✅ Chấp nhận được |
| DigiShop Android | ~20–40 MB | ~24–46 MB | ~4–6 MB | ✅ Chấp nhận được |
| DigiShop iOS | ~15–35 MB | ~18–40 MB | ~3–5 MB | ✅ Chấp nhận được |

*Lưu ý: Kích thước thực tế cần được đo trong Task 6 với APK/IPA thực tế.*

**Kết luận:** Mức tăng kích thước 3–6 MB là chấp nhận được trong bối cảnh hiện đại. Người dùng với kết nối 4G/5G sẽ không nhận thấy sự khác biệt đáng kể.

---

## 4. Tác động đến Thời gian Khởi động Ứng dụng

### 4.1 Cơ chế ảnh hưởng đến startup time

Khi ứng dụng đã được shield khởi động, SHIELD Core RASP thực hiện các kiểm tra sau trước khi cho phép ứng dụng chạy bình thường:

```
[App Launch]
     │
     ▼
[RASP Engine Initialization]  ← ~50–200ms
     │
     ▼
[Environment Integrity Checks]  ← ~50–150ms
  • Root/Jailbreak detection
  • Emulator detection
  • Debugger detection
  • Signature verification
     │
     ▼
[App Code Execution Begins]
```

### 4.2 Ước tính tác động đến startup time

| Giai đoạn | Thời gian thêm vào (ước tính) | Ghi chú |
|---|---|---|
| RASP engine initialization | 50–200 ms | Tải native libraries, khởi tạo guards |
| Environment integrity checks | 50–150 ms | Tùy số guard được bật |
| Signature/checksum verification | 20–80 ms | Kiểm tra tính toàn vẹn APK |
| **Tổng overhead cold start** | **120–430 ms** | Lần đầu khởi động |
| **Overhead warm start** | **20–80 ms** | Khởi động lại (guards đã cache) |

### 4.3 So sánh với ngưỡng chấp nhận của người dùng

| Ngưỡng | Giá trị | Đánh giá |
|---|---|---|
| Người dùng không nhận thấy | < 100 ms | Overhead warm start: ✅ Đạt |
| Người dùng chấp nhận được | 100–300 ms | Overhead cold start nhẹ: ✅ Đạt |
| Người dùng bắt đầu khó chịu | 300–1000 ms | Overhead cold start nặng: ⚠️ Cần kiểm tra |
| Người dùng không chấp nhận | > 1000 ms | Không áp dụng với Promon SHIELD |

**Nhận xét:** Với cấu hình guard tiêu chuẩn, overhead cold start ước tính 120–430 ms là chấp nhận được. Tuy nhiên, trên thiết bị Android cấu hình thấp (RAM 2GB, CPU lõi tứ cũ), overhead có thể cao hơn.

### 4.4 Các yếu tố ảnh hưởng đến overhead thực tế

**Tăng overhead:**
- Bật nhiều guard đồng thời (root + emulator + debugger + hooking + repack + screen mirror + accessibility)
- Thiết bị cấu hình thấp (CPU chậm, RAM ít)
- App có nhiều native libraries (mỗi library cần kiểm tra riêng)
- Bật Code Protection với mức obfuscation cao

**Giảm overhead:**
- Tắt các guard không cần thiết (ví dụ: tắt emulator detection cho production)
- Sử dụng lazy initialization cho một số guard
- Tối ưu cấu hình blueprint JSON

### 4.5 Khuyến nghị cấu hình cho VNPT Media

Để cân bằng giữa bảo mật và hiệu năng, đề xuất cấu hình theo mức độ ưu tiên:

**Cấu hình tối thiểu (bắt buộc):**
- Root/Jailbreak Detection: BẬT (ExitOn)
- Repackaging Detection: BẬT (ExitOn)
- Debugger Detection: BẬT (ExitOn)
- Hooking Framework Detection: BẬT (ExitOn)

**Cấu hình khuyến nghị (thêm vào):**
- Emulator Detection: BẬT (ExitOn)
- Screen Mirroring Detection: BẬT (Callback)
- Accessibility Abuse Detection: BẬT (Callback)

**Cấu hình tùy chọn (đánh giá tác động trước khi bật):**
- ADB Detection: Callback (không ExitOn — ảnh hưởng developer testing)
- Developer Options Detection: Callback
- Virtual Space Detection: BẬT

*Kết quả thực tế sẽ được đo trong Task 6 với thiết bị thực tế.*

---

## 5. Yêu cầu Môi trường và Cấu hình Hệ thống

### 5.1 Yêu cầu cho Máy Build (Shielder Tool)

#### Android Shielding

| Thành phần | Yêu cầu tối thiểu | Khuyến nghị | Ghi chú |
|---|---|---|---|
| **Hệ điều hành** | Windows 10, Ubuntu 18.04+, macOS 10.15+ | Ubuntu 20.04 LTS | Linux khuyến nghị cho CI/CD |
| **Java (JDK)** | JDK 8 (Java SE 8) | JDK 11 hoặc 17 LTS | OpenJDK hoặc Oracle JDK |
| **Android SDK** | Android SDK Build Tools | Build Tools 30+ | Cần aapt, zipalign, apksigner |
| **RAM** | 4 GB | 8 GB+ | Shielding app lớn cần nhiều RAM |
| **Disk** | 2 GB trống | 10 GB+ | Cho workspace và temp files |
| **CPU** | Dual-core | Quad-core+ | Shielding song song nhiều app |
| **Signing Keystore** | Android keystore (.jks/.p12) | Keystore riêng cho production | Cần sau bước shielding |

#### iOS Shielding

| Thành phần | Yêu cầu tối thiểu | Khuyến nghị | Ghi chú |
|---|---|---|---|
| **Hệ điều hành** | **macOS 11 Big Sur+** | macOS 13 Ventura+ | **Bắt buộc macOS — không thể dùng Linux/Windows** |
| **Xcode** | Xcode 12+ | Xcode 14+ | Cần Xcode Command Line Tools |
| **Apple Developer Account** | Có | Có | Cần provisioning profile và certificate |
| **Signing Certificate** | iOS Distribution Certificate | Có | Cần sau bước shielding |

### 5.2 Yêu cầu cho CI/CD Pipeline

| Thành phần | Yêu cầu | Ghi chú |
|---|---|---|
| **CI/CD Platform** | Jenkins, GitLab CI, GitHub Actions, Azure DevOps | Bất kỳ platform hỗ trợ CLI |
| **Docker** | Docker 20.10+ (tùy chọn) | Khuyến nghị cho môi trường nhất quán |
| **Shielder License** | License key hợp lệ | Cần kết nối internet để validate (hoặc offline license) |
| **Secure Storage** | Vault, AWS Secrets Manager, hoặc tương đương | Lưu trữ keystore và license key an toàn |
| **Network** | Kết nối internet (cho license validation) | Có thể cấu hình offline mode |

### 5.3 Yêu cầu cho Promon Insight (On-Premise)

| Thành phần | Yêu cầu tối thiểu | Khuyến nghị |
|---|---|---|
| **Hệ điều hành** | Ubuntu 18.04+ hoặc CentOS 7+ | Ubuntu 20.04 LTS |
| **RAM** | 8 GB | 16 GB+ |
| **CPU** | 4 cores | 8 cores+ |
| **Disk** | 100 GB SSD | 500 GB+ SSD (tùy lượng telemetry) |
| **Database** | PostgreSQL 12+ hoặc tương đương | PostgreSQL 14+ |
| **Network** | Port 443 (HTTPS inbound từ mobile apps) | Firewall rules cụ thể |
| **Docker** | Docker + Docker Compose | Khuyến nghị cho deployment |

*Lưu ý: Yêu cầu chính xác cho Insight on-premise cần được xác nhận với Promon AS trong quá trình POC.*

### 5.4 Yêu cầu Đặc biệt và Lưu ý

#### Vấn đề ký lại ứng dụng (Re-signing)

Sau khi Shielder xử lý APK/IPA, chữ ký gốc bị vô hiệu hóa. Cần ký lại với:
- **Android:** Keystore (.jks hoặc .p12) + mật khẩu keystore + alias
- **iOS:** Distribution Certificate + Provisioning Profile

**Rủi ro:** Nếu keystore bị mất hoặc hết hạn, không thể phát hành bản cập nhật. Cần có quy trình backup keystore nghiêm ngặt.

#### Tương thích với Google Play App Signing

Nếu VNPT Media sử dụng Google Play App Signing (khuyến nghị), cần lưu ý:
- Upload key (dùng để ký APK trước khi upload) phải được dùng sau bước shielding
- App Signing key (Google quản lý) sẽ được Google áp dụng khi phân phối
- Quy trình: Build → Shield → Sign với Upload Key → Upload lên Google Play

#### Tương thích với ProGuard/R8

Promon SHIELD tương thích với ProGuard/R8, nhưng cần lưu ý:
- Chạy ProGuard/R8 trước, sau đó mới chạy Shielder
- Một số cấu hình ProGuard có thể xung đột với Code Protection của Promon
- Cần test kỹ sau khi kết hợp cả hai

### 5.5 Đánh giá Tính khả thi trong Môi trường VNPT Media

| Yêu cầu | Trạng thái ước tính | Ghi chú |
|---|---|---|
| Máy build Linux/Windows | ✅ Có sẵn | Hầu hết CI/CD của VNPT Media chạy Linux |
| macOS cho iOS shielding | ⚠️ Cần xác nhận | Cần ít nhất 1 macOS agent trong CI/CD |
| JDK 8/11 | ✅ Có sẵn | Tiêu chuẩn trong môi trường Java |
| Android SDK | ✅ Có sẵn | Đã có cho build Android |
| Keystore management | ⚠️ Cần cải thiện | Cần quy trình quản lý keystore an toàn |
| Hạ tầng cho Insight on-prem | ⚠️ Cần đánh giá | Cần server riêng với 8–16 GB RAM |
| Kết nối internet từ CI/CD | ✅ Có sẵn | Cần whitelist domain Promon |

---

## 6. Phân tích Mô hình Licensing

### 6.1 Tổng quan Mô hình Licensing của Promon SHIELD

Promon AS không công bố bảng giá công khai. Mô hình licensing được thương lượng trực tiếp và thường dựa trên các yếu tố sau:

```
Chi phí Promon SHIELD = f(
    số lượng ứng dụng,
    số lượng người dùng hoạt động,
    tính năng/module được bật,
    thời hạn hợp đồng,
    mức độ hỗ trợ kỹ thuật,
    khu vực địa lý
)
```

### 6.2 Các Mô hình Licensing Phổ biến trong Ngành

Dựa trên thông tin thị trường từ các giải pháp tương đương (DexGuard, Digital.ai, OneSpan):

#### Mô hình 1: Per-Application (Theo số ứng dụng)

| Số ứng dụng | Chi phí ước tính/năm (USD) | Ghi chú |
|---|---|---|
| 1 ứng dụng | $20,000–$50,000 | Phổ biến nhất cho SME |
| 2–5 ứng dụng | $35,000–$120,000 | Thường có discount bundle |
| 5–10 ứng dụng | $80,000–$200,000 | Enterprise tier |
| 10+ ứng dụng | Thương lượng | Enterprise unlimited |

**Phù hợp với VNPT Media:** Nếu chỉ bảo vệ MyVNPT và DigiShop (2 ứng dụng), mô hình này có thể phù hợp.

#### Mô hình 2: Per-User / Per-MAU (Theo số người dùng hoạt động hàng tháng)

| Số MAU (Monthly Active Users) | Chi phí ước tính/năm (USD) | Ghi chú |
|---|---|---|
| < 100,000 MAU | $15,000–$40,000 | Startup/SME tier |
| 100K–500K MAU | $30,000–$80,000 | Mid-market tier |
| 500K–2M MAU | $60,000–$150,000 | Enterprise tier |
| 2M+ MAU | Thương lượng | Large enterprise |

**Phù hợp với VNPT Media:** MyVNPT có thể có hàng triệu MAU — mô hình này có thể đắt hơn per-app.

#### Mô hình 3: Subscription (Theo thời hạn)

| Thời hạn | Discount ước tính | Ghi chú |
|---|---|---|
| 1 năm | 0% (base price) | Linh hoạt nhất |
| 2 năm | 10–15% | Cân bằng |
| 3 năm | 20–25% | Tiết kiệm nhất nhưng lock-in dài |

**Khuyến nghị:** Bắt đầu với hợp đồng 1 năm để đánh giá, sau đó ký dài hạn nếu hài lòng.

### 6.3 Cấu trúc Chi phí Tổng thể (TCO — Total Cost of Ownership)

| Hạng mục chi phí | Ước tính (USD/năm) | Ghi chú |
|---|---|---|
| **License fee** | $30,000–$100,000 | Tùy mô hình và quy mô |
| **Phí hỗ trợ kỹ thuật** | Thường bao gồm trong license | Cần xác nhận SLA |
| **Phí training** | $2,000–$5,000 (một lần) | Đào tạo đội DevOps/Security |
| **Chi phí tích hợp nội bộ** | $5,000–$15,000 (một lần) | Nhân công tích hợp CI/CD |
| **Hạ tầng Insight on-prem** | $3,000–$8,000/năm | Server, điện, bandwidth |
| **Chi phí vận hành** | $2,000–$5,000/năm | Quản lý, cập nhật, monitoring |
| **Tổng TCO năm đầu** | **$42,000–$133,000** | Bao gồm chi phí một lần |
| **Tổng TCO từ năm 2+** | **$35,000–$113,000/năm** | Không có chi phí một lần |

*Lưu ý: Đây là ước tính dựa trên thông tin thị trường. Cần yêu cầu báo giá chính thức từ Promon AS.*

### 6.4 So sánh Chi phí với Các Giải pháp Thay thế

| Giải pháp | Chi phí ước tính/năm (USD) | Mô hình |
|---|---|---|
| **Promon SHIELD** | **$30,000–$100,000** | Per-app hoặc per-user |
| DexGuard (Guardsquare) | $15,000–$60,000 | Per-app |
| Digital.ai App Protection | $25,000–$80,000 | Per-app |
| OneSpan App Shielding | $20,000–$70,000 | Per-app |
| ProGuard/R8 | $0 (open source) | N/A |
| RASP tự xây dựng | $50,000–$200,000 (phát triển) + $20,000–$50,000/năm (duy trì) | N/A |

### 6.5 Phân tích Chi phí-Lợi ích (Cost-Benefit Analysis)

#### Kịch bản 1: Chỉ bảo vệ MyVNPT và DigiShop

**Chi phí:** ~$40,000–$80,000/năm (ước tính 2 app)

**Lợi ích định lượng:**
- Giảm rủi ro vi phạm dữ liệu: Chi phí trung bình một vụ vi phạm dữ liệu tại Đông Nam Á: $2.5–5M
- Tránh phạt theo quy định bảo vệ dữ liệu (Nghị định 13/2023/NĐ-CP): Lên đến 5% doanh thu
- Tránh mất uy tín thương hiệu: Khó định lượng nhưng rất đáng kể

**Kết luận kịch bản 1:** ROI dương nếu ngăn chặn được ít nhất 1 vụ vi phạm dữ liệu nghiêm trọng.

#### Kịch bản 2: Mở rộng cho toàn bộ portfolio ứng dụng VNPT Media

**Chi phí:** ~$80,000–$200,000/năm (ước tính 5–10 app)

**Lợi ích:** Bảo vệ toàn diện, compliance với các quy định bảo mật, tăng điểm tin cậy với khách hàng doanh nghiệp.

### 6.6 Các Điều khoản Hợp đồng Cần Đàm phán

Khi đàm phán với Promon AS, VNPT Media cần yêu cầu rõ ràng:

1. **SLA hỗ trợ:** Thời gian phản hồi cho critical issues (< 4 giờ), major issues (< 24 giờ)
2. **Cam kết cập nhật OS:** Thời gian tối đa để hỗ trợ Android/iOS version mới sau khi phát hành
3. **Điều khoản escrow:** Quyền truy cập mã nguồn nếu Promon AS ngừng hoạt động
4. **Giới hạn tăng giá:** Không tăng quá X% mỗi năm khi gia hạn
5. **Điều khoản chấm dứt:** Quyền chấm dứt hợp đồng sớm và điều kiện hoàn tiền
6. **Quyền sở hữu dữ liệu:** Xác nhận VNPT Media sở hữu toàn bộ dữ liệu telemetry
7. **Audit rights:** Quyền kiểm tra bảo mật của Promon AS định kỳ

---

## 7. Đánh giá Tổng thể

### 7.1 Bảng điểm đánh giá theo từng khía cạnh

| Khía cạnh | Điểm (1-10) | Nhận xét |
|---|---|---|
| **Hiệu quả bảo vệ kỹ thuật** | **8/10** | Bao phủ 8/10 tiêu chí Checklist_VNPT; bảo vệ đa lớp toàn diện |
| **Dễ tích hợp** | **7/10** | Post-build tốt, nhưng re-sign và CI/CD setup cần effort |
| **Tác động hiệu năng** | **7/10** | Overhead chấp nhận được; cần đo thực tế |
| **Tác động kích thước app** | **7/10** | Tăng 3–6 MB — chấp nhận được với app hiện đại |
| **Chi phí / Giá trị** | **6/10** | Chi phí cao nhưng ROI dương nếu ngăn được vi phạm dữ liệu |
| **Vendor reliability** | **8/10** | Công ty uy tín, ISO 27001, SOC 2, 15+ năm kinh nghiệm |
| **Hỗ trợ kỹ thuật** | **7/10** | Cần xác nhận SLA thực tế qua POC |
| **Khả năng mở rộng** | **8/10** | Hỗ trợ đa nền tảng, đa framework, CI/CD tốt |
| **Tuân thủ quy định** | **9/10** | ISO 27001, SOC 2, EMVCo SMBP, EU DORA |
| **Rủi ro vendor lock-in** | **5/10** | Rủi ro trung bình-cao; cần điều khoản hợp đồng chặt |
| **Điểm tổng thể** | **7.2/10** | Giải pháp tốt, phù hợp với nhu cầu VNPT Media |

### 7.2 Ma trận Rủi ro — Lợi ích

```
                    LỢI ÍCH CAO
                         │
    Bảo vệ RASP runtime  │  Post-build integration
    Bảo vệ đa lớp        │  CI/CD support
    Chứng nhận tuân thủ  │  Đa nền tảng
                         │
RỦI RO THẤP ─────────────┼───────────────── RỦI RO CAO
                         │
    Tác động hiệu năng   │  Chi phí cao
    Tác động kích thước  │  Vendor lock-in
                         │  Khả năng bypass
                    LỢI ÍCH THẤP
```

### 7.3 Phân tích SWOT

#### Strengths (Điểm mạnh)
- Post-build integration không yêu cầu thay đổi mã nguồn
- RASP runtime bảo vệ chủ động, đa lớp
- Hỗ trợ CI/CD và đa nền tảng
- Chứng nhận quốc tế uy tín (ISO 27001, SOC 2, EMVCo SMBP)
- Telemetry và giám sát qua Promon Insight
- Bảo vệ dữ liệu nhạy cảm toàn diện (SAROM + SLS)

#### Weaknesses (Điểm yếu)
- Chi phí cao, không minh bạch
- Vendor lock-in sau khi tích hợp sâu
- Bảo vệ MiTM gián tiếp — cần bổ sung certificate pinning
- Yêu cầu macOS cho iOS shielding
- Overhead startup time trên thiết bị cấu hình thấp

#### Opportunities (Cơ hội)
- Nâng điểm Checklist_VNPT từ 2/10 lên 8–9/10
- Đáp ứng yêu cầu compliance ngày càng nghiêm ngặt
- Tăng uy tín với khách hàng doanh nghiệp và đối tác
- Nền tảng cho mở rộng bảo mật toàn bộ portfolio ứng dụng VNPT Media

#### Threats (Mối đe dọa)
- Kỹ thuật bypass mới liên tục được phát triển
- Phụ thuộc vào Promon AS cho cập nhật bảo mật
- Chi phí có thể tăng khi gia hạn
- Thay đổi chính sách của Google Play/App Store có thể ảnh hưởng đến shielding

---

## 8. Kết luận và Khuyến nghị Sơ bộ

### 8.1 Kết luận

Promon SHIELD là giải pháp bảo mật ứng dụng di động toàn diện với nhiều ưu điểm kỹ thuật nổi bật, đặc biệt là mô hình post-build integration và RASP runtime protection. Giải pháp có khả năng nâng điểm bảo mật của MyVNPT và DigiShop từ mức 2/10 và 1.5/10 lên 7–8/10 theo Checklist_VNPT.

**Điểm mạnh cốt lõi:**
- Không yêu cầu thay đổi mã nguồn — giảm thiểu tác động đến đội phát triển
- Bảo vệ runtime chủ động — phù hợp với mô hình threat của ứng dụng tài chính/viễn thông
- Chứng nhận EMVCo SMBP — đặc biệt có giá trị cho DigiShop

**Rủi ro chính cần quản lý:**
- Chi phí licensing cần được đàm phán kỹ và so sánh với các giải pháp thay thế
- Vendor lock-in cần được giảm thiểu qua điều khoản hợp đồng chặt chẽ
- Bảo vệ MiTM cần được bổ sung bằng certificate pinning

### 8.2 Khuyến nghị Sơ bộ

> **Khuyến nghị: Tiến hành POC (Proof of Concept) trước khi quyết định mua**

**Lý do:**
1. Chi phí đáng kể — cần xác nhận hiệu quả thực tế trước khi cam kết
2. Cần đo overhead thực tế trên thiết bị Android phổ biến tại Việt Nam
3. Cần xác nhận tương thích với framework và kiến trúc cụ thể của MyVNPT/DigiShop
4. Cần đàm phán và so sánh báo giá với ít nhất 2 giải pháp thay thế (DexGuard, Digital.ai)

**Các bước tiếp theo:**
1. ✅ **Task 3:** Xây dựng ma trận so sánh với DexGuard và Digital.ai
2. ✅ **Task 4:** Nghiên cứu quy trình tích hợp chi tiết
3. 🔄 **Task 5–12:** Thực hiện POC với memory-game.apk
4. 📋 **Sau POC:** Yêu cầu báo giá chính thức từ Promon AS và các đối thủ cạnh tranh

### 8.3 Câu hỏi cần xác nhận trong POC

| # | Câu hỏi | Phương pháp xác nhận |
|---|---|---|
| 1 | Overhead startup time thực tế trên thiết bị Android phổ biến tại VN? | Đo bằng adb shell am start -W |
| 2 | Kích thước APK tăng thực tế với memory-game.apk? | So sánh trước/sau shielding |
| 3 | Root detection có hoạt động với Magisk + Shamiko? | Test trên thiết bị root thực tế |
| 4 | Frida detection có hoạt động với Frida non-default port? | Test với Frida gadget |
| 5 | App Verify có block Burp Suite hoàn toàn không? | Test với Burp Suite proxy |
| 6 | Có xung đột với thư viện nào trong MyVNPT/DigiShop không? | Thử nghiệm tích hợp thực tế |

---

## Tài liệu Tham khảo

1. **01-tong-quan-kien-truc-tinh-nang.md** — Tài liệu Task 1, ANTT-TSC VNPT Media (2025)
2. **Promon SHIELD Introduction - 2020_NoDEMO.pdf** — Tài liệu giới thiệu Promon SHIELD, có sẵn trong workspace
3. **Promon_Intro_short_2026 (1).pdf** — Tài liệu giới thiệu ngắn Promon 2026, có sẵn trong workspace
4. **protect-android-4.11.0-userguide.pdf** — Hướng dẫn sử dụng Digital.ai App Protection for Android (tham khảo kỹ thuật), có sẵn trong workspace
5. **"Honey, I Shrunk Your App Security: The State of Android App Hardening"** — Nghiên cứu học thuật về RASP bypass, ResearchGate (2018). URL: https://www.researchgate.net/publication/325640295
6. **Promon Shield for Mobile™** — https://promon.io/software-security-solutions/app-shielding
7. **Promon Insight for App Visibility™** — https://promon.io/promon-insight-for-app-visibility
8. **Promon SHIELD 7.0 Release Notes** — https://promon.io/security-news/new-promon-shield-7.0-september-2024
9. **Promon AI-assisted vulnerability research response** — https://promon.io/security-news/ai-assisted-vulnerability-research-still-requires-responsible-disclosure
10. **Guardsquare DexGuard Factsheet** — https://www.guardsquare.com/factsheet-dexguard (tham khảo so sánh)
11. **Digital.ai Application Protection** — Tài liệu có sẵn trong thư mục `Digital.ai\` của workspace

---

*Tài liệu này được lập bởi Nhóm_Nghiên_Cứu thuộc ANTT-TSC, VNPT Media.*  
*Phiên bản: 1.0 | Ngày: 2025 | Trạng thái: Hoàn thành (Giai đoạn 1 - Task 2)*  
*Tài liệu tiếp theo: Task 3 — Ma trận so sánh với các giải pháp thay thế (`03-ma-tran-so-sanh.md`)*
