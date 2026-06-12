# 7. Finalized Policy Decisions

| Policy Area                                    | Final Decision                                                                                                                                 |
|------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| Check-in Allocation                            | Khi check-in, xe máy được gợi ý Zone còn capacity; ô tô Walk-in/Booking chỉ được gợi ý Zone thuộc nhóm `GENERAL`, chưa gán Slot tại thời điểm gợi ý; ô tô thẻ tháng dùng Slot đã cấp trong Zone `MONTHLY`. |
| Monthly Subscription - Motorcycle              | Xe máy thẻ tháng được đảm bảo có chỗ bằng capacity động: mỗi subscription `ACTIVE` làm giảm capacity Walk-in/Booking một đơn vị. |
| Monthly Subscription - Car                     | Ô tô thẻ tháng được cấp Slot riêng trong Zone `MONTHLY`; quyền giữ Slot nằm ở `monthly_subscription.assigned_slot_id`. |
| Monthly Subscription - Vehicle Binding         | Mỗi vé tháng chỉ áp dụng cho một xe đã đăng ký; không dùng `max_registered_plate`.                                                             |
| Card vs Monthly Subscription                   | Card có `card_type = NORMAL, MONTHLY`; Card `MONTHLY` gắn với Monthly Subscription, nhưng quyền lợi tháng vẫn do Monthly Subscription hợp lệ quyết định. |
| Booking - Motorcycle                           | Xe máy booking phải chọn Building và Vehicle/biển số; Driver không chọn Zone/Slot, hệ thống gán Zone khi check-in. |
| Booking - Car                                  | Ô tô booking phải chọn Building và Vehicle/biển số; Driver không chọn Zone/Slot; Booking chỉ giữ general car capacity tại Building, actual Zone/Slot được xác định khi check-in/đỗ xe. |
| Booking Time Limit                             | Booking phải được đặt trước tối thiểu 1 tiếng và tối đa 8 tiếng tính từ thời điểm thanh toán cọc thành công.                                   |
| Booking Deposit                                | Deposit Fee bằng Base Price của block đầu tiên theo Booking Policy áp dụng tại thời điểm Booking được tạo và thanh toán; Payment deposit lưu `pricing_policy_id` của Booking Policy và không tính lại Deposit khi xe check-in. |
| Booking Cancellation                           | Khách hủy trước giờ booking ít nhất 1 tiếng thì được hoàn cọc.                                                                                 |
| Booking No-show                                | Nếu `current_time > scheduled_check_in_time + checkin_grace_minutes` và Booking chưa được dùng để check-in, Booking chuyển sang `EXPIRED`, Deposit Fee không hoàn, general capacity được giải phóng và không cập nhật `slot_status`. |
| Early Arrival                                  | Nếu khách đến sớm hơn giờ booking, hệ thống cho check-in sớm nếu còn chỗ phù hợp; thời gian gửi xe bắt đầu tính từ lúc khách thực tế check-in. |
| Booking Final Payment                          | Số tiền còn phải trả bằng `max(0, total_actual_fee - paid_deposit_fee)`, trong đó `total_actual_fee = actual_parking_fee + penalties + surcharges`; chỉ Deposit Payment `PAID`, đúng Booking, chưa `REFUNDED` và chưa khấu trừ lần khác mới được trừ. |
| Booking Deposit Exceeds Actual Fee             | Nếu Deposit Fee lớn hơn Tổng phí thực tế, `amount_due = 0`, Driver không phải thanh toán thêm và check-out tiếp tục theo flow hiện tại; hệ thống không hoàn phần chênh lệch, không lưu/xử lý riêng phần chênh lệch, không tạo credit/wallet/account balance, không tạo Payment âm và không thay đổi Deposit Payment đã tồn tại. |
| Overtime After Booking                         | Overtime có thể hiển thị như breakdown riêng nhưng không được tính hai lần; tổng phí được tính một lần trên thời gian thực tế theo Pricing Window. |
| Pricing Model | Hệ thống tính phí theo từng Pricing Window; session được chia tại ranh giới Pricing Window và mỗi đoạn áp dụng Base Duration, Base Price, Increment Block, Increment Price, Grace Period và Window Cap. |
| Window Cap | `window_cap` áp dụng cho từng đoạn/lần xuất hiện của Pricing Window tương ứng, không áp dụng cho toàn bộ Parking Session. |
| 24/7 Session | Hệ thống không reset session khi qua ngày mới. |
| Pricing Policy at Check-in | Pricing Policy của Parking Session được xác định bằng Vehicle Type và `check_in_time`; không bắt buộc lưu `pricing_policy_id` trên Parking Session; không xác định lại theo `check_out_time` hoặc khi Manager kích hoạt policy mới. |
| Pricing Policy Scope | Pricing Policy có phạm vi toàn hệ thống theo Vehicle Type trong phiên bản hiện tại; không cấu hình riêng theo Building và `pricing_policy` không có `building_id`. |
| Pricing Policy Timeline | Với cùng Vehicle Type, các khoảng `effective_start` - `effective_end` không được overlap; tại một thời điểm chỉ có tối đa một Policy áp dụng; khoảng trống được phép tồn tại nhưng check-in trong khoảng trống bị từ chối. |
| Pricing Policy Lock | Policy đã `ACTIVE` hoặc đã từng được dùng để chấp nhận Parking Session không được sửa Vehicle Type, Pricing Window, Base Duration, Base Price, Increment Block, Increment Price, Grace Period, Window Cap hoặc dữ liệu làm đổi kết quả tính phí. |
| Pricing Policy Effective Time Edit | `effective_start` của Policy `ACTIVE` không được đổi; Policy tương lai có thể đổi `effective_start` nếu vẫn hợp lệ. `effective_end` của Policy tương lai hoặc `ACTIVE` có thể đổi nếu ở tương lai, không overlap và không làm mất hiệu lực hồi tố; Policy `EXPIRED` không được đổi `effective_end`. |
| Pricing Window Coverage | Mỗi Pricing Policy phải có ít nhất một Pricing Window, các Window không overlap và phủ đủ 24 giờ; cho phép Window đi qua nửa đêm. |
| Pricing Window Short Segment | Nếu Parking Session có một đoạn thời gian lớn hơn 0 trong một Pricing Window, đoạn đó chịu ít nhất Base Price của Window tương ứng. |
| Pricing Window Boundary | Thời điểm bắt đầu Window thuộc Window đó; thời điểm kết thúc Window thuộc Window tiếp theo; đoạn thời lượng bằng 0 không tính phí. |
| Monthly Subscription Downgrade | Nếu vé tháng hết hạn và xe vẫn còn trong bãi, hệ thống downgrade và tính phí vãng lai từ thời điểm hết hạn. |
| Monthly Subscription Checkout Evaluation | Quyền lợi Monthly Subscription trong một lần checkout được đánh giá tại `check_out_time` đã ghi nhận; nếu Payment Pending kéo dài qua `expired_at`, hệ thống không tính thêm phí sau `check_out_time`. |
| System State Management | Trạng thái hệ thống được quản lý bằng các status riêng cho Building, Floor, Zone, Slot, Vehicle, Card, Parking Session, Booking, Incident, Monthly Subscription và Pricing Policy. |
| Booking Payment Timeout | Nếu booking chưa thanh toán sau `booking_payment_timeout_minutes`, Booking hết hiệu lực hoặc bị hủy theo status model; general capacity đã tạm giữ được giải phóng và không cập nhật `slot_status`. |
| Monthly Subscription Payment Timeout | Thanh toán online khi đăng ký/gia hạn Monthly Subscription dùng `monthly_subscription_payment_timeout_minutes`; nếu hết timeout mà Payment chưa `PAID`, Payment chuyển `FAILED` và subscription không được kích hoạt/gia hạn. |
| Booking Check-in Grace | `checkin_grace_minutes` là biến cấu hình động; không hard-code giá trị mặc định trong SRS. |
| Monthly Zero Checkout | Với Monthly Subscription còn `ACTIVE`, đúng Vehicle/biển số, đúng Building, không downgrade, không phụ phí/phí phạt, `actual_parking_fee = 0` và `amount_due = 0`; hệ thống bỏ qua bước thu tiền nhưng vẫn hoàn tất toàn bộ logic check-out. |
| Payment Status Semantics | `PENDING` nghĩa là quy trình thanh toán còn mở; `FAILED` chỉ khi quy trình đã kết thúc không thành công; `PAID` chỉ khi ngân hàng hoặc Staff xác nhận đã nhận tiền. |
| Payment Source | Mỗi Payment phải có đúng một nguồn nghiệp vụ chính trong `session_id`, `booking_id`, `monthly_subscription_id`. |
| Checkout Time Capture | Hệ thống ghi nhận `check_out_time` ngay khi Driver tap Card hoặc Staff tiếp nhận yêu cầu check-out; phí tính từ `check_in_time` đến `check_out_time` đã ghi nhận. |
| Checkout Pending Payment | Nếu Payment vẫn `PENDING`, Parking Session chưa `COMPLETED`, Zone/Slot/Card chưa được giải phóng và phí không tăng thêm trong cùng quá trình thanh toán. |
| Checkout Payment Timeout | Checkout Payment không có automatic timeout; Payment giữ `PENDING` cho đến khi `PAID`, Driver hủy, Staff xác nhận không thể tiếp tục hoặc Staff kết thúc/đánh dấu `FAILED`. |
| Payment Retry | Payment đã `FAILED` không được đổi lại `PENDING`; lần thử mới tạo Payment mới, giữ Payment cũ để audit và không được có hai Payment `PAID` cho cùng một nghĩa vụ thanh toán. |
| Change Payment Method During Checkout | Đổi từ online sang tiền mặt hoặc ngược lại trong cùng lần checkout giữ nguyên `check_out_time`, không tính lại phí và không tạo lần check-out mới. |
| Checkout Failed Rollback | Một Payment cụ thể có thể `FAILED` mà checkout vẫn tiếp tục; chỉ khi toàn bộ checkout bị kết thúc `FAILED`/bị hủy, hệ thống rollback nghiệp vụ của lần check-out, hủy `check_out_time`, giữ session tiếp tục đang gửi và giữ Payment `FAILED` để audit. |
| Time Rounding | Nếu thời gian phát sinh nhỏ hơn hoặc bằng grace_period_minutes thì không tính block mới; nếu lớn hơn thì tính block mới. |
| Cash Rounding | Thanh toán tiền mặt làm tròn theo cash_rounding_unit và rounding_threshold trên số tiền cuối cùng sau phí gửi xe, phụ phí/phí phạt và Booking Deposit hợp lệ. |
| Online Rounding | Thanh toán online không làm tròn. |
| Lost Card Penalty | Khi Staff xử lý Card bị mất, khách phải trả phí gửi xe hiện tại và `lost_card_penalty`; Card giữ trạng thái `LOST` cho đến khi Manager xử lý. |
| Wrong Zone Penalty | Xe đỗ sai khu vực có thể bị áp dụng wrong_zone_penalty khi được Staff xác nhận. |
| Configurable Variables Storage | Các biến giá tiền, timeout, grace period, penalty và rounding được cấu hình động ở tầng nghiệp vụ/application/admin configuration; không tạo thêm table riêng trong physical model hiện tại. |

---

## Pricing Window Multi-day Example

Ví dụ minh họa dựa trên một cấu hình bảng giá giả định.

Xe đậu từ 20:00 ngày 14/02 đến 12:00 ngày 16/02.

Hệ thống chia thành:

1. Night Window: 20:00 ngày 14/02 → 06:00 ngày 15/02.
2. Day Window: 06:00 ngày 15/02 → 18:00 ngày 15/02.
3. Night Window: 18:00 ngày 15/02 → 06:00 ngày 16/02.
4. Day Window: 06:00 ngày 16/02 → 12:00 ngày 16/02.

Mỗi đoạn được tính theo rule của Pricing Window tương ứng và áp dụng `window_cap` của chính đoạn đó.

Parking Session vẫn là một session duy nhất và không bị reset tại 00:00 hoặc khi chuyển sang ngày mới.
