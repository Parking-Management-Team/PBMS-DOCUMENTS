# 4. Business Context

## 4.1 Problem Statement

Hiện tại, nghiệp vụ gửi xe trong tòa nhà nhiều tầng có nhiều điểm dễ sai sót: xe ra/vào liên tục, cần kiểm soát chỗ
trống, cần tính phí, cần xử lý mất vé/mã gửi xe, quá hạn, sai thông tin xe, xe gửi sai khu vực và xe chưa thanh toán.
Nếu quản lý thủ công, bãi xe dễ bị ùn ứ, sai lệch dữ liệu, khó kiểm soát sức chứa và khó đối soát doanh thu.

Trong phiên bản này, vì không có hardware thật, hệ thống cần mô phỏng nghiệp vụ bằng web app nhưng vẫn phải giữ đúng
logic vận hành: xe vào phải có session, xe phải được phân bổ đúng khu vực/slot, booking và thẻ tháng không được làm vượt
sức chứa, xe ra phải được thanh toán và cập nhật trạng thái.

---

## 4.2 Business Goals

| Goal ID | Business Goal                                                                         |
|---------|---------------------------------------------------------------------------------------|
| BG-001  | Chuẩn hóa quy trình xe vào/ra bằng web app.                                           |
| BG-002  | Giảm sai sót khi ghi nhận biển số, mã gửi xe, giờ vào/ra, phí gửi xe.                 |
| BG-003  | Kiểm soát được chỗ trống theo Zone cho xe máy và Slot cho ô tô.                       |
| BG-004  | Hỗ trợ đặt chỗ trước để người dùng chủ động kế hoạch gửi xe.                          |
| BG-005  | Hỗ trợ thẻ tháng nhưng không vượt quá khả năng phục vụ của bãi.                       |
| BG-006  | Hỗ trợ thanh toán tiền mặt và online qua ngân hàng; không hỗ trợ thanh toán bằng thẻ. |
| BG-007  | Cho phép mở rộng mô hình bãi xe khi có thêm Building, Floor, Zone, Slot.              |
| BG-008  | Giúp Manager theo dõi vận hành, doanh thu, lượt xe, tỷ lệ lấp đầy.                    |

---

## 4.3 Success Criteria

