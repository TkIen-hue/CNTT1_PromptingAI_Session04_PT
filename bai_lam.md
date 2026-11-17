# So sánh Trade-off các giải pháp ra lệnh cho Kỹ thuật Prompting Nâng cao (Logic & Chuỗi)
- Giải pháp 1: Zero-shot Cot.

Bạn là một chuyên viên chăm sóc khách hàng có nhiệm vụ phản hồi các tin nhắn của khách. Bạn cần đọc phản hồi của khách hàng và phân loại mức độ nghiêm trọng (Thấp, Trung bình, Cao), lỗi gặp phải. Sau đó, dùng kết quả đó để viết một báo cáo gồm: Tên lỗi, Mức độ, và Hướng xử lý. Nếu không có thông tin thig ghi vào đó là "null".

- Giải pháp 2: CoT và Prompt Chaining.

Bạn là Hệ thống phân loại sự cố kỹ thuật tự động. Có nhiệm vụ xử lý phản hồi khách hàng theo chuỗi 3 bước.
1. Phân tích và suy luận để phân loại mức độ khẩn cấp. VD: Mất tiền hay sập hệ thống -> Cao, Lỗi giao diện, chạy chậm -> Trung bình, Góp ý của khách hàng -> Thấp.
2. Từ phân tích mức độ khẩn cấp trên cho thêm các trường thông tin gồm Tên lỗi và hướng xử lý. VD: [Lỗi giao dịch chuyển khoản, Cao, Liên hệ khách hàng để thu thập bổ sung mã giao dịch, thời gian và thiết bị sử dụng; sau đó chuyển bộ phận Đối soát kiểm tra lệnh chuyển tiền.]
3. Đưa tất cả về định dạng JSON.

Chú ý không được bịa đặt thông tin, các vị trí bị bỏ trống cần ghi "null".

| Tiêu chí | Giải pháp 1 | Giải pháp 2 |
| :--- | :--- | :--- |
| Độ dễ hiểu | Cao. Vì dùng ngôn ngữ tự nhiên, ngắn gọn | Trung bình. Vì cần tư duy cấu trúc các bước cần làm, nhiều ràng buộc nên khó hiểu hơn |
| Tốn token | Ít. Câu từ ngắn gọn | Nhiều. Chủ yếu vì phần output khi đưa ra tốn nhiều token hơn khi đưa vào |
| Chặn lỗi biên | Trung bình. AI có thể tự bịa thông tin khi thiếu dữ liệu | Tốt. Thông qua các bước lọc và ràng buộc nghiêm ngặt nên ít bị lỗi |
| Tính ổn định của chuỗi | Thấp. Dễ bị gãy dữ liệu | Cao. Đảm bảo đầu ra bước trước khớp với đầu vào bước sau |

- Chọn giải pháp 2. Vì có thể khắc phục tình trạng đứt gãy dữ liệu ép về định dạng JSON để các dữ liệu khớp nhau qua từng bước, có các lớp ràng buộc ngăn AI tự bịa ra thông tin không chính xác.
## Chạy thử:
- Bạn là Hệ thống phân loại sự cố kỹ thuật tự động. Có nhiệm vụ xử lý phản hồi khách hàng theo chuỗi 3 bước.
1. Phân tích và suy luận để phân loại mức độ khẩn cấp. VD: Mất tiền hay sập hệ thống -> Cao, Lỗi giao diện, chạy chậm -> Trung bình, Góp ý của khách hàng -> Thấp.
2. Từ phân tích mức độ khẩn cấp trên cho thêm các trường thông tin gồm Tên lỗi và hướng xử lý. VD: [Lỗi giao dịch chuyển khoản, Cao, Liên hệ khách hàng để thu thập bổ sung mã giao dịch, thời gian và thiết bị sử dụng; sau đó chuyển bộ phận Đối soát kiểm tra lệnh chuyển tiền.]
3. Đưa tất cả về định dạng JSON.

Chú ý không được bịa đặt thông tin, các vị trí bị bỏ trống cần ghi "null".

