# 6. Business Rules

## 6.1 Parking Structure Rules

| Rule ID | Rule                                                                |
|---------|---------------------------------------------------------------------|
| BR-001  | Building có thể có nhiều Floor.                                     |
| BR-002  | Floor có thể có nhiều Zone.                                         |
| BR-003  | Zone có thể dùng cho xe máy hoặc ô tô tùy cấu hình.                 |
| BR-004  | Xe máy được quản lý theo sức chứa của Zone.                         |
| BR-005  | Ô tô được quản lý theo từng Slot.                                   |
| BR-006  | Zone/Slot bị khóa hoặc bảo trì không được dùng để check-in/booking. |
| BR-007  | Zone ô tô phải phân loại `zone_access_type` là `GENERAL` hoặc `MONTHLY`. |
| BR-008  | Slot status chỉ phản ánh trạng thái vật lý/vận hành, không phản ánh quyền giữ Slot tháng. |

---

## 6.2 Hardware Simulation Rules

| Rule ID   | Rule                                  | Cách xử lý                                                                          |
|-----------|---------------------------------------|-------------------------------------------------------------------------------------|
| BR-HW-001 | Hệ thống không có camera thật.        | Biển số được nhập thủ công trên web.                                                |
| BR-HW-002 | Hệ thống không có thẻ vật lý thật.    | Dùng mã gửi xe/mã thẻ mô phỏng.                                                     |
| BR-HW-003 | Hệ thống không có cảm biến slot thật. | Trạng thái Zone/Slot được cập nhật qua thao tác check-in/check-out/booking/manager. |
| BR-HW-004 | Hệ thống không có barrier thật.       | Trạng thái cho phép ra/vào chỉ là trạng thái nghiệp vụ trong hệ thống.              |

---

## 6.3 Driver Account & Vehicle Rules

| Rule ID    | Rule                                                                        | Cách xử lý                                                         |
|------------|-----------------------------------------------------------------------------|--------------------------------------------------------------------|
| BR-ACC-001 | Tài khoản Driver có thể được tạo mà chưa cần đăng ký xe.                    | Cho phép account tồn tại với danh sách xe rỗng.                    |
| BR-ACC-002 | Một Driver Account có thể có nhiều xe.                                      | Driver được thêm nhiều biển số/xe vào tài khoản.                   |
| BR-ACC-003 | Xe có thể được thêm sau khi tài khoản đã được tạo.                          | Cung cấp chức năng Add Vehicle.                                    |
| BR-ACC-004 | Khi booking hoặc đăng ký thẻ tháng, hệ thống bắt buộc có thông tin biển số. | Nếu tài khoản chưa có xe, yêu cầu thêm xe hoặc nhập biển số trước. |

---

## 6.4 Parking Allocation Rules

| Rule ID      | Rule                                                                                                   | Cách xử lý                                                                     |
|--------------|--------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| BR-ALLOC-001 | Khi check-in, xe máy được gợi ý theo Zone/Area còn chỗ.                                                | Hệ thống tìm Zone còn capacity phù hợp.                                        |
| BR-ALLOC-002 | Khi check-in, ô tô Walk-in/Booking chỉ được gợi ý Zone thuộc nhóm `GENERAL`, chưa gán Slot tại thời điểm gợi ý. | Hệ thống kiểm tra general capacity và Zone `GENERAL` phù hợp. |
| BR-ALLOC-003 | Slot thực tế của ô tô Walk-in/Booking chỉ được ghi nhận sau khi xe đã đậu. | Staff hoặc hệ thống cập nhật `parking_session.slot_id`; Slot phải thuộc Zone `GENERAL` đã gợi ý, phù hợp loại xe, không bị khóa/bảo trì và chưa có xe khác chiếm dụng. |
| BR-ALLOC-004 | Ô tô thẻ tháng được gán theo Slot riêng đã cấp trong Zone `MONTHLY`. | Hệ thống dùng `monthly_subscription.assigned_slot_id`. |
| BR-ALLOC-005 | Zone/Slot đang bảo trì hoặc tạm khóa không được phân bổ.                                               | Hệ thống loại khỏi danh sách gợi ý.                                            |
| BR-ALLOC-006 | Walk-in/Booking không được dùng capacity hoặc Slot đã bảo vệ cho Monthly Subscription. | Xe máy trừ active motorcycle subscriptions khỏi general capacity; ô tô general chỉ dùng Zone `GENERAL`. |
| BR-ALLOC-007 | Hệ thống phải tránh trạng thái lấp đầy 100% nếu điều đó làm mất quyền đảm bảo chỗ của khách thẻ tháng. | Khi capacity chạm ngưỡng giới hạn, hệ thống từ chối nhận thêm walk-in/booking. |

---

## 6.5 Booking Rules

