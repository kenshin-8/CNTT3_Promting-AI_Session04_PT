# PHÂN TÍCH QUY TRÌNH QUẢN LÝ ĐƠN HÀNG TRỰC TUYẾN (PROMPT CHAINING)

## GIAI ĐOẠN 1: PHÂN TÍCH ĐẦU VÀO

* **Mục tiêu:** Xây dựng bức tranh toàn cảnh, chuẩn hóa và tối ưu hóa quy trình vận hành đơn hàng trực tuyến (Order Management System - OMS) từ khâu tiếp nhận đến khi giao hàng thành công cho khách hàng.
* **Dữ liệu đầu vào:** 
  * Phạm vi: Quy trình quản lý đơn hàng trực tuyến tiêu chuẩn (E-commerce Order Fulfillment).
  * Điểm bắt đầu: Khách hàng đặt hàng thành công trên hệ thống.
  * Điểm kết thúc: Đơn hàng được giao thành công tới tay khách hàng.
* **Điều kiện cần thiết:**
  * Sự tham gia và phối hợp của các bộ phận: Hệ thống Website/App (Frontend/Backend), Bộ phận Quản lý Kho (WMS), Bộ phận Kế toán/Thanh toán, Đơn vị Vận chuyển (3PL).
  * Dữ liệu tồn kho chính xác theo thời gian thực (Real-time inventory).
* **Phát hiện dữ liệu thiếu hoặc mâu thuẫn:** 
  * *Dữ liệu thiếu:* Do yêu cầu đầu vào tổng quát, chưa chỉ định rõ mô hình kinh doanh cụ thể (B2C, B2B, thương mại điện tử xuyên biên giới hay nội địa) và hình thức thanh toán (COD hay trả trước), hệ thống sẽ phân tích dựa trên mô hình B2C nội địa phổ biến nhất.
  * *Mâu thuẫn:* Không có.

---

## GIAI ĐOẠN 2: XỬ LÝ LOGIC

Quy trình được chia thành 5 bước tuần tự, đảm bảo đầu ra của bước này là đầu vào hợp lệ cho bước tiếp theo:

1. **Bước 1: Tiếp nhận và xác thực đơn hàng**
   * *Mô tả:* Hệ thống ghi nhận thông tin đặt hàng, kiểm tra tính hợp lệ của dữ liệu (thông tin người nhận, sản phẩm, số lượng) và trạng thái thanh toán.
   * *Căn cứ quyết định:* Đảm bảo đơn hàng không bị trùng lặp, thông tin giao hàng đầy đủ trước khi chuyển sang khâu xử lý tồn kho để tránh lãng phí nguồn lực.

2. **Bước 2: Kiểm tra tồn kho và phân bổ hàng hóa**
   * *Mô tả:* Hệ thống hoặc nhân viên kho kiểm tra xem sản phẩm có sẵn trong kho hay không, tiến hành giữ chỗ (reserve stock) và tạo phiếu xuất kho.
   * *Căn cứ quyết định:* Tránh tình trạng bán quá số lượng tồn kho (overselling) và xác định kho hàng gần nhất để tối ưu chi phí vận chuyển.

3. **Bước 3: Xử lý kho và đóng gói (Fulfillment & Packing)**
   * *Mô tả:* Nhân viên kho nhận phiếu, nhặt hàng (picking), đóng gói (packing) theo tiêu chuẩn sản phẩm và dán nhãn vận chuyển (shipping label) có mã vạch/QR code.
   * *Căn cứ quyết định:* Đảm bảo đúng sản phẩm, đúng số lượng, hạn chế tối đa sai sót và hư hỏng trong quá trình vận chuyển.

4. **Bước 4: Bàn giao vận chuyển (Handover to 3PL)**
   * *Mô tả:* Cân trọng lượng, quét mã vận đơn, lập biên bản bàn giao và chuyển giao bưu kiện cho đơn vị vận chuyển bên thứ ba (3PL).
   * *Căn cứ quyết định:* Chuyển giao trách nhiệm bảo quản hàng hóa sang đơn vị vận chuyển và cập nhật trạng thái "Đang giao hàng" lên hệ thống để khách hàng theo dõi.