```JSON
{
: [
    {
      "stt": 1
,
      "noi_dung_phan_hoi": "Tôi vừa thực hiện chuyển khoản 5 triệu đồng, tài khoản đã bị trừ tiền nhưng người nhận vẫn chưa nhận được."
,
      "ten_loi": "Lỗi giao dịch chuyển khoản"
,
      "muc_do_khan_cap": "Cao"
,
      "huong_xu_ly": "Liên hệ khách hàng để thu thập bổ sung mã giao dịch, thời gian và thiết bị sử dụng; sau đó chuyển bộ phận Đối soát kiểm tra lệnh chuyển tiền."
    },
    {
      "stt": 2
,
      "noi_dung_phan_hoi": "App bị sập hoàn toàn từ sáng đến giờ, tôi không thể đăng nhập để thanh toán hóa đơn được!"
,
      "ten_loi": "Lỗi sập hệ thống (System Crash)"
,
      "muc_do_khan_cap": "Cao"
,
      "huong_xu_ly": "Ghi nhận sự cố diện rộng, báo cáo ngay cho đội ngũ Kỹ thuật (DevOps/Backend) kiểm tra server và thông báo thời gian khắc phục dự kiến cho khách hàng."
    },
    {
      "stt": 3
,
      "noi_dung_phan_hoi": "Tôi nạp tiền vào ví điện tử qua thẻ ngân hàng, báo giao dịch thất bại nhưng tiền trong thẻ vẫn bị trừ."
,
      "ten_loi": "Lỗi nạp tiền không thành công"
,
      "muc_do_khan_cap": "Cao"
,
      "huong_xu_ly": "Xin lỗi khách hàng, yêu cầu cung cấp ảnh chụp biên lai giao dịch và 4 số cuối thẻ; chuyển bộ phận Kế toán/Đối soát để hoàn tiền hoặc cộng bù vào ví."
    },
    {
      "stt": 4
,
      "noi_dung_phan_hoi": "Tài khoản của tôi tự nhiên bị trừ 2 lần phí dịch vụ trong cùng một ngày."
,
      "ten_loi": "Lỗi trừ tiền trùng lặp (Double Charge)"
,
      "muc_do_khan_cap": "Cao"
,
      "huong_xu_ly": "Kiểm tra lịch sử giao dịch trên hệ thống quản trị, xác nhận giao dịch trùng và tạo phiếu yêu cầu hoàn tiền tự động cho khách hàng trong 24h."
    },
    {
      "stt": 5
,
      "noi_dung_phan_hoi": "Mỗi lần mở mục Lịch sử giao dịch là ứng dụng quay vòng vòng rất lâu mới tải xong."
,
      "ten_loi": "Lỗi hiệu năng / Tải dữ liệu chậm"
,
      "muc_do_khan_cap": "Trung bình"
,
      "huong_xu_ly": "Hỏi thêm thông tin về dòng máy, phiên bản hệ điều hành và mạng internet khách đang dùng; chuyển đội Kỹ thuật tối ưu hóa truy vấn dữ liệu (API)."
    },
    {
      "stt": 6
,
      "noi_dung_phan_hoi": "Nút 'Xác nhận' ở màn hình thanh toán bị che khuất một nửa trên điện thoại màn hình nhỏ."
,
      "ten_loi": "Lỗi hiển thị giao diện (UI)"
,
      "muc_do_khan_cap": "Trung bình"
,
      "huong_xu_ly": "Ghi nhận kích thước màn hình/dòng máy của khách hàng, chụp ảnh màn hình lỗi và chuyển cho đội ngũ Thiết kế (UI/UX) & Frontend điều chỉnh lại bố cục."
    },
    {
      "stt": 7
,
      "noi_dung_phan_hoi": "Tôi không thể đổi ảnh đại diện mới, cứ bấm lưu là hệ thống báo lỗi không xác định."
,
      "ten_loi": "Lỗi tính năng cập nhật hồ sơ"
,
      "muc_do_khan_cap": "Trung bình"
,
      "huong_xu_ly": "Hướng dẫn khách hàng kiểm tra định dạng/dung lượng ảnh và thử lại; đồng thời chuyển log lỗi cho đội Kỹ thuật kiểm tra dịch vụ lưu trữ ảnh."
    },
    {
      "stt": 8
,
      "noi_dung_phan_hoi": "Thông báo biến động số dư gửi về máy hơi chậm, khoảng 5-10 phút sau khi giao dịch mới nhận được."
,
      "ten_loi": "Lỗi độ trễ thông báo (Push Notification)"
,
      "muc_do_khan_cap": "Trung bình"
,
      "huong_xu_ly": "Kiểm tra trạng thái dịch vụ gửi thông báo (Firebase/APNs), hướng dẫn khách kiểm tra cài đặt thông báo trên điện thoại và báo bộ phận Kỹ thuật theo dõi."
    },
    {
      "stt": 9
,
      "noi_dung_phan_hoi": "Ứng dụng nên có thêm chế độ nền tối (Dark Mode) để dùng ban đêm đỡ mỏi mắt hơn."
,
      "ten_loi": "Góp ý tính năng giao diện (Dark Mode)"
,
      "muc_do_khan_cap": "Thấp"
,
      "huong_xu_ly": "Cảm ơn đóng góp của khách hàng, ghi nhận ý tưởng vào danh sách đề xuất tính năng mới (Product Backlog) gửi đội ngũ Phát triển sản phẩm xem xét."
    },
    {
      "stt": 10
,
      "noi_dung_phan_hoi": "Phần tìm kiếm danh bạ chuyển tiền hơi khó dùng, giá như có thể tìm theo tên gợi nhớ thì tốt quá."
,
      "ten_loi": "Góp ý cải thiện trải nghiệm tìm kiếm"
,
      "muc_do_khan_cap": "Thấp"
,
      "huong_xu_ly": "Ghi nhận phản hồi chi tiết của khách hàng, tổng hợp vào báo cáo trải nghiệm người dùng hàng tháng để đội Product nghiên cứu cải tiến ở các bản cập nhật sau."
    },
    {
      "stt": 11
,
      "noi_dung_phan_hoi": "Màu sắc của biểu tượng ứng dụng mới nhìn hơi chói, tôi thích tông màu dịu như bản cũ hơn."
,
      "ten_loi": "Góp ý thẩm mỹ giao diện"
,
      "muc_do_khan_cap": "Thấp"
,
      "huong_xu_ly": "Tiếp nhận ý kiến đánh giá thẩm mỹ của khách hàng, lưu trữ dữ liệu khảo sát sự hài lòng để tham khảo cho các đợt làm mới thương hiệu tiếp theo."
    },
    {
      "stt": 12
,
      "noi_dung_phan_hoi": "Các bạn có thể tích hợp thêm tính năng chia hóa đơn nhóm khi đi ăn uống cùng bạn bè không?"
,
      "ten_loi": "Đề xuất tính năng mới (Chia hóa đơn)"
,
      "muc_do_khan_cap": "Thấp"
,
      "huong_xu_ly": "Cảm ơn khách hàng đã tin tưởng và đề xuất tính năng hữu ích; chuyển thông tin cho bộ phận Quản lý sản phẩm (PM) đánh giá nhu cầu thị trường."
    }
  ]
}
```