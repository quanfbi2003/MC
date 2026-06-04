# Kết quả ôn tập — Xây dựng tiêu chuẩn nguyên liệu hóa dược, ví dụ 3-5

> **Ngày:** 05/06/2026
> **Môn:** Xây dựng tiêu chuẩn nguyên liệu hóa dược
> **Nguồn đề:** Ví dụ 3-5, tài liệu "XÂY DỰNG TIÊU CHUẨN NGUYÊN LIỆU HÓA DƯỢC"
> **Model AI:** Codex (3-vai-trò kiểm chứng)

---

## Bảng kết quả

| Mục | Nội dung | Kết quả | Mức tin cậy | Nguồn |
|-----|----------|---------|-------------|-------|
| Ví dụ 3a | Chỉ tiêu chất lượng dự kiến của nguyên liệu X dạng rắn | Xem lời giải | Có thể | [XÂY DỰNG TIÊU CHUẨN NGUYÊN LIỆU HÓA DƯỢC, tr. 260] |
| Ví dụ 3b | Xây dựng phương pháp xác định giới hạn tạp chất liên quan bằng HPLC | Xem lời giải | Chắc chắn | [XÂY DỰNG TIÊU CHUẨN NGUYÊN LIỆU HÓA DƯỢC, tr. 261] |
| Ví dụ 3c | Xác định LOD, LOQ | LOD = 0,0031 mcg/ml; LOQ = 0,0093 mcg/ml | Chắc chắn | [XÂY DỰNG TIÊU CHUẨN NGUYÊN LIỆU HÓA DƯỢC, tr. 178-180, 262] |
| Ví dụ 4b | Xây dựng phương pháp thử độ hòa tan | Xem lời giải | Chắc chắn | [XÂY DỰNG TIÊU CHUẨN NGUYÊN LIỆU HÓA DƯỢC, tr. 264] |
| Ví dụ 4c | Đánh giá độ lặp lại phép thử hòa tan | Trung bình = 77,28%; RSD = 0,84%; đạt | Chắc chắn | [XÂY DỰNG TIÊU CHUẨN NGUYÊN LIỆU HÓA DƯỢC, tr. 265] |
| Ví dụ 5 | Lượng chế phẩm thử giới hạn kim loại nặng | m = 0,80 g | Chắc chắn | [XÂY DỰNG TIÊU CHUẨN NGUYÊN LIỆU HÓA DƯỢC, tr. 266] |

---

## FULL ANALYSIS — Kiểm chứng 3 vai trò

### Agent 1 — Dược sĩ lâm sàng/phân tích

Nội dung trong ảnh khớp với các trang 260-266 của tài liệu. Với Ví dụ 3, trọng tâm là xây dựng tiêu chuẩn cho nguyên liệu hóa dược dạng rắn, trong đó chi tiết "ethyl acetat, methanol và benzen" gợi ý bắt buộc xét chỉ tiêu dung môi tồn dư. Phương pháp HPLC ở ý b là phép thử giới hạn tạp chất liên quan theo so sánh diện tích pic: từng tạp <= 1% và tổng tạp <= 3%.

Với Ví dụ 3c, đề cho dãy pha loãng để xác định LOD bằng quan sát. Nồng độ thấp nhất còn đáp ứng là 0,0031 mcg/ml; 0,0016 mcg/ml không đáp ứng. Theo lý thuyết trong tài liệu, thường lấy LOQ = 3LOD, do đó LOQ = 0,0093 mcg/ml.

Với Ví dụ 4c, đánh giá độ lặp lại bằng trung bình, độ lệch chuẩn và RSD của 6 kết quả hòa tan. Trung bình = 77,2750%, SD mẫu = 0,6503, RSD = 0,8416%, làm tròn là 0,84%. Với tiêu chí thẩm định thường dùng cho độ lặp lại định lượng là RSD <= 2,0%, phương pháp đạt.

Với Ví dụ 5, ống chuẩn có 20 ml Pb 1 ppm, tương đương 20 mcg Pb. Giới hạn cho phép 25 ppm = 25 mcg/g, nên lượng mẫu cần dùng là 20/25 = 0,80 g.

### Agent 2 — Phản biện

Các điểm cần kiểm tra:

| Hạng mục | Kết quả | Ghi chú |
|----------|---------|---------|
| Khớp giáo trình | Đạt | Các dữ kiện lấy từ tr. 260-266; lý thuyết LOD/LOQ lấy từ tr. 178-180 |
| Phép tính LOD | Đạt | LOD theo quan sát là nồng độ thấp nhất còn đáp ứng: 0,0031 mcg/ml |
| Phép tính LOQ | Có điều kiện | Dùng quy ước trong slide "Thường: LOQ = 3LOD"; nếu giảng viên yêu cầu S/N = 10:1 thì cần dữ liệu nhiễu nền |
| Phép tính RSD | Đạt | Dùng SD mẫu cho 6 lần thử; RSD = 0,8416% |
| Kim loại nặng | Đạt | 1 ppm = 1 mcg/ml; 20 ml tạo 20 mcg Pb; m = 20/25 = 0,80 g |
| Tiêu chí đạt độ lặp lại | Có điều kiện | File đề không nêu ngưỡng cụ thể; dùng ngưỡng thường gặp RSD <= 2,0% |

