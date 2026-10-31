# Marketing Audit Rubric — 6-Dimension Weighted Checklist

> Nguồn ý tưởng: methodology của `zubair-trabzada/ai-marketing-claude` (MIT). Đây là
> **checklist thủ công**, không phải code tự động — dùng để audit landing page/trang
> marketing ATOSA hoặc đối thủ bằng mắt + dữ liệu có sẵn, kết hợp với
> `audit-tools/cro-audit/cro_audit.py` cho phần đo được tự động.
>
> **Cảnh báo:** điểm số là heuristic/opinion, không phải dữ liệu đo conversion/revenue
> thật. Không trình bày với Founder/Sales như là ROI đã được validate.

## Trọng số

| Category | Weight | Đo cái gì |
|---|---|---|
| Content & Messaging | 25% | Chất lượng copy, value proposition, headline, CTA wording |
| Conversion Optimization | 20% | Funnel, form, social proof, friction, urgency — **phần này nên lấy từ `cro_audit.py` output** |
| SEO & Discoverability | 20% | On-page SEO, technical SEO, cấu trúc content |
| Competitive Positioning | 15% | Mức độ khác biệt, nhận thức thị trường, so sánh đối thủ |
| Brand & Trust | 10% | Chất lượng thiết kế, trust signal, authority |
| Growth & Strategy | 10% | Pricing, acquisition channel, retention mechanism |

**Overall Score** = trung bình có trọng số của 6 category (0–100).

## Quy trình dùng

1. Chạy `cro_audit.py --url <url> --industry saas --json` để lấy số liệu khách quan
   cho **Conversion Optimization** (20%) và một phần **Content & Messaging** (25%:
   Headline Clarity) và **Brand & Trust** (10%: Trust Signals).
2. Với **SEO & Discoverability**, dùng skill `keyword-research`/`competitor-analysis`
   đã có trong profile (không trùng lặp công cụ).
3. Với **Competitive Positioning**, dùng skill `competitive-teardown`/`battlecards`
   đã có trong `atosa-shopee-ads-product-knowledge/04-competitors/`.
4. Với **Growth & Strategy**, đối chiếu `.agents/product-marketing.md` (pricing thật,
   acquisition channel đã verify) — KHÔNG tự suy đoán số liệu.
5. Tổng hợp điểm 6 category theo trọng số trên → 1 con số + breakdown.
6. Mọi điểm "thấp" phải kèm **fix cụ thể, có thể hành động**, không chỉ là con số.

## Giới hạn đã biết (rút từ TEST-LOG.md)

- `cro_audit.py` chỉ đọc HTML tĩnh — nội dung render bằng JavaScript (React/Vue hydrate)
  có thể bị đánh giá thấp hơn thực tế. Luôn verify bằng mắt trước khi kết luận.
- Rubric 6-category là framework tham khảo, không phải chuẩn ngành được validate —
  trước khi dùng để quyết định ngân sách, cần ít nhất 1 vòng so sánh với baseline đối
  thủ và dữ liệu conversion thật từ GA4/analytics ATOSA.
