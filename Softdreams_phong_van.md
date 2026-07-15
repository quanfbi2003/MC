# Chuẩn bị phỏng vấn Softdreams

Vị trí: **Senior Offensive Security Engineer**  
Ngày chuẩn bị: 16/07/2026

## 1. Softdreams đang làm gì?

Theo thông tin công khai, Softdreams là công ty công nghệ về tự động hóa quản trị doanh nghiệp, với hệ sinh thái sản phẩm liên quan đến kế toán, hóa đơn điện tử, thuế, chữ ký số và xác thực điện tử.

Các sản phẩm nên biết:

- **EasyInvoice**: hóa đơn điện tử.
- **EasyCA**: chữ ký số/chứng thư số.
- **EasyBooks**: phần mềm kế toán trực tuyến cho doanh nghiệp, hộ kinh doanh và đơn vị dịch vụ kế toán.
- **EasyPIT**: hỗ trợ khấu trừ thuế thu nhập cá nhân.
- **Easy SmartOTP**: xác thực OTP.
- **EasyKYC**: định danh/xác thực khách hàng.
- **EasyPOS**: giải pháp bán hàng, có thông tin công khai về tích hợp sản phẩm tài chính.
- EasyBooks công bố có các phân hệ kết nối thuế điện tử, hóa đơn điện tử và ngân hàng điện tử.

### Cách nói trong phỏng vấn

> Theo thông tin công khai, em thấy Softdreams có hệ sinh thái liên quan đến kế toán, hóa đơn điện tử, thuế, chữ ký số, OTP/KYC và kết nối ngân hàng. Em chưa có thông tin nội bộ về kiến trúc hoặc topology thực tế nên nếu được nhận, em muốn tìm hiểu rõ hơn phạm vi sản phẩm và các luồng dữ liệu quan trọng.

Không nên tự khẳng định Softdreams đang dùng cloud, HSM, SIEM, ngân hàng hoặc kiến trúc cụ thể nào nếu chưa được xác nhận.

## 2. Bề mặt tấn công cần hình dung

### EasyBooks/kế toán trực tuyến

- Multi-tenant và cô lập dữ liệu giữa các doanh nghiệp.
- Phân quyền owner, accountant, employee, auditor.
- IDOR/BOLA với invoice, customer, report, account.
- Sửa số tiền, thuế suất, trạng thái hóa đơn.
- Import/export Excel, XML, PDF.
- Upload file và xử lý dữ liệu hóa đơn.
- API kết nối thuế, ngân hàng, hóa đơn điện tử.
- Audit log và khả năng truy vết giao dịch.

### EasyInvoice/hóa đơn điện tử

- Phân biệt hóa đơn nháp, đã phát hành, đã ký và đã gửi cơ quan thuế.
- Không cho sửa hóa đơn sau khi ký/phát hành trái quy trình.
- Kiểm tra chữ ký và hash ở server, không chỉ ở client.
- Chống replay khi phát hành hóa đơn.
- Kiểm tra tenantId, invoiceId, customerId và quyền truy cập.
- Kiểm tra XML/PDF, schema và canonicalization.
- Xử lý timeout, retry và trạng thái không xác định khi gọi bên thứ ba.

### EasyCA/chữ ký số

- Khóa riêng được lưu và bảo vệ ở đâu?
- Có tách quyền tạo tài liệu và quyền ký không?
- Có step-up authentication trước khi ký không?
- Có chống replay yêu cầu ký không?
- Hash tài liệu có được kiểm tra lại trước khi ký không?
- Audit có ghi nhận ai ký, lúc nào, tài liệu nào, thiết bị/IP nào không?
- Xử lý chứng thư hết hạn hoặc bị thu hồi thế nào?

### Easy SmartOTP

