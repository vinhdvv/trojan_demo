# trojan_demo
demo Trojan Horse


reveart code


Phân tích Đoạn 1: Khởi tạo và Thư viện Hệ thống
1. Khai báo các mảng dữ liệu toàn cục (_lmba6VHFnLX, _kSEyk2WuFVe, _h0dJTEyPZIL,...)
Bản chất: Đây là các mảng chứa danh sách các số nguyên (âm/dương), số thực (float) và chuỗi ký tự được xáo trộn với tên biến ngẫu nhiên.

Mục đích: Trong các mã nguồn bị làm rối (obfuscated) hoặc mã độc, các mảng này đóng vai trò là bảng khóa (key tables), hằng số toán học tùy biến, hoặc dữ liệu cấu hình để phục vụ cho các hàm giải mã động diễn ra ở phần sau của chương trình. Chúng giúp che giấu các giá trị nhạy cảm khỏi việc bị đọc trực tiếp bằng mắt thường.

2. Các thư viện được nạp (import)
sys, inspect: Được sử dụng để can thiệp vào tiến trình Python, kiểm tra stack frame (ngăn xếp lời gọi hàm), và phát hiện xem script có đang bị gỡ lỗi (debug) hay không.

ctypes, ctypes.wintypes: Thư viện cực kỳ quan trọng cho phép Python tương tác trực tiếp với các hàm API gốc của hệ điều hành Windows (ví dụ: các hàm trong kernel32.dll). Việc xuất hiện ctypes cho thấy script này được thiết kế riêng cho nền tảng Windows và có khả năng thao tác với bộ nhớ cấp thấp.

uuid, struct, time: Hỗ trợ xử lý dữ liệu nhị phân, định danh và tạo độ trễ (delay) nhằm đánh lừa các hệ thống phân tích tự động (sandbox).

Phân tích Đoạn 2: Hàm tính toán băm (_qicq9ife0DI) và các hằng số trạng thái
1. Hàm _qicq9ife0DI(_vEkUV2B7, _yvnty4oA, _niYVtTm9)
Bản chất: Đây là một hàm sinh giá trị giả ngẫu nhiên dựa trên mật mã (Pseudo-Random Generator / Key Derivation) sử dụng thuật toán HMAC-SHA256.

Cách thức hoạt động:

Nó sử dụng một khóa cố định (1110125465 chuyển thành chuỗi byte dạng big-endian).

Đầu vào truyền vào (_vEkUV2B7) được dùng làm thông điệp (message) cho HMAC.

Nó lấy 4 byte đầu tiên của kết quả băm SHA256 (_rNcAXEI8[:4]), chuyển thành số nguyên và thực hiện phép toán chia lấy dư (modulo) trong khoảng từ _yvnty4oA đến _niYVtTm9.

Mục đích: Hàm này đóng vai trò là "trái tim" trong việc tạo ra các chỉ số (index) hoặc khóa giải mã động. Thay vì lưu sẵn các hằng số tĩnh dễ bị quét ra, chương trình dùng hàm này kết hợp với các tham số đầu vào để "tính toán ra" các khóa hoặc vị trí byte cần giải mã ngay tại thời điểm chạy (runtime).

2. Các biến hằng số toàn cục (_wUP2CyYehM3, _meiECXhSUp2, _wfzN1ovQirn, _zSyCFjtvimR,...)
Bản chất: Đây là các biến lưu trữ giá trị số nguyên lớn (magic numbers / state variables).

Mục đích: Chúng đóng vai trò là các biến trạng thái kiểm tra tính toàn vẹn (integrity state) hoặc khóa dịch chuyển. Giá trị của các biến này sẽ liên tục được cập nhật, biến đổi thông qua các hàm kiểm tra mã nguồn bytecode ở đoạn tiếp theo để đảm bảo chương trình không bị ai đó can thiệp, sửa đổi code hay đem đi gỡ lỗi.

Phân tích Đoạn 3: Hàm kiểm tra tính toàn vẹn mã nguồn (_phoadNAkqRy)
Đoạn mã này thực hiện một kỹ thuật nâng cao gọi là Kiểm tra tính toàn vẹn bytecode (Bytecode Integrity Check / Self-Checksumming) kết hợp với cơ chế chống chỉnh sửa code (Anti-Tampering).

1. Duyệt qua cấu trúc mã nguồn (Bytecode Inspection)
Truy xuất Stack Frame: Sử dụng sys._getframe() để tìm về phạm vi thực thi gốc (<module>).

