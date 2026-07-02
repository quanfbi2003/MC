# Chuẩn bị phỏng vấn BIDV

## 1. Đánh giá nhanh 3 bài phỏng vấn trước

### Điểm mạnh nên phát huy

Có kinh nghiệm rất phù hợp ngân hàng:

- VNPT Money / ví điện tử / thanh toán / tích hợp ngân hàng.
- eKYC, VNeID/C06, fake camera, root/jailbreak, Frida, Burp.
- Lỗi business logic thanh toán 0 đồng, duplicate URL parameter.
- SAST/DAST/SCA, Fortify, GitLab CI/CD.
- CVE, hardening, webshell/malware scanner.
- Training dev/vận hành, hỗ trợ PCI DSS/evidence.

Có mindset thực chiến:

- Không chỉ chạy tool.
- Biết lọc false positive.
- Biết phối hợp dev để fix.
- Biết ưu tiên theo mức độ ảnh hưởng nghiệp vụ.

### Điểm yếu cần sửa khi nói

#### Trả lời hơi dài, dễ lan man

Ngày mai nên trả lời theo khung:

> Bối cảnh → Cách làm → Kết quả/rủi ro → Bài học/khắc phục.

#### Đừng nói “em chưa rõ shift-left” nữa

Nói thẳng:

> “Shift-left security là đưa security vào sớm từ requirement/design/code review/CI-CD, thay vì chờ dev xong mới pentest.”

#### Đừng nhấn mạnh quá “em muốn Red Team”

Với BIDV, nên nói:

> “Định hướng của em vẫn là technical security, nhưng trong môi trường ngân hàng em muốn phát triển theo AppSec/Vulnerability Management/Secure SDLC trước, vì đây là phần tạo giá trị trực tiếp và bền vững cho tổ chức.”

#### Đừng nói “tự rà quét hệ thống bên ngoài” nếu không có ngữ cảnh rõ ràng

Câu này dễ bị hiểu sai. Nên đổi thành:

> “Ngoài công việc, em luyện lab/CTF, đọc CVE, dựng môi trường test nội bộ để rèn kỹ năng.”

#### Về compliance/policy, đừng nhận quá tay

Nên nói thật nhưng khéo:

> “Em chưa phải người ownership toàn bộ compliance framework, nhưng đã tham gia hỗ trợ evidence, review tiêu chuẩn, training dev và kiểm tra tuân thủ kỹ thuật. Nếu vào BIDV, em có thể học thêm framework nội bộ/NHNN và đóng góp từ góc nhìn AppSec thực tế.”

## 2. Màn giới thiệu bản thân nên nói ngày mai

Nên chuẩn bị bản 60-90 giây:

> Em là Phạm Ngọc Quân, hiện có gần 5 năm kinh nghiệm tại VNPT Media trong mảng pentest và application security. Công việc chính của em là tiếp nhận yêu cầu đánh giá, xác định phạm vi, lên kế hoạch, phân công thành viên, trực tiếp kiểm thử web/API/mobile và tổng hợp báo cáo cho dev/vận hành khắc phục.
>
> Các hệ thống em tham gia nhiều liên quan đến dịch vụ tài chính số như VNPT Money/VNPT Pay, tích hợp ngân hàng, thanh toán đối tác, eKYC/sinh trắc học và các hệ thống nội bộ. Ngoài pentest thủ công, em cũng tham gia SAST/DAST/SCA, review cảnh báo Fortify/GitLab CI-CD, đánh giá CVE, hardening server, training dev và hỗ trợ evidence cho các đợt đánh giá như PCI DSS.
>
> Điểm mạnh của em là kiểm thử business logic, mobile/API security và khả năng phối hợp với dev để xử lý lỗ hổng theo rủi ro thực tế. Em muốn chuyển sang môi trường ngân hàng như BIDV vì đây là nơi có hệ thống lớn, yêu cầu an toàn cao, nhiều bài toán về AppSec, quản lý lỗ hổng, tuân thủ và bảo vệ giao dịch tài chính.

## 3. Những câu BIDV có thể hỏi và cách trả lời

### Câu 1: Vì sao em muốn vào BIDV/ngân hàng?

Không nên nói:

> “Ai cũng muốn vào bank.”

Nên nói:

> Em muốn vào ngân hàng vì đây là môi trường có yêu cầu an toàn thông tin rất cao, hệ thống lớn, nhiều nghiệp vụ quan trọng và tác động trực tiếp đến khách hàng. Kinh nghiệm hiện tại của em đang liên quan nhiều đến ví điện tử, thanh toán, tích hợp ngân hàng, eKYC và mobile/API security nên em thấy khá phù hợp để phát triển tiếp trong môi trường BIDV.
>
> Em cũng muốn học thêm cách một ngân hàng lớn vận hành Secure SDLC, quản lý lỗ hổng, tuân thủ, kiểm toán và phối hợp giữa các khối công nghệ/risk/compliance.

### Câu 2: Em hiểu Secure SDLC là gì?

Trả lời gọn:

> Secure SDLC là đưa yêu cầu bảo mật vào toàn bộ vòng đời phát triển phần mềm, không chỉ pentest ở cuối. Theo em có thể chia thành các bước:
>
> 1. Requirement: xác định yêu cầu bảo mật, dữ liệu nhạy cảm, phân quyền, logging, compliance.
> 2. Design: threat modeling, review kiến trúc, luồng xác thực, phân quyền, luồng giao dịch.
> 3. Development: coding guideline, secret management, dependency management.
> 4. CI/CD: SAST, SCA, secret scan, kiểm soát build.
> 5. Testing: DAST, API test, mobile test, manual pentest, business logic test.
> 6. Release: risk acceptance, checklist go-live.
> 7. Operation: vulnerability management, monitoring, retest, đánh giá định kỳ.
>
> Kinh nghiệm của em trước đây là từ chỗ chủ yếu pentest cuối vòng đời, sau đó dần tham gia sớm hơn vào review yêu cầu, review thiết kế, CI/CD scanning và training dev.

### Câu 3: Shift-left security là gì?

Nên trả lời chắc:

> Shift-left security là dịch chuyển hoạt động bảo mật sang sớm hơn trong quy trình phát triển. Thay vì đợi dev xong rồi mới pentest, security tham gia từ requirement, design, coding và CI/CD.
>
> Ví dụ một tính năng thanh toán mới: ngay từ lúc review requirement, security cần hỏi các câu như: ai được thực hiện giao dịch, hạn mức thế nào, dữ liệu nào phải ký/hash, chống replay ra sao, log/audit thế nào, có thể spam/abuse không. Làm sớm như vậy giúp giảm chi phí sửa lỗi và tránh lỗi logic nghiêm trọng khi gần go-live.

### Câu 4: Quy trình pentest một ứng dụng web/API/mobile của em?

Trả lời theo quy trình:

> Thường em làm theo các bước:
>
> 1. Xác định scope, môi trường, tài khoản test, tài liệu nghiệp vụ/API.
> 2. Thu thập thông tin: endpoint, role, luồng nghiệp vụ, dữ liệu nhạy cảm.
> 3. Với web/API: kiểm tra authen/authz, IDOR, injection, upload, business logic, rate limit, session/token, logging.
> 4. Với mobile: kiểm tra root/jailbreak detection, SSL pinning, local storage, hardcoded secret, reverse app, API traffic.
> 5. Kiểm thử business logic thủ công vì tool thường không phát hiện tốt.
> 6. Ghi nhận bằng chứng, đánh giá impact, đề xuất fix cụ thể.
> 7. Trao đổi với dev, retest sau khi fix.
>
> Em thường ưu tiên các lỗi ảnh hưởng trực tiếp đến giao dịch, dữ liệu khách hàng, phân quyền và toàn vẹn dữ liệu.

### Câu 5: Kể một lỗi nghiêm trọng em từng tìm được

Nên chọn case duplicate URL parameter / thanh toán vé máy bay, vì rất hợp ngân hàng.

Trả lời:

> Một case em nhớ là luồng thanh toán/tích hợp đối tác. Hệ thống bên em chuyển tham số qua URL sang đối tác. Khi request có hai parameter trùng tên, hai hệ thống lại xử lý khác nhau: bên em kiểm tra theo một giá trị, còn đối tác xử lý theo giá trị còn lại.
>
> Em kiểm thử bằng cách thêm parameter trùng tên vào request để xem mỗi bên lấy giá trị nào. Kết quả là có thể tạo sai lệch giữa dữ liệu được kiểm tra và dữ liệu được xử lý ở phía đối tác, dẫn đến rủi ro giao dịch không đúng giá trị.
>
> Bài học là với hệ thống tài chính, không được trust dữ liệu chỉ vì đến từ đối tác hoặc hệ thống nội bộ. Cần chuẩn hóa canonical request, không cho phép duplicate parameter, ký đúng toàn bộ dữ liệu quan trọng, kiểm tra lại amount/order/user/service ở server và thống nhất cách parse giữa các hệ thống.