- OTP có gắn với đúng user, session, thiết bị và transaction không?
- OTP của giao dịch A có dùng được cho giao dịch B không?
- Có thay đổi amount/beneficiary sau khi OTP được tạo không?
- Có bypass bước OTP bằng cách gọi API trực tiếp không?
- Có rate limit, giới hạn số lần thử, resend và expiry không?
- Có race condition khi verify OTP không?
- Luồng reset OTP/mất thiết bị có yếu hơn luồng đăng nhập không?

### EasyKYC

- Root/jailbreak, emulator, tamper, fake camera.
- SSL pinning và bảo vệ API.
- Replay ảnh/video hoặc request KYC.
- Device binding và risk signal.
- Một thiết bị tạo quá nhiều tài khoản.
- Một giấy tờ được dùng cho nhiều tài khoản.
- Server-side validation, monitoring và hậu kiểm.

## 3. Câu hỏi kỹ thuật có thể được hỏi

### Nếu được giao pentest EasyBooks, bạn bắt đầu thế nào?

> Em sẽ xác định scope, môi trường, tenant test, role, tài khoản và các hệ thống tích hợp. Sau đó em lập sơ đồ các luồng chính: đăng nhập, quản lý doanh nghiệp, tạo/sửa hóa đơn, ký số, phát hành hóa đơn, kết nối thuế, kết nối ngân hàng và export báo cáo.
>
> Em ưu tiên kiểm tra tenant isolation, authorization ở object và function level, IDOR/BOLA, session/token, sửa dữ liệu hóa đơn, upload/import file, API integration, replay, rate limit và audit log.
>
> Với hệ thống kế toán, em không chỉ kiểm tra OWASP Top 10 mà còn kiểm tra các invariant nghiệp vụ: user không được sửa hóa đơn đã ký, số tiền và thuế phải được server tính/kiểm tra lại, tài liệu đã ký phải giữ nguyên hash, tenant này không thể truy cập dữ liệu tenant khác.

### Kiểm tra phân quyền trong hệ thống multi-tenant thế nào?

> Em cần ít nhất hai tenant và nhiều role trong mỗi tenant. Em kiểm tra cả horizontal và vertical privilege escalation. Với từng object như invoice, customer, report hoặc file, em thay đổi ID trong URL, body, query, GraphQL hoặc API rồi kiểm tra server có xác minh tenant và quyền trên object hay không.
>
> UUID không phải biện pháp khắc phục đầy đủ. Server phải kiểm tra user hiện tại có quyền truy cập object đó và object có thuộc đúng tenant hay không.

### Kiểm tra chức năng phát hành hóa đơn điện tử thế nào?

> Em kiểm tra vòng đời hóa đơn: nháp, lập, ký, phát hành, gửi cơ quan thuế, điều chỉnh, thay thế và hủy. Em tập trung vào sửa amount, thuế suất, mã số thuế, người mua, trạng thái hoặc người ký; gửi lại request; gọi API bỏ qua bước UI; phát hành trùng; và xử lý timeout/retry với bên thứ ba.
>
> Em cũng kiểm tra chữ ký có bao phủ toàn bộ dữ liệu quan trọng không, server có xác nhận lại hash/tài liệu trước khi ký không, và audit log có truy vết được ai thực hiện hành động nào không.

### Nếu client có thể sửa số tiền trước khi thanh toán?

> Đây là lỗi business logic nghiêm trọng. Em sẽ xác nhận trong môi trường được cấp phép, xác định phạm vi ảnh hưởng và chứng minh bằng request/response tối thiểu.
>
> Root cause thường là server tin amount từ client. Cách khắc phục là server lấy giá trị từ database hoặc nguồn nghiệp vụ tin cậy, kiểm tra amount, fee, invoice, merchant, account và transaction ID trước khi xử lý. Giao dịch quan trọng cần integrity check phù hợp và chống replay.

### Kiểm tra OTP thế nào?

> Em kiểm tra OTP có gắn với đúng user, session, thiết bị và transaction hay không; có thể dùng OTP của giao dịch A cho giao dịch B không; có thể đổi amount hoặc beneficiary sau khi OTP được tạo không; có bypass được bước OTP bằng API trực tiếp không; và có race condition khi verify không.
>
> Ngoài ra em kiểm tra rate limit, số lần thử, resend, expiry, logout/login lại, reset thiết bị và khôi phục tài khoản.

