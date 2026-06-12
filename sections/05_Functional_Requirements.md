# 5. Functional Requirements

## Feature List

| Feature ID | Feature Name                        | Priority | Description                                                                                |
|------------|-------------------------------------|----------|--------------------------------------------------------------------------------------------|
| F-001      | Parking Structure Management        | Must     | Quản lý Building, Floor, Zone, Slot để hệ thống có thể mở rộng.                            |
| F-002      | Driver Account & Vehicle Management | Must     | Cho phép tạo tài khoản Driver không cần có xe ban đầu; một tài khoản có thể thêm nhiều xe. |
| F-003      | Vehicle Check-in                    | Must     | Tạo lượt gửi xe bằng nhập liệu thủ công trên web.                                          |
| F-004      | Parking Allocation                  | Must     | Xe máy được gợi ý Zone/Area; ô tô Walk-in/Booking được gợi ý Zone `GENERAL` trước và ghi nhận Slot thực tế sau khi đậu; ô tô thẻ tháng dùng Slot đã cấp trong Zone `MONTHLY`. |
| F-005      | Booking Management                  | Must     | Booking yêu cầu chọn Building trước, nhập biển số, thanh toán Deposit Fee bằng Base Price của block đầu tiên theo Booking Policy trong main flow và chỉ giữ general capacity ở Building. |
| F-006      | Monthly Subscription Management     | Must     | Đăng ký/gia hạn quyền lợi gửi xe tháng gắn với Vehicle, đảm bảo luôn còn chỗ bằng capacity/slot riêng. |
| F-007      | Parking Session Tracking            | Must     | Theo dõi lượt gửi xe từ check-in đến check-out.                                            |
| F-008      | Vehicle Check-out                   | Must     | Tính phí, xác nhận thanh toán và kết thúc lượt gửi xe.                                     |
| F-009      | Payment Management                  | Must     | Hỗ trợ tiền mặt và thanh toán online thật qua ngân hàng; không hỗ trợ thanh toán thẻ.      |
| F-010      | Fee Calculation                     | Must     | Tính phí theo giờ, loại xe, khung giờ, qua đêm và nhiều ngày.                              |
| F-011      | Exception Handling                  | Should   | Xử lý mất mã, sai biển số, quá hạn, gửi sai khu vực, chưa thanh toán.                      |
| F-012      | Operation Monitoring                | Should   | Manager xem trạng thái bãi, doanh thu, lượt xe, tỷ lệ lấp đầy.                             |
| F-013      | Pricing Policy Management           | Must     | Manager cấu hình Pricing Policy dùng chung toàn hệ thống theo Vehicle Type, không theo Building. |

---

## FR-001: Parking Structure Management

### Description

Hệ thống cho phép Manager cấu hình cấu trúc bãi xe để phục vụ vận hành và mở rộng sau này.

### Actors

- Parking Manager
- System Administrator

### Preconditions

- Actor đã đăng nhập.
- Actor có quyền quản lý cấu trúc bãi xe.

### Trigger

- Actor mở màn hình quản lý cấu trúc bãi xe.

### Main Flow

1. Actor chọn chức năng quản lý cấu trúc bãi xe.
2. Hệ thống hiển thị danh sách Building, Floor, Zone, Slot hiện có.
3. Actor thêm hoặc cập nhật cấu trúc.
4. Hệ thống kiểm tra dữ liệu hợp lệ.
5. Hệ thống lưu cấu hình.
6. Hệ thống cập nhật dữ liệu để phục vụ phân bổ chỗ đỗ.

### Alternative Flow

- Actor tạm khóa Zone/Slot để bảo trì.
- Actor mở lại Zone/Slot sau khi bảo trì.
- Actor thay đổi Zone dành cho loại xe cụ thể.

### Exception Flow

- Nếu Zone/Slot đang có xe hoặc booking, hệ thống không cho xóa trực tiếp.
- Nếu cấu hình làm vượt hoặc sai sức chứa, hệ thống báo lỗi.
- Nếu actor không có quyền, hệ thống từ chối thao tác.

### Business Rules

- BR-001
- BR-002
- BR-003
- BR-004
- BR-005
- BR-006

### Postconditions

- Hệ thống lưu cấu hình.
- Hệ thống cập nhật dữ liệu để phục vụ phân bổ chỗ đỗ.

### Acceptance Criteria

- Given Manager có quyền quản lý cấu trúc  
  When Manager thêm Building/Floor/Zone/Slot hợp lệ  
  Then hệ thống lưu cấu trúc và cho phép dùng trong phân bổ chỗ.

- Given Zone/Slot đang bảo trì  
  When Staff check-in hoặc Driver booking  
  Then hệ thống không phân bổ Zone/Slot đó.

---

## FR-002: Driver Account & Vehicle Management

### Description

Hệ thống cho phép tạo tài khoản Driver trước, không bắt buộc phải có xe ngay lúc tạo tài khoản. Driver có thể thêm một
hoặc nhiều xe sau này.

### Actors

- Driver
- Parking Staff
- System

### Preconditions

- Driver hoặc Staff có quyền tạo/cập nhật thông tin tài khoản.
- Biển số xe được cung cấp khi thêm xe.

### Trigger

- Driver đăng ký tài khoản.
- Driver thêm xe vào tài khoản.
- Staff tạo hoặc cập nhật tài khoản hộ Driver.

### Main Flow

1. Actor mở màn hình quản lý tài khoản Driver.
2. Actor tạo tài khoản Driver với thông tin cơ bản.
3. Hệ thống cho phép lưu tài khoản dù chưa có xe.
4. Khi cần, Actor chọn Add Vehicle.
5. Actor nhập biển số, loại xe.
6. Hệ thống kiểm tra dữ liệu xe.
7. Hệ thống lưu xe vào tài khoản Driver.

### Alternative Flow

- Staff thêm xe hộ Driver.
- Driver thêm nhiều xe vào cùng một tài khoản.

### Exception Flow

| Trường hợp                                  | Xử lý                                          |
|---------------------------------------------|------------------------------------------------|
| Biển số trống khi thêm xe                   | Hệ thống yêu cầu nhập biển số.                 |
| Loại xe không được hỗ trợ                   | Hệ thống từ chối.                              |
| Biển số đã tồn tại theo rule kiểm tra trùng | Hệ thống cảnh báo hoặc từ chối tùy chính sách. |

### Business Rules

- BR-ACC-001
- BR-ACC-002
- BR-ACC-003
- BR-ACC-004

### Postconditions

- Driver Account được tạo.
- Xe được liên kết với Driver Account nếu Actor thêm xe.

### Acceptance Criteria

- Given Driver chưa có xe  
  When Driver tạo tài khoản  
  Then hệ thống vẫn cho tạo tài khoản thành công.

- Given Driver đã có tài khoản  
  When Driver thêm nhiều xe hợp lệ  
  Then hệ thống lưu các xe vào cùng tài khoản.

---

## FR-003: Vehicle Check-in

### Description

Hệ thống cho phép Staff tạo lượt gửi xe khi xe vào bãi bằng cách nhập dữ liệu thủ công trên web.

### Actors

- Parking Staff
- System

### Preconditions

- Staff đã đăng nhập.
- Bãi xe đang hoạt động.
- Có general capacity hoặc Zone/Slot còn khả dụng theo loại xe và nhóm khách tương ứng.

### Trigger

- Xe đến bãi và cần check-in.

### Main Flow

1. Staff mở màn hình Check-in.
2. Staff nhập biển số, mã booking hoặc `card_code`; Walk-in/Booking dùng Card `NORMAL`, Monthly Subscription dùng Card `MONTHLY` đã gán với subscription.
3. Hệ thống xác định loại xe và trạng thái liên quan:
    - Khách vãng lai.
    - Khách có booking.
    - Khách có thẻ tháng.
4. Hệ thống kiểm tra xe có session đang mở hay không.
5. Hệ thống xác định Vehicle Type và kiểm tra tại thời điểm check-in có đúng một Pricing Policy hợp lệ cho Vehicle Type đó:
    - Policy thuộc đúng Vehicle Type.
    - `effective_start <= check_in_time`.
    - `effective_end > check_in_time`.
    - Pricing Window của Policy hợp lệ, không overlap và phủ đủ 24 giờ.
6. Hệ thống kiểm tra điều kiện chỗ đỗ:
    - Xe máy: tìm Zone/Area còn sức chứa.
    - Ô tô Walk-in/Booking: kiểm tra general capacity và gợi ý Zone thuộc nhóm `GENERAL`.
    - Xe máy thẻ tháng: dùng capacity tháng đã giữ tại Building.
    - Ô tô thẻ tháng: dùng Slot riêng trong Zone `MONTHLY`.
    - Khách booking: kiểm tra booking còn hiệu lực.
7. Hệ thống gợi ý Zone/Slot phù hợp; với ô tô Walk-in/Booking chỉ gợi ý Zone `GENERAL` ở bước này.
8. Staff xác nhận check-in.
9. Hệ thống tạo parking session có `card_id`; nếu áp dụng quyền lợi tháng thì lưu `monthly_subscription_id`. Với ô tô Walk-in/Booking, `parking_session.slot_id` có thể `NULL` cho đến khi Slot thực tế được xác nhận. Check-in không tính phí và không bắt buộc lưu `pricing_policy_id` trên Parking Session.
10. Sau khi ô tô Walk-in/Booking thực tế đậu, Staff hoặc hệ thống ghi nhận Slot vào `parking_session.slot_id`; Slot phải thuộc Zone `GENERAL` đã gợi ý, phù hợp loại xe, không bị khóa/bảo trì và chưa bị xe khác chiếm dụng.
11. Khi Slot thực tế được xác nhận, hệ thống chuyển Slot sang `OCCUPIED`; hệ thống chuyển Card `NORMAL` sang `ASSIGNED`, Card `MONTHLY` giữ trạng thái `ASSIGNED`.