| Criteria ID | Success Criteria                                                                                       |
|-------------|--------------------------------------------------------------------------------------------------------|
| SC-001      | Driver có thể tạo tài khoản mà chưa cần thêm xe.                                                       |
| SC-002      | Một Driver Account có thể thêm nhiều xe.                                                               |
| SC-003      | Booking bắt buộc nhập biển số.                                                                         |
| SC-004      | Booking chỉ confirmed sau khi thanh toán cọc thành công.                                               |
| SC-005      | Booking phải được đặt trước tối thiểu 1 tiếng và tối đa 8 tiếng tính từ lúc thanh toán cọc thành công. |
| SC-006      | Xe máy booking phải chọn Building trước; người dùng không chọn Zone/Slot, hệ thống chọn Zone khi check-in. |
| SC-007      | Ô tô booking phải chọn Building trước; người dùng không chọn Zone/Slot, hệ thống chỉ gợi ý Zone `GENERAL` khi check-in và ghi nhận Slot thực tế sau khi xe đã đậu. |
| SC-008      | Booking yêu cầu thanh toán Deposit Fee bằng Base Price của block đầu tiên theo Booking Policy tại thời điểm Booking được tạo.            |
| SC-009      | Booking được hoàn cọc nếu khách hủy trước giờ booking ít nhất 1 tiếng.                                 |
| SC-010      | Booking no-show chuyển sang `EXPIRED` và mất cọc nếu khách không check-in trước `scheduled_check_in_time + checkin_grace_minutes`. |
| SC-011      | Thẻ tháng xe máy đảm bảo có chỗ bằng capacity động: mỗi subscription `ACTIVE` giảm capacity Walk-in/Booking một đơn vị. |
| SC-012      | Thẻ tháng ô tô được cấp Slot riêng trong Zone `MONTHLY`; Slot được giữ bằng `monthly_subscription.assigned_slot_id`. |
| SC-013      | Thẻ tháng không giới hạn số lượt vào/ra mỗi ngày.                                                      |
| SC-014      | Hệ thống không cho cấp thêm thẻ tháng nếu vượt khả năng đảm bảo chỗ.                                   |
| SC-015      | Hệ thống hỗ trợ thanh toán tiền mặt và online qua ngân hàng.                                           |
| SC-016      | Hệ thống không hiển thị thanh toán bằng thẻ.                                                           |
| SC-017      | Phí gửi xe được tính theo giờ, loại xe, khung giờ sáng/tối, qua đêm và nhiều ngày.                     |
| SC-018      | Hệ thống không cấp hết chỗ cho walk-in/booking nếu phần còn lại cần giữ cho khách thẻ tháng.           |
| SC-019      | Staff có thể tạo lượt gửi xe bằng nhập thủ công biển số, loại xe, mã gửi xe.                           |
| SC-020      | Xe máy được hệ thống gợi ý Zone/Area còn trống.                                                        |
| SC-021      | Ô tô Walk-in/Booking chỉ được hệ thống gợi ý Zone `GENERAL` trước; Slot thực tế được ghi nhận sau khi xe đậu. Ô tô thẻ tháng dùng Slot đã cấp trong Zone `MONTHLY`. |
| SC-022      | Khi check-out, hệ thống tính được phí và ghi nhận thanh toán tiền mặt hoặc online.                     |
| SC-023      | Sau khi xe ra, Zone/Slot được cập nhật lại trạng thái còn trống.                                       |
| SC-024      | Manager có thể xem tình trạng chỗ đỗ, lượt xe và doanh thu.                                            |
| SC-025      | Có thể thêm Building/Floor/Zone/Slot mới mà không làm thay đổi luồng nghiệp vụ chính.                  |
| SC-026 | Hệ thống tính phí theo Time Window, Base Price, Increment/Block Pricing và Window Cap. |
| SC-027 | Nếu session đi qua nhiều khung giờ, hệ thống tách session và tính phí riêng cho từng khung. |
| SC-028 | Window Cap chỉ áp dụng trong từng khung giờ riêng biệt. |
| SC-029 | Session không bị reset khi qua ngày mới. |
| SC-030 | Hệ thống tự động downgrade vé tháng hết hạn nếu xe vẫn còn trong bãi và tính phí vãng lai từ thời điểm hết hạn. |
| SC-031 | Booking chưa thanh toán sau booking payment timeout sẽ hết hiệu lực hoặc bị hủy theo status model; general capacity được giải phóng và không cập nhật `slot_status`. |
| SC-032 | Booking no-show sau check-in grace time chuyển sang `EXPIRED` và không hoàn deposit fee. |
| SC-033 | Thời gian phát sinh nhỏ hơn hoặc bằng grace period không bị tính block mới. |
| SC-034 | Thanh toán tiền mặt được làm tròn theo cash rounding rule. |
| SC-035 | Thanh toán online giữ nguyên giá trị chính xác, không làm tròn. |
| SC-036 | Thời điểm check-out được ghi nhận ngay khi Driver/Staff bắt đầu check-out và được dùng làm mốc kết thúc tính phí. |
| SC-037 | Payment `PENDING` trong cùng quá trình check-out không làm phí tiếp tục tăng và không giải phóng Zone/Slot. |
| SC-038 | Pricing Policy của Parking Session được xác định bằng Vehicle Type và `check_in_time`, dùng cho toàn bộ session và không chọn lại theo `check_out_time`. |
| SC-039 | Pricing Policy dùng chung toàn hệ thống theo Vehicle Type, không cấu hình riêng theo Building. |
| SC-040 | Check-in bị từ chối nếu không có đúng một Pricing Policy hợp lệ cho Vehicle Type tại `check_in_time`. |
| SC-041 | Pricing Policy đã `ACTIVE` hoặc đã được sử dụng không được sửa cấu hình giá hoặc Pricing Window. |
| SC-042 | Checkout Payment không tự động timeout; Payment retry tạo Payment mới và giữ Payment `FAILED` để audit. |
| SC-043 | Quyền lợi Monthly Subscription khi checkout được đánh giá tại `check_out_time`, không đánh giá lại theo thời điểm Payment hoàn tất. |