| Rule ID     | Rule                                                                                                  | Cách xử lý                                                                                 |
|-------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|
| BR-BOOK-001 | Booking bắt buộc nhập biển số xe.                                                                     | Nếu thiếu biển số, không cho tạo booking.                                                  |
| BR-BOOK-002 | Booking chỉ được xác nhận sau khi thanh toán Deposit Fee thành công. | Trạng thái ban đầu là `PENDING`; sau khi Payment `PAID` chuyển thành `CONFIRMED`. |
| BR-BOOK-003 | Booking phải được đặt trước tối thiểu 1 tiếng tính từ thời điểm thanh toán cọc thành công.            | Nếu thời gian dự kiến vào bãi nhỏ hơn 1 tiếng sau khi thanh toán, hệ thống từ chối booking. |
| BR-BOOK-004 | Booking chỉ được đặt trước tối đa 8 tiếng tính từ thời điểm thanh toán cọc thành công.                | Nếu thời gian dự kiến vào bãi lớn hơn 8 tiếng sau khi thanh toán, hệ thống từ chối booking. |
| BR-BOOK-005 | Booking phải có thời gian dự kiến check-in và check-out hoặc thời lượng gửi xe dự kiến. | Hệ thống dùng thông tin này để kiểm tra thời gian đặt trước và tính Deposit Fee. |
| BR-BOOK-006 | Deposit Fee bằng Base Price của block đầu tiên theo Booking Policy áp dụng tại thời điểm Booking được tạo và thanh toán. | Payment Booking Deposit lưu đúng số tiền deposit đã yêu cầu, `pricing_policy_id` của Booking Policy và `booking_id` là nguồn duy nhất. |
| BR-BOOK-007 | Booking phải chọn Building trước. | Hệ thống kiểm tra general capacity trong Building đã chọn. |
| BR-BOOK-008 | Xe máy booking không được cho Driver chọn Zone hoặc Slot. | Hệ thống giữ một general motorcycle capacity unit ở Building và gán Zone khi check-in. |
| BR-BOOK-009 | Ô tô booking không được cho Driver chọn Zone hoặc Slot. | Hệ thống giữ general car capacity ở Building; Zone `GENERAL` được gợi ý khi check-in và Slot thực tế được ghi nhận sau khi xe đậu. |
| BR-BOOK-010 | Booking không được dùng phần capacity/slot đã giữ cho nhóm thẻ tháng. | Hệ thống trừ active motorcycle subscriptions khỏi motorcycle general capacity và loại Zone `MONTHLY` khỏi car booking. |
| BR-BOOK-011 | Nếu khách hủy booking trước giờ booking ít nhất 1 tiếng, khách được hoàn cọc. | Hệ thống chuyển booking sang `CANCELLED` và ghi nhận `REFUNDED` cho khoản cọc đủ điều kiện. |
| BR-BOOK-012 | Nếu khách hủy booking trễ hơn thời hạn được hoàn cọc, khách mất cọc. | Hệ thống chuyển booking sang `CANCELLED` và giữ cọc. |
| BR-BOOK-013 | Nếu khách không check-in trước `scheduled_check_in_time + checkin_grace_minutes`, booking no-show chuyển sang `EXPIRED` và mất cọc. | Hệ thống giải phóng general capacity đã giữ ở Building và không cập nhật `slot_status`. |
| BR-BOOK-014 | Nếu khách đến đúng hạn hoặc trong thời gian trễ cho phép, booking được dùng để tạo parking session. | Hệ thống chuyển booking thành session khi check-in thành công. |
| BR-BOOK-015 | Nếu khách đến sớm hơn giờ booking, hệ thống cho phép check-in sớm nếu còn chỗ phù hợp. | Thời gian gửi xe bắt đầu tính từ thời điểm check-in thực tế. |
| BR-BOOK-016 | Khi check-out booking, số tiền còn phải trả được tính bằng Tổng phí thực tế trừ Deposit Fee đã thanh toán. | Tổng phí thực tế = phí gửi xe + phụ phí/phí phạt; `amount_due = max(0, total_actual_fee - paid_deposit_fee)`. |
| BR-BOOK-017 | Nếu khách gửi vượt quá thời gian đã đặt, phần overtime không được tính hai lần. | Có thể hiển thị overtime như breakdown riêng, nhưng tổng phí được tính một lần trên thời gian thực tế theo Pricing Window. |
| BR-BOOK-018 | Một biển số không nên có nhiều booking active trong cùng khoảng thời gian. | Hệ thống kiểm tra trùng lịch booking theo biển số. |
| BR-BOOK-019 | Booking chưa thanh toán trong `booking_payment_timeout_minutes` sẽ hết hiệu lực hoặc bị hủy theo status model. | Hệ thống giải phóng general capacity đã tạm giữ ở Building và không cập nhật `slot_status`. |
| BR-BOOK-020 | Sau khi thanh toán booking thành công, general capacity được giữ cho booking ở mức Building. | Không lưu Driver-selected Zone/Slot và không cập nhật `slot_status` cho Booking. |
| BR-BOOK-021 | Chỉ Deposit Payment đủ điều kiện mới được khấu trừ khi check-out. | Payment phải `PAID`, thuộc đúng Booking đã tạo Parking Session, chưa `REFUNDED` và chưa được dùng/khấu trừ hai lần. |
| BR-BOOK-022 | Booking Deposit không được tính lại khi Pricing Policy thay đổi trước check-in. | Session dùng Policy áp dụng tại `check_in_time`; khi check-out chỉ trừ đúng Deposit thực tế đã `PAID` theo Booking Policy. |
| BR-BOOK-023 | Nếu Deposit Fee lớn hơn Tổng phí thực tế khi check-out, `amount_due = 0` và Driver không phải thanh toán thêm. | Check-out tiếp tục theo flow hiện tại; không hoàn phần chênh lệch, không lưu/xử lý riêng phần chênh lệch, không tạo credit/wallet/account balance, không tạo Payment âm và không thay đổi Deposit Payment đã tồn tại. |

---

## 6.6 Monthly Subscription Rules