### Alternative Flow

- Nếu xe có booking hợp lệ, hệ thống lấy thông tin booking để tạo session.
- Nếu xe có thẻ tháng hợp lệ, hệ thống áp dụng quyền lợi Monthly Subscription thông qua Card `MONTHLY` đã gán với subscription.
- Nếu Staff nhập sai thông tin trước khi xác nhận, Staff có thể sửa lại.

### Exception Flow

| Trường hợp                           | Xử lý                                                   |
|--------------------------------------|---------------------------------------------------------|
| Biển số trống                        | Yêu cầu nhập biển số.                                   |
| Xe đã có session đang mở             | Không cho tạo session mới.                              |
| Booking chưa thanh toán cọc          | Không cho check-in theo booking.                        |
| Booking đã `EXPIRED` do no-show | Không áp dụng booking, xử lý như khách mới nếu còn chỗ. |
| Hết chỗ phù hợp                      | Từ chối check-in.                                       |
| Card không khả dụng                  | Từ chối check-in.                                       |
| Zone/Slot bị khóa hoặc bảo trì       | Không phân bổ.                                          |
| Không xác định được Vehicle Type | Từ chối check-in và không tạo Parking Session. |
| Không có đúng một Pricing Policy hợp lệ tại `check_in_time` | Từ chối check-in và không tạo Parking Session. |
| Pricing Window của Policy không hợp lệ hoặc không phủ đủ 24 giờ | Từ chối check-in và không tạo Parking Session. |

### Business Rules

- BR-HW-001
- BR-HW-002
- BR-HW-003
- BR-ALLOC-001
- BR-ALLOC-002
- BR-ALLOC-003
- BR-ALLOC-004
- BR-ALLOC-005
- BR-BOOK-008
- BR-MONTH-001
- BR-MONTH-002
- BR-MONTH-003
- BR-028
- BR-030
- BR-FEE-024
- BR-FEE-025
- BR-FEE-027
- BR-FEE-028

### Postconditions

- Hệ thống tạo parking session.
- Parking session có `card_id` bắt buộc trong scope hiện tại.
- Hệ thống cập nhật trạng thái chỗ đỗ.

### Acceptance Criteria

- Given Staff nhập biển số và loại xe hợp lệ  
  When hệ thống còn chỗ phù hợp  
  Then hệ thống tạo parking session thành công.

- Given xe đang có session chưa hoàn tất  
  When Staff check-in cùng biển số  
  Then hệ thống báo xe đang ở trong bãi.

- Given không có Pricing Policy hợp lệ cho Vehicle Type  
  When Staff thực hiện check-in  
  Then hệ thống từ chối check-in  
  And không tạo Parking Session.

- Given có nhiều Pricing Policy cùng hợp lệ cho Vehicle Type tại thời điểm check-in  
  When Staff thực hiện check-in  
  Then hệ thống từ chối check-in  
  And yêu cầu Manager xử lý overlap Policy.

- Given Pricing Policy của Vehicle Type có Pricing Window overlap hoặc không phủ đủ 24 giờ  
  When Staff thực hiện check-in  
  Then hệ thống từ chối check-in  
  And không tạo Parking Session.

---

## FR-004: Parking Allocation

### Description

Hệ thống phân bổ chỗ đỗ theo loại xe và theo trạng thái khách vãng lai, booking hoặc thẻ tháng.

### Actors

- Parking Staff
- Driver
- System

### Preconditions

- Cấu trúc Building/Floor/Zone/Slot đã được cấu hình.
- Zone/Slot có trạng thái hợp lệ.
- Loại xe đã được xác định.

### Trigger

- Check-in xe vãng lai.
- Check-in xe có booking.
- Check-in xe có thẻ tháng.

### Main Flow

1. Hệ thống nhận thông tin loại xe và trạng thái khách.
2. Hệ thống xác định nhóm phân bổ:
    - Walk-in.
    - Booking.
    - Monthly Subscription.
3. Hệ thống kiểm tra Zone/capacity/Slot khả dụng theo nhóm phân bổ.
4. Hệ thống loại bỏ Zone/Slot bảo trì, tạm khóa hoặc không phù hợp.
5. Nếu là xe máy, hệ thống gợi ý Zone/Area còn sức chứa.
6. Nếu là ô tô Walk-in/Booking, hệ thống kiểm tra general capacity và gợi ý Zone thuộc nhóm `GENERAL`; chưa gán Slot tại bước gợi ý.
7. Nếu là ô tô thẻ tháng, hệ thống dùng Slot riêng đã cấp trong Zone `MONTHLY`.
8. Nếu là xe máy thẻ tháng, hệ thống dùng capacity tháng đã giữ và gợi ý Zone còn chỗ khi check-in.
9. Walk-in/Booking không được dùng capacity xe máy đã giữ cho Monthly Subscription hoặc Slot trong Zone `MONTHLY`.
10. Staff xác nhận phân bổ.
11. Với ô tô Walk-in/Booking, sau khi xe thực tế đậu, Staff hoặc hệ thống ghi nhận Slot thực tế vào `parking_session.slot_id` và cập nhật Slot thành `OCCUPIED`.
12. Hệ thống cập nhật trạng thái.

### Alternative Flow

- Staff có thể điều chỉnh Zone gợi ý hoặc Slot thực tế nếu vẫn hợp lệ với nhóm sử dụng: ô tô Walk-in/Booking chỉ Zone `GENERAL`, ô tô thẻ tháng chỉ Slot đã gán trong `MONTHLY`.
- Hệ thống có thể gợi ý nhiều lựa chọn nếu có nhiều chỗ phù hợp.

### Exception Flow

| Trường hợp                              | Xử lý                          |
|-----------------------------------------|--------------------------------|
| Không còn Zone cho xe máy               | Từ chối check-in.              |
| Không còn Zone/Slot phù hợp cho ô tô    | Từ chối check-in.              |
| Phần chỗ còn lại phải giữ cho thẻ tháng | Không cấp cho walk-in/booking. |
| Zone/Slot bảo trì                       | Không phân bổ.                 |

### Business Rules

- BR-ALLOC-001
- BR-ALLOC-002
- BR-ALLOC-003
- BR-ALLOC-004
- BR-ALLOC-005

### Postconditions

- Xe được phân bổ Zone/capacity phù hợp; Slot chỉ được ghi nhận khi nghiệp vụ yêu cầu Slot thực tế.
- Capacity được cập nhật theo session/booking/subscription; Slot status chỉ phản ánh trạng thái vật lý hoặc vận hành của Slot.

### Acceptance Criteria

- Given xe máy check-in  
  When còn Zone phù hợp  
  Then hệ thống gợi ý Zone/Area còn sức chứa.

- Given ô tô Walk-in/Booking check-in  
  When còn general capacity phù hợp  
  Then hệ thống gợi ý Zone `GENERAL` và chưa bắt buộc gán Slot.

- Given ô tô Walk-in/Booking đã đậu thực tế  
  When Staff hoặc hệ thống xác nhận Slot hợp lệ trong Zone đã gợi ý  
  Then hệ thống ghi nhận Slot vào `parking_session.slot_id` và cập nhật Slot thành `OCCUPIED`.

- Given khách thẻ tháng check-in  
  When Monthly Subscription còn hiệu lực  
  Then xe máy dùng capacity tháng đã giữ, ô tô dùng Slot `MONTHLY` đã gán.

---

## FR-005: Booking Management

### Description

Hệ thống cho phép Driver đặt trước chỗ gửi xe.
Booking yêu cầu chọn Building trước, nhập biển số, thời gian gửi dự kiến và thanh toán Deposit Fee trong Main Flow.
Deposit Fee bằng Base Price của block đầu tiên theo Booking Policy áp dụng tại thời điểm Booking được tạo và thanh toán.
Booking phải được tạo tối thiểu 1 tiếng và tối đa 8 tiếng trước thời điểm vào bãi tính từ lúc thanh toán cọc thành công.
Driver không chọn Zone/Slot khi Booking. Booking chỉ giữ general capacity ở mức Building; actual Zone và Slot chỉ được xác định trong quá trình check-in/đỗ xe và lưu vào Parking Session.

### Actors

- Driver
- Parking Staff
- System
- Bank Payment Gateway

### Preconditions

- Driver có tài khoản hoặc được Staff tạo booking hộ.
- Driver chọn Building trước khi tạo booking.
- Driver cung cấp biển số xe.
- Driver cung cấp thời gian dự kiến vào bãi.
- Driver cung cấp thời lượng gửi xe hoặc thời gian dự kiến rời bãi.
- Có đúng một Pricing Policy hợp lệ cho Vehicle Type tại thời điểm Booking được tạo để tính Deposit Fee.
- Hệ thống còn chỗ phù hợp trong Building đã chọn:
    - Xe máy: còn general capacity sau khi trừ Monthly Subscription xe máy `ACTIVE`.
    - Ô tô: còn general car capacity dành cho nhóm `GENERAL`.

