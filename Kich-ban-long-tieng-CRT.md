# Kịch bản lồng tiếng — Bài giảng đầy đủ CRT

**Thời lượng:** khoảng 23 phút 41 giây  
**Ngôn ngữ:** Tiếng Việt  
**Giọng đọc:** giọng tổng hợp nam, nhịp chậm và rõ, tham chiếu phong cách thuyết minh của video demo.

## 00:00 — Từ điểm số đến một giá trị C
*Chương: Mở đầu · Thời lượng cảnh: 46.0 giây*

Chào mừng bạn đến với bài giảng đầy đủ về hệ thống quản lý điểm sinh viên có tích hợp định lý số dư Trung Hoa, hay CRT. Video này không chỉ giới thiệu công thức toán học. Chúng ta sẽ đi xuyên suốt toàn bộ sản phẩm: từ thao tác của giảng viên trên giao diện, cách frontend đọc file Excel, cách backend kiểm tra quyền và dữ liệu, cách Java chọn vùng số nguyên tố, tạo giá trị C, lưu C vào SQL Server, cho đến quá trình sinh viên nhập PIN để khôi phục điểm. Phần cuối sẽ giải thích chức năng đổi PIN, cơ chế mã hóa lại dữ liệu, các kịch bản kiểm thử và những giới hạn bảo mật cần nói rõ khi bảo vệ đồ án. Sau video, bạn cần trả lời được ba câu hỏi: dữ liệu nào thực sự được lưu, vì sao cùng một giá trị C có thể chứa ba điểm, và vì sao đổi PIN bắt buộc phải mã hóa lại các bản ghi cũ.

## 00:46 — Vì sao không nên lưu điểm dạng rõ?
*Chương: Bài toán · Thời lượng cảnh: 43.3 giây*

Trong một hệ thống quản lý đào tạo thông thường, điểm giữa kỳ, điểm cuối kỳ và điểm trung bình thường được lưu thành ba cột số. Cách này thuận tiện cho truy vấn, nhưng có một rủi ro rõ ràng: người có quyền đọc database, bản sao lưu hoặc file xuất dữ liệu có thể nhìn thấy ngay điểm của toàn bộ sinh viên mà không cần đi qua giao diện và quy trình phân quyền của ứng dụng. Đăng nhập và phân quyền vẫn cần thiết, nhưng chúng chủ yếu kiểm soát đường đi hợp lệ ở tầng ứng dụng. Đề tài đặt thêm một yêu cầu ở tầng lưu trữ: trong bảng điểm mã hóa, không lưu trực tiếp ba giá trị GK, CK và TB. Thay vào đó, hệ thống chuẩn hóa chúng thành số nguyên, kết hợp bằng CRT để tạo một giá trị đại diện gọi là C, rồi chỉ khôi phục điểm khi người dùng hợp lệ thực hiện đúng luồng giải mã.

## 01:29 — CRT trong đồ án là gì — và không phải là gì?
*Chương: Phạm vi · Thời lượng cảnh: 45.0 giây*

Cần xác định đúng phạm vi để tránh khẳng định quá mức. CRT là một định lý số học cho phép tìm một nghiệm duy nhất theo modulo tích của các modulo đôi một nguyên tố cùng nhau. Trong đồ án, tính chất đó được dùng để biểu diễn ba điểm đã chuẩn hóa bằng một số C. Tuy nhiên, CRT tự nó không cung cấp đầy đủ tính bí mật, xác thực và toàn vẹn như một hệ mật mã hiện đại. Nếu một người có cả C và đúng ba số nguyên tố, họ chỉ cần lấy phần dư để khôi phục điểm. Vì vậy, cách trình bày chính xác là: hệ thống minh họa cơ chế không lưu trực tiếp điểm thật trong bảng DIEM_CRT và dẫn xuất bộ modulo từ ngữ cảnh cùng thông tin PIN, public key. Khi triển khai thực tế, vẫn cần TLS, quản lý bí mật, mã hóa chuẩn, kiểm soát truy cập, giám sát và bảo vệ API.

## 02:14 — Bốn thành phần phối hợp
*Chương: Kiến trúc · Thời lượng cảnh: 58.3 giây*