---

## 4.4 Current Workflow

### Trước khi có hệ thống

1. Nhân viên tiếp nhận xe.
2. Nhân viên ghi nhận biển số, loại xe, giờ vào bằng cách thủ công.
3. Nhân viên tự kiểm tra hoặc ước lượng còn chỗ hay không.
4. Người gửi xe tự tìm khu vực/slot.
5. Khi xe ra, nhân viên tìm lại thông tin gửi xe.
6. Nhân viên tính phí.
7. Người gửi xe thanh toán.
8. Nhân viên ghi nhận xe đã rời bãi.
9. Manager tổng hợp doanh thu/lượt xe thủ công hoặc bán thủ công.

### Pain Points

| Pain Point                                 | Ảnh hưởng                                     |
|--------------------------------------------|-----------------------------------------------|
| Khó biết còn chỗ chính xác                 | Dễ nhận xe vượt sức chứa.                     |
| Xe máy và ô tô cần logic phân bổ khác nhau | Dễ gửi sai khu vực.                           |
| Không có trạng thái booking rõ ràng        | Có thể trùng chỗ.                             |
| Thẻ tháng nếu cấp quá nhiều                | Không đảm bảo còn chỗ.                        |
| Ghi nhận thủ công                          | Dễ sai biển số, giờ vào, phí.                 |
| Thanh toán khó đối soát                    | Dễ lệch doanh thu.                            |
| Không có hardware thật                     | Cần cơ chế mô phỏng nhưng vẫn đúng nghiệp vụ. |

---

## 4.5 Target Workflow

### Luồng tổng quát sau khi có hệ thống

1. Staff hoặc Driver mở chức năng tương ứng trên web.
2. Hệ thống hiển thị thông tin bãi xe, chỗ trống, giá và quy định.
3. Người dùng nhập dữ liệu: biển số, loại xe, mã thẻ/mã gửi xe, thời gian booking hoặc thông tin thẻ tháng.
4. Hệ thống kiểm tra điều kiện hợp lệ.
5. Hệ thống phân bổ:
    - Xe máy → Zone/Area còn trống.
    - Ô tô Walk-in/Booking → Zone thuộc nhóm `GENERAL`.
    - Ô tô thẻ tháng → Slot riêng trong Zone `MONTHLY` đã được cấp qua `monthly_subscription.assigned_slot_id`.
6. Hệ thống tạo booking, monthly registration hoặc parking session.
7. Khi xe ra, Staff tìm session.
8. Hệ thống tính phí.
9. Người dùng thanh toán tiền mặt hoặc online.
10. Hệ thống cập nhật trạng thái session, Zone/Slot thực tế và giao dịch.
11. Manager theo dõi trạng thái vận hành qua màn hình quản lý.

### 4.5.1 Booking Workflow

#### Main Flow

1. Driver đăng nhập, tạo booking.
2. Driver chọn Building muốn gửi xe.
3. Driver chọn loại xe: xe máy hoặc ô tô.
4. Driver nhập/chọn biển số xe.
5. Driver nhập thời gian dự kiến vào bãi.
6. Driver nhập thời lượng gửi xe hoặc thời gian dự kiến rời bãi.
7. Hệ thống kiểm tra thời gian booking:
    - Tối thiểu trước 1 tiếng so với thời điểm thanh toán cọc thành công.
    - Tối đa trước 8 tiếng so với thời điểm thanh toán cọc thành công.