| Rule ID      | Rule                                                                              | Cách xử lý                                                                                 |
|--------------|-----------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|
| BR-MONTH-001 | Mỗi Monthly Subscription xe máy `ACTIVE` giữ một capacity unit tại Building đã đăng ký. | Hệ thống trừ số active motorcycle subscriptions khỏi capacity Walk-in/Booking xe máy. |
| BR-MONTH-002 | Thẻ tháng xe máy không phân theo Zone/Slot cụ thể. | Khi xe vào bãi, hệ thống gợi ý Zone còn chỗ và dùng capacity tháng đã giữ. |
| BR-MONTH-003 | Thẻ tháng ô tô được cấp Slot riêng trong Zone `MONTHLY`. | Hệ thống gắn xe ô tô thẻ tháng với `monthly_subscription.assigned_slot_id`. |
| BR-MONTH-004 | Thẻ tháng không giới hạn số lượt vào/ra mỗi ngày.                                 | Cho phép nhiều parking session trong ngày nếu thẻ còn hiệu lực và không có session đang mở. |
| BR-MONTH-005 | Một xe không được có nhiều thẻ tháng còn hiệu lực trùng thời gian.                | Hệ thống kiểm tra biển số trước khi đăng ký/gia hạn.                                       |
| BR-MONTH-006 | Thẻ tháng chỉ được kích hoạt sau khi thanh toán thành công.                       | Nếu chưa thanh toán, trạng thái là Pending.                                         |
| BR-MONTH-007 | Không được cấp thêm thẻ tháng xe máy nếu active motorcycle subscriptions đã dùng hết capacity usable. | Hệ thống từ chối đăng ký và báo hết capacity tháng xe máy. |
| BR-MONTH-008 | Không được cấp thêm thẻ tháng ô tô nếu không còn Slot usable trong Zone `MONTHLY`. | Hệ thống từ chối đăng ký và báo hết slot thẻ tháng ô tô. |
| BR-MONTH-009 | Capacity/Slot dành cho thẻ tháng phải được bảo vệ khỏi Walk-in và Booking. | Xe máy dùng công thức dynamic capacity; ô tô dùng Zone `GENERAL`/`MONTHLY` cố định. |
| BR-MONTH-010 | Vé tháng áp dụng cho biển số đăng ký cố định. | Hệ thống kiểm tra biển số khi check-in để xác định quyền lợi vé tháng. |
| BR-MONTH-011 | Vé tháng được thanh toán trả trước theo chu kỳ. | Vé chỉ active sau khi thanh toán thành công. |
| BR-MONTH-012 | Vé tháng có số ngày hiệu lực cấu hình. | Hệ thống xác định ngày bắt đầu và ngày hết hạn. |
| BR-MONTH-013 | Mỗi vé tháng chỉ áp dụng cho một xe đã đăng ký. | Không cho dùng cùng một vé tháng cho nhiều xe. |
| BR-MONTH-014 | Hệ thống quét vé tháng hết hạn lúc 00:00 mỗi ngày. | Nếu vé hết hạn và xe vẫn còn trong bãi, hệ thống downgrade. |
| BR-MONTH-015 | Khi vé tháng bị downgrade, xe bắt đầu bị tính phí vãng lai từ thời điểm hết hạn. | Áp dụng bảng giá vãng lai cho đến khi xe rời bãi. |
| BR-MONTH-016 | Sau khi gia hạn thành công, quyền lợi vé tháng được kích hoạt lại ngay lập tức. | Hệ thống cập nhật lại trạng thái active. |
| BR-MONTH-017 | Monthly Subscription phải gán với một Card `MONTHLY` khi quyền lợi được cấp. | `monthly_subscription.assigned_card_id` trỏ tới Card `MONTHLY`; Card này không trở về `AVAILABLE` sau mỗi check-out. |
| BR-MONTH-018 | Monthly Subscription `PENDING` có thể chưa có thời điểm kích hoạt/hết hạn. | `activated_at` và `expired_at` được phép `NULL` cho đến khi thanh toán thành công. |
| BR-MONTH-019 | Card type không tự cấp quyền lợi tháng. | Quyền lợi tháng chỉ áp dụng khi có Monthly Subscription hợp lệ cùng Vehicle và Building. |
| BR-MONTH-020 | Slot tháng ô tô không được biểu diễn bằng `slot_status`. | Quyền giữ Slot nằm ở `monthly_subscription.assigned_slot_id`; `slot_status` chỉ phản ánh AVAILABLE/OCCUPIED/BLOCKED/MAINTENANCE. |
| BR-MONTH-021 | Thanh toán online khi đăng ký hoặc gia hạn Monthly Subscription dùng timeout cấu hình `monthly_subscription_payment_timeout_minutes`. | Nếu hết timeout mà Payment chưa `PAID`, Payment chuyển `FAILED`, hồ sơ chờ không được kích hoạt/gia hạn và không giữ capacity/Slot như subscription `ACTIVE`. |
| BR-MONTH-022 | Gia hạn Monthly Subscription chưa thanh toán thành công không được thay đổi quyền lợi hiện tại. | Quyền lợi hiện tại chỉ thay đổi sau khi Payment gia hạn `PAID`. |
| BR-MONTH-023 | Quyền lợi Monthly Subscription trong một lần checkout được đánh giá tại `check_out_time` đã ghi nhận khi bắt đầu checkout. | Không đánh giá lại theo thời điểm Payment hoàn tất; Payment Pending sau `check_out_time` không làm phát sinh thêm phí. |
| BR-MONTH-024 | Nếu `expired_at <= check_out_time`, phần từ `expired_at` đến `check_out_time` được tính theo giá Walk-in. | Nếu checkout Failed và rollback, lần checkout sau ghi nhận mốc mới và đánh giá lại quyền lợi theo mốc mới. |

---

## 6.7 Payment Rules