Điểm có thể gây nhầm: Ví dụ 3a là câu mở, không có bảng đáp án mẫu trong ảnh, nên phần chỉ tiêu chất lượng dự kiến được suy ra theo logic xây dựng tiêu chuẩn và dữ kiện dung môi tinh chế. Vì vậy mục 3a để mức tin cậy `[Có thể]`, còn các phép tính có số liệu rõ để `[Chắc chắn]`.

### Agent 3 — Tổng hợp

Kết luận cuối:

- Ví dụ 3a: cần nêu nhóm chỉ tiêu đầy đủ cho nguyên liệu rắn, đặc biệt không bỏ sót dung môi tồn dư ethyl acetat, methanol, benzen.
- Ví dụ 3b: phương pháp HPLC dùng dung dịch đối chiếu 1%; giới hạn từng tạp <= 1%, tổng tạp <= 3%.
- Ví dụ 3c: LOD = 0,0031 mcg/ml; LOQ = 0,0093 mcg/ml theo quy ước LOQ = 3LOD trong tài liệu.
- Ví dụ 4b: mô tả được phép thử hòa tan bằng giỏ quay trong đệm phosphat pH 6,8, sau đó định lượng bằng HPLC.
- Ví dụ 4c: trung bình = 77,28%; SD = 0,6503; RSD = 0,84%; phương pháp đạt độ lặp lại nếu áp dụng tiêu chí RSD <= 2,0%.
- Ví dụ 5: cần dùng 0,80 g chế phẩm.

Điểm tin cậy tổng thể: **8/10**. Các phép tính chắc chắn; phần chỉ tiêu dự kiến ở Ví dụ 3a vẫn nên đối chiếu thêm tiêu chuẩn mẫu hoặc đáp án giảng viên nếu có.

## Ví dụ 3a — Chỉ tiêu chất lượng dự kiến của nguyên liệu X dạng rắn

Với nguyên liệu hóa dược X dạng rắn, dự kiến xây dựng các chỉ tiêu:

- Tính chất/cảm quan: màu sắc, trạng thái, độ tan nếu cần.
- Định tính hoạt chất X.
- Độ tinh khiết: tạp chất liên quan.
- Dung môi tồn dư: ethyl acetat, methanol và đặc biệt benzen.
- Nước hoặc mất khối lượng do làm khô.
- Tro sulfat/cắn sau nung nếu giáo trình yêu cầu cho nguyên liệu này.
- Kim loại nặng hoặc tạp vô cơ liên quan nếu có nguy cơ từ quy trình.
- Định lượng hàm lượng hoạt chất X.
- Bảo quản, đóng gói, nhãn nếu lập tiêu chuẩn đầy đủ.

**Lưu ý bẫy đề:** đề cho ethyl acetat, methanol và benzen để tinh chế nên phải đưa **dung môi tồn dư** vào tiêu chuẩn. Benzen là dung môi độc, cần kiểm soát rất chặt.

---

## Ví dụ 3b — Xây dựng phương pháp giới hạn tạp chất liên quan bằng HPLC

### Nguyên tắc

So sánh diện tích các pic phụ trong dung dịch thử với diện tích pic chính của dung dịch đối chiếu 1%.

### Điều kiện sắc ký

- Pha động: dung dịch đệm phosphat pH 7,0 : methanol = 60 : 40.
- Cột: C18.
- Detector: UV 235 nm.
- Tốc độ dòng: 1,0 ml/phút.
- Thể tích tiêm: 10 microlit.
- Thời gian chạy: ít nhất 8 lần thời gian lưu của pic chính.

### Chuẩn bị dung dịch

- Dung dịch thử: hòa tan 25,0 mg chế phẩm trong pha động, pha loãng thành 25,0 ml.
- Dung dịch đối chiếu: hòa tan 25,0 mg chất chuẩn X trong pha động, pha loãng thành 25,0 ml. Lấy 1,0 ml dung dịch này pha loãng thành 100,0 ml bằng pha động.

Nồng độ dung dịch thử là 1,0 mg/ml. Dung dịch đối chiếu sau pha loãng là 0,01 mg/ml, tương đương 1% so với dung dịch thử.

### Tiêu chuẩn chấp nhận

- Mỗi pic phụ, trừ pic chính, không được có diện tích lớn hơn diện tích pic chính trong dung dịch đối chiếu: giới hạn từng tạp <= 1%.
- Tổng diện tích các pic phụ không lớn hơn 3 lần diện tích pic chính trong dung dịch đối chiếu: tổng tạp <= 3%.

---

## Ví dụ 3c — Xác định LOD, LOQ

### Cách tiến hành