8. Hệ thống xác định Booking Policy theo loại xe và thời điểm Booking được tạo.
9. Hệ thống tính Deposit Fee bằng Base Price của block đầu tiên theo Booking Policy.
10. Hệ thống kiểm tra chỗ khả dụng trong Building đã chọn:
    - Xe máy: Driver không chọn Zone/Slot; hệ thống kiểm tra general capacity của Building sau khi trừ Monthly Subscription xe máy active.
    - Ô tô: Driver không chọn Zone/Slot; hệ thống kiểm tra general car capacity của Building, chỉ tính phần capacity dành cho Zone `GENERAL`.
11. Hệ thống tạo Booking ở trạng thái `PENDING` và tạm giữ general capacity tại Building.
12. Hệ thống tạo Payment cho Booking Deposit với đúng số tiền Deposit đã yêu cầu qua QR, `pricing_policy_id` của Booking Policy và `booking_id` là nguồn duy nhất.
13. Driver thanh toán cọc qua ngân hàng.
14. Nếu Payment thành công, Payment chuyển `PAID`, hệ thống xác nhận booking và chuyển trạng thái booking sang `CONFIRMED`.
15. Booking chỉ giữ capacity ở mức Building; vị trí thực tế được xác định trong quá trình check-in/đỗ xe và lưu ở Parking Session.
16. Khi Driver đến đúng thời gian hợp lệ, booking được chuyển thành parking session.

#### Exception Flow

| Trường hợp                                            | Xử lý                                                           |
|-------------------------------------------------------|-----------------------------------------------------------------|
| Thiếu Building                                        | Không cho tạo booking.                                          |
| Thiếu biển số                                         | Không cho tạo booking.                                          |
| Không nhập thời lượng gửi xe hoặc giờ rời bãi dự kiến | Không cho tạo booking vì hệ thống không đủ dữ liệu để tính cọc. |
| Thời gian booking nhỏ hơn 1 tiếng từ lúc thanh toán   | Từ chối booking.                                                |
| Thời gian booking lớn hơn 8 tiếng từ lúc thanh toán   | Từ chối booking.                                                |
| Thanh toán cọc chưa hoàn tất trong timeout             | Booking hết hiệu lực hoặc bị hủy theo status model, general capacity đã tạm giữ được giải phóng, không cập nhật `slot_status`. |
| Xe máy không còn capacity đảm bảo                     | Từ chối booking.                                                |
| Ô tô không còn general capacity phù hợp                | Từ chối booking.                                                |
| Khách hủy trước giờ booking ít nhất 1 tiếng           | Hủy booking và hoàn cọc.                                        |
| Khách hủy trễ hơn thời hạn được hoàn cọc              | Hủy booking và không hoàn cọc.                                  |
| Khách không check-in trước `scheduled_check_in_time + checkin_grace_minutes` | Booking chuyển `EXPIRED`, mất cọc, giải phóng general capacity và không cập nhật `slot_status`. |
| Biển số đã có booking active trùng thời gian          | Cảnh báo hoặc từ chối tạo booking mới.                          |
| Quá thời gian thanh toán booking | Booking hết hiệu lực hoặc bị hủy theo status model; general capacity đã tạm giữ ở Building được giải phóng, không cập nhật `slot_status`. |
| Khách không check-in trong thời gian ân hạn | Booking chuyển `EXPIRED`, Deposit Fee không được hoàn trả. |

### 4.5.2 Monthly Subscription Workflow

#### Main Flow

1. Driver mở chức năng đăng ký Monthly Subscription/thẻ tháng.
2. Driver chọn hoặc thêm xe vào tài khoản.
3. Hệ thống kiểm tra biển số xe và loại xe.
4. Hệ thống kiểm tra thẻ tháng hiện tại của xe.
5. Hệ thống kiểm tra khả năng đảm bảo chỗ theo loại xe:
    - Xe máy: kiểm tra capacity động; mỗi Monthly Subscription xe máy `ACTIVE` giữ một đơn vị và làm giảm capacity Walk-in/Booking.
    - Ô tô: kiểm tra Slot còn có thể cấp riêng trong Zone `MONTHLY` của Building đã chọn.