### Câu 6: Lỗi business logic khác em từng gặp?

Dùng case mua gói cước 0 đồng:

> Có case mua gói cước trên mobile. Người dùng bình thường không sửa được giá trên UI, nhưng khi em intercept request/luồng xử lý thì thấy trước bước thanh toán có tham số giá được app nhận và dùng tiếp. Khi sửa giá trị đó về 0, server vẫn xử lý theo giá client gửi lên.
>
> Root cause là server trust dữ liệu từ client. Cách fix là server phải tự lấy giá từ backend/config theo mã sản phẩm/gói cước, không dùng amount từ client làm nguồn tin cậy. Với giao dịch tài chính, các trường như amount, fee, account, merchant, orderId phải được kiểm tra toàn vẹn ở server.

### Câu 7: Em đánh giá eKYC/deepfake/fake camera thế nào?

Trả lời:

> Với eKYC, rủi ro không chỉ nằm ở thuật toán nhận diện mà còn ở môi trường thiết bị và luồng truyền dữ liệu. Nếu app bị bypass root/jailbreak, bị hook bằng Frida, bị fake camera/VCam hoặc intercept request thì attacker có thể đưa ảnh/video đã chuẩn bị sẵn vào luồng eKYC.
>
> Em thường kiểm tra các hướng:
>
> - App có phát hiện root/jailbreak/hooking/tamper không.
> - SSL pinning có bypass được không.
> - Có thể intercept hoặc sửa request eKYC không.
> - App có chống fake camera/mirror/emulator không.
> - Server có kiểm tra toàn vẹn dữ liệu, replay, device binding, risk signal không.
> - Có monitoring bất thường như nhiều tài khoản từ cùng device/IP/hành vi giống nhau không.
>
> Theo em, phòng thủ eKYC phải kết hợp cả app protection, server-side validation, risk monitoring và hậu kiểm.

### Câu 8: Em dùng SAST/DAST/SCA thế nào?

Trả lời:

> Với SAST, bên em dùng tool như Fortify trong pipeline để phát hiện lỗi source code. Tuy nhiên không phải cảnh báo nào cũng đúng, nên AppSec cần review false positive, xác định khả năng khai thác và hướng dẫn dev fix.
>
> Với SCA, em kiểm tra thư viện/phụ thuộc của bản build có CVE không. Nhưng không chỉ nhìn CVSS, mà phải đối chiếu version, component có được sử dụng thật không, có expose ra attack surface không, có điều kiện khai thác trong môi trường hiện tại không.
>
> Với DAST/manual pentest, em kiểm thử runtime, API và business logic. Theo em ba nhóm này bổ trợ nhau: SAST/SCA giúp phát hiện sớm, còn DAST/manual giúp xác nhận khả năng khai thác thực tế.

### Câu 9: Em xử lý CVE như thế nào?

Trả lời:

> Em sẽ xử lý theo hướng risk-based:
>
> 1. Xác định asset/service bị ảnh hưởng.
> 2. Xác định version thư viện/phần mềm.
> 3. Đối chiếu CVE, exploitability, điều kiện khai thác.
> 4. Kiểm tra service có expose internet/nội bộ, có quyền truy cập nào cần thiết không.
> 5. Ưu tiên xử lý theo impact: RCE, auth bypass, data leak, privilege escalation sẽ ưu tiên cao.
> 6. Đề xuất upgrade/patch/mitigation.
> 7. Retest hoặc verify sau khi xử lý.
>
> Em không chỉ nhìn điểm CVSS, vì trong thực tế có CVE điểm cao nhưng không khai thác được trong ngữ cảnh hệ thống, và cũng có lỗi điểm không quá cao nhưng ảnh hưởng nghiệp vụ rất lớn.

### Câu 10: Em hiểu hardening thế nào?

Trả lời:

> Hardening là cấu hình hệ thống an toàn hơn theo baseline/benchmark. Ví dụ với server có thể kiểm tra account/password policy, SSH/RDP, service không cần thiết, permission, logging, firewall, TLS, patch level, audit policy.
>
> Kinh nghiệm của em là dùng script/baseline để kiểm tra tự động các tiêu chí, sinh kết quả đạt/chưa đạt, sau đó phối hợp vận hành để xử lý. Với môi trường ngân hàng, hardening cần gắn với baseline nội bộ, tiêu chuẩn kiểm toán và yêu cầu tuân thủ.

### Câu 11: Em có kinh nghiệm compliance/policy không?

Nên nói thật, không overclaim:

> Em chưa ownership toàn bộ compliance framework như một bộ phận GRC chuyên trách. Tuy nhiên em đã tham gia ở góc độ kỹ thuật: review tiêu chuẩn an toàn cho dev, hỗ trợ evidence cho đánh giá, training đội phát triển/vận hành, kiểm tra hardening, rà soát lỗ hổng và đề xuất remediation.
>
> Nếu vào BIDV, em có thể đóng góp tốt ở phần chuyển yêu cầu compliance thành checklist kỹ thuật cụ thể cho dev/vận hành: ví dụ yêu cầu về logging, phân quyền, mã hóa, quản lý secret, kiểm soát thư viện, hardening, SAST/SCA/DAST và quy trình retest.

### Câu 12: Nếu có nhiều lỗ hổng, em ưu tiên xử lý thế nào?

Trả lời:

> Em ưu tiên theo rủi ro thực tế, không chỉ theo số lượng. Các yếu tố gồm:
>
> - Ảnh hưởng đến tiền/giao dịch/dữ liệu khách hàng.
> - Có khai thác được từ internet không.
> - Có cần tài khoản/quyền đặc biệt không.
> - Có exploit public không.
> - Có ảnh hưởng nhiều hệ thống không.
> - Có khả năng bị phát hiện bởi monitoring không.
>
> Ví dụ RCE, auth bypass, IDOR lộ dữ liệu khách hàng, sửa amount giao dịch sẽ ưu tiên rất cao. Các lỗi cấu hình ít impact hơn có thể xử lý theo kế hoạch hardening.

### Câu 13: Em quản lý/chia việc trong team thế nào?

Trả lời:

> Khi nhận job, em xác định scope và module trước, sau đó chia theo tính năng hoặc luồng nghiệp vụ. Thường một phần quan trọng sẽ có ít nhất hai người review chéo để giảm miss lỗi.
>
> Sau khi các bạn có finding, em review lại bằng chứng, impact, khả năng khai thác và cách viết khuyến nghị. Với lỗi nghiêm trọng, em thường kiểm tra lại trực tiếp để tránh false positive hoặc đánh giá sai mức độ.

### Câu 14: Điểm yếu của em là gì?

Không nên nói “em yếu compliance/cloud” quá thô. Nên nói:

> Điểm em muốn cải thiện thêm là phần framework/process ở môi trường ngân hàng lớn, ví dụ cách chuẩn hóa Secure SDLC, risk acceptance, compliance mapping và phối hợp nhiều khối. Trước đây em mạnh hơn ở phần hands-on AppSec/pentest, nhưng em thấy đây là nền tốt để học và đóng góp vào quy trình.
>
> Cách em cải thiện là khi vào môi trường mới, em sẽ đọc guideline nội bộ, học từ các anh chị đang vận hành process, sau đó đóng góp bằng các checklist kỹ thuật và kinh nghiệm thực chiến em đã có.

### Câu 15: Kỳ vọng lương / thời gian nhận việc

#### Lương

Nói theo tổng package, không sa vào số tháng lương:

> Em mong muốn nhìn theo tổng thu nhập năm và scope công việc. Nếu role phù hợp với kinh nghiệm AppSec/pentest gần 5 năm và có cơ hội phát triển lâu dài ở BIDV, em sẵn sàng trao đổi linh hoạt theo khung của ngân hàng.

Nếu bắt buộc nêu số, nên chuẩn bị một khoảng rõ ràng trước khi đi.

#### Thời gian nhận việc

