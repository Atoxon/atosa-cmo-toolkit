# TEST-LOG — cro_audit.py

Nguyên tắc Idea → Test → Data → Insight: ghi lại nguyên trạng, không chỉnh sửa số liệu.

## Run 1 — `https://app.atosa.asia` (2026-10-31)

- **Idea:** dùng tool để audit landing page ATOSA.
- **Lỗi phát hiện:** không phải bug code — đây là **SPA login shell** (title "Atosa -
  Trợ lý Shopee Marketing", `<div id=app>` rỗng, không có nội dung marketing tĩnh).
  Tool vẫn chạy nhưng kết quả không có ý nghĩa cho mục đích "audit landing page".
- **Kết quả (không dùng làm insight CRO):** Overall 31.5/100 (F) — chỉ phản ánh việc
  đây không phải trang marketing, không phản ánh chất lượng UX thật của app sau login.
- **Bài học:** phải xác định đúng URL target (marketing site vs. app shell) trước khi
  chạy audit, không suy ra kết luận CRO từ domain sai.

## Run 2 — `https://atosa.asia` (2026-10-31) — TARGET ĐÚNG

- **Lỗi code phát hiện khi chạy lần đầu:** `AttributeError: 'tuple' object has no
  attribute 'lower'` tại `score_social_proof` (dòng ~306 upstream). Nguyên nhân:
  `re.findall()` trên regex có nhóm con trả `tuple`, code gốc gọi `.lower()` trực tiếp.
  Lặp lại ở `score_urgency` và `score_trust_signals` — cùng 1 class bug, 3 vị trí.
- **Fix:** thêm bước flatten tuple→string trước `.lower()` ở cả 3 hàm (xem diff trong
  `cro_audit.py`, patch áp dụng cục bộ trong repo này, chưa gửi PR upstream).
- **Kết quả sau fix — DỮ LIỆU THẬT, không fabricate:**

```
URL: https://atosa.asia
Overall: 64.1 (C)
  - Headline Clarity: 30/100 — "No H1 tag found on the page"; có hero/banner section
  - CTA Visibility: 75/100 — 13 CTA elements, 8 có button styling, multi-CTA reinforcement
  - Social Proof: 65/100 — text signals "companies, customer, trusted by";
                             54 logo elements detected; star/rating elements present
  - Urgency: 50/100 — chỉ bắt được "flash sale"
  - Trust Signals: 61/100 — "iso", "privacy"; có link privacy/terms
  - Form Friction: 85/100 — 1 visible field, nhưng 3 forms trên trang (có thể gây rối)
  - Mobile Responsiveness: 95/100 — viewport tag đúng, responsive framework có
  - Page Speed Indicators: 40/100 — HTML 304KB, 176 ảnh (70 lazy-load = 40%),
                             123 ảnh dùng WebP/AVIF, 11 external scripts
```

- **Top priority fixes do tool đề xuất (HIGH impact):**
  1. Thêm `<h1>` nêu rõ value proposition chính.
  2. Social proof thiếu định lượng cụ thể (dù có 54 logo, text không có số "X khách
     hàng/shop" rõ ràng mà tool bắt được — cần kiểm tra lại bằng mắt vì có thể số liệu
     nằm trong ảnh/JS render, tool chỉ đọc HTML tĩnh).
  3. Giảm 304KB HTML và 176 ảnh / tăng lazy-load để cải thiện speed.

## Insight (Idea → Test → Data → Insight, bước cuối)

1. **Tool hoạt động đúng sau fix** — dùng được cho audit định kỳ, miễn phí, không cần
   API key. Đáng đưa vào quy trình audit landing page hàng tuần/tháng.
2. **Giới hạn quan trọng:** tool chỉ đọc HTML tĩnh (`requests`, không chạy JS). Nếu
   `atosa.asia` render social proof/số liệu qua JavaScript (React/Vue hydrate), điểm
   "Social Proof 65" có thể **đánh giá thấp hơn thực tế** — cần verify bằng mắt trước
   khi coi điểm số là final. Đây là giả định rõ ràng, không phải kết luận.
3. **Headline Clarity 30/100 là tín hiệu đáng tin** — thiếu `<h1>` là sự thật khách
   quan từ DOM, không phụ thuộc JS rendering, nên đây là fix ưu tiên thật, có thể hành
   động ngay.
4. Chưa test trên landing page đối thủ để có baseline so sánh — cần làm ở vòng sau
   trước khi dùng điểm số này để thuyết phục Founder đầu tư vào CRO.