6. Nếu còn khả năng đảm bảo chỗ, hệ thống tạo hồ sơ `monthly_subscription` ở trạng thái `PENDING` và tạo yêu cầu thanh toán.
7. Driver thanh toán phí thẻ tháng.
8. Khi thanh toán thành công, hệ thống kích hoạt Monthly Subscription, thiết lập thời gian hiệu lực, gán Card `MONTHLY`, và giữ capacity/slot tương ứng.
9. Khi xe máy có Monthly Subscription vào bãi, Driver dùng Card `MONTHLY`; hệ thống kiểm tra subscription hợp lệ, dùng capacity tháng đã giữ và gợi ý Zone còn chỗ.
10. Khi ô tô có Monthly Subscription vào bãi, Driver dùng Card `MONTHLY`; hệ thống dùng Slot riêng trong Zone `MONTHLY` đã được cấp cho xe đó.
11. Driver có thể ra/vào nhiều lượt trong ngày nếu thẻ còn hiệu lực và không có session đang mở cùng lúc.
12. Hệ thống lưu quyền lợi tháng theo Vehicle, Building, Card `MONTHLY` và Slot nếu là ô tô; Card type không tự cấp quyền lợi nếu không có Monthly Subscription hợp lệ.
13. Hệ thống ghi nhận chu kỳ hiệu lực của vé tháng theo valid duration.
14. Nếu vé tháng được gia hạn thành công trước hoặc sau khi hết hạn, quyền lợi vé tháng được kích hoạt lại ngay sau khi thanh toán thành công.

#### Downgrade Flow

1. Hệ thống quét trạng thái vé tháng vào 00:00 mỗi ngày.
2. Nếu vé tháng đã hết hạn và xe vẫn còn trong bãi, hệ thống chuyển quyền lợi vé tháng sang trạng thái downgraded/transient.
3. Từ thời điểm hết hạn, hệ thống bắt đầu tính phí theo bảng giá vãng lai.
4. Khi xe check-out, khách thanh toán phần phí phát sinh theo bảng giá vãng lai.

#### Exception Flow

| Trường hợp                                | Xử lý                                           |
|-------------------------------------------|-------------------------------------------------|
| Xe chưa được thêm vào tài khoản           | Yêu cầu thêm xe hoặc nhập biển số.              |
| Xe đã có thẻ tháng còn hiệu lực           | Không cho tạo thẻ tháng trùng thời gian.        |
| Hết capacity đảm bảo cho xe máy thẻ tháng | Từ chối đăng ký thẻ tháng xe máy.               |
| Không còn Slot để cấp cho ô tô thẻ tháng  | Từ chối đăng ký thẻ tháng ô tô.                 |
| Thanh toán thất bại                       | Thẻ tháng không được kích hoạt.                 |
| Thẻ tháng hết hạn                         | Không áp dụng quyền lợi thẻ tháng khi check-in. |
| Vé tháng hết hạn khi xe vẫn còn trong bãi | Hệ thống downgrade vé tháng và tính phí vãng lai từ thời điểm hết hạn. |
| Muốn dùng cùng một vé tháng cho xe khác | Hệ thống từ chối vì mỗi vé tháng chỉ áp dụng cho một xe đã đăng ký. |

### 4.5.3 Check-in Workflow

#### Main Flow

1. Staff mở màn hình Check-in.
2. Staff nhập biển số hoặc mã booking, sau đó nhập `card_code` của Card vận hành được cấp cho lượt gửi hiện tại.
3. Hệ thống xác định loại xe và trạng thái liên quan:
    - Khách vãng lai.
    - Khách có booking.
    - Khách có thẻ tháng.