Kiến trúc của hệ thống gồm bốn thành phần. Frontend ReactJS hiển thị giao diện cho quản trị viên, giảng viên và sinh viên. Frontend cũng đọc file Excel bằng thư viện xlsx, chuyển dữ liệu thành danh sách JSON rồi gửi sang backend. Backend Node.js và Express là cổng tin cậy trung tâm: quản lý session, xác định vai trò, kiểm tra giảng viên có phụ trách học phần hay không, truy vấn SQL Server và điều phối các lời gọi đến dịch vụ Java. Java Spring Boot chỉ tập trung vào thuật toán: tạo public key mô phỏng, tính shared secret, xác định prime range, mã hóa CRT và giải mã CRT bằng BigInteger. Java không truy vấn trực tiếp SQL Server. Backend nhận startIndex và endIndex từ Java, lấy đúng ba số nguyên tố trong bảng SNT rồi gửi danh sách số dạng chuỗi trở lại Java. SQL Server lưu dữ liệu quản lý đào tạo, tài khoản, PIN hash, public key, bảng năm triệu số nguyên tố, log và bảng DIEM_CRT.

## 03:13 — Ba vai trò và ranh giới tin cậy
*Chương: Phân quyền · Thời lượng cảnh: 46.9 giây*

Hệ thống tách ba vai trò. Admin có mã vai trò một và truy cập khu vực quản trị dữ liệu nền. Giảng viên có mã vai trò hai và được phép nhập điểm, nhưng chỉ cho học phần gắn với mã giảng viên của chính họ. Sinh viên có mã vai trò không và xem thông tin, thời khóa biểu, lịch sử đăng nhập, lịch sử đổi PIN và kết quả học tập của chính mình. Điểm quan trọng là kiểm tra quyền phải nằm ở backend. Frontend có thể ẩn nút, nhưng người dùng vẫn có thể tự gửi request. Vì vậy endpoint nhập điểm phải xác thực session, xác nhận vai trò giảng viên, đối chiếu mã giảng viên với học phần và từ chối admin hoặc người không phụ trách. Tương tự, endpoint xem điểm nên lấy mã sinh viên từ session, không tin một MSSV tùy ý do frontend gửi lên. Đây là ranh giới giữa giao diện thuận tiện và kiểm soát bảo mật thực sự.

## 04:00 — Bảng nào tham gia vào luồng CRT?
*Chương: Dữ liệu · Thời lượng cảnh: 50.8 giây*

Để mã hóa và giải mã đúng, backend phải kết hợp nhiều bảng. SINH_VIEN cung cấp mã sinh viên, mã lớp và public key sinh viên. Từ lớp, hệ thống xác định khoa. GIANG_VIEN cung cấp mã giảng viên, PIN hash và public key giảng viên. HOC_PHAN xác định năm học, học kỳ, lớp, khoa và giảng viên phụ trách. THOI_KHOA_BIEU liên kết sinh viên với các học phần mà họ được phép xem. SNT lưu năm triệu số nguyên tố, mỗi số có một chỉ số. DIEM_CRT là bảng trọng tâm. Bảng này lưu MASV, MAHP, MAGV, giá trị C, năm học, học kỳ, mã khoa và mã lớp tại thời điểm mã hóa, public key sinh viên dùng trong ngữ cảnh CRT, phiên bản thuật toán, phiên bản khóa và thời gian tạo cập nhật. Hệ thống không lưu startIndex, endIndex hoặc ba số nguyên tố trong DIEM_CRT; các giá trị đó được tính lại khi cần.

## 04:50 — Hai chiều của cùng một dữ liệu
*Chương: Luồng tổng thể · Thời lượng cảnh: 51.1 giây*

Toàn bộ hệ thống có thể hiểu bằng hai chiều. Chiều thứ nhất là nhập điểm. Giảng viên chọn năm học, học kỳ và học phần, nhập PIN và chọn file Excel. Frontend đọc file, kiểm tra cấu trúc cơ bản rồi gửi danh sách điểm lên backend. Backend kiểm tra quyền, xác thực PIN, lấy ngữ cảnh sinh viên và học phần, chuẩn hóa điểm, tính prime range, lấy ba số nguyên tố, gọi Java mã hóa và lưu C. Chiều thứ hai là xem điểm. Sinh viên chọn học phần và nhập PIN. Backend xác định sinh viên từ session, kiểm tra PIN, lấy C và mã giảng viên từ DIEM_CRT, tính lại đúng prime range, lấy lại ba số nguyên tố rồi gọi Java giải mã. Java trả ba số nguyên đã chuẩn hóa; backend chia cho mười và frontend hiển thị. Hai chiều phải dùng cùng ngữ cảnh và tạo cùng shared secret, nếu không bộ prime sẽ khác và kết quả không chính xác.

## 05:41 — Giảng viên thao tác trên giao diện
*Chương: Nhập điểm · Thời lượng cảnh: 50.8 giây*