### Nếu dev nói security review làm chậm tiến độ?

> Em sẽ phân loại theo rủi ro. Nếu lỗi ảnh hưởng đến khóa ký, xác thực, phân quyền, dữ liệu tenant hoặc tính toàn vẹn hóa đơn thì em đề xuất không go-live khi chưa có fix hoặc mitigation phù hợp.
>
> Với lỗi rủi ro thấp hơn, có thể đưa vào phase sau nhưng phải có owner, deadline, biện pháp tạm thời và người có thẩm quyền chấp nhận rủi ro. Tất cả cần được ghi nhận chính thức, theo dõi và retest.

### Đưa security vào Secure SDLC thế nào?

> Em đưa security vào từ requirement và design, nhất là các luồng chứa dữ liệu tài chính, dữ liệu cá nhân, chữ ký số và xác thực. Ở design, em threat model và review trust boundary. Trong coding/CI-CD, áp dụng SAST, SCA, secret scanning và kiểm soát dependency. Trước release, thực hiện DAST, API testing, manual business logic testing và kiểm tra cấu hình. Sau release, quản lý vulnerability, retest, monitoring và review định kỳ.
>
> Với mô hình tribe/squad, nên có security champion hoặc đầu mối ở từng squad, security gate rõ ràng và quy trình risk acceptance minh bạch.

## 4. Câu hỏi về hạ tầng có thể xuất hiện

Nguồn tuyển dụng hạ tầng công khai đề cập đến firewall, switch, server, VMware, Windows/Linux và log tập trung. Điều này không chứng minh vị trí của bạn chắc chắn làm các công nghệ đó, nhưng nên ôn để phối hợp với Infrastructure/SOC.

Có thể hỏi:

- Khi pentest web/API sau WAF, xác định scope và đánh giá WAF thế nào?
- Phân biệt lỗi ứng dụng và lỗi cấu hình hạ tầng ra sao?
- Kiểm tra network segmentation thế nào?
- Đánh giá server cấu hình yếu theo exposure, exploitability và impact ra sao?
- Log nào cần có để điều tra brute force, privileged activity hoặc config change?
- Khi test có thể tạo cảnh báo hoặc ảnh hưởng production, phối hợp với SOC/Infrastructure thế nào?

Câu trả lời nên dùng:

> Trước khi test, em thống nhất scope, thời gian, nguồn IP, loại payload và cơ chế liên lạc khi có cảnh báo. Em không tự ý bypass hoặc tạo tải lớn trên production. Với finding hạ tầng, em kết hợp khả năng khai thác, mức exposure, quyền cần có và impact để xếp hạng, sau đó phối hợp Infra/SOC remediation và retest.

## 5. Câu hỏi nên hỏi ngược

Chọn khoảng 3-4 câu:

1. Vị trí Senior Offensive Security Engineer tập trung nhiều hơn vào pentest sản phẩm, Secure SDLC, vulnerability management hay red team nội bộ?
2. Phạm vi sản phẩm chính là EasyBooks/EasyInvoice hay sẽ làm xuyên các sản phẩm như EasyCA, OTP, KYC và POS?
3. Security Engineer được tham gia từ requirement/design hay chủ yếu nhận build để pentest trước go-live?
4. Khi có finding High/Critical, ai sở hữu remediation và ai có thẩm quyền risk acceptance?
5. Team đã có security gate trong CI/CD chưa? Đang dùng SAST, SCA, DAST, secret scanning hoặc công cụ nào?
6. Các sản phẩm vận hành theo cloud, on-premise hay hybrid?
7. Với hệ thống nhiều doanh nghiệp dùng chung, team kiểm soát tenant isolation và quyền dữ liệu thế nào?
8. Với chữ ký số, OTP và KYC, security có tham gia review quản lý khóa, chống replay và bảo vệ dữ liệu cá nhân không?
9. Offensive Security phối hợp với Infrastructure/SOC thế nào khi pentest tạo cảnh báo hoặc ảnh hưởng hệ thống?
10. Trong 90 ngày đầu, anh/chị kỳ vọng người ở vị trí này tạo ra kết quả cụ thể nào?

