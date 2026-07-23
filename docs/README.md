# CampusLoop — Bộ tài liệu lập kế hoạch dự án

Phiên bản kế hoạch: 1.0  
Ngày lập: 23/07/2026  
Nguồn nghiệp vụ: `CampusLoop_Tai_Lieu_Mo_Ta_Chi_Tiet_Luong_Hoat_Dong.docx`, phiên bản 1.0, tháng 07/2026  
Phạm vi: Marketplace đồ cũ, kết nối gia sư, thuê xe máy P2P và nền tảng dùng chung

## Mục đích

Bộ tài liệu chuyển mô tả luồng vận hành thành kế hoạch có thể phân công, phát triển, kiểm thử và nghiệm thu. Nội dung không tự đặt mức phí, tiền cọc hoặc thời hạn nghiệp vụ chưa được phê duyệt.

## Danh mục

1. [01_PROJECT_CHARTER.md](01_PROJECT_CHARTER.md) — mục tiêu, phạm vi, bên liên quan, nguyên tắc và tiêu chí thành công.
2. [02_SCOPE_REQUIREMENTS.md](02_SCOPE_REQUIREMENTS.md) — yêu cầu theo phân hệ, trạng thái nghiệp vụ, quyền dữ liệu và truy vết nguồn.
3. [03_ROADMAP_RELEASE_PLAN.md](03_ROADMAP_RELEASE_PLAN.md) — chiến lược phát hành, phụ thuộc, cổng kiểm soát và kế hoạch pilot.
4. [04_PRODUCT_BACKLOG.md](04_PRODUCT_BACKLOG.md) — epic, story, mức ưu tiên, đầu ra và tiêu chí hoàn thành.
5. [05_TECHNICAL_DELIVERY_PLAN.md](05_TECHNICAL_DELIVERY_PLAN.md) — hiện trạng code, kiến trúc mục tiêu, module, dữ liệu, bảo mật và DevOps.
6. [06_QA_OPERATIONS_RISK_PLAN.md](06_QA_OPERATIONS_RISK_PLAN.md) — kế hoạch kiểm thử, nghiệm thu, vận hành, rủi ro và sổ quyết định chính sách.

## Cách dùng

- Product Owner dùng tài liệu 01–04 để chốt phạm vi release và ưu tiên backlog.
- Tech Lead dùng tài liệu 02, 04, 05 để thiết kế solution, chia sprint và review thay đổi.
- QA Lead dùng tài liệu 02, 04, 06 để lập test case và kiểm soát release gate.
- Operations Lead dùng tài liệu 03, 06 để chuẩn bị checklist, SLA, pilot và xử lý sự cố.
- Mọi chính sách có nhãn `CẦN CHỐT` phải được phê duyệt trước khi bật thanh toán thật hoặc mở Rental công khai.

## Quy ước

- `P0`: bắt buộc để an toàn hoặc chạy đúng luồng cốt lõi.
- `P1`: cần cho MVP vận hành ổn định.
- `P2`: tối ưu sau khi pilot chứng minh nhu cầu.
- `Release gate`: điều kiện bắt buộc trước khi phát hành; không phải mục tiêu tùy chọn.
- Thời lượng dùng đơn vị sprint. Giả định lập kế hoạch: sprint 2 tuần; phải hiệu chỉnh theo nhân sự và velocity thực tế.

## Trình tự ra quyết định

1. Chốt chủ sở hữu sản phẩm, phạm vi pilot và đối tượng dùng thử.
2. Chốt chính sách tiền, hủy, hoàn, thời hạn, khiếu nại và dữ liệu nhạy cảm.
3. Xác nhận backlog Release 1; loại bỏ hạng mục không phục vụ cổng nghiệm thu.
4. Thiết kế API/data migration, test strategy và vận hành trước khi code từng domain.
5. Chạy internal test, closed pilot, review chỉ số; chỉ mở rộng khi đạt release gate.