> Quy định hiện tại của em là bàn giao khoảng 45 ngày. Tuy nhiên nếu hai bên thống nhất và công ty hiện tại sắp xếp được, em sẽ cố gắng xin rút ngắn. Em muốn bàn giao đúng trách nhiệm để không ảnh hưởng đội hiện tại.

## 4. Những điều nên tránh nói ở BIDV

Tránh các câu sau:

- “Em chưa nhớ shift-left là gì.”
- “Em chủ yếu muốn làm Red Team.”
- “Em hay tự rà quét hệ thống bên ngoài.”
- “Cloud em không biết nhiều” → đổi thành “em chưa làm sâu cloud public, nhưng có kinh nghiệm private/internal cloud ở mức kiểm thử ứng dụng/API và sẵn sàng học thêm cloud security.”
- “Compliance em chưa làm” → đổi thành “em chưa ownership formal toàn bộ, nhưng đã tham gia evidence/review/checklist kỹ thuật.”
- “Tool báo gì em gửi dev fix” → phải nhấn mạnh có lọc false positive và đánh giá khả năng khai thác.
- Không kể quá chi tiết thông tin nhạy cảm nội bộ VNPT/đối tác. Chỉ nói ở mức bài học kỹ thuật.

## 5. Bộ 3 câu chuyện nên chuẩn bị thật chắc

### Story 1: Business logic thanh toán 0 đồng

Dùng khi hỏi: lỗi nghiêm trọng, business logic, mobile/API.

Thông điệp chính:

> Client không đáng tin. Amount/gói cước/sản phẩm phải được server kiểm tra lại từ nguồn tin cậy.

### Story 2: Duplicate URL parameter với đối tác

Dùng khi hỏi: tích hợp đối tác, lỗi tài chính, risk lớn.

Thông điệp chính:

> Hệ thống tích hợp phải thống nhất canonicalization, không cho duplicate parameter, ký đúng dữ liệu quan trọng và không trust đối tác tuyệt đối.

### Story 3: eKYC fake camera / VCam

Dùng khi hỏi: mobile, eKYC, deepfake, fraud.

Thông điệp chính:

> eKYC cần phòng thủ nhiều lớp: app protection, anti-tamper, device risk, server validation, monitoring bất thường.

## 6. Câu hỏi nên hỏi ngược BIDV

Cuối buổi nên hỏi 2-3 câu này:

1. > Anh/chị có thể chia sẻ team hiện tại đang tập trung nhiều hơn vào Secure SDLC, pentest, vulnerability management hay compliance không ạ?

2. > Với các hệ thống trọng yếu, BIDV đang triển khai security review từ giai đoạn requirement/design như thế nào ạ?

3. > Nếu em vào team, trong 3-6 tháng đầu kỳ vọng chính của anh/chị với vị trí này là gì ạ?

4. > Các công cụ hiện tại team đang dùng cho SAST/SCA/DAST/vulnerability management là gì, và phần nào team muốn cải thiện thêm?

Câu hỏi này giúp em thể hiện là người muốn vào làm thật, hiểu process, không chỉ hỏi lương.

## 7. Checklist tối nay

- Học thuộc intro 60-90 giây.
- Chuẩn bị 3 story:
  - Thanh toán 0 đồng.
  - Duplicate parameter đối tác.
  - eKYC fake camera.
- Ôn kỹ:
  - Secure SDLC.
  - Shift-left.
  - SAST/DAST/SCA.
  - CVE risk-based.
  - Hardening.
  - JWT.
  - Password hashing.
  - Insecure deserialization.
- Chuẩn bị câu hỏi ngược.
- Không kể quá sâu thông tin nhạy cảm của công ty cũ/đối tác.
- Trả lời ngắn hơn các buổi trước: mỗi câu 1-2 phút, nếu HR muốn sâu thì mới đào thêm.

## Câu chốt nên dùng

> Điểm em nghĩ phù hợp với BIDV là em đã làm nhiều với hệ thống tài chính số, mobile/API, eKYC và lỗi business logic giao dịch. Em muốn chuyển từ môi trường ví điện tử/telco-fintech sang ngân hàng để làm bài bản hơn về AppSec, Secure SDLC và quản lý lỗ hổng ở quy mô lớn hơn.

---

# Bộ câu hỏi và trả lời học thuộc

## 1. Nếu được giao test một hệ thống ngân hàng mới, em bắt đầu thế nào?