### Trigger

- Driver muốn đặt trước chỗ gửi xe.

### Main Flow

1. Driver mở chức năng Booking.
2. Driver chọn Building muốn gửi xe.
3. Driver chọn hoặc nhập biển số xe.
4. Driver chọn loại xe.
5. Driver nhập thời gian dự kiến vào bãi.
6. Driver nhập thời lượng gửi xe hoặc thời gian dự kiến rời bãi.
7. Hệ thống kiểm tra thời gian đặt trước:
    - Không nhỏ hơn 1 tiếng.
    - Không lớn hơn 8 tiếng.
8. Hệ thống xác định Booking Policy theo Vehicle Type và thời điểm Booking được tạo.
9. Hệ thống tính Deposit Fee bằng Base Price của block đầu tiên theo Booking Policy.
10. Hệ thống kiểm tra general capacity trong Building đã chọn:
    - Xe máy: tính capacity còn lại sau khi trừ active motorcycle Monthly Subscriptions, active general motorcycle sessions và confirmed motorcycle bookings.
    - Ô tô: tính general car capacity của Building dành cho Zone `GENERAL`, trừ active general car sessions và confirmed car bookings; không giữ Slot cụ thể.
11. Hệ thống tạo Booking ở trạng thái `PENDING`.
12. Hệ thống tạm giữ general capacity tại Building.
13. Hệ thống tạo Payment cho Booking Deposit với đúng số tiền Deposit đã yêu cầu qua QR, `pricing_policy_id` của Booking Policy và `booking_id` là nguồn duy nhất.
14. Driver thực hiện thanh toán.
15. Bank Payment Gateway trả kết quả thanh toán.
16. Nếu thanh toán thành công, hệ thống chuyển Payment sang `PAID`.
17. Hệ thống chuyển Booking sang `CONFIRMED`.
18. Booking confirmed giữ general capacity ở mức Building; không dùng `slot_status`, không giữ Zone và không giữ Slot cụ thể.
19. Khi Driver đến bãi trong thời gian hợp lệ, hệ thống chuyển booking thành parking session; actual Zone/Slot được xử lý trong luồng check-in và đỗ xe.

### Alternative Flow

- Staff tạo booking hộ Driver.
- Driver hủy booking trước thời gian quy định nếu chính sách cho phép.
- Driver đổi thời gian booking nếu còn general capacity phù hợp trong Building đã chọn.

### Exception Flow

| Trường hợp                                                     | Xử lý                                       |
|----------------------------------------------------------------|---------------------------------------------|
| Không chọn Building                                            | Không cho tạo booking.                      |
| Không nhập biển số                                             | Không cho tạo booking.                      |
| Không có Pricing Policy hợp lệ tại thời điểm Booking | Không cho tạo booking/payment deposit. |
| Thời gian booking không đủ tối thiểu 1 tiếng từ lúc thanh toán | Từ chối booking.                            |
| Thanh toán cọc chưa hoàn tất trong `booking_payment_timeout_minutes` | Booking hết hiệu lực hoặc bị hủy theo status model, general capacity đã tạm giữ được giải phóng, không cập nhật `slot_status`. |
| Không còn chỗ phù hợp                                          | Từ chối booking.                            |
| Không check-in trước `scheduled_check_in_time + checkin_grace_minutes` | Booking chuyển `EXPIRED`, general capacity đã giữ được giải phóng, Deposit Fee không được hoàn và không cập nhật `slot_status`. |
| Booking trùng biển số trong cùng thời gian                     | Cảnh báo hoặc từ chối.                      |

### Business Rules

- BR-BOOK-001
- BR-BOOK-002
- BR-BOOK-003
- BR-BOOK-004
- BR-BOOK-005
- BR-BOOK-006
- BR-BOOK-007
- BR-BOOK-008
- BR-BOOK-009
- BR-BOOK-010
- BR-BOOK-018
- BR-BOOK-019
- BR-BOOK-020
- BR-BOOK-021
- BR-BOOK-022
- BR-BOOK-023
- BR-FEE-009

### Postconditions

- Deposit Payment đã `PAID`.
- Booking đã `CONFIRMED`.
- General capacity của Building được giữ theo rule.
- Không có Zone hoặc Slot cụ thể được giữ cho Booking; actual Zone/Slot chỉ được lưu khi check-in/đỗ xe.
- Booking có thể chuyển thành Parking Session khi check-in hợp lệ.

### Acceptance Criteria

- Given Driver nhập biển số, thời gian vào bãi và thời lượng gửi xe hợp lệ  
  When Driver thanh toán Deposit Fee bằng Base Price của block đầu tiên theo Booking Policy thành công  
  Then hệ thống tạo booking confirmed.

- Given booking được xác nhận  
  When thanh toán Deposit Fee thành công  
  Then hệ thống giữ general capacity ở Building và không đổi `slot_status`.

- Given Driver chưa thanh toán booking sau booking payment timeout  
  When hệ thống kiểm tra trạng thái booking  
  Then booking hết hiệu lực hoặc bị hủy theo status model, general capacity đã tạm giữ được giải phóng và `slot_status` không đổi.

- Given Driver không check-in trong check-in grace time  
  When hệ thống kiểm tra booking  
  Then booking chuyển sang `EXPIRED`, general capacity đã giữ được giải phóng, Deposit Fee không được hoàn trả và `slot_status` không đổi.

- Given Driver đặt booking dưới 1 tiếng từ thời điểm thanh toán  
  When Driver xác nhận booking  
  Then hệ thống từ chối.

- Given Driver đặt booking quá 8 tiếng từ thời điểm thanh toán  
  When Driver xác nhận booking  
  Then hệ thống từ chối.

- Given Driver booking xe máy  
  When còn capacity phù hợp  
  Then hệ thống xác nhận booking nhưng không gán Zone/Slot cụ thể.

- Given Driver booking ô tô  
  When còn general car capacity phù hợp trong Building  
  Then hệ thống xác nhận booking ở mức Building và không giữ Zone/Slot cụ thể.

- Given Driver hủy booking trước giờ booking ít nhất 1 tiếng  
  When hệ thống xử lý hủy  
  Then booking bị hủy và cọc được hoàn.

- Given Driver không check-in trước `scheduled_check_in_time + checkin_grace_minutes`  
  When hệ thống kiểm tra booking  
  Then booking chuyển sang `EXPIRED` và cọc không hoàn lại.

- Given Driver đến sớm hơn giờ booking  
  When còn chỗ phù hợp  
  Then hệ thống cho check-in sớm và bắt đầu tính giờ từ thời điểm check-in thực tế.

- Given Booking được tạo khi Policy A áp dụng  
  And Deposit Payment đã `PAID` theo Policy A  
  And xe check-in sau khi Policy B bắt đầu  
  When hệ thống checkout session  
  Then phí Parking Session được tính theo Policy B tại `check_in_time`  
  And hệ thống chỉ trừ đúng Deposit đã trả theo Policy A.

- Given Booking Deposit lớn hơn Tổng phí thực tế  
  When hệ thống tính `amount_due`  
  Then `amount_due = 0`  
  And Driver không phải thanh toán thêm  
  And check-out tiếp tục theo flow hiện tại  
  And hệ thống không hoàn phần chênh lệch  
  And không lưu hoặc xử lý riêng phần chênh lệch  
  And không tạo credit, wallet hoặc account balance  
  And không tạo Payment âm  
  And không thay đổi Deposit Payment đã tồn tại.

---

## FR-006: Monthly Subscription Management

### Description

Hệ thống cho phép Driver đăng ký/gia hạn Monthly Subscription/thẻ tháng. Monthly Subscription là hồ sơ đăng ký và quyền lợi gửi xe định kỳ gắn với một Vehicle, một Building và một Card `MONTHLY`.
Xe máy được đảm bảo có chỗ bằng capacity động nhưng không phân theo Zone/Slot cụ thể; ô tô được cấp Slot riêng trong Zone `MONTHLY` trong thời hạn quyền lợi.
Quyền lợi tháng áp dụng cho Vehicle đăng ký cố định, được thanh toán trả trước theo chu kỳ, có thời hạn hiệu lực và được xác định bởi Monthly Subscription hợp lệ, không chỉ bởi `card_type`.

### Actors

- Driver
- Parking Staff
- Parking Manager
- System
- Bank Payment Gateway

### Preconditions

- Driver có tài khoản.
- Driver đã có hoặc nhập thông tin xe.
- Hệ thống còn capacity động cho xe máy tháng hoặc Slot trong Zone `MONTHLY` cho ô tô tháng.
- Thanh toán phí thẻ tháng thành công.

### Trigger

- Driver muốn đăng ký hoặc gia hạn thẻ tháng.

### Main Flow

1. Driver hoặc Staff mở chức năng Monthly Subscription.
2. Driver chọn hoặc thêm xe.
3. Hệ thống kiểm tra biển số và loại xe.
4. Hệ thống kiểm tra xe có thẻ tháng còn hiệu lực hay không.
5. Hệ thống kiểm tra khả năng đảm bảo chỗ:
    - Xe máy: kiểm tra capacity động còn lại tại Building.
    - Ô tô: kiểm tra Slot còn có thể cấp riêng trong Zone `MONTHLY`.