4. Hệ thống kiểm tra xe có session đang mở hay không.
5. Hệ thống kiểm tra tại thời điểm check-in có đúng một Pricing Policy hợp lệ cho Vehicle Type, Policy đã đến thời gian áp dụng, chưa kết thúc và Pricing Window không overlap, phủ đủ 24 giờ.
6. Hệ thống kiểm tra điều kiện chỗ đỗ:
    - Xe máy vãng lai: tìm Zone/Area còn sức chứa.
    - Ô tô vãng lai: kiểm tra general capacity và Zone `GENERAL` còn phù hợp.
    - Xe máy thẻ tháng: đảm bảo có chỗ, không phân theo Zone/Slot cố định.
    - Ô tô thẻ tháng: kiểm tra Slot riêng trong Zone `MONTHLY` đã được cấp cho xe.
    - Xe máy booking: kiểm tra booking còn hiệu lực và còn trong thời gian cho phép.
    - Ô tô booking: kiểm tra booking còn hiệu lực và general capacity đã giữ ở Building.
7. Hệ thống gợi ý chỗ đỗ:
    - Xe máy: gợi ý Zone/Area.
    - Ô tô Walk-in/Booking: gợi ý Zone thuộc nhóm `GENERAL`; Slot thực tế được ghi nhận sau khi xe đã đậu.
    - Ô tô thẻ tháng: hiển thị Slot riêng trong Zone `MONTHLY`.
8. Staff xác nhận check-in.
9. Hệ thống tạo parking session có `card_id`; nếu áp dụng quyền lợi tháng thì lưu thêm `monthly_subscription_id`, nếu từ booking thì lưu `booking_id`. Check-in không tính phí và không bắt buộc lưu `pricing_policy_id` trên Parking Session.
10. Với ô tô Walk-in/Booking, sau khi xe thực tế đậu, Staff hoặc hệ thống ghi nhận Slot vào `parking_session.slot_id`; Slot phải thuộc Zone `GENERAL` đã gợi ý, phù hợp loại xe, không bị khóa/bảo trì và chưa bị xe khác chiếm dụng.
11. Khi Slot thực tế được xác nhận, hệ thống cập nhật Slot thành `OCCUPIED`. Với Card `NORMAL`, hệ thống chuyển Card sang `ASSIGNED`; với Card `MONTHLY`, Card giữ trạng thái `ASSIGNED`.

#### Exception Flow

| Trường hợp                           | Xử lý                                                                                             |
|--------------------------------------|---------------------------------------------------------------------------------------------------|
| Biển số trống                        | Yêu cầu nhập biển số.                                                                             |
| Xe đã có session đang mở             | Không cho tạo session mới.                                                                        |
| Booking chưa thanh toán cọc          | Không cho check-in theo booking.                                                                  |
| Booking đã `EXPIRED` do no-show | Không áp dụng booking, khách mất cọc và chỉ được xử lý như khách mới nếu còn chỗ. |
| Khách đến sớm hơn giờ booking        | Cho phép check-in sớm nếu còn chỗ phù hợp; thời gian gửi xe bắt đầu tính từ lúc check-in thực tế. |
| Hết chỗ phù hợp                      | Từ chối check-in.                                                                                 |
| Zone/Slot bị khóa hoặc bảo trì       | Không phân bổ.                                                                                    |
| Không có đúng một Pricing Policy hợp lệ tại `check_in_time` | Từ chối check-in và không tạo Parking Session. |

### 4.5.4 Check-out & Payment Workflow

#### Main Flow