Đọc thuộc tính Code Object: Nó sử dụng vòng lặp để duyệt qua toàn bộ các đối tượng mã lệnh (co_code), hằng số (co_consts), tên biến, và cấu trúc của các hàm bên trong script.

Chuyển đổi thành chuỗi đặc trưng: Hàm nội bộ bên trong chuyển đổi cấu trúc bytecode thành một chuỗi văn bản (_t0lSpbelkiO) đại diện cho "vân tay" (fingerprint) của toàn bộ tập lệnh.

2. Tính toán mã băm kiểm tra (Checksum Loop)
Chương trình khởi tạo một giá trị băm ban đầu và chạy một vòng lặp qua từng ký tự của chuỗi đặc trưng bytecode vừa tạo, kết hợp với các phép toán XOR và nhân (16777619 — đây là hệ số nhân quen thuộc trong thuật toán băm FNV-1a).

Kết quả tạo ra một số nguyên duy nhất (_ibVjsApaKw2) phản ánh chính xác nội dung hiện tại của file script.

3. Mục đích bảo vệ (Anti-Tamper & Key Derivation)
Chống chỉnh sửa: Nếu bất kỳ ai cố gắng sửa đổi dù chỉ một ký tự nhỏ trong mã nguồn (ví dụ: thêm câu lệnh print để dò tìm, hoặc xóa bỏ các đoạn code kiểm tra), mã băm _ibVjsApaKw2 thay đổi hoàn toàn.

Sinh khóa động: Dựa vào giá trị băm tính được, nó cập nhật hàng loạt các biến toàn cục (_meiECXhSUp2, _wUP2CyYehM3, _ha3JVjP9S6R, _wfzN1ovQirn,...). Các biến này chính là khóa giải mã dùng ở các đoạn sau. Nếu file bị chỉnh sửa, khóa này sẽ sai lệch, khiến chương trình không thể giải mã được payload và tự sập (Exception).

Đánh dấu trạng thái hoàn thành kiểm tra bằng cách gán _hMKbcQUkaKf = 1.

Phân tích Đoạn 4: Hàm xác thực bytecode thứ hai (_ixl3xlNf5jr) và Kiểm tra gỡ lỗi (Anti-Debugging)
1. Hàm _ixl3xlNf5jr()
Bản chất: Đây là hàm bổ trợ tiếp theo cho cơ chế kiểm tra tính toàn vẹn code.

Cách thức hoạt động: Nó thực hiện các bước băm bytecode tương tự như hàm _phoadNAkqRy() ở trên, nhưng thay vì chỉ sinh khóa mới, nó thực hiện bước so sánh (int(_ibVjsApaKw2) != int(_iiLzYWLpOOZ)).

Mục đích: Kiểm tra xem trạng thái mã nguồn hiện tại có khớp với mốc ban đầu hay không. Nếu có sự thay đổi hoặc bị can thiệp, nó sẽ kích hoạt lại các công thức toán học để làm lệch các biến khóa giải mã (_meiECXhSUp2, _wUP2CyYehM3,...), khiến cho payload phía sau bị giải mã sai và hỏng hoàn toàn.

2. Kích hoạt chuỗi kiểm tra
Lần lượt gọi _phoadNAkqRy() và _ixl3xlNf5jr() để thiết lập và khóa chặt các tầng bảo mật trạng thái trước khi chương trình thực hiện các bước tiếp theo.

3. Bộ đôi kiểm tra chống gỡ lỗi (Anti-Debugging / Anti-Analysis)
Ngay sau phần xác thực code, chương trình tiến hành kiểm tra môi trường thực thi:

Kiểm tra Python Tracer (sys.gettrace()):
Nếu Python đang chạy dưới một trình gỡ lỗi dòng lệnh hoặc công cụ theo dõi (như pdb hoặc các profiler), hàm này sẽ trả về giá trị khác None. Chương trình lập tức gọi sys.exit() để thoát.

Kiểm tra Windows API (IsDebuggerPresent):

Script nạp thư viện kernel32.dll thông qua ctypes.WinDLL.

Gọi trực tiếp hàm Windows API IsDebuggerPresent(). Hàm này kiểm tra cờ PEB (Process Environment Block) trong hệ điều hành Windows để xem tiến trình có đang bị giám sát bởi một debugger gốc (như x64dbg, IDA Pro, Ghidra debugger) hay không.

Nếu phát hiện, chương trình thoát ngay lập tức với mã lỗi được xáo trộn bằng phép toán (34 ^ _wfzN1ovQirn & 127).