6. Nếu còn khả năng đảm bảo, hệ thống tạo `monthly_subscription` ở trạng thái `PENDING` và tạo yêu cầu thanh toán.
7. Driver thanh toán phí thẻ tháng trong thời gian `monthly_subscription_payment_timeout_minutes`.
8. Hệ thống nhận kết quả thanh toán.
9. Nếu thanh toán thành công, hệ thống kích hoạt Monthly Subscription, thiết lập `activated_at`, `expired_at`, gán Card `MONTHLY`, và gán `assigned_slot_id` nếu là ô tô.
10. Khi xe vào bãi:
- Xe máy thẻ tháng được gợi ý Zone còn chỗ.
- Ô tô thẻ tháng được điều hướng tới Slot riêng đã cấp.
11. Hệ thống lưu thời hạn hiệu lực của vé tháng.
12. Hệ thống xác nhận mỗi vé tháng chỉ liên kết với một xe đã đăng ký.
13. Hệ thống hỗ trợ gia hạn vé tháng; sau khi gia hạn thành công, quyền lợi vé tháng được kích hoạt lại ngay lập tức.
14. Hệ thống gán một Card `MONTHLY` cho Monthly Subscription; Card này được Driver giữ trong thời hạn quyền lợi.

### Alternative Flow

- Staff đăng ký thẻ tháng hộ Driver.
- Driver gia hạn thẻ tháng trước khi hết hạn.
- Manager điều chỉnh cấu hình Zone/capacity; hệ thống không dùng counter quota rời làm nguồn sự thật nếu có thể tính từ Zone, Slot, session, booking và subscription.
- Nếu vé tháng hết hạn và xe vẫn còn trong bãi, hệ thống downgrade quyền lợi vé tháng và tính phí vãng lai từ thời điểm hết hạn.

### Exception Flow

| Trường hợp                      | Xử lý                                 |
|---------------------------------|---------------------------------------|
| Xe chưa được thêm               | Yêu cầu thêm xe hoặc nhập biển số.    |
| Xe đã có thẻ tháng còn hiệu lực | Không cho tạo trùng.                  |
| Hết capacity xe máy tháng hoặc hết Slot `MONTHLY` cho ô tô tháng | Từ chối đăng ký/gia hạn. |
| Thanh toán thất bại             | Không kích hoạt thẻ tháng.            |
| Thanh toán không hoàn tất trong `monthly_subscription_payment_timeout_minutes` | Payment chuyển `FAILED`; Monthly Subscription không được kích hoạt hoặc gia hạn; nếu là gia hạn, quyền lợi hiện tại không thay đổi khi khoản thanh toán mới chưa thành công. |
| Thẻ tháng hết hạn               | Không áp dụng quyền lợi khi check-in. |

### Business Rules

- BR-MONTH-001
- BR-MONTH-002
- BR-MONTH-003
- BR-MONTH-004
- BR-MONTH-005
- BR-MONTH-006
- BR-MONTH-007
- BR-MONTH-008
- BR-MONTH-009
- BR-MONTH-010
- BR-MONTH-011
- BR-MONTH-012
- BR-MONTH-013
- BR-MONTH-014
- BR-MONTH-015
- BR-MONTH-016
- BR-MONTH-021
- BR-MONTH-022

### Postconditions

- Thẻ tháng được kích hoạt nếu thanh toán thành công.
- Capacity động xe máy hoặc Slot tháng ô tô được xác định từ Monthly Subscription active và `assigned_slot_id`.
- Mỗi thẻ tháng chỉ liên kết với một xe đã đăng ký.
- Driver có thể gửi xe nhiều lượt/ngày trong thời gian thẻ còn hiệu lực.

### Acceptance Criteria

- Given Driver đăng ký thẻ tháng xe máy  
  When còn capacity đảm bảo  
  Then thẻ tháng được kích hoạt sau khi thanh toán thành công.

- Given Driver đăng ký thẻ tháng ô tô  
  When còn Slot phù hợp  
  Then hệ thống cấp Slot riêng cho xe sau khi thanh toán thành công.

- Given không còn Slot dành cho ô tô thẻ tháng  
  When Driver đăng ký thẻ tháng ô tô  
  Then hệ thống từ chối và báo hết slot thẻ tháng ô tô.

- Given vé tháng hết hạn và xe vẫn còn trong bãi  
  When hệ thống quét trạng thái lúc 00:00  
  Then hệ thống downgrade vé tháng và bắt đầu tính phí vãng lai.

- Given khách gia hạn vé tháng thành công  
  When thanh toán gia hạn được xác nhận  
  Then quyền lợi vé tháng được kích hoạt lại ngay lập tức.

- Given Driver đã có Monthly Subscription còn hiệu lực cho một Vehicle  
  When Driver muốn dùng quyền lợi đó cho Vehicle khác  
  Then hệ thống từ chối vì mỗi Monthly Subscription chỉ áp dụng cho một Vehicle.

- Given Monthly Subscription Payment đang `PENDING`  
  When quá `monthly_subscription_payment_timeout_minutes` mà Payment chưa `PAID`  
  Then Payment chuyển sang `FAILED`  
  And Monthly Subscription không được kích hoạt hoặc gia hạn.

- Given Driver gia hạn Monthly Subscription  
  And Payment gia hạn chưa `PAID`  
  When hệ thống xử lý quyền lợi hiện tại  
  Then quyền lợi hiện tại không bị thay đổi bởi khoản gia hạn chưa thanh toán thành công.

---

## FR-007: Parking Session Tracking

### Description

Hệ thống theo dõi một lượt gửi xe từ lúc check-in đến lúc check-out.

### Actors

- Parking Staff
- Driver
- Parking Manager
- System

### Preconditions

- Parking session đã được tạo.

### Trigger

- Staff, Driver hoặc Manager cần xem trạng thái lượt gửi.

### Main Flow

1. Actor mở màn hình theo dõi lượt gửi.
2. Hệ thống hiển thị danh sách session.
3. Actor tìm theo biển số, mã gửi xe, mã booking hoặc trạng thái.
4. Hệ thống hiển thị thông tin:
    - Biển số.
    - Loại xe.
    - Giờ vào.
    - Zone/Slot.
    - Trạng thái.
    - Phí tạm tính nếu có.
5. Actor xem chi tiết session.

### Alternative Flow

- Driver chỉ xem session của chính mình.
- Manager xem toàn bộ session.
- Staff lọc session theo cổng/khu vực/trạng thái.

### Exception Flow

- Không tìm thấy session.
- Session đã check-out.
- Actor không có quyền xem session.

### Business Rules

- BR-028
- BR-029
- BR-030
- BR-031

### Postconditions

- Actor xem được thông tin session theo quyền.

### Acceptance Criteria

- Given xe đã check-in  
  When Staff tìm bằng biển số  
  Then hệ thống hiển thị session đang mở.

- Given Driver có session đang gửi  
  When Driver mở màn hình theo dõi  
  Then hệ thống hiển thị thông tin lượt gửi hiện tại.

---

## FR-008: Vehicle Check-out

### Description

Hệ thống cho phép Staff xử lý xe ra bãi, tính phí, thanh toán và cập nhật trạng thái chỗ đỗ.

### Actors

- Parking Staff
- System
- Driver

### Preconditions

- Xe có parking session đang mở.
- Staff có quyền check-out.

### Trigger

- Driver muốn rời bãi.

### Main Flow

1. Driver tap Card hoặc Staff tiếp nhận yêu cầu check-out.
2. Hệ thống tìm Parking Session đang mở.
3. Hệ thống xác minh:
    - Card hoặc biển số.
    - Vehicle.
    - Building.
    - Zone/Slot.
    - Booking hoặc Monthly Subscription nếu có.
    - Các Incident/phụ phí chưa xử lý.
4. Hệ thống ghi nhận ngay thời điểm check-out hiện tại vào `check_out_time` tạm thời của lần check-out.
5. Hệ thống xác định Pricing Policy áp dụng cho Parking Session bằng Vehicle Type của xe và `check_in_time`; không chọn Policy theo thời điểm check-out và không yêu cầu Policy đó còn `ACTIVE` tại thời điểm check-out.
6. Hệ thống tính phí từ `check_in_time` đến `check_out_time` đã ghi nhận. Thời gian Driver thực hiện thanh toán sau mốc này không làm tăng phí nếu cùng một quá trình check-out vẫn đang tiếp tục.
7. Hệ thống:
    - Cộng phụ phí/phí phạt.
    - Trừ Booking Deposit hợp lệ.
    - Áp dụng quyền lợi Monthly Subscription.
    - Áp dụng cash rounding nếu chọn tiền mặt.
    - Không làm tròn nếu thanh toán online.