| Rule ID    | Rule                                                                                                               | Cách xử lý                                                                |
|------------|--------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| BR-PAY-001 | Hệ thống hỗ trợ thanh toán tiền mặt.                                                                               | Staff xác nhận đã thu tiền trên hệ thống.                                 |
| BR-PAY-002 | Hệ thống hỗ trợ thanh toán online thật qua ngân hàng.                                                              | Hệ thống nhận kết quả thanh toán từ kênh ngân hàng/payment gateway.       |
| BR-PAY-003 | Hệ thống không hỗ trợ thanh toán bằng thẻ.                                                                         | Không hiển thị phương thức card payment.                                  |
| BR-PAY-004 | Booking phải thanh toán Deposit Fee trước. | Booking chỉ confirmed khi Deposit Fee được thanh toán thành công.         |
| BR-PAY-005 | Nếu booking no-show chuyển sang `EXPIRED`, khách mất cọc. | Không hoàn cọc cho no-show theo `scheduled_check_in_time + checkin_grace_minutes`. |
| BR-PAY-006 | Check-out chỉ phải xác nhận thanh toán khi `amount_due > 0`. | Nếu `amount_due = 0`, hệ thống bỏ qua bước thu tiền nhưng vẫn hoàn tất toàn bộ logic check-out. |
| BR-PAY-007 | Thẻ tháng chỉ active sau khi thanh toán thành công.                                                                | Nếu payment failed, thẻ tháng không có hiệu lực.                          |
| BR-PAY-008 | Nếu khách hủy booking trước giờ booking ít nhất 1 tiếng, khách được hoàn cọc.                                      | Hệ thống tạo trạng thái Refund/Refunded cho khoản cọc.                    |
| BR-PAY-009 | Nếu khách đã đặt cọc booking, khi check-out hệ thống trừ cọc khỏi tổng phí cần thanh toán theo chính sách booking. | Khách chỉ thanh toán phần còn lại hoặc phần phát sinh nếu có.             |
| BR-PAY-010 | Booking chưa thanh toán trong `booking_payment_timeout_minutes` sẽ hết hiệu lực hoặc bị hủy theo status model. | Hệ thống giải phóng general capacity đã giữ ở Building và không đổi `slot_status`. |
| BR-PAY-011 | Thanh toán tiền mặt áp dụng cash rounding rule nếu cần. | Làm tròn theo cash_rounding_unit và rounding_threshold.                   |
| BR-PAY-012 | Thanh toán online không làm tròn. | Giữ nguyên giá trị chính xác của giao dịch.                               |
| BR-PAY-013 | Nếu cần hoàn Deposit Fee, hệ thống ghi nhận trạng thái refund/refunded. | Áp dụng cho các trường hợp được hoàn theo chính sách booking.             |
| BR-PAY-014 | Payment phải có đúng một nguồn nghiệp vụ chính. | `COUNT_NON_NULL(session_id, booking_id, monthly_subscription_id) = 1`. |
| BR-PAY-015 | Một lần thử giao dịch online bị từ chối không nhất thiết làm Payment `FAILED`. | Nếu payment session còn hiệu lực và Driver còn có thể thử lại, Payment vẫn `PENDING`. |
| BR-PAY-016 | Payment chỉ chuyển `FAILED` khi quy trình thanh toán đã kết thúc không thành công. | Booking Payment và Monthly Subscription Payment có timeout riêng; Checkout Payment không tự động chuyển `FAILED` chỉ vì chờ quá một khoảng thời gian cố định. |
| BR-PAY-017 | Payment chỉ chuyển `PAID` khi ngân hàng hoặc Staff xác nhận đã nhận tiền thành công. | Không cập nhật `PAID` chỉ dựa trên việc tạo yêu cầu thanh toán. |
| BR-PAY-018 | Checkout Payment `PENDING` không làm Parking Session `COMPLETED`. | Zone/Slot, Card và general capacity chưa được giải phóng; phí giữ theo `check_out_time` đã ghi nhận. |
| BR-PAY-019 | Checkout Payment `FAILED` phải được giữ để audit. | Khi rollback nghiệp vụ của lần check-out, không xóa Payment thất bại. |
| BR-PAY-020 | Checkout Payment không có automatic timeout. | Checkout Payment giữ `PENDING` cho đến khi `PAID`, Driver hủy, Staff xác nhận không thể tiếp tục, hoặc Staff kết thúc/đánh dấu lần thanh toán là `FAILED`. |
| BR-PAY-021 | Một Payment `FAILED` không được đổi ngược về `PENDING`. | Nếu Driver thử thanh toán lại, hệ thống giữ Payment cũ để audit và tạo Payment mới có đúng một nguồn nghiệp vụ. |
| BR-PAY-022 | Một Payment cụ thể có thể `FAILED` nhưng toàn bộ lần checkout vẫn tiếp tục. | Hệ thống có thể tạo Payment mới trong cùng lần checkout và giữ nguyên `check_out_time` đã ghi nhận. |
| BR-PAY-023 | Không được ghi nhận hai Payment `PAID` cho cùng một nghĩa vụ thanh toán. | Các lần retry tạo Payment mới nhưng chỉ một Payment được hoàn tất `PAID` cho nghĩa vụ đó. |
| BR-PAY-024 | Đổi phương thức thanh toán trong cùng lần checkout không tạo lần check-out mới. | Giữ nguyên `check_out_time`, không tính lại phí theo thời điểm đổi phương thức và không giải phóng Zone/Slot/Card khi chưa thanh toán thành công. |

---

## 6.8 Fee Calculation Rules