Ở góc nhìn người dùng, luồng nhập điểm được thiết kế gần với thói quen thực tế. Giảng viên không nhập từng dòng thủ công mà chọn đúng học phần rồi tải file Excel. File mẫu có các cột định danh sinh viên và ba giá trị điểm. Trước khi lưu, giao diện hiển thị danh sách để người dùng kiểm tra. Khi bấm lưu, frontend tạo payload gồm courseId, academicYear, semester, PIN và mảng grades. Mỗi phần tử có mã sinh viên, điểm giữa kỳ, điểm cuối kỳ và điểm trung bình. Sau đó frontend gửi POST đến endpoint admin nhập điểm với credentials include để cookie session đi kèm. Video demo cho thấy giao diện có thể tải danh sách sinh viên theo học phần, nhận dữ liệu Excel và trả thông báo sau khi xử lý. Tuy nhiên, điều quan trọng không phải chỉ là nút import hoạt động, mà là dữ liệu sau nút đó phải đi qua đầy đủ các kiểm tra và mã hóa trước khi chạm database.

## 06:32 — Frontend đọc Excel, backend không đọc file ổ đĩa
*Chương: Nhập điểm · Thời lượng cảnh: 45.6 giây*

Một chi tiết thường bị trình bày sai là ai thực sự đọc file Excel. Trình duyệt của người dùng có quyền truy cập file mà họ vừa chọn, nên frontend dùng thư viện xlsx để đọc workbook, lấy sheet, ánh xạ tên cột và tạo danh sách đối tượng JavaScript. Backend không mở đường dẫn file trên máy người dùng. Nó chỉ nhận JSON đã được frontend tạo ra. Frontend có thể phát hiện sớm các lỗi như thiếu cột, thiếu mã sinh viên, ô điểm không phải số hoặc điểm nằm ngoài khoảng không đến mười. Tuy vậy, kiểm tra frontend chỉ phục vụ trải nghiệm. Backend vẫn phải kiểm tra lại tất cả vì request có thể bị sửa. Nguyên tắc quan trọng là: không tin dữ liệu từ client, kể cả khi giao diện đã kiểm tra. Chính backend mới là nơi quyết định bản ghi nào hợp lệ để đi tiếp vào luồng CRT.

## 07:18 — Các lớp kiểm tra trước khi mã hóa
*Chương: Backend · Thời lượng cảnh: 47.0 giây*

Endpoint nhập điểm phải xử lý nhiều lớp kiểm tra. Trước hết, request phải có session hợp lệ và vai trò giảng viên. Tiếp theo backend tìm học phần theo mã học phần, năm học và học kỳ, rồi đối chiếu mã giảng viên phụ trách với tài khoản hiện tại. Sau đó hệ thống xác thực PIN giảng viên bằng PIN hash đã lưu. Với từng sinh viên, backend kiểm tra mã sinh viên tồn tại, thuộc ngữ cảnh học phần, có mã lớp, mã khoa và public key. Ba điểm phải là số trong khoảng từ không đến mười. Nếu thiếu bất kỳ dữ liệu nào, bản ghi đó không được mã hóa. Backend còn kiểm tra Java service có trả prime range hợp lệ hay không, truy vấn SNT có lấy đủ đúng ba số nguyên tố hay không, và kết quả encrypt có chứa giá trị C hợp lệ hay không. Những bước này ngăn tình trạng lưu một bản ghi nửa vời mà sau đó không thể giải mã.

## 08:05 — Bước 1 — Chuẩn hóa điểm
*Chương: Thuật toán · Thời lượng cảnh: 43.3 giây*

CRT và BigInteger làm việc trên số nguyên, trong khi điểm thường có một chữ số thập phân. Vì vậy backend chuẩn hóa mỗi điểm bằng cách nhân mười và làm tròn. Điểm tám phẩy năm trở thành tám mươi lăm. Điểm bảy trở thành bảy mươi. Điểm mười trở thành một trăm. Khi giải mã, backend chia kết quả cho mười để hiển thị lại. Cần lưu ý độ chính xác của thiết kế này chỉ giữ một chữ số thập phân. Ví dụ chín phẩy hai ba nhân mười bằng chín mươi hai phẩy ba; Math.round cho kết quả chín mươi hai, nên khi hiển thị sẽ là chín phẩy hai. Đây không phải lỗi CRT mà là quyết định lượng tử hóa của bước chuẩn hóa. Nếu muốn giữ hai chữ số thập phân, hệ thống phải nhân một trăm và chọn modulo lớn hơn giá trị tối đa một nghìn.

## 08:48 — Định lý số dư Trung Hoa
*Chương: CRT · Thời lượng cảnh: 44.9 giây*