8. Nếu session đi từ Booking, hệ thống tính Tổng phí thực tế bằng phí gửi xe cộng phụ phí/phí phạt, sau đó tính `amount_due = max(0, total_actual_fee - paid_deposit_fee)`.
9. Nếu Deposit Fee đã thanh toán lớn hơn Tổng phí thực tế, `amount_due = 0`; hệ thống không hoàn phần chênh lệch, không lưu/xử lý riêng phần chênh lệch, không tạo credit/wallet/account balance, không tạo Payment âm và không thay đổi Deposit Payment đã tồn tại.
10. Nếu `amount_due = 0`, hệ thống bỏ qua bước thu tiền và hoàn tất check-out.
11. Nếu `amount_due > 0`, hệ thống tạo hoặc sử dụng Payment của lần check-out, lưu `pricing_policy_id` của Policy đã dùng để tính phí và Driver thực hiện thanh toán.
12. Nếu một Payment cụ thể `FAILED` nhưng Driver/Staff vẫn tiếp tục cùng lần checkout, hệ thống giữ Payment cũ để audit, tạo Payment mới nếu cần và giữ nguyên `check_out_time`.
13. Nếu Driver đổi phương thức thanh toán trong cùng lần checkout, hệ thống giữ nguyên `check_out_time`, không tính lại phí và không coi đây là lần check-out mới.
14. Nếu Payment `PAID`, hệ thống hoàn tất Parking Session, giải phóng Zone/Slot và hoàn tất check-out.
15. Nếu Payment vẫn `PENDING`, hệ thống giữ `check_out_time` đã ghi nhận, giữ nguyên occupancy, giữ Card và chờ Driver tiếp tục thanh toán. Parking Session chưa `COMPLETED`.
16. Nếu toàn bộ quá trình checkout chuyển sang `FAILED` hoặc bị hủy, hệ thống rollback nghiệp vụ của lần check-out, hủy `check_out_time` đã ghi nhận, giữ session ở trạng thái đang gửi và lần check-out sau phải ghi nhận `check_out_time` mới.

### Alternative Flow

- Nếu Driver có thẻ tháng hợp lệ và không có khoản phát sinh, hệ thống cho check-out thành công với `amount_due = 0`.
- Nếu Driver có thẻ tháng hợp lệ nhưng có phụ phí/phí phạt, `parking_fee = 0` và `amount_due = penalties + surcharges`; hệ thống phải tạo và hoàn tất Payment trước khi kết thúc check-out.
- Quyền lợi Monthly Subscription được đánh giá tại `check_out_time` đã ghi nhận. Nếu `check_out_time < expired_at`, quyền lợi tháng vẫn áp dụng cho lần checkout đó kể cả khi Payment hoàn tất sau `expired_at`.
- Nếu `expired_at <= check_out_time`, thời gian trước `expired_at` được quyền lợi tháng bao phủ; thời gian từ `expired_at` đến `check_out_time` được tính theo giá Walk-in. Thời gian Payment Pending sau `check_out_time` không tính thêm.
- Nếu Payment còn `PENDING`, xe vẫn được xem là còn trong bãi; Zone/Slot, Card và general capacity chưa được giải phóng.
- Nếu check-out `FAILED`, Payment thất bại vẫn được giữ trạng thái `FAILED` để audit, không xóa lịch sử giao dịch.
- Nếu có phí phát sinh, hệ thống cộng vào tổng phí.

### Exception Flow

| Trường hợp                 | Xử lý                                       |
|----------------------------|---------------------------------------------|
| Không tìm thấy session     | Báo không có lượt gửi đang mở.              |
| Một Payment checkout bị `FAILED` nhưng checkout vẫn tiếp tục | Giữ Payment cũ để audit, tạo Payment mới nếu cần và giữ nguyên `check_out_time`. |
| Đổi phương thức thanh toán trong cùng lần checkout | Giữ nguyên `check_out_time`, không tính lại phí và không tạo lần check-out mới. |
| Chưa thanh toán            | Không cho hoàn tất check-out.               |
| Check-out bị hủy hoặc thất bại | Rollback nghiệp vụ của lần check-out, hủy `check_out_time` đã ghi nhận và giữ session tiếp tục đang gửi. |
| Sai biển số                | Staff xử lý ngoại lệ trước khi check-out.   |
| Có phí phát sinh           | Cộng vào tổng phí trước khi thanh toán.     |
| Mất mã gửi xe              | Staff xác minh bằng biển số/thông tin khác. |

### Business Rules

- BR-032
- BR-033
- BR-034
- BR-035
- BR-036
- BR-037
- BR-038
- BR-039
- BR-040
- BR-041
- BR-PAY-006
- BR-PAY-018
- BR-PAY-019
- BR-PAY-020
- BR-PAY-021
- BR-PAY-022
- BR-PAY-023
- BR-PAY-024
- BR-MONTH-023
- BR-MONTH-024
- BR-FEE-001
- BR-FEE-002
- BR-FEE-003
- BR-FEE-004
- BR-FEE-005
- BR-FEE-007
- BR-FEE-017
- BR-FEE-018
- BR-FEE-019

### Postconditions

- Hệ thống kết thúc session.
- Hệ thống giải phóng Zone/Slot.
- Nếu `amount_due = 0`, bước thu tiền được bỏ qua nhưng các cập nhật check-out, Card, Slot/Zone, Audit Log và dữ liệu vận hành vẫn hoàn tất.
- Nếu Payment còn `PENDING`, Parking Session chưa `COMPLETED`, Zone/Slot chưa được giải phóng và phí vẫn được giữ theo `check_out_time` đã ghi nhận.
- Nếu check-out `FAILED`, `check_out_time` của lần thất bại bị hủy và lần check-out sau tính phí đến thời điểm mới.

### Acceptance Criteria

- Given xe có session đang mở  
  When Staff xác nhận check-out và thanh toán thành công  
  Then session kết thúc và chỗ đỗ được giải phóng.

- Given xe có Parking Session đang mở  
  When Driver bắt đầu check-out lúc 10:00  
  Then hệ thống dùng 10:00 làm thời điểm kết thúc để tính phí  
  And thời gian thanh toán sau đó không làm tăng phí.

- Given thời điểm check-out đã được ghi nhận  
  When Payment vẫn còn `PENDING`  
  Then Parking Session chưa `COMPLETED`  
  And Zone/Slot chưa được giải phóng  
  And phí vẫn được giữ theo thời điểm check-out đã ghi nhận.

- Given thời điểm check-out đã được ghi nhận  
  When toàn bộ quá trình check-out chuyển sang `FAILED`  
  Then thời điểm check-out của lần thất bại bị hủy  
  And Parking Session tiếp tục ở trạng thái đang gửi  
  And lần check-out sau tính phí đến thời điểm mới.

- Given xe Monthly Subscription còn hiệu lực và không có khoản phát sinh  
  When Staff xác nhận check-out  
  Then hệ thống hoàn tất check-out với `actual_parking_fee = 0`, `amount_due = 0` và không yêu cầu chọn payment method.

- Given Driver bắt đầu checkout lúc 10:00  
  When Driver đổi từ online sang tiền mặt trong cùng lần checkout  
  Then hệ thống giữ `check_out_time` là 10:00  
  And không tính lại phí theo thời điểm đổi phương thức.

- Given hệ thống tạo Online Payment trong lần checkout  
  And Online Payment chuyển `FAILED`  
  When Driver tiếp tục thanh toán bằng Cash trong cùng lần checkout  
  Then hệ thống giữ Payment online cũ để audit  
  And tạo Cash Payment mới  
  And giữ nguyên `check_out_time` ban đầu.

- Given Monthly Subscription còn hiệu lực tại `check_out_time`  
  And Subscription hết hạn trong lúc Payment đang `PENDING`  
  When checkout hoàn tất  
  Then hệ thống không tính thêm phí sau `check_out_time`.

- Given Monthly Subscription hết hạn trước hoặc đúng `check_out_time`  
  When hệ thống tính phí  
  Then hệ thống tính phí Walk-in từ `expired_at` đến `check_out_time`.

- Given checkout bị `FAILED` và rollback  
  When Driver checkout lại sau khi Monthly Subscription đã hết hạn  
  Then hệ thống ghi nhận `check_out_time` mới  
  And tính phí Walk-in từ `expired_at` đến `check_out_time` mới nếu áp dụng.

---

## FR-009: Payment Management

### Description

Hệ thống hỗ trợ thanh toán tiền mặt và thanh toán online thật qua ngân hàng. Hệ thống không hỗ trợ thanh toán bằng thẻ.

### Actors

- Driver
- Parking Staff
- System
- Bank Payment Gateway

### Preconditions

- Có khoản phí cần thanh toán.
- Khoản phí thuộc một trong các nghiệp vụ: parking fee, booking deposit, monthly subscription fee hoặc phí phát sinh.
- Mỗi Payment thuộc đúng một nguồn nghiệp vụ chính: `session_id`, `booking_id` hoặc `monthly_subscription_id`.

### Trigger

- Driver thanh toán phí.

### Main Flow - Cash Payment

1. Staff chọn phương thức tiền mặt.
2. Hệ thống hiển thị số tiền cần thu.
3. Staff nhận tiền từ Driver.
4. Staff xác nhận đã thu tiền.
5. Hệ thống cập nhật trạng thái `PAID`.
6. Nếu số tiền cuối cùng sau phí gửi xe, phụ phí/phí phạt và Booking Deposit hợp lệ cần làm tròn khi thu tiền mặt, hệ thống áp dụng cash rounding rule.
7. Hệ thống hiển thị số tiền sau làm tròn để Staff thu tiền.

### Main Flow - Bank Online Payment