## 6. Giới thiệu bản thân phù hợp với Softdreams

> Em có hơn 5 năm kinh nghiệm về pentest và application security, trong đó làm nhiều với web/API/mobile và các hệ thống tài chính số như ví điện tử, thanh toán, tích hợp ngân hàng và eKYC.
>
> Điểm mạnh của em là kiểm thử business logic, phân quyền, API và các luồng giao dịch; bên cạnh đó em có kinh nghiệm với SAST/DAST/SCA, lọc false positive, đánh giá CVE, hardening và phối hợp với dev để remediation.
>
> Khi tìm hiểu Softdreams, em thấy công ty có hệ sinh thái liên quan đến kế toán, hóa đơn điện tử, thuế, chữ ký số, OTP/KYC và kết nối ngân hàng. Đây là những hệ thống có yêu cầu cao về toàn vẹn dữ liệu, phân quyền, xác thực và bảo vệ dữ liệu cá nhân, khá phù hợp với kinh nghiệm của em.
>
> Em muốn phát triển theo hướng offensive security nhưng gắn với Secure SDLC và giảm rủi ro thực tế cho sản phẩm, không chỉ dừng ở việc tìm và báo cáo lỗ hổng.

## 7. Câu chốt tạo ấn tượng

> Với các sản phẩm như kế toán, hóa đơn điện tử và chữ ký số, em nghĩ rủi ro quan trọng không chỉ là OWASP Top 10 mà còn là việc một user có thể tác động sai đến dữ liệu hoặc trạng thái nghiệp vụ. Vì vậy khi pentest, em luôn muốn hiểu transaction end-to-end và kiểm tra cả tính toàn vẹn, quyền hạn, auditability và khả năng đối soát.

## 8. Thứ tự ôn tối nay

1. Multi-tenant authorization, IDOR/BOLA.
2. Business logic của hóa đơn và thanh toán.
3. Chữ ký số, bảo vệ khóa và chống replay.
4. OTP/MFA và transaction binding.
5. KYC, fake camera, device risk.
6. Secure SDLC trong mô hình tribe/squad.
7. SAST/SCA/DAST, secret scanning và vulnerability triage.
8. Threat modeling API và third-party integration.

## Nguồn tham khảo

- [Softdreams](https://softdreams.vn/)
- [Sản phẩm Softdreams](https://softdreams.vn/san-pham)
- [EasyBooks](https://easybooks.vn/)
- [Phân hệ thuế điện tử trên EasyBooks](https://easybooks.vn/phan-he-thue-dien-tu-tren-easybooks)
- [Phân hệ ngân hàng điện tử trên EasyBooks](https://easybooks.vn/phan-he-ngan-hang-dien-tu-tren-easybooks)
- [Tuyển dụng Senior Security Engineer](https://softdreams.vn/tuyen-dung/senior-security-engineer/)
- [Tuyển dụng Infrastructure Security Engineer](https://softdreams.vn/tuyen-dung/infrastructure-security-engineer-security-engineer-middle/)
- [Chính sách bảo mật Softdreams](https://softdreams.vn/chinh-sach-bao-mat)

> Lưu ý: Website sản phẩm và tin tuyển dụng là nguồn Softdreams tự công bố. Chúng xác nhận phạm vi sản phẩm và yêu cầu vai trò, nhưng không đủ để kết luận kiến trúc nội bộ, công nghệ đang dùng hoặc mức độ triển khai thực tế. Khi phỏng vấn nên dùng cụm “theo thông tin công khai”, “em hình dung có thể có bề mặt rủi ro…” rồi hỏi lại để xác minh.