> Đầu tiên em sẽ xác định scope, môi trường test, tài khoản theo từng role, tài liệu nghiệp vụ và luồng dữ liệu chính. Sau đó em map các luồng quan trọng như đăng nhập, phân quyền, truy vấn thông tin, chuyển tiền/thanh toán, upload/download, tích hợp đối tác.
>
> Với ngân hàng, em ưu tiên kiểm tra các điểm ảnh hưởng đến tiền, dữ liệu khách hàng và toàn vẹn giao dịch: authentication, authorization, IDOR, sửa amount, replay request, rate limit, token/session, logging/audit và business logic.

## 2. Theo em lỗi nào nguy hiểm nhất trong hệ thống ngân hàng?

> Theo em nguy hiểm nhất là các lỗi ảnh hưởng trực tiếp đến giao dịch hoặc dữ liệu khách hàng, ví dụ auth bypass, IDOR/BOLA, sửa số tiền giao dịch, replay giao dịch, bypass OTP, lộ thông tin khách hàng hoặc RCE trên hệ thống trọng yếu.
>
> Các lỗi này không chỉ ảnh hưởng kỹ thuật mà còn ảnh hưởng tài chính, uy tín và tuân thủ pháp lý, nên khi đánh giá em luôn ưu tiên theo impact thực tế chứ không chỉ theo điểm CVSS.

## 3. Em hiểu IDOR/BOLA là gì?

> IDOR hoặc BOLA là lỗi khi người dùng truy cập được tài nguyên của người khác bằng cách thay đổi ID hoặc tham số trong request. Ví dụ user A đổi accountId/orderId/customerId sang của user B mà server vẫn trả dữ liệu.
>
> Cách test là dùng nhiều tài khoản/role khác nhau, thay đổi các ID trong URL, body, header hoặc token rồi kiểm tra server có kiểm soát quyền theo object hay không.
>
> Cách fix là server phải kiểm tra quyền truy cập trên từng object, không tin ID từ client, không chỉ ẩn ID hoặc dùng UUID là đủ.

## 4. Khi test API giao dịch tài chính, em kiểm tra gì?

> Em sẽ kiểm tra các nhóm chính:
>
> - User có được phép thực hiện giao dịch đó không.
> - Amount, fee, account, merchant, orderId có bị sửa được không.
> - Request có bị replay được không.
> - Có kiểm tra toàn vẹn dữ liệu/signature không.
> - Có rate limit/chống spam không.
> - Có bypass OTP/step xác thực không.
> - Log/audit có đủ để truy vết không.
>
> Với giao dịch tài chính, nguyên tắc của em là client không đáng tin; server phải tự xác thực lại toàn bộ dữ liệu quan trọng.

## 5. Nếu phát hiện lỗi critical ngay trước go-live, em xử lý thế nào?

> Em sẽ xác nhận lại khả năng khai thác và impact trước, sau đó báo ngay cho các bên liên quan như dev, quản lý dự án, vận hành và security lead. Em sẽ trình bày ngắn gọn: điều kiện khai thác, impact, bằng chứng, mức độ rủi ro và hướng xử lý.
>
> Nếu lỗi ảnh hưởng đến tiền, dữ liệu khách hàng hoặc quyền truy cập, em sẽ đề xuất chưa go-live cho đến khi có fix hoặc mitigation đủ an toàn. Sau khi fix, em retest lại và ghi nhận kết quả rõ ràng.

## 6. Em phân biệt SAST, DAST, SCA như thế nào?

> SAST là phân tích source code để tìm lỗi sớm trong quá trình dev.
>
> DAST là kiểm thử ứng dụng đang chạy từ bên ngoài, giống cách attacker tương tác với hệ thống.
>
> SCA là kiểm tra thư viện/phụ thuộc để phát hiện CVE hoặc license risk.
>
> Ba phần này bổ trợ nhau. SAST/SCA giúp shift-left, phát hiện sớm trong CI/CD; DAST và manual pentest giúp xác nhận khả năng khai thác thực tế, đặc biệt là business logic.

## 7. Tool báo rất nhiều lỗi false positive thì em làm gì?