1. Driver chọn thanh toán online qua ngân hàng.
2. Hệ thống tạo yêu cầu thanh toán.
3. Driver thực hiện thanh toán.
4. Bank Payment Gateway trả kết quả.
5. Nếu ngân hàng xác nhận đã nhận tiền thành công, hệ thống cập nhật trạng thái Payment sang `PAID`.
6. Nếu một lần thử giao dịch bị ngân hàng từ chối nhưng payment session vẫn còn hiệu lực và Driver vẫn có thể thử lại, Payment vẫn là `PENDING`.
7. Nếu Driver hủy, Staff xác nhận quy trình không thể tiếp tục, hoặc hệ thống nhận kết quả cuối cùng không thể tiếp tục, Payment chuyển sang `FAILED`. Timeout chỉ áp dụng cho Booking Payment và Monthly Subscription Payment theo cấu hình riêng, không áp dụng cho Checkout Payment.

### Alternative Flow

- Cho thanh toán lại hoặc đổi sang tiền mặt trong cùng lần checkout bằng cách giữ Payment cũ để audit nếu đã `FAILED` và tạo Payment mới; không đổi Payment `FAILED` trở lại `PENDING`.
- Với checkout Payment còn `PENDING`, Parking Session chưa `COMPLETED`, Zone/Slot và Card chưa được giải phóng, nhưng phí không tiếp tục tăng vì `check_out_time` của cùng quá trình check-out đã được ghi nhận.
- Nếu một checkout Payment chuyển `FAILED` nhưng checkout vẫn tiếp tục bằng Payment mới, hệ thống không rollback `check_out_time`. Chỉ khi toàn bộ checkout bị kết thúc Failed mới rollback nghiệp vụ của lần check-out.
- Payment tạo cho Booking Deposit chỉ có `booking_id`; Payment tạo cho Monthly Subscription purchase/renewal chỉ có `monthly_subscription_id`; Payment tạo cho parking fee hoặc checkout penalty chỉ có `session_id`.

### Exception Flow

| Trường hợp                   | Xử lý                                                             |
|------------------------------|-------------------------------------------------------------------|
| Một lần thử online bị từ chối nhưng quy trình còn hiệu lực | Payment có thể vẫn `PENDING` nếu gateway chưa kết thúc giao dịch; nếu Payment đã `FAILED`, lần thử lại tạo Payment mới. |
| Quy trình thanh toán kết thúc không thành công | Payment chuyển `FAILED`. |
| Thanh toán chưa xác nhận     | Không hoàn tất check-out/booking/monthly subscription.            |
| Người dùng chọn card payment | Không hiển thị hoặc báo không hỗ trợ.                             |
| Staff xác nhận sai           | Cần xử lý điều chỉnh theo quyền quản lý.                          |
| Booking quá thời gian thanh toán | Payment chuyển `FAILED`; booking hết hiệu lực hoặc bị hủy theo status model, general capacity đã giữ được giải phóng và không cập nhật `slot_status`. |
| Monthly Subscription quá thời gian thanh toán | Payment chuyển `FAILED`; subscription không được kích hoạt/gia hạn và không giữ capacity/Slot như subscription `ACTIVE`. |
| Checkout Payment chờ lâu | Không tự động chuyển `FAILED`; Staff chịu trách nhiệm xử lý Payment Pending tại cổng hoặc quầy checkout. |
| Cần hoàn cọc booking | Hệ thống tạo trạng thái refund/refunded theo chính sách hoàn tiền. |

### Business Rules

- BR-PAY-001
- BR-PAY-002
- BR-PAY-003
- BR-PAY-004
- BR-PAY-005
- BR-PAY-006
- BR-PAY-007
- BR-PAY-009
- BR-PAY-012
- BR-PAY-014
- BR-PAY-015
- BR-PAY-016
- BR-PAY-017
- BR-PAY-018
- BR-PAY-019
- BR-PAY-020
- BR-PAY-021
- BR-PAY-022
- BR-PAY-023
- BR-PAY-024
- BR-MONTH-021
- BR-MONTH-022

### Postconditions

- Khoản phí được cập nhật trạng thái thanh toán.
- Nghiệp vụ liên quan chỉ tiếp tục khi payment hợp lệ.
- Mỗi Payment có đúng một trong ba nguồn `session_id`, `booking_id`, `monthly_subscription_id`.

### Acceptance Criteria

- Given Driver chọn online banking  
  When ngân hàng xác nhận thành công  
  Then hệ thống cập nhật payment là `PAID`.

- Given một lần thử online bị ngân hàng từ chối nhưng payment session chưa timeout và Driver có thể thử lại  
  When hệ thống nhận kết quả từ ngân hàng  
  Then Payment vẫn là `PENDING`.

- Given Driver hủy thanh toán hoặc Payment timeout áp dụng cho Booking/Monthly Subscription  
  When quy trình thanh toán kết thúc không thành công  
  Then Payment chuyển sang `FAILED`.

- Given Checkout Payment đang `PENDING`  
  When chưa có Staff hoặc Driver kết thúc quá trình  
  Then hệ thống không tự động chuyển Payment sang `FAILED` chỉ vì thời gian chờ.

- Given Payment cũ đã `FAILED`  
  When Driver thử thanh toán lại  
  Then hệ thống giữ Payment cũ để audit  
  And tạo Payment mới.

- Given Driver bắt đầu checkout lúc 10:00  
  When Driver đổi từ online sang tiền mặt trong cùng lần checkout  
  Then hệ thống giữ `check_out_time` là 10:00.

- Given check-out Payment vẫn còn `PENDING`  
  When Driver tiếp tục cùng quá trình thanh toán  
  Then phí không tiếp tục tăng theo thời gian thanh toán  
  And Zone/Slot chưa được giải phóng.

- Given Driver chọn thanh toán bằng thẻ  
  When hệ thống hiển thị phương thức thanh toán  
  Then card payment không xuất hiện.

- Given khách thanh toán tiền mặt và số tiền cần làm tròn  
  When hệ thống áp dụng cash rounding rule  
  Then số tiền thu được làm tròn theo cash_rounding_unit và rounding_threshold.

- Given khách thanh toán online  
  When hệ thống tạo giao dịch  
  Then số tiền thanh toán giữ nguyên giá trị chính xác, không làm tròn.

---

## FR-010: Fee Calculation

### Description

Hệ thống tính phí gửi xe dựa trên thời gian thực tế sử dụng, loại xe, pricing window, base duration, base price, increment block, increment price, window cap, grace period và các phụ phí/phí phạt nếu có.

### Actors

- Parking Staff
- Driver
- System

### Preconditions

- Parking session có thời gian check-in.
- Vehicle Type của Parking Session đã được xác định.
- Có đúng một Pricing Policy áp dụng cho Vehicle Type tại `check_in_time`.
- Có Pricing Window hợp lệ trong Policy áp dụng tại `check_in_time`.
- Các biến cấu hình tính phí đã được thiết lập.

### Trigger

- Staff thực hiện check-out.
- Driver xem phí tạm tính.
- Hệ thống cần tính tổng phí.

### Main Flow

1. Hệ thống lấy thông tin parking session.
2. Hệ thống xác định loại xe.
3. Hệ thống dùng `check_out_time` đã ghi nhận khi bắt đầu check-out làm mốc kết thúc tính phí.
4. Hệ thống tìm Pricing Policy áp dụng cho Vehicle Type tại `check_in_time`; không xác định lại Pricing Policy theo thời điểm check-out và không yêu cầu Policy đó còn `ACTIVE` tại thời điểm check-out.
5. Hệ thống xác định các Pricing Window trong Policy áp dụng tại `check_in_time` mà session đi qua.
6. Nếu session đi qua nhiều Pricing Window hoặc nhiều ngày, hệ thống tách session thành các đoạn thời gian tương ứng.
7. Với từng đoạn thời gian có thời lượng lớn hơn 0, hệ thống áp dụng:
   - Base Duration.
   - Base Price.
   - Increment Block.
   - Increment Price.
   - Grace Period.
   - Window Cap.
8. Nếu đoạn có thời lượng lớn hơn 0, đoạn đó được tính ít nhất Base Price của Pricing Window tương ứng. Grace Period chỉ áp dụng cho phần dư của thời gian phát sinh và không loại bỏ Base Price.
9. Phần vượt quá Base Duration được chia thành các Increment Block; mỗi block đầy đủ tính một Increment Price.
10. Nếu còn thời gian dư sau các block đầy đủ:
    - Nếu phần dư nhỏ hơn hoặc bằng `grace_period_minutes`, không tính thêm block.
    - Nếu phần dư lớn hơn `grace_period_minutes`, tính thêm một block.
11. Sau khi tính Base Price và Increment Price, hệ thống áp dụng `window_cap` nếu được cấu hình; phí của đoạn không vượt quá Window Cap.
12. Tổng phí session là tổng phí của tất cả các đoạn sau khi áp dụng `window_cap` cho từng đoạn/lần xuất hiện của Pricing Window tương ứng.
13. Nếu session có booking deposit, hệ thống chỉ khấu trừ Deposit Payment đã `PAID`, thuộc đúng Booking đã tạo Parking Session, chưa `REFUNDED` và chưa được dùng/khấu trừ lần khác.
14. Hệ thống tính Tổng phí thực tế:
    `total_actual_fee = actual_parking_fee + penalties + surcharges`.
15. Hệ thống tính số tiền còn phải trả:
    `amount_due = max(0, total_actual_fee - paid_deposit_fee)`.