Pha loãng dần dung dịch chất phân tích, chạy HPLC và ghi nhận nồng độ thấp nhất còn cho đáp ứng pic. Theo bảng:

| Dung dịch | 1 | 2 | 3 | 4 | 5 | 6 |
|----------|---|---|---|---|---|---|
| Nồng độ (mcg/ml) | 0,0500 | 0,0250 | 0,0125 | 0,0063 | 0,0031 | 0,0016 |
| Diện tích pic (mAu.s) | 30672 | 15345 | 7690 | 3534 | 1617 | Không đáp ứng |

Nồng độ 0,0031 mcg/ml vẫn còn đáp ứng, còn 0,0016 mcg/ml không đáp ứng.

### Kết quả

```text
LOD = 0,0031 mcg/ml
LOQ = 3 x LOD = 3 x 0,0031 = 0,0093 mcg/ml
```

Kết luận: LOD của phương pháp là **0,0031 mcg/ml**, LOQ ước tính là **0,0093 mcg/ml**.

---

## Ví dụ 4b — Xây dựng phương pháp thử độ hòa tan

### Điều kiện hòa tan

- Thiết bị: kiểu giỏ quay.
- Môi trường: 900 ml dung dịch đệm phosphat pH 6,8.
- Tốc độ quay: 100 vòng/phút.
- Thời gian: 30 phút.

### Định lượng hoạt chất hòa tan bằng HPLC

- Pha động: acetonitril : dung dịch đệm phosphat pH 7,6 = 34 : 66.
- Cột: C8.
- Detector: UV 280 nm.
- Tốc độ dòng: 1 ml/phút.
- Thể tích tiêm: 20 microlit.
- Dung dịch chuẩn: X trong môi trường hòa tan, nồng độ khoảng 8 mcg/ml.
- Dung dịch thử: lấy dịch hòa tan, pha loãng bằng môi trường hòa tan để có nồng độ tương đương dung dịch chuẩn.

### Các bước thực hiện

1. Cho lượng pellet tương ứng vào thiết bị hòa tan.
2. Tiến hành hòa tan trong 900 ml đệm phosphat pH 6,8 ở 100 vòng/phút trong 30 phút.
3. Lấy mẫu dịch hòa tan, lọc nếu cần.
4. Pha loãng dịch thử đến nồng độ tương đương chuẩn.
5. Tiêm dung dịch chuẩn và dung dịch thử vào hệ HPLC.
6. Tính phần trăm hoạt chất hòa tan từ đáp ứng pic của mẫu thử so với mẫu chuẩn.
7. Thẩm định phương pháp theo các chỉ tiêu phù hợp, trong đó có độ đặc hiệu, tuyến tính, đúng, chính xác/lặp lại và độ bền nếu cần.

---

## Ví dụ 4c — Đánh giá độ lặp lại

Số liệu 6 mẫu:

| Mẫu | 1 | 2 | 3 | 4 | 5 | 6 |
|-----|---|---|---|---|---|---|
| Kết quả (%) | 77,62 | 77,21 | 76,22 | 78,19 | 77,08 | 77,33 |

### Xử lý số liệu

```text
Trung bình = 77,28%
SD = 0,6503
RSD = SD / Trung bình x 100 = 0,84%
```

### Kết luận

Phương pháp đạt yêu cầu về độ lặp lại vì RSD nhỏ, các kết quả 6 mẫu gần nhau. Nếu áp dụng tiêu chí thường dùng trong thẩm định phương pháp định lượng/hòa tan là RSD <= 2,0%, kết quả **RSD = 0,84%** đạt.

---

## Ví dụ 5 — Lượng chế phẩm cần dùng cho thử giới hạn kim loại nặng

Dữ kiện:

- Giới hạn cho phép: 25 ppm = 25 mcg/g.
- Ống chuẩn dùng 20 ml dung dịch Pb 1 ppm.
- 1 ppm Pb trong dung dịch tương đương 1 mcg/ml.

Lượng Pb trong ống chuẩn:

```text
20 ml x 1 mcg/ml = 20 mcg Pb
```

Khối lượng mẫu cần dùng:

```text
m = lượng Pb chuẩn / giới hạn cho phép
m = 20 mcg / 25 mcg/g = 0,80 g
```

Kết luận: cần dùng **0,80 g chế phẩm** để xây dựng phương pháp thử giới hạn kim loại nặng.

---

## Tổng kết

- Ví dụ 3c: LOD = **0,0031 mcg/ml**, LOQ = **0,0093 mcg/ml**.
- Ví dụ 4c: Trung bình = **77,28%**, RSD = **0,84%**, đạt độ lặp lại.
- Ví dụ 5: Lượng chế phẩm cần dùng = **0,80 g**.

> **Lưu ý:** File này dùng để hỗ trợ học tập. Khi dùng để nộp bài/thi, nên đối chiếu lại với tài liệu gốc, đặc biệt các tiêu chí chấp nhận cụ thể nếu giảng viên có quy định riêng.