| Rule ID | Rule | Cách xử lý |
|---|---|---|
| BR-FEE-001 | Phí gửi xe phụ thuộc vào loại xe. | Hệ thống áp dụng bảng giá theo vehicle_type. |
| BR-FEE-002 | Phí gửi xe được tính theo thời gian thực tế sử dụng. | Hệ thống tính từ check-in time đến check-out time. |
| BR-FEE-003 | Hệ thống áp dụng mô hình pricing window. | Mỗi khung giờ có bảng giá riêng. |
| BR-FEE-004 | Mỗi pricing window có base duration và base price. | Nếu thời gian nằm trong base duration, áp dụng base price. |
| BR-FEE-005 | Sau khi vượt base duration, hệ thống tính thêm theo increment block. | Mỗi block phát sinh áp dụng increment price. |
| BR-FEE-006 | Nếu session đi qua nhiều pricing window hoặc nhiều ngày, hệ thống tách session theo từng window occurrence. | Mỗi đoạn thời gian được tính theo bảng giá riêng; Parking Session không reset khi qua ngày mới. |
| BR-FEE-007 | Window Cap chỉ áp dụng cho từng đoạn/lần xuất hiện của pricing window tương ứng. | Không dùng window cap để giới hạn toàn bộ session. |
| BR-FEE-008 | Hệ thống vận hành 24/7 và không reset session khi qua ngày mới. | Session tiếp tục được tính theo pricing window của ngày tiếp theo. |
| BR-FEE-009 | Deposit Fee của booking bằng Base Price của block đầu tiên theo Booking Policy tại thời điểm Booking được tạo và thanh toán. | Nếu Policy áp dụng lúc check-in khác Booking Policy, Deposit không bị tính lại. |
| BR-FEE-010 | Booking final payment khấu trừ Deposit Fee đã thanh toán khỏi Tổng phí thực tế. | Tổng phí thực tế = phí gửi xe + phụ phí/phí phạt; `amount_due = max(0, total_actual_fee - paid_deposit_fee)`. |
| BR-FEE-011 | Nếu thời gian phát sinh nhỏ hơn hoặc bằng grace_period_minutes, hệ thống không tính block mới. | Không cộng increment price cho phần thời gian này. |
| BR-FEE-012 | Nếu thời gian phát sinh lớn hơn grace_period_minutes, hệ thống tính thành block mới. | Cộng thêm increment price tương ứng. |
| BR-FEE-013 | Thanh toán tiền mặt áp dụng cash rounding cho số tiền cuối cùng sau phí gửi xe, phụ phí/phí phạt và Booking Deposit hợp lệ. | Hệ thống làm tròn theo cash_rounding_unit và rounding_threshold. |
| BR-FEE-014 | Thanh toán online không làm tròn. | Hệ thống giữ nguyên giá trị chính xác. |
| BR-FEE-015 | Nếu có phí phạt hoặc phụ phí, hệ thống cộng vào tổng phí trước khi thanh toán. | Áp dụng lost card penalty, wrong zone penalty hoặc phí phát sinh khác nếu có. |
| BR-FEE-016 | Phần overtime của Booking không được tính hai lần. | Có thể hiển thị overtime như breakdown riêng, nhưng tổng phí phải được tính một lần trên thời gian thực tế theo Pricing Window. |
| BR-FEE-017 | Monthly Subscription còn hiệu lực có phí gửi xe bằng 0 nếu không có downgrade, phụ phí hoặc phí phạt. | `actual_parking_fee = 0` và `amount_due = 0`; bỏ qua bước thu tiền. |
| BR-FEE-018 | Nếu Monthly Subscription hết hạn trong lúc xe đang ở trong bãi, chỉ phần thời gian sau khi hết hạn bị tính phí Walk-in. | Khoảng thời gian trước khi hết hạn được quyền lợi tháng bao phủ. |
| BR-FEE-019 | Pricing Policy của Parking Session được xác định bằng Vehicle Type và `check_in_time`. | Không bắt buộc lưu `pricing_policy_id` trên `parking_session`; check-out tìm lại Policy áp dụng tại `check_in_time`, không chọn theo `check_out_time`. |
| BR-FEE-020 | Pricing Policy có phạm vi toàn hệ thống theo Vehicle Type trong phiên bản hiện tại. | Không cấu hình `pricing_policy` theo Building và không có `pricing_policy.building_id`. |
| BR-FEE-021 | Mỗi đoạn thời gian lớn hơn 0 trong một Pricing Window phải chịu ít nhất Base Price của Window đó. | Không có minimum duration, free transition, dominant window hoặc sharing Base Price giữa hai Window. |
| BR-FEE-022 | Grace Period chỉ áp dụng cho phần dư sau các Increment Block đầy đủ. | Grace Period không miễn hoặc loại bỏ Base Price của một đoạn Pricing Window. |
| BR-FEE-023 | Ranh giới Pricing Window không được tính trùng. | Thời điểm bắt đầu thuộc Window đó, thời điểm kết thúc thuộc Window tiếp theo; đoạn thời lượng bằng 0 không tính phí. |
| BR-FEE-024 | Check-in chỉ được chấp nhận khi có đúng một Pricing Policy hợp lệ cho Vehicle Type tại `check_in_time`. | Áp dụng cho Walk-in, Booking và Monthly Subscription; nếu không có Policy hợp lệ hoặc có nhiều Policy hợp lệ, hệ thống từ chối check-in và không tạo Parking Session. |
| BR-FEE-025 | Pricing Policy cùng Vehicle Type không được overlap khoảng `effective_start` - `effective_end`. | Tại một thời điểm chỉ có tối đa một Policy áp dụng; thời điểm kết thúc Policy trước có thể bằng thời điểm bắt đầu Policy sau và tại thời điểm chuyển tiếp chỉ Policy mới áp dụng. |
| BR-FEE-026 | Không tự động dùng Policy gần nhất, Policy đã hết hạn, Policy tương lai, giá mặc định hoặc Policy của Vehicle Type khác. | Nếu xe check-in trong khoảng trống không có Policy, hệ thống từ chối check-in. |
| BR-FEE-027 | Pricing Window trong cùng Policy phải có ít nhất một Window, không overlap và phủ đủ 24 giờ. | Một thời điểm chỉ thuộc đúng một Window; cho phép Window đi qua nửa đêm. |
| BR-FEE-028 | Không cho kích hoạt Pricing Policy nếu Pricing Window overlap hoặc không phủ đủ 24 giờ. | Manager phải sửa Window trước khi kích hoạt Policy. |
| BR-FEE-029 | Pricing Policy đã `ACTIVE` hoặc đã từng được dùng để chấp nhận Parking Session không được sửa cấu hình giá. | Không sửa Vehicle Type, Pricing Window, Base Duration, Base Price, Increment Block, Increment Price, Grace Period, Window Cap hoặc dữ liệu làm thay đổi kết quả tính phí. |
| BR-FEE-030 | Pricing Policy đã được Parking Session hoặc Payment sử dụng không được xóa trực tiếp. | Policy lịch sử phải được giữ lại để audit, kể cả khi đã `EXPIRED`. |
| BR-FEE-031 | `effective_start` của Policy đang `ACTIVE` không được thay đổi. | Policy tương lai có thể đổi `effective_start` nếu thời điểm mới vẫn ở tương lai, không overlap và Policy chưa được dùng. |
| BR-FEE-032 | `effective_end` của Policy tương lai hoặc đang `ACTIVE` có thể được thay đổi theo rule hợp lệ. | `effective_end` mới phải ở tương lai, không làm mất hiệu lực hồi tố với session đã check-in, không overlap; Policy `EXPIRED` không được đổi `effective_end`. |