5. **Bước 5: Giao hàng và hoàn tất đơn hàng (Delivery & Completion)**
   * *Mô tả:* Đơn vị vận chuyển phát hàng cho khách hàng, thu tiền (nếu là COD), khách hàng ký nhận (hoặc xác thực OTP), và hệ thống cập nhật trạng thái "Giao hàng thành công".
   * *Căn cứ quyết định:* Hoàn tất vòng đời xử lý đơn hàng, kích hoạt chính sách đổi trả/bảo hành (nếu có).

---

## GIAI ĐOẠN 3: KIỂM TRA VÀ XỬ LÝ ĐỨT GÃY

* **Kiểm tra tính đầy đủ và nhất quán:** 
  * Các bước từ tiếp nhận đến giao hàng liên kết chặt chẽ với nhau qua hệ thống dữ liệu tập trung.
* **Xử lý dữ liệu thiếu / Mâu thuẫn:** 
  * *Tình huống phát sinh 1 (Thiếu hàng ở Bước 2):* Nếu hệ thống báo hết hàng, quy trình phải dừng lại ở Bước 2, hệ thống tự động thông báo cho bộ phận mua hàng/chăm sóc khách hàng để liên hệ khách đổi sản phẩm hoặc hủy đơn. Không chuyển sang Bước 3.
  * *Tình huống phát sinh 2 (Thanh toán lỗi ở Bước 1):* Nếu đơn hàng chọn thanh toán trực tuyến nhưng giao dịch bị lỗi/chưa thanh toán, đơn hàng ở trạng thái "Chờ thanh toán", không chuyển sang kho.

---

## GIAI ĐOẠN 4: TỔNG HỢP KẾT QUẢ

### Bảng tổng hợp quy trình quản lý đơn hàng

| STT | Bước thực hiện | Đầu vào | Đầu ra | Trạng thái |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Tiếp nhận & Xác thực đơn | Thông tin đặt hàng từ khách (SKU, số lượng, địa chỉ, hình thức thanh toán) | Đơn hàng hợp lệ, đã xác thực thông tin | Hợp lệ / Đã kiểm tra |
| 2 | Kiểm tra tồn kho & Phân bổ | Đơn hàng đã xác thực + Dữ liệu tồn kho thời gian thực | Phiếu xuất kho, kho được chỉ định | Hợp lệ / Đã kiểm tra |
| 3 | Xử lý kho & Đóng gói | Phiếu xuất kho + Hàng hóa thực tế | Bưu kiện đã đóng gói hoàn chỉnh, dán mã vận đơn | Hợp lệ / Đã kiểm tra |
| 4 | Bàn giao vận chuyển | Bưu kiện đã đóng gói + Biên bản bàn giao | Đơn vị vận chuyển (3PL) tiếp nhận, mã vận đơn kích hoạt | Hợp lệ / Đã kiểm tra |
| 5 | Giao hàng & Hoàn tất | Bưu kiện đang trên đường vận chuyển + Thông tin liên hệ khách | Chữ ký/xác nhận nhận hàng, dòng tiền COD (nếu có) | Hợp lệ / Đã kiểm tra |

### Kết luận và Rủi ro tồn tại
* **Kết luận:** Quy trình quản lý đơn hàng trực tuyến chuẩn hóa gồm 5 bước cốt lõi giúp doanh nghiệp vận hành trơn tru, minh bạch từ lúc khách đặt đến khi nhận hàng, đồng thời đảm bảo khả năng kiểm soát rủi ro ở từng điểm chạm.
* **Rủi ro còn tồn tại:**
  1. *Sai lệch tồn kho thực tế và trên hệ thống:* Dẫn đến việc xác nhận đơn ở Bước 1 nhưng hết hàng ở Bước 2, gây trễ hẹn với khách hàng.
  2. *Sự cố từ đơn vị vận chuyển (3PL):* Giao hàng chậm, làm móp méo/hư hỏng hàng hóa hoặc thất lạc bưu phẩm trong Bước 5.
  3. *Tỷ lệ hoàn đơn (Return Rate):* Khách hàng không nhận hàng (đối với đơn COD) ở bước cuối cùng, phát sinh chi phí vận chuyển 2 chiều.