> Em sẽ không gửi toàn bộ kết quả tool cho dev ngay. Em sẽ lọc theo khả năng khai thác thực tế: vị trí lỗi, dữ liệu đầu vào, điều kiện khai thác, quyền cần có và impact.
>
> Lỗi nào xác nhận được thì viết lại thành finding rõ ràng, có bằng chứng, impact và khuyến nghị fix. Lỗi nào chưa đủ bằng chứng thì ghi nhận để theo dõi hoặc trao đổi thêm, tránh làm dev mất thời gian vì false positive.

## 8. Em kiểm tra JWT như thế nào?

> Em kiểm tra các điểm chính:
>
> - Token có verify signature không.
> - Có chấp nhận thuật toán none hoặc thuật toán yếu không.
> - Có kiểm tra exp/iat/nbf không.
> - Có phân biệt access token và refresh token không.
> - Token hết hạn có còn dùng được không.
> - Có thể sửa payload như userId/role rồi vẫn được chấp nhận không.
>
> Nguyên tắc là server không được tin dữ liệu trong JWT nếu chưa verify signature và kiểm tra đầy đủ thời hạn/quyền.

## 9. Password nên lưu thế nào?

> Mật khẩu không được lưu plaintext và không nên mã hóa hai chiều. Cách đúng là hash một chiều bằng thuật toán phù hợp cho password như bcrypt, Argon2 hoặc PBKDF2, có salt riêng cho từng user và cost đủ mạnh.
>
> Ngoài lưu trữ, cần bảo vệ cả quá trình truyền bằng HTTPS, không đưa password lên URL/query string, có rate limit chống brute-force, lock/captcha theo rủi ro và logging phù hợp.

## 10. Em hiểu insecure deserialization thế nào?

> Serialization là chuyển object thành dạng dữ liệu để lưu hoặc truyền đi. Deserialization là dựng lại object từ dữ liệu đó.
>
> Lỗ hổng xảy ra khi server deserialize dữ liệu không tin cậy, khiến attacker có thể kích hoạt magic method/callback hoặc gadget chain để thực hiện hành vi ngoài ý muốn, nghiêm trọng nhất là RCE.
>
> Cách phòng tránh là không deserialize dữ liệu từ nguồn không tin cậy, dùng format an toàn hơn, whitelist class/type, kiểm tra integrity và hạn chế quyền của process.

## 11. Nếu BIDV hỏi em mạnh nhất mảng nào?

> Điểm mạnh nhất của em là AppSec thực chiến trên web/API/mobile, đặc biệt là business logic trong hệ thống tài chính số. Em có kinh nghiệm kiểm thử các luồng thanh toán, tích hợp đối tác, eKYC, mobile API, SAST/SCA và phối hợp dev để xử lý lỗ hổng.
>
> Em không chỉ chạy tool mà tập trung hiểu nghiệp vụ, vì nhiều lỗi nghiêm trọng trong hệ thống tài chính thường nằm ở logic xử lý chứ tool tự động khó phát hiện.

## 12. Nếu hỏi điểm yếu của em?

> Điểm em muốn phát triển thêm là phần quy trình và compliance trong môi trường ngân hàng lớn, ví dụ mapping yêu cầu NHNN/nội bộ vào checklist kỹ thuật, risk acceptance và quản lý tuân thủ ở quy mô lớn.
>
> Trước đây em mạnh hơn ở phần hands-on AppSec/pentest, nhưng em nghĩ nền tảng đó giúp em hiểu rõ rủi ro thực tế. Nếu vào BIDV, em sẽ học framework nội bộ và đóng góp bằng kinh nghiệm kỹ thuật của mình.

## 13. Nếu hỏi vì sao rời công ty hiện tại?

> Công ty hiện tại cho em nền tảng rất tốt về pentest và AppSec, team cũng hỗ trợ nhau tốt. Tuy nhiên sau gần 5 năm, em muốn sang môi trường lớn hơn, có nhiều hệ thống trọng yếu hơn và quy trình bảo mật bài bản hơn.
>
> BIDV là môi trường ngân hàng lớn, có nhiều bài toán về AppSec, Secure SDLC, vulnerability management, compliance và bảo vệ giao dịch tài chính, nên em thấy phù hợp với định hướng phát triển tiếp theo.

## 14. Nếu hỏi em có biết cloud security không?