---

## 6.9 Parking Session Rules

| Rule ID | Rule                                                   |
|---------|--------------------------------------------------------|
| BR-028  | Session bắt đầu khi check-in thành công.               |
| BR-029  | Session chỉ kết thúc khi check-out thành công: `amount_due = 0` hoặc khoản cần thanh toán đã `PAID`. |
| BR-030  | Session đang mở phải giữ Zone/capacity tương ứng; `slot_id` chỉ bắt buộc khi nghiệp vụ đã xác nhận Slot thực tế. |
| BR-031  | Driver chỉ được xem thông tin của mình.                |

---

## 6.10 Vehicle Check-out Rules

| Rule ID | Rule                                                                         |
|---------|------------------------------------------------------------------------------|
| BR-032  | Xe chỉ được check-out nếu có session đang mở.                                |
| BR-033  | Hệ thống phải ghi nhận `check_out_time` ngay khi Driver/Staff bắt đầu check-out. | `check_out_time` này là mốc kết thúc tính phí cho cùng quá trình check-out. |
| BR-034  | Check-out phải xác nhận thanh toán trước khi hoàn tất nếu `amount_due > 0`; nếu `amount_due = 0` thì bỏ qua bước thu tiền. |
| BR-035  | Nếu có thẻ tháng hợp lệ, phí được xử lý theo chính sách thẻ tháng.           |
| BR-036  | Nếu phát sinh lỗi thông tin, Staff phải xử lý exception trước khi cho xe ra. |
| BR-037  | Trong khi Payment còn `PENDING`, Parking Session chưa `COMPLETED` và xe vẫn được xem là còn trong bãi. | Zone/Slot, Card và general capacity chưa được giải phóng. |
| BR-038  | Nếu check-out `FAILED` hoặc bị hủy, hệ thống rollback nghiệp vụ của lần check-out. | Hủy `check_out_time` của lần thất bại; session tiếp tục đang gửi và lần check-out sau ghi nhận mốc mới. |
| BR-039  | Sau check-out thành công, occupancy/capacity vật lý mới được giải phóng. | Slot tháng ô tô vẫn được giữ quyền sở hữu qua `assigned_slot_id`. |
| BR-040  | Thời gian Driver thanh toán trong cùng một quá trình check-out không làm phí tăng thêm. | Phí được tính từ `check_in_time` đến `check_out_time` đã ghi nhận. |
| BR-041  | Checkout chỉ được coi là Failed khi toàn bộ lần checkout kết thúc không thành công. | Điều kiện gồm Staff/Driver kết thúc checkout, không còn Payment đang xử lý, không có Payment thành công; khi đó rollback `check_out_time` và session tiếp tục đang gửi. |

---

## 6.11 Exception Handling Rules

| Rule ID | Rule                                                                              |
|---------|-----------------------------------------------------------------------------------|
| BR-042  | Ngoại lệ phải có lý do xử lý.                                                     |
| BR-043  | Một số ngoại lệ cần Manager xác nhận.                                             |
| BR-044  | Xe chưa thanh toán không được hoàn tất check-out, trừ khi Manager xử lý đặc biệt. |
| BR-045  | Sai biển số phải được chỉnh trước khi hoàn tất session.                           |
| BR-046  | Gửi sai khu vực có thể phát sinh cảnh báo hoặc phí tùy chính sách.                |
| BR-052 | Khi Staff chuyển trạng thái vé/mã gửi xe thành LOST, hệ thống áp dụng lost card penalty. |
| BR-053 | Xe mất vé/mã gửi xe chỉ được rời bãi sau khi thanh toán phí gửi xe hiện tại và lost card penalty. |
| BR-054 | Xe đỗ sai khu vực có thể bị áp dụng wrong zone penalty nếu được Staff xác nhận. |
| BR-055 | Phí phạt được cộng vào tổng phí trước khi thanh toán. |

---

## 6.12 Operation Monitoring Rules

| Rule ID | Rule                                            |
|---------|-------------------------------------------------|
| BR-047  | Manager được xem toàn bộ dữ liệu vận hành.      |
| BR-048  | Dashboard phải phân biệt xe máy và ô tô.        |
| BR-049  | Xe máy hiển thị theo Zone capacity.             |
| BR-050  | Ô tô hiển thị theo Slot status.                 |
| BR-051  | Doanh thu dựa trên các giao dịch đã thanh toán. |

---

## 6.13 System State Rules

### Permission Status

| Status | Ý nghĩa |
|---|---|
| ACTIVE | Quyền đang được sử dụng |
| INACTIVE | Quyền tạm ngừng sử dụng |

### Account Status

| Status | Ý nghĩa |
|---|---|
| ACTIVE | Tài khoản đang hoạt động bình thường |
| SUSPENDED | Tài khoản bị khóa, đình chỉ tạm thời|
| ARCHIVED | Tài khoản không còn được sử dụng |


### Building Status