16. Nếu Deposit Fee đã thanh toán lớn hơn Tổng phí thực tế, `amount_due = 0`, Driver không phải thanh toán thêm, không hoàn phần chênh lệch, không lưu/xử lý riêng phần chênh lệch, không tạo credit/wallet/account balance, không tạo Payment âm và không thay đổi Deposit Payment đã tồn tại.
17. Phần overtime của Booking không được tính hai lần; hệ thống có thể hiển thị overtime như một dòng breakdown riêng, nhưng tổng phí được tính một lần trên thời gian thực tế theo Pricing Window.
18. Nếu thanh toán tiền mặt, hệ thống áp dụng cash rounding rule.
19. Nếu thanh toán online, hệ thống giữ nguyên giá trị chính xác.
20. Hệ thống hiển thị tổng phí cần thanh toán.

### Alternative Flow

- Nếu xe có vé tháng hợp lệ và không có khoản phát sinh, hệ thống đặt `actual_parking_fee = 0` và `amount_due = 0`.
- Nếu vé tháng hết hạn trong lúc xe vẫn còn trong bãi, hệ thống tính phí vãng lai từ thời điểm hết hạn.
- Nếu Manager kích hoạt Pricing Policy mới sau khi xe check-in, Parking Session vẫn dùng Pricing Policy áp dụng tại `check_in_time`.
- Nếu xe check-out trễ hơn thời gian booking, phần phát sinh có thể hiển thị riêng trong breakdown nhưng không được cộng thêm lần thứ hai ngoài tổng phí thực tế theo Pricing Window.
- Nếu có lost card penalty hoặc wrong zone penalty, hệ thống cộng vào tổng phí.

### Pricing Window Example

Ví dụ minh họa dựa trên một cấu hình bảng giá giả định.

Xe đậu từ 20:00 ngày 14/02 đến 12:00 ngày 16/02.

Hệ thống chia thành:

1. Night Window: 20:00 ngày 14/02 → 06:00 ngày 15/02.
2. Day Window: 06:00 ngày 15/02 → 18:00 ngày 15/02.
3. Night Window: 18:00 ngày 15/02 → 06:00 ngày 16/02.
4. Day Window: 06:00 ngày 16/02 → 12:00 ngày 16/02.

Mỗi đoạn được tính theo rule của Pricing Window tương ứng và áp dụng `window_cap` của chính đoạn đó. Parking Session vẫn là một session duy nhất và không bị reset tại 00:00 hoặc khi chuyển sang ngày mới.

Nếu Parking Session đi qua một Pricing Window trong thời lượng lớn hơn 0, đoạn đó phải chịu ít nhất Base Price của Window tương ứng.

Ví dụ minh họa dựa trên một cấu hình bảng giá giả định:

- Day Window: 06:00–18:00.
- Night Window: 18:00–06:00.
- Xe gửi từ 17:59 đến 18:01.

Hệ thống chia thành:

1. Day Window: 17:59 → 18:00.
2. Night Window: 18:00 → 18:01.

Cả hai đoạn đều có thời lượng lớn hơn 0, nên mỗi đoạn được tính ít nhất Base Price của Window tương ứng.

Ranh giới Pricing Window được xử lý như sau: thời điểm bắt đầu Window thuộc Window đó; thời điểm kết thúc Window thuộc Window tiếp theo; một thời điểm không được ghi nhận đồng thời trong hai Pricing Window. Nếu đoạn có thời lượng bằng 0 thì không tính phí cho đoạn đó.

### Exception Flow

| Trường hợp | Xử lý |
|---|---|
| Thiếu bảng giá | Hệ thống báo không thể tính phí. |
| Thiếu pricing window | Hệ thống báo thiếu cấu hình khung giờ. |
| Thiếu base duration/base price/increment block/increment price | Hệ thống báo thiếu cấu hình bảng giá. |
| Thời gian check-out nhỏ hơn check-in | Hệ thống báo lỗi dữ liệu. |
| Không xác định được loại xe | Hệ thống yêu cầu cập nhật thông tin. |
| Thiếu cấu hình grace period hoặc rounding | Hệ thống báo thiếu cấu hình chính sách tính phí. |

### Business Rules

- BR-FEE-001
- BR-FEE-002
- BR-FEE-003
- BR-FEE-004
- BR-FEE-005
- BR-FEE-006
- BR-FEE-007
- BR-FEE-008
- BR-FEE-009
- BR-FEE-010
- BR-FEE-011
- BR-FEE-012
- BR-FEE-013
- BR-FEE-014
- BR-FEE-015
- BR-FEE-016
- BR-FEE-017
- BR-FEE-018
- BR-FEE-019
- BR-FEE-020
- BR-FEE-021
- BR-FEE-022
- BR-FEE-023
- BR-FEE-024
- BR-FEE-025
- BR-FEE-026
- BR-FEE-027
- BR-FEE-028
- BR-FEE-029
- BR-FEE-030

### Postconditions

- Tổng phí được tính và hiển thị.
- Payment record có thể được tạo dựa trên tổng phí.
- Nếu có rounding, hệ thống lưu cả số tiền gốc và số tiền sau làm tròn nếu cần đối soát.

### Acceptance Criteria

- Given xe có session hợp lệ  
  When Staff check-out  
  Then hệ thống tính phí theo loại xe và thời gian thực tế sử dụng.

- Given session đi qua nhiều pricing window  
  When hệ thống tính phí  
  Then hệ thống tách session theo từng pricing window và áp dụng bảng giá riêng cho từng window.

- Given xe check-in khi Policy A đang áp dụng  
  And Policy B được kích hoạt trước khi xe check-out  
  When hệ thống tính phí  
  Then toàn bộ Parking Session vẫn sử dụng Policy A.

- Given xe check-in khi Policy A áp dụng  
  And Policy A đã hết hạn trước lúc xe checkout  
  When hệ thống tính phí  
  Then hệ thống vẫn sử dụng Policy A cho toàn bộ session.

- Given phí trong một pricing window vượt window cap  
  When hệ thống tính phí window đó  
  Then phí của window đó không vượt quá window cap.

- Given session đi qua ngày mới  
  When hệ thống tính phí  
  Then session không bị reset và tiếp tục được tính theo pricing window tương ứng.

- Given session đi qua một Pricing Window trong thời lượng lớn hơn 0  
  When hệ thống tính phí đoạn đó  
  Then đoạn đó được tính ít nhất Base Price của Window tương ứng.

- Given Day Window kết thúc và Night Window bắt đầu lúc 18:00  
  When một thời điểm đúng bằng 18:00 được phân loại  
  Then thời điểm đó chỉ thuộc Night Window.

- Given thời gian phát sinh nhỏ hơn hoặc bằng grace period  
  When hệ thống tính block phát sinh  
  Then hệ thống không tính block mới.

- Given thời gian phát sinh lớn hơn grace period  
  When hệ thống tính block phát sinh  
  Then hệ thống tính thành block mới.

- Given khách thanh toán tiền mặt và số tiền cần làm tròn  
  When hệ thống tính tổng phí  
  Then hệ thống áp dụng cash rounding rule.

- Given khách thanh toán online  
  When hệ thống tính tổng phí  
  Then hệ thống giữ nguyên số tiền chính xác, không làm tròn.

---

## FR-011: Exception Handling

### Description

Hệ thống hỗ trợ Staff xử lý các tình huống ngoại lệ trong vận hành.

### Actors

- Parking Staff
- Parking Manager
- System

### Preconditions

- Có tình huống ngoại lệ phát sinh.
- Staff có quyền xử lý.

### Trigger

- Có tình huống ngoại lệ phát sinh.

### Supported Exceptions

| Exception                  | Mô tả                                        |
|----------------------------|----------------------------------------------|
| Lost Virtual Card Code | Driver mất mã gửi xe/mã thẻ mô phỏng. |
| Wrong Plate Number | Biển số nhập sai hoặc không khớp. |
| Overdue Parking | Xe gửi quá thời gian dự kiến/booking. |
| Wrong Zone Parking | Xe đỗ sai khu vực quy định hoặc quá thời gian cho phép ở khu vực đó. |
| Unpaid Session | Xe chưa thanh toán. |
| Slot Occupied Unexpectedly | Slot đã bị chiếm hoặc trạng thái không đúng. |
| Lost Card Penalty | Phí phạt áp dụng khi Staff chuyển trạng thái vé thành LOST. |
| Wrong Zone Penalty | Phí phạt áp dụng khi xe đỗ sai khu vực và được Staff xác nhận. |

### Main Flow

1. Staff mở session hoặc booking có vấn đề.
2. Staff chọn loại ngoại lệ.
3. Hệ thống hiển thị thông tin liên quan.
4. Staff nhập lý do xử lý.
5. Hệ thống yêu cầu xác nhận.
6. Hệ thống cập nhật trạng thái hoặc phí phát sinh nếu có.
7. Hệ thống lưu kết quả xử lý.
8. Nếu ngoại lệ phát sinh phí phạt, hệ thống cộng phí phạt vào tổng phí cần thanh toán.
9. Nếu là mất vé/mã gửi xe, hệ thống chỉ cho phép xe rời bãi sau khi khách thanh toán phí gửi xe hiện tại và lost card penalty.

### Alternative Flow

- Một số ngoại lệ cần Manager xác nhận.

### Exception Flow

- Xe chưa thanh toán không được hoàn tất check-out, trừ khi Manager xử lý đặc biệt.
- Sai biển số phải được chỉnh trước khi hoàn tất session.

### Business Rules

