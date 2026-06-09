# 7. Finalized Policy Decisions

| Policy Area                                    | Final Decision                                                                                                                                 |
|------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| Check-in Allocation                            | Khi check-in, xe máy được gợi ý Zone còn capacity; ô tô Walk-in/Booking dùng Slot trong Zone `GENERAL`; ô tô thẻ tháng dùng Slot đã cấp trong Zone `MONTHLY`. |
| Monthly Subscription - Motorcycle              | Xe máy thẻ tháng được đảm bảo có chỗ bằng capacity động: mỗi subscription `ACTIVE` làm giảm capacity Walk-in/Booking một đơn vị. |
| Monthly Subscription - Car                     | Ô tô thẻ tháng được cấp Slot riêng trong Zone `MONTHLY`; quyền giữ Slot nằm ở `monthly_subscription.assigned_slot_id`. |
| Monthly Subscription - Vehicle Binding         | Mỗi vé tháng chỉ áp dụng cho một xe đã đăng ký; không dùng `max_registered_plate`.                                                             |
| Card vs Monthly Subscription                   | Card có `card_type = NORMAL, MONTHLY`; Card `MONTHLY` gắn với Monthly Subscription, nhưng quyền lợi tháng vẫn do Monthly Subscription hợp lệ quyết định. |
| Booking - Motorcycle                           | Xe máy booking phải chọn Building và Vehicle/biển số; Driver không chọn Zone/Slot, hệ thống gán Zone khi check-in. |
| Booking - Car                                  | Ô tô booking phải chọn Building và Vehicle/biển số; Driver không chọn Zone/Slot, hệ thống gán Slot trong Zone `GENERAL` khi check-in. |
| Booking Time Limit                             | Booking phải được đặt trước tối thiểu 1 tiếng và tối đa 8 tiếng tính từ thời điểm thanh toán cọc thành công.                                   |
| Booking Deposit                                | Deposit Fee bằng giá của block đầu tiên theo bảng giá hiện hành.                                                                               |
| Booking Cancellation                           | Khách hủy trước giờ booking ít nhất 1 tiếng thì được hoàn cọc.                                                                                 |
| Booking No-show                                | Khách đến trễ quá 45 phút so với giờ booking thì booking bị hủy và mất cọc.                                                                    |
| Early Arrival                                  | Nếu khách đến sớm hơn giờ booking, hệ thống cho check-in sớm nếu còn chỗ phù hợp; thời gian gửi xe bắt đầu tính từ lúc khách thực tế check-in. |
| Booking Final Payment                          | Nếu khách gửi xe trong thời gian đã đặt, khi thanh toán khách trả phần còn lại sau khi trừ cọc.                                                |
| Overtime After Booking                         | Nếu khách gửi vượt quá thời gian đã đặt, phần vượt được tính phí theo chính sách gửi xe thông thường.                                          |
| Phí qua đêm và nhiều ngày tính theo block nào? | Theo giờ / Theo ngày / Theo mốc qua đêm / Kết hợp.                                                                                             |
| Pricing Model | Hệ thống tính phí theo Time Window, Base Price, Increment/Block Pricing và Window Cap. |
| Window Cap | Window Cap chỉ áp dụng cho từng khung giờ riêng biệt, không áp dụng cho toàn bộ session. |
| 24/7 Session | Hệ thống không reset session khi qua ngày mới. |
| Monthly Subscription Downgrade | Nếu vé tháng hết hạn và xe vẫn còn trong bãi, hệ thống downgrade và tính phí vãng lai từ thời điểm hết hạn. |
| System State Management | Trạng thái hệ thống được quản lý bằng các status riêng cho Building, Floor, Zone, Slot, Vehicle, Card, Parking Session, Booking, Incident, Monthly Subscription và Pricing Policy. |
| Booking Payment Timeout | Nếu booking chưa thanh toán sau booking_payment_timeout_minutes, hệ thống tự động hủy booking. |
| Booking Check-in Grace | Nếu khách không check-in trong 45 phút sau giờ booking hoặc sau checkin_grace_minutes được cấu hình tương ứng, booking bị hủy và không hoàn Deposit Fee. |
| Overtime After Booking | Nếu khách check-out trễ hơn thời gian booking, phần phát sinh tính theo block pricing của bảng giá vãng lai. |
| Time Rounding | Nếu thời gian phát sinh nhỏ hơn hoặc bằng grace_period_minutes thì không tính block mới; nếu lớn hơn thì tính block mới. |
| Cash Rounding | Thanh toán tiền mặt làm tròn theo cash_rounding_unit và rounding_threshold. |
| Online Rounding | Thanh toán online không làm tròn. |
| Lost Card Penalty | Khi Staff xử lý Card bị mất, khách phải trả phí gửi xe hiện tại và `lost_card_penalty`; Card giữ trạng thái `LOST` cho đến khi Manager xử lý. |
| Wrong Zone Penalty | Xe đỗ sai khu vực có thể bị áp dụng wrong_zone_penalty khi được Staff xác nhận. |
| Configurable Variables Storage | Các biến giá tiền, timeout, grace period, penalty và rounding được cấu hình động ở tầng nghiệp vụ/application/admin configuration; không tạo thêm table riêng trong physical model hiện tại. |

---