Bây giờ đến nền tảng toán học. Giả sử p một, p hai và p ba là ba số nguyên tố khác nhau, nên chúng đôi một nguyên tố cùng nhau. Ta muốn tìm C sao cho phần dư của C khi chia p một là a một, phần dư khi chia p hai là a hai và phần dư khi chia p ba là a ba. Định lý số dư Trung Hoa bảo đảm hệ đồng dư này có một nghiệm duy nhất trong khoảng từ không đến M trừ một, với M bằng tích ba modulo. Trong hệ thống, a một, a hai và a ba chính là GK, CK và TB sau chuẩn hóa. Nhờ vậy, một số C có thể đại diện cho ba điểm. Khi biết đúng p một, p hai và p ba, ta không cần chạy lại toàn bộ công thức để giải mã; chỉ cần lấy C modulo từng p. Điều kiện quan trọng là mỗi p phải lớn hơn giá trị điểm tối đa sau chuẩn hóa, tức lớn hơn một trăm.

## 09:33 — Công thức tạo giá trị C
*Chương: CRT · Thời lượng cảnh: 41.1 giây*

Công thức xây dựng nghiệm được thực hiện như sau. Đầu tiên tính M bằng tích của ba số nguyên tố. Với mỗi vị trí i, tính M i bằng M chia p i. Sau đó tìm y i là nghịch đảo modulo của M i theo p i. Cuối cùng cộng ba hạng a i nhân M i nhân y i và lấy modulo M. Vì tích các số nguyên tố có thể vượt giới hạn số nguyên sáu mươi bốn bit, Java service dùng BigInteger cho toàn bộ phép nhân, chia, modulo và modular inverse. Trước khi tính, service kiểm tra số lượng điểm bằng số lượng prime, danh sách không rỗng, điểm không âm và mỗi điểm nhỏ hơn modulo tương ứng. Nếu một điểm lớn hơn hoặc bằng prime, phép lấy dư sẽ không còn khôi phục được đúng giá trị ban đầu, nên request phải bị từ chối.

## 10:14 — Ví dụ mã hóa từng bước
*Chương: Ví dụ · Thời lượng cảnh: 37.1 giây*

Hãy đi qua một ví dụ nhỏ để thấy rõ ý nghĩa. Một sinh viên có điểm giữa kỳ tám phẩy năm, cuối kỳ bảy và trung bình bảy phẩy tám. Sau chuẩn hóa, ta có tám mươi lăm, bảy mươi và bảy mươi tám. Để dễ tính, giả sử ba modulo là một trăm lẻ một, một trăm lẻ ba và một trăm lẻ bảy. Đây chỉ là ví dụ minh họa; hệ thống thật lấy các prime ở vị trí rất lớn trong bảng SNT. Bài toán trở thành tìm C sao cho C chia một trăm lẻ một dư tám mươi lăm, chia một trăm lẻ ba dư bảy mươi, và chia một trăm lẻ bảy dư bảy mươi tám. Áp dụng công thức CRT thu được một nghiệm là một trăm mười nghìn không trăm bảy mươi bốn.

## 10:51 — Kiểm tra giải mã
*Chương: Ví dụ · Thời lượng cảnh: 5.9 giây*

Bây giờ kiểm tra ngược. Một trăm mười nghìn không trăm bảy mươi bốn modulo một trăm lẻ một bằng tám mươi lăm. Modulo một trăm lẻ ba bằng bảy mươi. Modulo một trăm lẻ bảy bằng bảy mươi tám. Chia cả ba kết quả cho mười, ta trở lại tám phẩy năm, bảy và bảy phẩy tám. Database có thể lưu C thay cho ba cột điểm trong bảng mã hóa. Nhưng cần nhắc lại: C không phải một bí mật tuyệt đối. Nếu bộ prime bị lộ, khôi phục điểm là trực tiếp. Vì vậy thiết kế dùng ngữ cảnh và shared secret để xác định vị trí prime, đồng thời phải bảo vệ backend, public key, PIN, session và quyền đọc bảng SNT.

## 10:57 — Diffie–Hellman mô phỏng
*Chương: Dẫn xuất tham số · Thời lượng cảnh: 46.6 giây*