- BR-042
- BR-043
- BR-044
- BR-045
- BR-046

### Postconditions

- Hệ thống cập nhật trạng thái hoặc phí phát sinh nếu có.
- Hệ thống lưu kết quả xử lý.

### Acceptance Criteria

- Given session có lỗi sai biển số  
  When Staff cập nhật và xác nhận lý do  
  Then hệ thống lưu thông tin đã chỉnh sửa.

- Given xe chưa thanh toán  
  When Staff cố check-out  
  Then hệ thống chặn hoàn tất check-out.

- Given Staff chuyển trạng thái vé thành LOST  
  When hệ thống tính phí check-out  
  Then hệ thống cộng phí gửi xe hiện tại và lost card penalty vào tổng phí.

- Given xe bị xác nhận đỗ sai khu vực  
  When Staff xử lý ngoại lệ  
  Then hệ thống cộng wrong zone penalty nếu chính sách áp dụng.

---

## FR-012: Operation Monitoring

### Description

Hệ thống cho phép Manager theo dõi tình trạng vận hành của bãi xe.

### Actors

- Parking Manager
- System

### Preconditions

- Manager đã đăng nhập.
- Có dữ liệu vận hành.

### Trigger

- Manager mở dashboard vận hành.

### Main Flow

1. Manager mở dashboard vận hành.
2. Hệ thống hiển thị:
    - Tổng số Zone/Slot.
    - Số chỗ còn trống.
    - Số chỗ đang sử dụng.
    - Số chỗ đang được giữ capacity hoặc được cấp quyền tháng.
    - Số chỗ bảo trì/tạm khóa.
    - Lượt xe vào/ra.
    - Doanh thu.
    - Tỷ lệ lấp đầy.
3. Manager lọc theo Building, Floor, Zone, loại xe hoặc thời gian.
4. Hệ thống cập nhật kết quả hiển thị.

### Alternative Flow

- Manager lọc theo Building, Floor, Zone, loại xe hoặc thời gian.

### Exception Flow

- Không có dữ liệu vận hành.

### Business Rules

- BR-047
- BR-048
- BR-049
- BR-050
- BR-051

### Postconditions

- Hệ thống hiển thị tình trạng bãi xe và doanh thu tương ứng.

### Acceptance Criteria

- Given Manager mở dashboard  
  When hệ thống có dữ liệu session/payment  
  Then hệ thống hiển thị tình trạng bãi xe và doanh thu tương ứng.

---

## FR-013: Pricing Policy Management

### Description

Hệ thống cho phép Parking Manager cấu hình Pricing Policy theo Vehicle Type. Trong phiên bản hiện tại, Pricing Policy có phạm vi toàn hệ thống theo Vehicle Type; hệ thống chưa hỗ trợ một Vehicle Type có giá khác nhau giữa các Building.

Pricing Policy áp dụng cho Parking Session được xác định bằng Vehicle Type của xe và `check_in_time`. Không bắt buộc lưu `pricing_policy_id` trên Parking Session. Payment phát sinh từ Parking Session hoặc Booking Deposit phải lưu `pricing_policy_id` của Policy thực sự được dùng để audit.

### Actors

- Parking Manager
- System Administrator
- System

### Preconditions

- Actor đã đăng nhập.
- Actor có quyền quản lý Pricing Policy.
- Vehicle Type cần áp dụng đã tồn tại.

### Trigger

- Manager cần tạo, xem, kích hoạt, điều chỉnh thời gian hiệu lực hợp lệ hoặc xem lịch sử Pricing Policy.

### Main Flow

1. Manager mở màn hình Pricing Policy Management.
2. Manager chọn Vehicle Type.
3. Manager tạo Pricing Policy mới.
4. Manager nhập `effective_start` và `effective_end`.
5. Manager thêm một hoặc nhiều Pricing Window.
6. Manager cấu hình cho từng Pricing Window:
    - Base Duration.
    - Base Price.
    - Increment Block.
    - Increment Price.
    - Grace Period.
    - Window Cap.
7. Hệ thống kiểm tra:
    - Policy không overlap với Policy khác cùng Vehicle Type.
    - Pricing Window không overlap.
    - Pricing Window phủ đủ 24 giờ.
    - Các giá trị thời gian và tiền hợp lệ.
8. Policy được lưu ở trạng thái chưa áp dụng.
9. Manager kích hoạt Policy.
10. Sau khi Policy `ACTIVE`, cấu hình giá và Pricing Window bị khóa.
11. Manager muốn thay đổi giá hoặc cấu trúc Pricing Window phải tạo Pricing Policy mới.

### Alternative Flow

- System Administrator thực hiện cấu hình nếu có quyền tương ứng.
- Manager thay đổi `effective_start` của Policy tương lai nếu thời điểm mới vẫn nằm trong tương lai, không overlap với Policy trước/sau và Policy chưa được dùng bởi Parking Session.
- Manager thay đổi `effective_end` của Policy tương lai hoặc đang `ACTIVE` nếu `effective_end` mới nằm trong tương lai, không gây overlap, không làm mất hiệu lực hồi tố đối với session đã check-in và không thay đổi cấu hình giá/Pricing Window.
- Manager xem Policy đã `EXPIRED` để audit; Policy `EXPIRED` không được thay đổi `effective_end` và không được sửa cấu hình giá nếu đã từng được sử dụng.

### Exception Flow

| Trường hợp | Xử lý |
|---|---|
| Actor chọn Building khi tạo policy | Hệ thống không hỗ trợ vì Pricing Policy dùng chung toàn hệ thống theo Vehicle Type. |
| Thiếu Vehicle Type | Không cho lưu policy. |
| Thiếu Pricing Window | Không cho kích hoạt policy. |
| Thiếu Base Duration/Base Price/Increment Block/Increment Price | Không cho lưu hoặc kích hoạt policy. |
| Policy overlap với Policy khác cùng Vehicle Type | Từ chối lưu hoặc kích hoạt Policy mới. |
| Pricing Window overlap | Từ chối kích hoạt Policy. |
| Pricing Window không phủ đủ 24 giờ | Từ chối kích hoạt Policy. |
| Manager cố sửa Base Price hoặc Pricing Window của Policy `ACTIVE` | Từ chối thay đổi và yêu cầu tạo Policy mới. |
| Manager cố sửa cấu hình giá của Policy đã được sử dụng | Từ chối thay đổi. |
| Manager cố đổi `effective_start` của Policy `ACTIVE` | Từ chối thay đổi. |
| Manager cố đổi `effective_end` theo hướng hồi tố hoặc gây overlap | Từ chối thay đổi. |
| Manager cố xóa Policy đã được Parking Session hoặc Payment sử dụng | Từ chối xóa trực tiếp và giữ Policy lịch sử để audit. |

### Business Rules

- BR-FEE-003
- BR-FEE-004
- BR-FEE-005
- BR-FEE-007
- BR-FEE-011
- BR-FEE-012
- BR-FEE-019
- BR-FEE-020
- BR-FEE-021
- BR-FEE-022
- BR-FEE-023
- BR-FEE-024
- BR-FEE-025
- BR-FEE-026
- BR-FEE-027
- BR-FEE-028
- BR-FEE-029
- BR-FEE-030
- BR-FEE-031
- BR-FEE-032

### Postconditions

- Pricing Policy được lưu theo Vehicle Type.
- Pricing Policy không gắn trực tiếp với Building.
- Parking Session không bắt buộc lưu `pricing_policy_id`; Policy áp dụng được truy xuất bằng Vehicle Type và `check_in_time`.
- Payment dùng cho session hoặc booking deposit lưu `pricing_policy_id` của Policy thực sự dùng để tính/audit.

### Acceptance Criteria

- Given hai Building cùng phục vụ một Vehicle Type  
  When một Pricing Policy của Vehicle Type đó được kích hoạt  
  Then policy được sử dụng chung cho cả hai Building.

- Given Manager tạo Pricing Policy  
  When Manager cố chọn Building áp dụng  
  Then hệ thống không cho chọn Building vì policy dùng chung theo Vehicle Type.

- Given Parking Session đã check-in với Policy A  
  When Manager kích hoạt Policy B sau đó  
  Then session hiện tại vẫn sử dụng Policy A được xác định theo `check_in_time`.

- Given Pricing Policy đang `ACTIVE`  
  When Manager cố thay đổi Base Price hoặc Pricing Window  
  Then hệ thống từ chối thay đổi  
  And yêu cầu Manager tạo Policy mới.

- Given Pricing Policy đã được dùng bởi Parking Session  
  When Manager cố sửa cấu hình giá  
  Then hệ thống từ chối thay đổi.

- Given Vehicle Type đã có một Policy trong khoảng thời gian xác định  
  When Manager tạo Policy khác có khoảng hiệu lực overlap  
  Then hệ thống từ chối lưu hoặc kích hoạt Policy mới.

- Given các Pricing Window overlap hoặc không phủ đủ 24 giờ  
  When Manager kích hoạt Policy  
  Then hệ thống từ chối kích hoạt.

- Given Policy đang `ACTIVE`  
  When Manager thay đổi `effective_end` mới nằm trong tương lai, không gây overlap và không làm mất hiệu lực hồi tố  
  Then hệ thống cho phép cập nhật `effective_end`  
  And không cho thay đổi cấu hình giá hoặc Pricing Window trong thao tác đó.

---
