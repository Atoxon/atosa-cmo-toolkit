# Provenance — nguồn gốc các thành phần trong `atosa-cmo-toolkit`

Nguyên tắc: chỉ lấy phần đã được **đọc kỹ code + test chạy thật**, không copy mù
`SKILL.md` lý thuyết. Mọi repo nguồn đã được clone về
`~/.hermes/shared/repos/` (source layer) để audit trước khi cherry-pick.

## 1. `audit-tools/cro-audit/cro_audit.py`

- **Nguồn:** `github.com/ericosiu/ai-marketing-skills`, dir `conversion-ops/`
- **License:** MIT (Copyright 2026 Single Grain) — cho phép copy/modify/sublicense.
- **Commit audit tại:** `c903113` (local clone, `~/.hermes/shared/repos/ai-marketing-skills`)
- **Thay đổi so với upstream:**
  1. Bỏ phần "Preamble" gọi `telemetry/version_check.py` và `telemetry/telemetry_init.py`
     — không mang theo cơ chế phone-home dù upstream nói opt-in.
  2. **Fix 3 bug thật** trong `score_social_proof`, `score_urgency`, `score_trust_signals`:
     `re.findall()` trên pattern có group con trả về `tuple`, code gốc gọi `.lower()`
     trực tiếp trên tuple → `AttributeError`. Đã thêm bước flatten tuple→string trước khi
     `.lower()`. Phát hiện khi test thật trên `atosa.asia` (crash ở cả 3 hàm trước khi fix).
  3. Giữ nguyên toàn bộ logic scoring 8 dimensions, không thay đổi trọng số.
- **Dependency:** `requests`, `beautifulsoup4`, `lxml` — không cần `scikit-learn`/`pandas`/
  `numpy` cho tool này cụ thể (những package đó phục vụ `survey_lead_magnet.py`, chưa lấy).
- **Không lấy:** `survey_lead_magnet.py` (cần data CSV khảo sát thật, ATOSA chưa có) —
  để lại cho vòng sau khi có survey data.

## 2. `growth-frameworks/marketing-audit-rubric.md`

- **Nguồn ý tưởng:** `github.com/zubair-trabzada/ai-marketing-claude`, file `README.md`
  (bảng trọng số 6 category) + `agents/market-*.md` (mô tả từng category).
- **License:** MIT (Copyright 2026 Zubair Trabzada).
- **Lấy gì:** CHỈ cấu trúc rubric (6 category + trọng số %) — viết lại hoàn toàn bằng
  tiếng Việt/Anh của riêng mình, không copy nguyên văn mô tả.
- **Không lấy:** `analyze_page.py`, `competitor_scanner.py`, `generate_pdf_report.py`,
  `install.sh`, `requirements.txt` — đây là orchestrator Python đầy đủ với 5 subagent
  chạy song song, overlap cao với `landing-page-optimizer`/`competitive-teardown` đã có
  trong profile Hermes. Không đáng chi phí tích hợp runtime riêng cho giá trị gia tăng
  nhỏ (chỉ là PDF report tự động).
- **Cảnh báo bắt buộc đi kèm khi dùng rubric này:** output là **heuristic/opinion**,
  không phải dữ liệu đo được. Không dùng để báo cáo ROI/conversion thật cho Founder/Sales
  mà không gắn nhãn rõ đây là audit checklist thủ công.

## 3. Repo đã audit nhưng KHÔNG lấy gì (và vì sao)

| Repo | ⭐ | License | Vì sao không lấy |
|---|---|---|---|
| `aaron-he-zhu/aaron-marketing-skills` | 3k | Apache-2.0 | 120 skill liên kết chặt qua "Truth Registry" event-sourcing (NDJSON append-only) + lifecycle 4-phase/discipline. Cắt lẻ 1 skill sẽ đứt chuỗi phase→gate. Đáng giá kiến trúc "chống bịa claim" (Offer & Claims Registry) để **tham khảo thiết kế**, nhưng việc cài nguyên 120 skill cần một quyết định riêng (xem mục Khuyến nghị dưới). |
| `Affitor/affiliate-skills` | 740 | MIT | Core use-case là affiliate quảng bá sản phẩm **của người khác** — không khớp mô hình ATOSA (seller SaaS tự bán). Một số skill con (`grand-slam-offer`, `content-angle-ranker`) là Markdown thuần, không cần API, có thể cherry-pick sau khi diff kỹ với `offers`/`content-creator` hiện có — **chưa làm ở vòng này**. |
| `kostja94/marketing-skills` | 1k | MIT | >160 skill, overlap cao với corpus 230 skill hiện tại của profile; chưa tìm thấy gap cụ thể đáng lấy riêng. |
| `AgriciDaniel/claude-blog` | 2.4k | MIT | Orchestrator blog nguyên khối, 30 sub-skill phụ thuộc lẫn nhau + Python/pytest — không tách lẻ được. |
| `YouMind-OpenLab/nano-banana-pro-prompts-recommend-skill` | 1.9k | MIT | Thư viện prompt tạo ảnh, phụ thuộc CMS bên thứ ba để refresh — không phải knowledge/skill marketing cốt lõi. |
| `ericosiu/ai-marketing-skills` (phần còn lại) | 3.6k | MIT | `sales-pipeline` cần `HUBSPOT_API_KEY`/`INSTANTLY_API_KEY`/`BRAVE_API_KEY`; `sales-playbook` cần `AHREFS_API_KEY`/`ANTHROPIC_API_KEY` (optional, có stub). ATOSA chưa có các tài khoản/API này → không triển khai cho đến khi có data contract thật. |

## Khuyến nghị tiếp theo (chưa thực hiện, cần quyết định của Founder)

1. **`aaron-he-zhu` Truth Registry pattern** đáng học để nâng cấp cơ chế `[CẦN VERIFY]`
   thủ công hiện tại trong `.agents/product-marketing.md` thành event-log có audit trail
   — nhưng đây là một dự án riêng (cần `scripts/registry-events.py` tương đương), không
   nên làm vội trong vòng này.
2. **`offer-claims-registry` / `consent-registry` concept** có thể inspire 1 file
   `claims-registry.ndjson` nội bộ cho ATOSA — ghi lại mọi claim marketing (giá, số
   lượng khách, giải thưởng) kèm nguồn, ngày review, trạng thái — tách biệt khỏi
   `.agents/product-marketing.md` (vốn là snapshot, không phải audit log).
3. `survey_lead_magnet.py` (từ `ericosiu/ai-marketing-skills/conversion-ops`) nên được
   lấy **sau khi** ATOSA có dữ liệu khảo sát khách hàng thật (CSV export) — không có
   giá trị để test ngay bây giờ.