Hệ thống không dùng một bộ prime cố định cho tất cả sinh viên. Để hai phía có thể tái lập cùng vùng prime, Java service mô phỏng trao đổi khóa Diffie–Hellman. Mỗi người có PIN đóng vai trò số mũ bí mật và public key bằng g lũy thừa PIN modulo một số nguyên tố lớn P. Khi mã hóa, giảng viên dùng public key của sinh viên lũy thừa PIN giảng viên. Khi giải mã, sinh viên dùng public key của giảng viên lũy thừa PIN sinh viên. Do tính chất lũy thừa modulo, hai kết quả đều tương ứng với g lũy thừa tích hai PIN modulo P, nên tạo cùng shared secret. Đây là lý do hai luồng dùng cặp đầu vào khác nhau nhưng vẫn dẫn xuất được cùng prime range. Trong báo cáo và khi thuyết trình, cần gọi đây là cơ chế mô phỏng phục vụ đồ án, không tuyên bố đây là hệ thống quản lý khóa production hoàn chỉnh.

## 11:44 — Shared secret chưa đủ — phải gắn với ngữ cảnh
*Chương: Dẫn xuất tham số · Thời lượng cảnh: 42.6 giây*

Sau khi có shared secret, Java ghép nó với ngữ cảnh của bản ghi: mã khoa, mã lớp, mã sinh viên, mã học phần và chuỗi phiên bản CRT_V1. Chuỗi kết quả được băm bằng SHA-256 rồi diễn giải thành một số nguyên lớn B. Việc đưa ngữ cảnh vào hàm băm giúp cùng một cặp người dùng nhưng khác học phần có thể nhận vùng prime khác. Mã phiên bản giúp hệ thống có khả năng phân biệt thuật toán nếu sau này thay đổi cách dẫn xuất. Khi mã hóa lại sau đổi PIN, hệ thống phải dùng đúng ngữ cảnh đã lưu tại thời điểm mã hóa, không chỉ đọc dữ liệu quản lý hiện tại, vì sinh viên có thể chuyển lớp hoặc thông tin học phần có thể thay đổi. Đó là lý do DIEM_CRT lưu MAKHOA_CRT, MALOP_CRT và một số trường phiên bản.

## 12:26 — Từ B đến ba dòng trong bảng SNT
*Chương: Dẫn xuất tham số · Thời lượng cảnh: 45.4 giây*

Bảng SNT trong dữ liệu thử nghiệm chứa năm triệu số nguyên tố. Java không trả trực tiếp prime mà trả chỉ số bắt đầu và kết thúc. Đầu tiên tính safeRange bằng N trừ giá trị điểm tối đa sau chuẩn hóa, trừ số lượng điểm, cộng một. Với N bằng năm triệu, MAX_SCORE_X10 bằng một trăm và NUMBER_OF_GRADES bằng ba, khoảng an toàn giúp startIndex không chạy vượt cuối bảng. Sau đó K bằng B modulo safeRange. StartIndex bằng K cộng một trăm, và endIndex bằng startIndex cộng hai. Backend truy vấn ba dòng liên tiếp trong SNT. Việc cộng một trăm đẩy chỉ số qua vùng đầu; do dãy prime tăng dần, các prime lấy được lớn hơn miền điểm. Hệ thống phải xác nhận truy vấn trả đúng ba số và mỗi số đều là chuỗi số hợp lệ trước khi gửi sang Java.

## 13:12 — Bốn endpoint nội bộ
*Chương: Java Service · Thời lượng cảnh: 56.0 giây*

GradeCrtController công bố bốn endpoint nội bộ. Public-key nhận PIN và trả public key mô phỏng. Prime-range nhận mã khoa, mã lớp, MSSV, mã học phần, PIN và public key của phía đối diện, rồi trả startIndex và endIndex. Encrypt nhận danh sách điểm chuẩn hóa cùng danh sách prime và trả C dưới dạng chuỗi. Decrypt nhận C và danh sách prime rồi trả plaintext gồm ba số nguyên. GradeCrtService thực hiện Diffie–Hellman mô phỏng, SHA-256, CRT bằng BigInteger và kiểm tra input. Java không biết mật khẩu SQL Server và không truy vấn SNT. Node mới là thành phần lấy prime từ database. Cách tách này tạo ranh giới rõ: Java là dịch vụ tính toán, còn backend chịu trách nhiệm xác thực, nghiệp vụ và quyền truy cập dữ liệu. Trong triển khai thực tế, các endpoint nội bộ cũng cần mạng riêng, xác thực giữa service, timeout và health check.

## 14:08 — Backend điều phối mã hóa từng sinh viên
*Chương: Mã hóa · Thời lượng cảnh: 50.1 giây*