> Em chưa làm sâu cloud public như AWS/Azure/GCP ở mức chuyên trách. Kinh nghiệm của em nhiều hơn ở application security, API/mobile và hệ thống private/internal cloud.
>
> Tuy nhiên các nguyên tắc bảo mật vẫn có nhiều điểm chung: phân quyền, network exposure, secret management, logging, hardening, vulnerability management và kiểm soát cấu hình. Nếu vị trí cần thêm cloud security, em sẵn sàng học và bổ sung nhanh.

## 15. Nếu hỏi về eKYC/deepfake/fake camera?

> Với eKYC, rủi ro không chỉ nằm ở thuật toán nhận diện mà còn nằm ở thiết bị và luồng dữ liệu. Nếu app bị root/jailbreak, bị hook bằng Frida, bypass SSL pinning hoặc fake camera thì attacker có thể đưa ảnh/video chuẩn bị sẵn vào luồng eKYC.
>
> Theo em cần phòng thủ nhiều lớp: app protection, anti-tamper, phát hiện root/jailbreak, chống fake camera/emulator, kiểm tra toàn vẹn dữ liệu phía server, device binding, risk scoring và monitoring hành vi bất thường.

## 16. Nếu hỏi em xử lý CVE thế nào?

> Em xử lý CVE theo hướng risk-based. Đầu tiên xác định asset nào bị ảnh hưởng, version có đúng vulnerable không, service có expose ra ngoài không, điều kiện khai thác là gì và impact thực tế thế nào.
>
> Sau đó em ưu tiên các lỗi như RCE, auth bypass, privilege escalation, data leak hoặc lỗi có exploit public. Với từng case, em đề xuất patch, upgrade, mitigation hoặc cấu hình tạm thời, rồi verify lại sau khi xử lý.

## 17. Nếu hỏi về responsible disclosure/đạo đức nghề nghiệp?

> Quan điểm của em là chỉ kiểm thử trong phạm vi được uỷ quyền. Với hệ thống thật, đặc biệt là ngân hàng/tài chính, việc kiểm thử ngoài scope có thể gây rủi ro pháp lý, ảnh hưởng khách hàng và tổ chức.
>
> Ngoài công việc, em rèn kỹ năng qua lab, CTF, môi trường tự dựng hoặc chương trình có scope rõ ràng. Nếu phát hiện vấn đề trong phạm vi được phép, em báo cáo có trách nhiệm, không khai thác để trục lợi.

## 18. Nếu hỏi em sẽ đóng góp gì trong 3 tháng đầu?

> Trong 3 tháng đầu, em muốn tập trung hiểu hệ thống, quy trình nội bộ, cách BIDV đang triển khai Secure SDLC, pentest, SAST/SCA/DAST và vulnerability management.
>
> Sau đó em có thể đóng góp ở các phần như review security requirement, pentest web/API/mobile, lọc false positive từ tool, xây checklist kỹ thuật cho dev, hỗ trợ retest và đề xuất cải thiện quy trình dựa trên kinh nghiệm thực tế.

## 19. Nếu gặp câu không biết thì trả lời thế nào?

Nói mẫu này, đừng đoán bừa:

> Phần này em chưa làm sâu nên em không muốn trả lời quá chắc khi chưa đủ thông tin. Nhưng nếu gặp thực tế, em sẽ tiếp cận bằng cách đọc tài liệu/standard nội bộ, xác định rủi ro chính, hỏi thêm người phụ trách hệ thống và thử nghiệm trên môi trường test trước khi đưa ra kết luận.
>
> Với nền tảng AppSec/pentest hiện tại, em nghĩ em có thể học nhanh để đáp ứng yêu cầu.

## 20. Câu chốt cuối buổi

> Em cảm ơn anh/chị đã trao đổi. Qua buổi hôm nay em thấy vị trí khá phù hợp với kinh nghiệm của em về AppSec, pentest, mobile/API, eKYC và business logic trong hệ thống tài chính số.
>
> Nếu có cơ hội tham gia BIDV, em mong muốn đóng góp vào việc phát hiện và giảm thiểu rủi ro bảo mật thực tế, đồng thời học thêm quy trình Secure SDLC và quản lý lỗ hổng ở môi trường ngân hàng lớn.

## Nên học thuộc nhất 5 câu này

1. Giới thiệu bản thân.
2. Vì sao muốn vào BIDV.
3. Secure SDLC / shift-left.
4. Case business logic thanh toán.
5. Responsible disclosure: chỉ test trong phạm vi được uỷ quyền.