| Status | Ý nghĩa |
|---|---|
| INACTIVE     | Nhà xe chưa được đưa vào sử dụng.                                              |
| ACTIVE       | Nhà xe hoạt động, cho phép xe ra vào.                                          |
| MAINTENANCE  | Bãi xe được bảo trì; không tiếp nhận xe mới. Cho phép xe đang gửi ra khỏi bãi |
| OUTOFSERVICE | Nhà xe không thể hoạt động                                                     |

### Floor Status

| Status | Ý nghĩa |
|---|---|
| INACTIVE     | Tầng chưa được đưa vào sử dụng                                              |
| ACTIVE       | Tầng được sử dụng và cho phép xe sử dụng các vị trí đỗ                      |
| MAINTENANCE  | Tầng được bảo trì; không tiếp nhận xe mới. Cho phép xe đang gửi ra khỏi bãi |
| OUTOFSERVICE | Hết hạn khi xe vẫn còn trong bãi và bị chuyển sang tính phí vãng lai        |

### Zone Status

| Status | Ý nghĩa |
|---|---|
| AVAILABLE | Khu vực trống              |
| OCCUPIED  | Khu vực không nhận thêm xe |
| BLOCKED   | Khu vực không hoạt động    |

### Slot Status

| Status | Ý nghĩa |
|---|---|
| AVAILABLE | Slot trống           |
| OCCUPIED  | Slot có xe           |
| BLOCKED   | Slot bị khóa         |
| MAINTENANCE | Slot đang bảo trì |

Slot status chỉ phản ánh trạng thái vật lý/vận hành. Slot được cấp cho ô tô thẻ tháng vẫn có thể là `AVAILABLE` khi xe đang ở ngoài bãi; quyền giữ Slot được xác định bằng `monthly_subscription.assigned_slot_id`.

### Vehicle Type Status

| Status | Ý nghĩa |
|---|---|
| INACTIVE | Loại xe đang được hỗ trợ |
| ACTIVE | Loại xe tạm ngừng hỗ trợ |

### Vehicle Status

| Status | Ý nghĩa |
|---|---|
| INACTIVE | Chưa đăng ký xe trên hệ thống |
| ACTIVE | Xe sử dụng trên hệ thống |
| PENDING | Xe mới đăng ký, đang chờ xác minh |
| SUSPENDED | Xe bị tạm khóa |
| ARCHIVED | Người dùng đã xóa hoặc không còn sử dụng xe|

### Card Status

| Status | Ý nghĩa |
|---|---|
| AVAILABLE | Card đang rảnh và có thể cấp cho một session mới |
| ASSIGNED | Card đang được cấp cho một Parking Session `NORMAL` đang mở hoặc được gán dài hạn cho một Monthly Subscription |
| LOST | Card bị báo mất |
| BLOCKED | Card bị khóa và không được sử dụng |

Card type hợp lệ gồm `NORMAL` và `MONTHLY`. Card `NORMAL` được cấp tạm thời cho Walk-in/Booking và trả về `AVAILABLE` sau check-out. Card `MONTHLY` được gán với Monthly Subscription, được Driver giữ qua nhiều lượt vào/ra và không tự cấp quyền lợi nếu subscription không hợp lệ.

### Parking Session Status

| Status | Ý nghĩa |
|---|---|
| ACTIVE | Session đang mở, xe đã check-in và vẫn còn trong bãi. Zone/capacity tương ứng đang bị giữ; Slot có thể chưa có với ô tô Walk-in/Booking cho đến khi vị trí thực tế được xác nhận. Nếu `check_out_time` đã được ghi nhận nhưng Payment còn `PENDING`, session vẫn chưa `COMPLETED` |
| COMPLETED  | Session đã kết thúc sau khi check-out thành công: `amount_due = 0` hoặc khoản cần thanh toán đã `PAID`. Zone/Slot vật lý được giải phóng sau thời điểm này |
| LOST  | Người gửi xe bị mất vé/mã gửi xe. Hệ thống xử lý theo luồng lost card penalty trước khi cho xe ra |
| EXPIRED   | Vé/session hết hạn hoặc không còn hợp lệ theo chính sách thời gian. Trong tài liệu chưa mô tả sâu cho parking session,nhưng có liên hệ với trường hợp vé hết hạn |
| DOWNGRADED | Quyền lợi thẻ tháng bị downgrade, thường xảy ra khi thẻ tháng hết hạn trong lúc xe vẫn còn trong bãi; từ thời điểm hết hạn, hệ thống chuyển sang tính phí vãng lai |

### Booking Status

| Status | Ý nghĩa |
|---|---|
| PENDING | Chờ thanh toán |
| CONFIRMED | Đã xác nhận |
| CANCELLED | Đã hủy |
| EXPIRED | Hết hạn check-in |
| COMPLETED | Đã sử dụng |


### Incident Status

| Status | Ý nghĩa |
|---|---|
| OPEN | Sự cố mới được ghi nhận và đang chờ xử lý  |
| PROCESSING | Sự cố đang được nhân viên xử lý |
| RESOLVED | Sự cố đã được xử lý hoàn tất |
| CANCELLED | Sự cố đã bị hủy hoặc không tiếp tục xử lý |

### Monthly Subscription Status

| Status | Ý nghĩa |
|---|---|
| PENDING | Chờ thanh toán/kích hoạt |
| ACTIVE | Đang hiệu lực |
| EXPIRED | Đã hết hạn |
| DOWNGRADED | Hết hạn khi xe vẫn còn trong bãi và bị chuyển sang tính phí vãng lai |
| CANCELLED | Đã hủy |

### Pricing Policy Status

| Status | Ý nghĩa |
|---|---|
| INACTIVE | Chính sách giá chưa được áp dụng hoặc tạm ngừng |
| ACTIVE | Chính sách giá đang có hiệu lực |
| EXPIRED | Chính sách giá đã hết thời gian hiệu lực |

### Payment Status