Với mỗi dòng hợp lệ trong danh sách Excel, backend lấy thông tin sinh viên, lớp, khoa, học phần và giảng viên. Ba điểm được chuẩn hóa thành mảng số nguyên. Backend gọi prime-range bằng PIN giảng viên và public key sinh viên. Sau khi nhận chỉ số, backend truy vấn SNT và gửi ba prime cùng mảng điểm sang endpoint encrypt. Java trả C dưới dạng chuỗi. Backend lưu hoặc cập nhật bản ghi theo cặp MASV và MAHP, đồng thời lưu MAGV và ngữ cảnh CRT. Điểm rõ không được ghi vào DIEM_CRT. Tuy nhiên trong quá trình xử lý, plaintext vẫn tồn tại tạm thời ở frontend, request và bộ nhớ backend. Vì vậy không được ghi toàn bộ request body, PIN hoặc mảng điểm vào log. Khi trình bày, câu đúng là không lưu trực tiếp điểm thật trong bảng DIEM_CRT, chứ không phải điểm thật không bao giờ xuất hiện trong hệ thống.

## 14:58 — Bằng chứng cần nhìn trong SQL Server
*Chương: Lưu trữ · Thời lượng cảnh: 43.6 giây*

Khi demo, đây là màn hình cần phóng to. Truy vấn DIEM_CRT theo đúng MSSV và mã học phần. Người xem phải thấy MASV, MAHP, MAGV, C, các trường ngữ cảnh và thời gian cập nhật. C được lưu dưới dạng NVARCHAR MAX vì kết quả BigInteger có thể vượt giới hạn BIGINT. Bảng trọng tâm không chứa ba cột GK, CK và TB. StartIndex, endIndex và danh sách prime cũng không được lưu; chúng được tính lại trong bộ nhớ khi mã hóa, giải mã hoặc đổi PIN. Nếu muốn chứng minh mạnh hơn, hãy ghi lại C trước khi đổi PIN, thực hiện đổi PIN, rồi truy vấn lại để cho thấy C mới khác C cũ nhưng điểm giải mã vẫn giữ nguyên. Đây là bằng chứng trực quan tốt hơn nhiều so với chỉ nói thuật toán đã chạy.

## 15:41 — Sinh viên xem điểm như thế nào?
*Chương: Giải mã · Thời lượng cảnh: 47.0 giây*

Khi sinh viên xem điểm, frontend gửi mã học phần và PIN. Backend phải lấy MSSV từ session hiện tại để tránh người dùng tự thay MSSV trong request. Sau khi xác thực PIN hash, backend tìm bản ghi DIEM_CRT theo MSSV và MAHP. Nếu có, backend lấy C và MAGV, rồi lấy public key hiện tại của giảng viên. Java prime-range được gọi bằng PIN sinh viên và public key giảng viên. Nhờ tính chất Diffie–Hellman mô phỏng, shared secret trùng với lúc giảng viên mã hóa bằng PIN giảng viên và public key sinh viên. Backend lấy đúng ba prime, gọi decrypt, kiểm tra plaintext có đủ ba số trong miền không đến một trăm, chia mười và trả GK, CK, TB về frontend. Người dùng chỉ thấy thao tác quen thuộc; toàn bộ CRT nằm phía sau.

## 16:28 — Không phải request nào cũng được giải mã
*Chương: Xử lý lỗi · Thời lượng cảnh: 43.0 giây*

Luồng giải mã cần phân biệt rõ các trường hợp. Nếu sinh viên nhập sai PIN, hệ thống dừng và trả thông báo sai PIN. Nếu PIN đúng nhưng DIEM_CRT chưa có bản ghi, thông báo nên là chưa có điểm cho môn học này, không phải lỗi kỹ thuật khó hiểu. Nếu thiếu public key giảng viên, không lấy đủ ba prime hoặc Java trả plaintext sai kích thước, backend phải từ chối và không hiển thị giá trị ngẫu nhiên. Thông báo gửi cho người dùng cần ngắn gọn, trong khi log máy chủ có thể ghi mã lỗi và request ID nhưng không ghi PIN, C kèm toàn bộ ngữ cảnh bí mật hoặc plaintext điểm. Ngoài ra nên giới hạn số lần thử PIN, khóa tạm thời sau nhiều lần sai và ghi nhận sự kiện bất thường. Đây là các lớp bảo vệ bổ sung mà CRT không tự cung cấp.

## 17:11 — Đổi PIN không chỉ là đổi PIN_HASH
*Chương: Đổi PIN · Thời lượng cảnh: 47.6 giây*