1. Driver tap Card hoặc Staff tiếp nhận yêu cầu check-out.
2. Staff nhập `card_code` hoặc thông tin thay thế khi xử lý ngoại lệ.
3. Hệ thống tìm Card và Parking Session đang mở theo `card_id`.
4. Hệ thống xác minh Card/biển số, Vehicle, Building, Zone/Slot, Booking hoặc Monthly Subscription nếu có, và các Incident/phụ phí chưa xử lý.
5. Hệ thống ghi nhận ngay thời điểm check-out hiện tại và dùng thời điểm này làm mốc kết thúc tính phí.
6. Hệ thống xác định Pricing Policy áp dụng cho session bằng Vehicle Type và `check_in_time`; không chọn Policy theo `check_out_time`.
7. Hệ thống tính phí từ `check_in_time` đến `check_out_time` đã ghi nhận dựa trên:
   - Loại xe.
   - Pricing Window thuộc Pricing Policy áp dụng tại `check_in_time`.
   - Base Duration.
   - Base Price.
   - Increment Block.
   - Increment Price.
   - Window Cap của từng khung giờ.
   - Grace Period.
   - Chính sách vé tháng nếu có.
   - Deposit Fee đã thanh toán nếu session đi từ booking.
   - Phí phạt hoặc phụ phí nếu có.
8. Nếu session đi qua nhiều pricing window, hệ thống tách session thành nhiều đoạn thời gian và tính phí riêng cho từng đoạn.
9. Nếu session đi từ Booking, hệ thống tính Tổng phí thực tế = phí gửi xe + phụ phí/phí phạt, sau đó tính `amount_due = max(0, total_actual_fee - paid_deposit_fee)`.
10. Nếu Deposit Fee đã thanh toán lớn hơn Tổng phí thực tế, `amount_due = 0`; Driver không phải thanh toán thêm, check-out tiếp tục theo flow hiện tại, không hoàn phần chênh lệch, không lưu/xử lý riêng phần chênh lệch, không tạo credit/wallet/account balance, không tạo Payment âm và không thay đổi Deposit Payment đã tồn tại.
11. Nếu Monthly Subscription còn hiệu lực tại `check_out_time`, đúng Vehicle/biển số, đúng Building, không có phụ phí hoặc phí phạt, hệ thống đặt `actual_parking_fee = 0`, `amount_due = 0` và bỏ qua bước thu tiền. Nếu subscription hết hạn trước hoặc đúng `check_out_time`, phần từ `expired_at` đến `check_out_time` được tính theo giá Walk-in.
12. Nếu `amount_due > 0`, Driver thanh toán tiền mặt hoặc online.
13. Nếu Payment `PAID` hoặc `amount_due = 0`, hệ thống cập nhật session `COMPLETED`, giải phóng occupancy/capacity vật lý, cập nhật Zone/Slot, xử lý Card, ghi Audit Log và cho phép xe rời bãi.
14. Nếu Payment vẫn `PENDING`, hệ thống giữ `check_out_time` đã ghi nhận, không cộng thêm thời gian thanh toán vào phí, chưa chuyển session sang `COMPLETED` và chưa giải phóng Zone/Slot/Card.
15. Nếu một Payment cụ thể `FAILED` nhưng checkout vẫn tiếp tục, hệ thống giữ Payment cũ để audit, tạo Payment mới nếu cần và giữ nguyên `check_out_time`.
16. Nếu toàn bộ checkout `FAILED`/bị hủy, hệ thống rollback nghiệp vụ của lần check-out, hủy `check_out_time` đã ghi nhận, giữ session tiếp tục đang gửi và giữ Payment `FAILED` để audit.

#### Exception Flow

| Trường hợp                 | Xử lý                                      |
|----------------------------|--------------------------------------------|
| Không tìm thấy session     | Báo không có lượt gửi đang mở.             |
| Một lần thử thanh toán online bị từ chối | Cho thanh toán lại hoặc đổi sang tiền mặt nếu quy trình thanh toán vẫn còn hiệu lực. |
| Chưa thanh toán            | Không cho hoàn tất check-out.              |
| Check-out thất bại hoặc bị hủy | Hủy thời điểm check-out của lần thất bại; session tiếp tục đang gửi và lần check-out sau tính phí đến mốc mới. |
| Sai biển số                | Staff xử lý ngoại lệ trước khi check-out.  |
| Có phí phát sinh           | Cộng vào tổng phí trước khi thanh toán.    |

---