| Status | Ý nghĩa |
|---|---|
| PENDING | Payment đã được tạo và quy trình thanh toán chưa kết thúc; Driver vẫn có thể tiếp tục hoặc thử lại. Với checkout Payment, Parking Session chưa `COMPLETED`, occupancy chưa giải phóng và phí giữ theo `check_out_time` đã ghi nhận |
| PAID | Ngân hàng hoặc Staff đã xác nhận nhận tiền thành công |
| FAILED | Quy trình thanh toán đã kết thúc không thành công do Driver hủy, timeout, Staff xác nhận không thể tiếp tục hoặc gateway trả kết quả cuối cùng không thể tiếp tục. Với checkout Payment, Payment `FAILED` được giữ để audit và lần check-out bị rollback nghiệp vụ |
| REFUNDED | Khoản tiền đã được hoàn theo chính sách |

## 6.14 Configurable Variables Rules

Các biến cấu hình dưới đây phải được quản lý động ở tầng cấu hình nghiệp vụ/application/admin configuration, không hard-code trực tiếp trong source code. Phiên bản physical model hiện tại không tạo thêm table riêng cho nhóm biến này.

| Key | Ý nghĩa |
|---|---|
| day_start_time | Giờ bắt đầu khung ngày. |
| night_start_time | Giờ bắt đầu khung đêm. |
| base_duration_minutes | Thời lượng cơ bản. |
| increment_block_minutes | Kích thước block. |
| grace_period_minutes | Thời gian ân hạn làm tròn block. |
| booking_payment_timeout_minutes | Timeout thanh toán booking. |
| monthly_subscription_payment_timeout_minutes | Timeout thanh toán online khi đăng ký hoặc gia hạn Monthly Subscription. |
| checkin_grace_minutes | Thời gian ân hạn check-in booking. |
| lost_card_penalty | Phí mất vé/mã gửi xe. |
| wrong_zone_penalty | Phí đỗ sai khu. |
| cash_rounding_unit | Đơn vị làm tròn tiền mặt. |
| rounding_threshold | Ngưỡng làm tròn. |

## 6.15 Business Rules Summary

| Rule Group           | Nội dung chính                                                                                                                           |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Hardware Simulation  | Không có camera, thẻ thật, cảm biến, barrier; toàn bộ nhập liệu trên web.                                                                |
| Vehicle Type         | Hiện chỉ hỗ trợ xe máy và ô tô.                                                                                                          |
| Motorcycle Parking   | Xe máy được phân bổ theo Zone/Area, không theo Slot cụ thể.                                                                              |
| Car Parking          | Ô tô Walk-in/Booking chỉ được gợi ý Zone `GENERAL` trước và ghi nhận Slot thực tế sau khi xe đậu; ô tô thẻ tháng dùng Slot đã gán trong Zone `MONTHLY`. |
| Check-in             | Chỉ tạo session khi còn chỗ hợp lệ.                                                                                                      |
| Booking | Booking yêu cầu chọn Building trước, nhập biển số/Vehicle, Deposit Fee bằng Base Price của block đầu tiên theo Booking Policy, đặt trước tối thiểu 1 tiếng và tối đa 8 tiếng; Driver không chọn Zone/Slot; Booking chỉ giữ general capacity tại Building. |
| Monthly Subscription | Thẻ tháng xe máy đảm bảo có chỗ bằng capacity động; thẻ tháng ô tô được cấp Slot riêng trong Zone `MONTHLY`; Monthly Subscription gán với Card `MONTHLY` và Card type không tự cấp quyền lợi nếu subscription không hợp lệ. |
| Booking Cancellation | Hủy chủ động trước giờ booking ít nhất 1 tiếng được hoàn cọc; no-show chuyển `EXPIRED` theo `checkin_grace_minutes` và mất cọc. |
| Payment              | Hỗ trợ tiền mặt và online qua ngân hàng; không hỗ trợ thanh toán bằng thẻ; mỗi Payment có đúng một nguồn nghiệp vụ chính; Payment `FAILED` được giữ để audit và retry tạo Payment mới. |
| Check-out | `check_out_time` được ghi nhận khi bắt đầu check-out; Payment `PENDING` chưa hoàn tất session và không làm phí tăng; Payment `FAILED` riêng lẻ có thể retry; chỉ Checkout Failed toàn phần mới rollback `check_out_time`. |
| Fee Calculation | Phí gửi xe tính theo Pricing Policy áp dụng cho Vehicle Type tại `check_in_time`, Pricing Window, Base Price, Increment/Block Pricing, Window Cap, Grace Period và trạng thái session 24/7 không reset qua ngày. |
| Pricing Policy | Pricing Policy dùng chung toàn hệ thống theo Vehicle Type, không gắn Building, không bắt buộc lưu trên Parking Session, không overlap theo timeline và bị khóa cấu hình giá sau khi `ACTIVE` hoặc đã được sử dụng. |
| Exception            | Các lỗi mất mã, sai biển số, quá hạn, gửi sai khu vực, chưa thanh toán cần quy trình xử lý.                                              |
| Scale                | Có thể mở rộng Building, Floor, Zone, Slot ở mức cấu hình nghiệp vụ.                                                                     |
| Monthly Subscription Downgrade | Quyền lợi tháng khi checkout được đánh giá tại `check_out_time`; nếu đã hết hạn tại mốc đó thì phần sau `expired_at` tính Walk-in, còn Payment Pending sau mốc đó không làm phát sinh thêm phí. |
| Rounding | Tiền mặt có thể làm tròn theo cấu hình; online payment không làm tròn. |
| Penalty | Mất vé/mã gửi xe và đỗ sai khu có thể phát sinh phí phạt. |
| Configurable Rules | Các biến pricing, booking payment timeout, monthly subscription payment timeout, grace period, penalty và rounding phải được cấu hình động ở tầng nghiệp vụ/application/admin configuration, không hard-code và không tạo thêm table riêng trong physical model hiện tại. |

---
