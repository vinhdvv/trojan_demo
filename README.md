# trojan_demo
demo Trojan Horse

Phân tích Đoạn 1: Khởi tạo và Thư viện Hệ thống
1. Khai báo các mảng dữ liệu toàn cục (_lmba6VHFnLX, _kSEyk2WuFVe, _h0dJTEyPZIL,...)
Bản chất: Đây là các mảng chứa danh sách các số nguyên (âm/dương), số thực (float) và chuỗi ký tự được xáo trộn với tên biến ngẫu nhiên.

Mục đích: Trong các mã nguồn bị làm rối (obfuscated) hoặc mã độc, các mảng này đóng vai trò là bảng khóa (key tables), hằng số toán học tùy biến, hoặc dữ liệu cấu hình để phục vụ cho các hàm giải mã động diễn ra ở phần sau của chương trình. Chúng giúp che giấu các giá trị nhạy cảm khỏi việc bị đọc trực tiếp bằng mắt thường.

2. Các thư viện được nạp (import)
sys, inspect: Được sử dụng để can thiệp vào tiến trình Python, kiểm tra stack frame (ngăn xếp lời gọi hàm), và phát hiện xem script có đang bị gỡ lỗi (debug) hay không.

ctypes, ctypes.wintypes: Thư viện cực kỳ quan trọng cho phép Python tương tác trực tiếp với các hàm API gốc của hệ điều hành Windows (ví dụ: các hàm trong kernel32.dll). Việc xuất hiện ctypes cho thấy script này được thiết kế riêng cho nền tảng Windows và có khả năng thao tác với bộ nhớ cấp thấp.

uuid, struct, time: Hỗ trợ xử lý dữ liệu nhị phân, định danh và tạo độ trễ (delay) nhằm đánh lừa các hệ thống phân tích tự động (sandbox).