PIN tham gia vào shared secret và prime range, vì vậy đổi PIN làm thay đổi bộ prime. Nếu hệ thống chỉ cập nhật PIN hash, các bản ghi C cũ vẫn gắn với prime cũ và người dùng sẽ không khôi phục đúng điểm. Quy trình đúng gồm bốn giai đoạn. Một, xác thực PIN hiện tại và lấy toàn bộ bản ghi DIEM_CRT liên quan. Hai, với từng bản ghi, tái lập range cũ và giải mã C cũ thành ba số chuẩn hóa. Ba, tạo public key mới, tính range mới và mã hóa plaintext thành C mới. Bốn, giải mã thử C mới bằng prime mới và so sánh chính xác với plaintext trước khi ghi. Khi sinh viên đổi PIN, mọi bản ghi của sinh viên được xử lý. Khi giảng viên đổi PIN, mọi bản ghi do giảng viên đó phụ trách được xử lý. Video demo nên chứng minh C cũ khác C mới trong khi điểm không đổi.

## 17:59 — Transaction, khóa dòng và job nền
*Chương: Đổi PIN · Thời lượng cảnh: 48.5 giây*

Mã hóa lại nhiều bản ghi có thể mất thời gian, nên source tổ chức thành job nền và frontend định kỳ hỏi trạng thái. Tuy nhiên tính nhất quán vẫn phải được bảo đảm bằng transaction. Backend chuẩn bị kết quả mới trong bộ nhớ, giải mã thử để xác nhận, sau đó mở transaction, khóa từng dòng hoặc dùng điều kiện kiểm tra C cũ chưa bị phiên khác thay đổi. Chỉ khi toàn bộ bản ghi cập nhật thành công, hệ thống mới cập nhật PIN hash, public key, key version, thời gian đổi PIN và lịch sử. Nếu bất kỳ bản ghi nào thất bại, transaction rollback, giữ nguyên PIN và C cũ. Cách này tránh trạng thái một phần môn học dùng PIN cũ, phần còn lại dùng PIN mới. Hướng production cần đưa job và trạng thái ra hàng đợi hoặc kho bền vững, vì lưu job trong RAM có thể mất khi process restart hoặc khi chạy nhiều replica.

## 18:48 — Log có ích nhưng không được trở thành nguồn rò rỉ
*Chương: Giám sát · Thời lượng cảnh: 45.8 giây*

Hệ thống đã có lịch sử đăng nhập và lịch sử đổi PIN. Sau đăng nhập thành công, backend ghi tài khoản, thời gian, địa chỉ IP và vị trí tương đối nếu có. Lịch sử đổi PIN chỉ nên được ghi khi toàn bộ quá trình mã hóa lại hoàn tất. Để vận hành tốt hơn, có thể thêm audit cho nhập điểm, cập nhật điểm, yêu cầu giải mã, lỗi Java service và thay đổi phiên bản khóa. Nhưng log phải được lọc dữ liệu. Không được console log toàn bộ request body của endpoint nhập điểm vì body chứa PIN và điểm rõ. Không nên ghi shared secret, ba prime hoặc plaintext sau giải mã. Log phù hợp gồm request ID, tài khoản, vai trò, mã học phần, số lượng bản ghi, trạng thái, thời gian xử lý và mã lỗi. Đây là nguyên tắc rất quan trọng khi đề tài mang chủ đề bảo mật.

## 19:33 — Bộ kiểm thử tối thiểu phải bao phủ gì?
*Chương: Kiểm thử · Thời lượng cảnh: 49.3 giây*

Một video hoặc báo cáo thực nghiệm thuyết phục cần có cả đường thành công và đường lỗi. Với đăng nhập, kiểm tra tài khoản đúng, sai và vai trò không hợp lệ. Với nhập Excel, kiểm tra file đúng, thiếu cột, MSSV không tồn tại, điểm âm, điểm lớn hơn mười và PIN sai. Kiểm tra giảng viên không thể nhập học phần không phụ trách. Với giải mã, kiểm tra session hết hạn, sai PIN, học phần chưa có điểm, thiếu public key, không lấy đủ prime và Java service không phản hồi. Với CRT, kiểm tra encrypt rồi decrypt cho kết quả đúng và mọi plaintext nằm trong không đến một trăm. Với đổi PIN, ghi C trước và sau, xác nhận điểm giữ nguyên, PIN cũ không còn hợp lệ và transaction rollback khi một bản ghi bị lỗi. Các kết quả nên có expected result và actual result thay vì chỉ ghi chung là thành công.

## 20:23 — Những giới hạn phải nói thẳng
*Chương: Bảo mật · Thời lượng cảnh: 52.2 giây*

