# ĐỌC CÁI NÀY 12/6/2026 CHANGE LOGS:
Các thay đổi quan trọng nhất của bộ SRS PBMS hiện tại:

  1. Booking và Allocation

  - Booking không giữ Zone hoặc Slot.
  - Xe máy/ô tô Booking chỉ giữ general capacity ở Building.
  - Ô tô Walk-in/Booking khi check-in chỉ được gợi ý Zone GENERAL; Slot thực tế chỉ ghi nhận sau khi xe đã đậu.
  - No-show chuyển Booking sang EXPIRED, không cập nhật slot_status.
  - Check-in grace dùng cấu hình động checkin_grace_minutes.

  2. Pricing Policy

  - Pricing Policy dùng chung toàn hệ thống theo Vehicle Type, không theo Building.
  - Parking Session không bắt buộc lưu pricing_policy_id.
  - Policy áp dụng cho session được xác định bằng:
    Vehicle -> Vehicle Type -> check_in_time -> Pricing Policy hiệu lực.

  - Check-in phải kiểm tra có đúng một Pricing Policy hợp lệ; nếu không có thì từ chối check-in và không tạo session.
  - Check-out luôn dùng Policy áp dụng tại check_in_time, không dùng Policy tại check_out_time.
  - Policy đã ACTIVE hoặc đã được dùng thì bị khóa cấu hình giá/Pricing Window.
  - Policy cùng Vehicle Type không được overlap timeline.
  - Payment lưu pricing_policy_id để audit.

  3. Pricing Window và Fee Calculation

  - Pricing Window trong cùng Policy phải phủ đủ 24 giờ và không overlap.
  - Cho phép Window đi qua nửa đêm.
  - Session không reset qua ngày.
  - Fee Calculation chia thời gian theo từng Pricing Window occurrence.
  - Một đoạn lớn hơn 0 trong Pricing Window phải chịu ít nhất Base Price.
  - Window Cap áp dụng cho từng đoạn/window occurrence, không áp dụng toàn session.

  4. Booking Deposit

  - Booking Deposit tính theo Booking Policy tại thời điểm Booking được tạo/thanh toán.
  - Deposit không bị tính lại khi xe check-in, kể cả Policy lúc check-in khác Booking Policy.
  - Checkout trừ đúng Deposit đã thanh toán.
  - Booking Cancellation Refund Policy vẫn giữ nguyên: hủy đúng hạn vẫn được hoàn Deposit.

  5. Check-out và Payment

  - check_out_time được ghi nhận ngay khi bắt đầu check-out, trước khi tính phí.
  - Payment PENDING trong cùng lần check-out không làm phí tiếp tục tăng.
  - Checkout Payment không có automatic timeout.
  - Booking Payment dùng booking_payment_timeout_minutes.
  - Monthly Subscription Payment dùng monthly_subscription_payment_timeout_minutes.
  - Payment phải có exactly one source: session, booking hoặc monthly subscription.
  - Payment FAILED không được reset về PENDING; retry tạo Payment mới.
  - Một Payment có thể FAILED nhưng checkout vẫn tiếp tục.
  - Chỉ khi toàn bộ checkout Failed thì rollback check_out_time, session tiếp tục đang gửi.
  - Đổi phương thức thanh toán trong cùng checkout giữ nguyên check_out_time.

  6. Monthly Subscription

  - Mỗi Monthly Subscription áp dụng cho một xe.
  - Ô tô tháng dùng Slot riêng trong Zone MONTHLY; xe máy tháng giữ capacity động.
  - Monthly Subscription checkout hợp lệ có thể có amount_due = 0.
  - Quyền lợi tháng trong checkout được đánh giá tại check_out_time, không đánh giá lại theo thời điểm Payment hoàn tất.
  - Nếu subscription hết hạn trong lúc Payment Pending nhưng còn hiệu lực tại check_out_time, không tính thêm phí.
  - Nếu đã hết hạn trước hoặc đúng check_out_time, tính Walk-in từ expired_at đến check_out_time.
  - Nếu checkout Failed rollback, lần checkout sau ghi nhận mốc mới và đánh giá lại quyền lợi theo mốc mới.

  7. Payment và Scope loại bỏ

  - Đã loại Discount, Promotion, Coupon, VAT/Tax calculation khỏi scope.
  - Cash rounding chỉ áp dụng trên số tiền cuối cùng sau:
    phí gửi xe + phụ phí/phí phạt - Booking Deposit hợp lệ.

  - Không tạo thêm entity/field cho Discount/VAT/credit/wallet/account balance.

  8. Data Model

  - pricing_policy liên kết Vehicle Type, không có building_id.
  - pricing_window thuộc Pricing Policy, phải phủ 24h và không overlap.
  - parking_session không bắt buộc có pricing_policy_id.
  - payment.pricing_policy_id dùng để audit policy thực sự được dùng.
  - Payment amount >= 0.
  - Không thêm entity mới cho phần Deposit dư.
  
---

# Parking Building Management System (PBMS)

Software Requirements Specification (SRS) repository for the Parking Building Management System project.

---

## Overview

Parking Building Management System (PBMS) is a parking facility management solution designed to support:

* Vehicle check-in and check-out
* Parking slot allocation
* Vehicle booking
* Monthly card management
* Payment processing
* Revenue monitoring
* Operational management

This repository contains the project's Software Requirements Specification (SRS) and supporting analysis documents.

---

## Main Documents

| Document                      | Description                                     |
| ------------------------------| ----------------------------------------------- |
| `PBMS_Feature_Actor_Based.md` | Features according to SRS document              |
| `PBMS_SRS_Document.md`        | Complete generated SRS document                 |
| `sections/`                   | Source sections used to build the SRS           |
| `build-srs.bat`               | Script for generating the complete SRS document |

---

## Repository Structure

```text
.
├── README.md
├── PBMS_Feature_Actor_Based.md
├── PBMS_SRS_Document.md
├── build-srs.bat
└── sections/
    ├── 00_Title_And_TOC.md
    ├── 01_Introduction.md
    ├── 02_Overall_Description.md
    ├── 03_Stakeholders_Actors.md
    ├── 04_Business_Context.md
    ├── 05_Functional_Requirements.md
    ├── 06_Business_Rules.md
    ├── 07_Finalized_Policy_Decisions.md
    └── 08_Concept_Entity_Physical_Model.md
```

---

## SRS Build Process

The complete SRS document is generated from the files located in the `sections/` directory.

### Update Workflow

1. Edit the appropriate file inside `sections/`
2. Run:

```bash
build-srs.bat
```

3. Verify the generated file:

```text
PBMS_SRS_Document.md
```

4. Commit and push changes:

```bash
git add .
git commit -m "Update SRS"
git push
```

---

## Important Note

`PBMS_SRS_Document.md` is generated from the source files located in `sections/`.

Do not edit the generated document directly.

All modifications should be made inside the `sections/` directory and then rebuilt using `build-srs.bat`.

---
<!-- Author: Nguyen Hoang Gia Thai -->