Giới hạn thứ nhất: CRT không phải mã hóa chuẩn. Có C và đúng prime thì lấy modulo là ra dữ liệu. Giới hạn thứ hai: PIN có miền nhỏ; băm SHA-256 trực tiếp không salt không phù hợp để chống dò ngoại tuyến trong production. Nên dùng Argon2, scrypt hoặc bcrypt với salt và tham số chi phí phù hợp, đồng thời rate limit ở online flow. Giới hạn thứ ba: CRT không tự phát hiện C bị sửa. Cần cơ chế toàn vẹn như HMAC hoặc mã hóa có xác thực, ví dụ AES-GCM, nếu xây sản phẩm thật. Giới hạn thứ tư: session secret, mật khẩu database và PIN seed không được dùng giá trị mặc định. Job đổi PIN lưu trong RAM không phù hợp khi restart hoặc scale nhiều instance. Cuối cùng, quyền đọc SNT, public key, source và API phải được kiểm soát; không thể coi việc giấu thuật toán là bảo mật.

## 21:15 — Đưa mô hình từ demo đến gần production hơn
*Chương: Hướng phát triển · Thời lượng cảnh: 50.5 giây*

Để phát triển tiếp, trước hết tách middleware xác thực và phân quyền, kiểm tra quyền theo từng tài nguyên chứ không chỉ theo vai trò. Sinh viên chỉ truy cập bản ghi của mình; giảng viên chỉ thao tác học phần mình phụ trách. Toàn bộ kết nối phải dùng TLS, secret được đưa vào secret manager, khóa có quy trình rotation và thu hồi. PIN và mật khẩu dùng hàm dẫn xuất chậm có salt, kết hợp rate limit, lockout và cảnh báo. Nếu mục tiêu là bảo mật thực tế, có thể giữ CRT như lớp biểu diễn dữ liệu nhưng bọc giá trị hoặc ngữ cảnh nhạy cảm bằng AES-GCM, hoặc dùng HMAC để phát hiện sửa đổi. Java service cần authentication nội bộ, health endpoint, timeout, retry có giới hạn và circuit breaker. Job đổi PIN nên chạy trên hàng đợi bền vững, có trạng thái idempotent, khả năng tiếp tục sau restart và metric đo thời gian xử lý.

## 22:05 — Mười câu hỏi bạn phải trả lời được
*Chương: Ôn tập · Thời lượng cảnh: 47.2 giây*

Trước khi kết thúc, hãy tự trả lời các câu hỏi sau. Một, vì sao modulo phải lớn hơn một trăm? Hai, nếu muốn giữ hai chữ số thập phân thì phải thay đổi gì? Ba, vì sao frontend đọc Excel nhưng backend vẫn phải kiểm tra lại? Bốn, tại sao Java không truy vấn SQL Server? Năm, giảng viên và sinh viên dùng cặp PIN public key khác nhau nhưng vẫn tạo cùng shared secret bằng cách nào? Sáu, startIndex được tính từ những thành phần nào? Bảy, DIEM_CRT lưu dữ liệu gì và cố ý không lưu dữ liệu gì? Tám, vì sao C được lưu dạng chuỗi? Chín, điều gì xảy ra nếu đổi PIN mà không mã hóa lại C? Mười, vì sao CRT không thể thay thế hoàn toàn AES hoặc một hệ mật mã hiện đại? Nếu giải thích trôi chảy mười câu này, bạn đã nắm được phần cốt lõi của đề tài.

## 22:53 — Chuỗi bằng chứng của đồ án
*Chương: Kết luận · Thời lượng cảnh: 48.0 giây*

Tóm lại, giá trị của đồ án không nằm ở một màn hình CRUD hay một công thức đứng riêng lẻ. Chuỗi bằng chứng đầy đủ là: giảng viên hợp lệ nhập dữ liệu cho học phần mình phụ trách; frontend chuyển Excel thành JSON; backend kiểm tra session, quyền, PIN và ngữ cảnh; Java dẫn xuất prime range và mã hóa ba điểm thành C; SQL Server lưu C cùng ngữ cảnh thay vì lưu trực tiếp GK, CK và TB trong DIEM_CRT; sinh viên hợp lệ nhập PIN để tái lập cùng bộ prime và khôi phục đúng điểm; khi PIN thay đổi, hệ thống giải mã bằng tham số cũ, mã hóa lại bằng tham số mới trong transaction, tạo C mới nhưng bảo toàn điểm. CRT trong dự án là một cơ chế học thuật có giới hạn, nhưng hệ thống đã minh họa rõ cách tích hợp toán học, cơ sở dữ liệu, web và bảo mật thành một luồng phần mềm hoàn chỉnh.
