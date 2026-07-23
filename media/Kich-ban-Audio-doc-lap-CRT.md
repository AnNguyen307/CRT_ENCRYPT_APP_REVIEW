# Bài nghe độc lập: Hiểu toàn diện dự án quản lý điểm dùng định lý số dư Trung Hoa

Đây là một bài giảng âm thanh được biên soạn riêng để nghe mà không cần nhìn phim bài giảng, trang trình chiếu hoặc mã nguồn. Mục tiêu của bài nghe là giúp bạn hình dung được toàn bộ dự án như một câu chuyện có đầu vào, quá trình xử lý, đầu ra, giới hạn và hướng cải tiến.

Trong bài, cụm từ định lý số dư Trung Hoa được dùng thay cho tên viết tắt thường gặp là C R T. Mã số cá nhân là dãy số người dùng nhập để tham gia tạo lại ngữ cảnh khóa. Giá trị C là số nguyên lớn được lưu thay cho ba điểm thành phần trong bảng điểm định lý số dư Trung Hoa.

Bạn không cần ghi nhớ mọi con số ngay lần nghe đầu tiên. Ở lần đầu, hãy tập trung vào ba ý. Thứ nhất, dữ liệu đi qua những thành phần nào. Thứ hai, định lý số dư Trung Hoa ghép ba điểm thành C ra sao. Thứ ba, tại sao phép ghép đúng về toán học nhưng chưa tương đương với mã hóa hiện đại.

Bài nghe gồm tám chương. Sau mỗi chương sẽ có một đoạn tóm tắt và một câu tự kiểm tra. Bạn có thể tạm dừng trước câu trả lời nếu muốn tự giải thích bằng lời của mình.

## Chương 1 — Bản đồ toàn bộ dự án

Hãy bắt đầu từ bài toán nghiệp vụ. Một hệ thống quản lý sinh viên cần cho giảng viên nhập điểm, cho sinh viên xem điểm và cho quản trị viên quản lý dữ liệu nền. Dự án bổ sung một mục tiêu: trong bảng điểm chính, không lưu trực tiếp ba cột điểm giữa kỳ, cuối kỳ và trung bình. Thay vào đó, hệ thống ghép ba giá trị thành một số C bằng định lý số dư Trung Hoa.

Ba vai trò chính là quản trị viên, giảng viên và sinh viên. Quản trị viên quản lý tài khoản, lớp, khoa, học phần và các dữ liệu liên quan. Giảng viên nhập điểm cho học phần được phân công. Sinh viên đăng nhập, nhập mã số cá nhân và xem điểm của chính mình.

Điểm quan trọng đầu tiên là phân biệt giao diện với quyền thật. Một nút bị ẩn trên giao diện không tạo ra bảo mật. Quyền phải được kiểm tra tại máy chủ ở mỗi địa chỉ xử lý. Nếu người dùng tự sửa yêu cầu gửi đi, máy chủ vẫn phải xác định vai trò từ phiên đăng nhập và từ chối thao tác không được phép.

Kiến trúc dự án có thể hiểu thành bốn khối. Khối thứ nhất là giao diện người dùng chạy trong trình duyệt. Khối thứ hai là máy chủ điều phối nghiệp vụ. Khối thứ ba là dịch vụ tính toán số nguyên lớn. Khối thứ tư là máy chủ cơ sở dữ liệu.

Giao diện cung cấp màn hình đăng nhập, nhập bảng tính điểm, xem điểm và đổi mã số cá nhân. Giao diện có thể kiểm tra nhanh dữ liệu để giúp người dùng phát hiện lỗi sớm, nhưng không được là nơi kiểm tra duy nhất.

Máy chủ điều phối là trung tâm của hệ thống. Nó đọc phiên đăng nhập, kiểm tra vai trò, truy vấn thông tin học phần, lấy dữ liệu sinh viên, lấy số nguyên tố, gọi dịch vụ tính toán và ghi kết quả vào cơ sở dữ liệu. Thành phần này quyết định bản ghi nào được đọc hoặc sửa.

Dịch vụ tính toán nhận dữ liệu có cấu trúc từ máy chủ điều phối. Nó tạo khóa công khai mô phỏng từ mã số cá nhân, xác định vùng số nguyên tố, ghép ba số bằng định lý số dư Trung Hoa và lấy lại ba phần dư từ C. Dịch vụ này dùng kiểu số nguyên lớn vì tích của các số nguyên tố có thể vượt giới hạn số nguyên sáu mươi bốn bit.

Cơ sở dữ liệu chứa tài khoản, sinh viên, giảng viên, học phần, thời khóa biểu, bảng số nguyên tố và bảng điểm định lý số dư Trung Hoa. Bảng điểm này lưu C dưới dạng chuỗi số rất dài, cùng mã sinh viên, mã học phần, mã giảng viên và một số bản chụp ngữ cảnh.

Một luồng nhập điểm đi qua bốn khối như sau. Giảng viên chọn tệp bảng tính trên giao diện. Máy chủ kiểm tra quyền và dữ liệu. Dịch vụ tính toán ghép ba điểm thành C. Cơ sở dữ liệu lưu C. Khi sinh viên xem điểm, luồng đi ngược lại: máy chủ lấy C, tái lập đúng ba số nguyên tố, dịch vụ tính toán lấy phần dư, rồi máy chủ chia cho mười và trả ba điểm về giao diện.

Hệ thống còn có luồng đổi mã số cá nhân. Đây không phải chỉ là thay giá trị băm của mã số cá nhân. Vì mã số cá nhân tham gia chọn vùng số nguyên tố, đổi mã số cá nhân làm thay đổi bộ số nguyên tố. Do đó mọi C liên quan phải được giải mã bằng vùng cũ và ghép lại bằng vùng mới trong cùng một giao dịch.

Khi đánh giá dự án, cần tách ba lớp. Lớp một là chức năng nghiệp vụ: đăng nhập, nhập điểm, xem điểm và đổi mã số cá nhân. Lớp hai là tính đúng toán học: C phải cho lại đúng ba phần dư. Lớp ba là an toàn thông tin: kẻ tấn công có thể đọc, đoán hoặc sửa dữ liệu đến mức nào.

Một dự án có thể làm tốt lớp một và lớp hai nhưng vẫn yếu ở lớp ba. Đây chính là điểm cần hiểu rõ. Định lý số dư Trung Hoa trong dự án thực hiện được phép ghép và tách điểm. Tuy nhiên, việc gọi phép ghép đó là mã hóa an toàn cần được kiểm chứng bằng mô hình đe dọa, không thể kết luận chỉ vì C nhìn rất lớn và khó đọc bằng mắt.

Tóm tắt chương một. Hệ thống có bốn khối, ba vai trò và ba luồng quan trọng. Máy chủ điều phối là ranh giới quyền. Dịch vụ tính toán xử lý số nguyên lớn. Cơ sở dữ liệu lưu C. Đổi mã số cá nhân bắt buộc phải ghép lại dữ liệu.

Câu tự kiểm tra: nếu giao diện đã ẩn nút nhập điểm đối với sinh viên, máy chủ có cần kiểm tra vai trò nữa không?

Câu trả lời là có. Người dùng có thể gửi yêu cầu trực tiếp mà không bấm nút trên giao diện. Vì vậy máy chủ phải kiểm tra phiên đăng nhập, vai trò và quyền trên từng học phần.

## Chương 2 — Dữ liệu đi vào và được lưu như thế nào

Hãy theo dõi một dòng điểm từ lúc còn nằm trong bảng tính. Dòng đó thường có mã số sinh viên, điểm giữa kỳ, điểm cuối kỳ và điểm trung bình. Điểm sử dụng thang từ không đến mười.

Giao diện đọc tệp bảng tính và phát hiện tên cột. Nó kiểm tra kiểu dữ liệu, điểm ngoài khoảng và mã số sinh viên không thuộc danh sách lớp. Những kiểm tra này giúp người dùng sửa tệp trước khi gửi.

Tuy nhiên, kiểm tra trên giao diện chỉ là lớp hỗ trợ. Máy chủ phải lặp lại toàn bộ quy tắc quan trọng. Lý do rất đơn giản: dữ liệu gửi đến máy chủ có thể được tạo bởi một chương trình khác, không nhất thiết đi qua giao diện chính thức.

Một điểm yếu cần chú ý là điểm trung bình do tệp bảng tính cung cấp. Nếu máy chủ tin trực tiếp giá trị này, người dùng có thể nhập giữa kỳ là bảy, cuối kỳ là tám nhưng trung bình là mười. Hệ thống cần có một quy tắc duy nhất để tự tính trung bình, hoặc ít nhất kiểm tra giá trị gửi lên có khớp công thức hay không.

Quy tắc tính trung bình phải được ghi rõ. Nếu giữa kỳ chiếm bốn mươi phần trăm và cuối kỳ chiếm sáu mươi phần trăm, máy chủ phải tính theo đúng tỉ lệ đó. Nếu mỗi học phần có tỉ lệ riêng, tỉ lệ phải nằm trong dữ liệu học phần và được quản lý có phiên bản.

Trước khi ghép bằng định lý số dư Trung Hoa, điểm được chuẩn hóa thành số nguyên. Cách đang dùng là nhân điểm với mười rồi làm tròn. Ví dụ bảy phẩy năm trở thành bảy mươi lăm. Tám phẩy hai trở thành tám mươi hai.

Chuẩn hóa là cần thiết vì phép đồng dư làm việc thuận tiện với số nguyên. Khi giải mã, hệ thống chia kết quả cho mười để trở về thang điểm ban đầu.

Phép làm tròn tạo ra một giới hạn chính xác. Nếu đầu vào có nhiều hơn một chữ số sau dấu phẩy, sai số tối đa do làm tròn đến một chữ số thập phân là không phẩy không năm điểm. Hệ thống nên quyết định rõ có cho phép nhiều hơn một chữ số thập phân hay không.

Sau chuẩn hóa, ba điểm phải nằm trong khoảng từ không đến một trăm. Mỗi số nguyên tố dùng làm mô-đun phải lớn hơn một trăm. Nếu mô-đun nhỏ hơn hoặc bằng điểm, phần dư có thể bị cuộn vòng và không còn bằng điểm gốc.

Máy chủ kiểm tra giảng viên có thực sự phụ trách học phần hay không. Việc này không thể chỉ dựa vào mã học phần gửi từ trình duyệt. Máy chủ phải truy vấn bản ghi học phần và so mã giảng viên được phân công với danh tính trong phiên đăng nhập.

Máy chủ cũng cần kiểm tra sinh viên có thuộc đúng lớp hoặc đã đăng ký học phần hay không. Nếu chỉ kiểm tra sinh viên tồn tại trong hệ thống, một mã số sinh viên hợp lệ nhưng không học môn đó vẫn có thể bị thêm điểm.

Khi dữ liệu hợp lệ, máy chủ lấy thông tin khoa, lớp, học phần và khóa công khai của sinh viên. Các giá trị này tham gia tạo ngữ cảnh để chọn vùng số nguyên tố.

Kết quả cuối cùng được ghi vào bảng điểm định lý số dư Trung Hoa. Trường quan trọng nhất là C. C được lưu dưới dạng chuỗi văn bản dài để không bị tràn số trong cơ sở dữ liệu.

Bảng còn lưu mã sinh viên, mã học phần và mã giảng viên. Nó cũng lưu bản chụp mã khoa, mã lớp và khóa công khai tại thời điểm ghép. Bản chụp có vai trò quan trọng vì thông tin hiện tại của sinh viên có thể thay đổi sau này.

Giả sử sinh viên chuyển lớp. Nếu khi xem điểm, máy chủ lấy mã lớp mới để tái lập vùng số nguyên tố, nó sẽ chọn bộ số khác với lúc nhập điểm. Khi đó phần dư lấy từ C không còn là ba điểm ban đầu. Bản chụp ngữ cảnh giúp tái lập đúng điều kiện lịch sử.

Lược đồ dữ liệu hiện tại có một rủi ro về danh tính bản ghi. Nếu khóa chính chỉ gồm mã sinh viên và mã học phần, sinh viên học lại cùng môn ở năm học hoặc học kỳ khác có thể làm bản ghi cũ bị ghi đè.

Một thiết kế đầy đủ hơn cần thêm năm học, học kỳ hoặc mã lần học vào khóa. Tốt nhất là có một mã định danh lần học duy nhất, rồi liên kết điểm, đăng ký học phần và lịch sử chỉnh sửa vào mã đó.

Dữ liệu lịch sử được tạo bằng một tập lệnh nạp riêng. Tập lệnh sinh điểm xác định rồi ghép chúng thành C. Vì vậy cần diễn đạt đúng: đây là dữ liệu mẫu lịch sử được sinh theo quy tắc, không phải toàn bộ điểm thật được lấy trực tiếp từ tập dữ liệu nghiên cứu.

Tập lệnh nạp dữ liệu phải chạy sau khi cơ sở dữ liệu và dịch vụ tính toán sẵn sàng, nhưng trước khi máy chủ chính phục vụ người dùng. Nếu cơ sở dữ liệu khởi tạo xóa bảng điểm ở cuối mà tập lệnh lịch sử không chạy lại, sinh viên sẽ không thấy điểm cũ.

Việc nhập nhiều dòng điểm nên có tính nguyên tử. Tính nguyên tử nghĩa là hoặc toàn bộ lô được ghi thành công, hoặc không dòng nào được xác nhận. Nếu dòng thứ năm mươi gặp lỗi nhưng bốn mươi chín dòng trước đã được ghi, giảng viên sẽ khó biết trạng thái thật của lô.

Có hai cách thiết kế. Cách một là dùng một giao dịch cho toàn bộ lô, phù hợp khi lô không quá lớn. Cách hai là dùng bảng tạm và trạng thái lô, kiểm tra toàn bộ trước rồi mới hợp nhất. Cách hai dễ theo dõi, thử lại và báo cáo lỗi theo từng dòng hơn.

Mỗi lô nên có mã định danh. Nhật ký chỉ cần ghi ai nhập, học phần nào, số dòng thành công, số dòng thất bại và mã lô. Không nên ghi mã số cá nhân, toàn bộ danh sách điểm hoặc dữ liệu rõ vào nhật ký.

Tóm tắt chương hai. Dữ liệu phải được kiểm tra lại ở máy chủ. Điểm được nhân mười và làm tròn. Máy chủ phải tự tính hoặc kiểm tra điểm trung bình. Bảng điểm lưu C cùng bản chụp ngữ cảnh. Khóa bản ghi cần phân biệt năm, kỳ hoặc lần học. Nhập lô cần tính nguyên tử.

Câu tự kiểm tra: vì sao lưu mã lớp hiện tại của sinh viên là chưa đủ để giải mã điểm lịch sử?

Câu trả lời là vì sinh viên có thể chuyển lớp. Vùng số nguyên tố phụ thuộc ngữ cảnh lúc tạo C, nên hệ thống phải lưu và dùng lại bản chụp mã lớp, mã khoa và các dữ liệu liên quan tại thời điểm đó.

## Chương 3 — Định lý số dư Trung Hoa từ trực giác đến công thức

Bây giờ ta đi vào phần toán học cốt lõi. Hãy tưởng tượng ba chiếc đồng hồ có độ dài vòng khác nhau. Đồng hồ thứ nhất quay lại sau một trăm lẻ một nấc. Đồng hồ thứ hai quay lại sau một trăm lẻ ba nấc. Đồng hồ thứ ba quay lại sau một trăm lẻ bảy nấc.

Ta cần tìm một số C sao cho khi chia C cho một trăm lẻ một, phần dư là bảy mươi lăm. Khi chia cho một trăm lẻ ba, phần dư là tám mươi lăm. Khi chia cho một trăm lẻ bảy, phần dư là tám mươi mốt.

Ba câu này được gọi là một hệ đồng dư. Bảy mươi lăm, tám mươi lăm và tám mươi mốt là ba phần dư. Một trăm lẻ một, một trăm lẻ ba và một trăm lẻ bảy là ba mô-đun.

Định lý số dư Trung Hoa nói rằng nếu các mô-đun đôi một nguyên tố cùng nhau, hệ đồng dư có một nghiệm duy nhất trong khoảng từ không đến tích của các mô-đun trừ một.

Đôi một nguyên tố cùng nhau nghĩa là ước chung lớn nhất của mỗi cặp bằng một. Ba số nguyên tố khác nhau tự động thỏa điều kiện này.

Tại sao điều kiện đó quan trọng? Trong công thức ghép, ta phải tìm nghịch đảo theo phép chia dư. Nghịch đảo chỉ tồn tại khi số cần đảo và mô-đun nguyên tố cùng nhau.

Gọi tích của ba mô-đun là M. Với ví dụ đang xét, M bằng một trăm lẻ một nhân một trăm lẻ ba nhân một trăm lẻ bảy. Kết quả là một triệu một trăm mười ba nghìn một trăm hai mươi mốt.

Với từng vị trí i, ta tính M i bằng M chia cho mô-đun ở vị trí đó. M một bằng mười một nghìn không trăm hai mươi mốt. M hai bằng mười nghìn tám trăm lẻ bảy. M ba bằng mười nghìn bốn trăm lẻ ba.

Tiếp theo, tìm y i sao cho M i nhân y i, khi chia cho mô-đun tương ứng, có phần dư bằng một. Đó chính là nghịch đảo theo phép chia dư.

Trong ví dụ này, y một bằng năm mươi chín. y hai bằng chín mươi. y ba bằng năm mươi tám.

Sau đó, với mỗi điểm a i, ta tạo một số hạng bằng a i nhân M i nhân y i. C bằng tổng ba số hạng, rồi lấy phần dư theo M.

Viết bằng lời, C là tổng của ba điểm đã chuẩn hóa, mỗi điểm nhân với tích phụ và nghịch đảo tương ứng, sau đó rút gọn trong một vòng có độ dài M.

Số hạng thứ nhất là bảy mươi lăm nhân mười một nghìn không trăm hai mươi mốt nhân năm mươi chín. Số hạng thứ hai là tám mươi lăm nhân mười nghìn tám trăm lẻ bảy nhân chín mươi. Số hạng thứ ba là tám mươi mốt nhân mười nghìn bốn trăm lẻ ba nhân năm mươi tám.

Sau khi cộng và lấy phần dư theo M, ta nhận C bằng một triệu một trăm lẻ hai nghìn hai trăm tám mươi tám.

Ta kiểm tra lại. C chia cho một trăm lẻ một dư bảy mươi lăm. C chia cho một trăm lẻ ba dư tám mươi lăm. C chia cho một trăm lẻ bảy dư tám mươi mốt. Ba điểm đã trở lại đúng.

Điều thú vị là giải mã rất đơn giản. Không cần đảo lại toàn bộ công thức. Chỉ cần lấy C chia lấy dư theo từng mô-đun.

Vì vậy, bí mật thực tế không nằm trong công thức lấy phần dư. Nếu ai có C và đúng ba mô-đun, họ lập tức lấy lại ba điểm.

Có vô số số nguyên khác cũng tạo cùng ba phần dư. Chúng khác C một bội của M. Định lý chọn nghiệm chuẩn trong khoảng từ không đến M trừ một. Điều này làm biểu diễn xác định: cùng ba điểm và cùng ba mô-đun luôn cho cùng C.

Tính xác định thuận tiện cho kiểm thử nhưng là một dấu hiệu cần thận trọng trong mật mã. Mã hóa hiện đại thường dùng một giá trị ngẫu nhiên chỉ dùng một lần để cùng dữ liệu và cùng khóa vẫn tạo bản mã khác nhau.

Hãy xét các trường hợp biên. Nếu ba điểm đều bằng không, C chuẩn bằng không. Nếu một điểm bằng một trăm, mô-đun phải lớn hơn một trăm để phần dư không bị biến thành giá trị khác.

Nếu hai mô-đun không nguyên tố cùng nhau, nghịch đảo có thể không tồn tại. Dịch vụ tính toán phải từ chối thay vì cố tạo C.

Nếu chỉ truyền hai số nguyên tố hoặc bốn số nguyên tố trong khi hệ thống mong đợi ba cột điểm, dịch vụ cũng phải từ chối. Kích thước mảng là một phần của quy ước giao tiếp.

Nếu C được truyền bằng kiểu số thông thường của trình duyệt, số quá lớn có thể mất chính xác. Vì vậy C phải đi qua các thành phần dưới dạng chuỗi chữ số và chỉ được chuyển thành kiểu số nguyên lớn ở nơi tính toán.

Khi kiểm thử, cần có các dãy giá trị cố định. Ví dụ vừa nêu là một dãy kiểm thử tốt: ba điểm bảy phẩy năm, tám phẩy năm và tám phẩy một; ba mô-đun một trăm lẻ một, một trăm lẻ ba và một trăm lẻ bảy; C bằng một triệu một trăm lẻ hai nghìn hai trăm tám mươi tám.

Một kiểm thử hoàn chỉnh cần làm hai chiều. Chiều một, ghép điểm thành C rồi so với C mong đợi. Chiều hai, lấy C chia lấy dư và so với ba điểm chuẩn hóa.

Ngoài ví dụ nhỏ, cần kiểm thử với các số nguyên tố thật trong cơ sở dữ liệu và C dài hơn sáu mươi bốn bit. Kết quả giữa mô phỏng trong ứng dụng học tập và dịch vụ tính toán phải giống nhau.

Tóm tắt chương ba. Ba điểm trở thành ba phần dư. Ba số nguyên tố là ba mô-đun. Công thức dùng tích M, ba tích phụ và ba nghịch đảo. Giải mã chỉ là lấy C chia lấy dư. Tính đúng toán học không tự tạo ra tính bí mật.

Câu tự kiểm tra: tại sao ba mô-đun phải đôi một nguyên tố cùng nhau?

Câu trả lời là để các nghịch đảo theo phép chia dư tồn tại, từ đó công thức ghép tạo được nghiệm duy nhất trong một chu kỳ có độ dài bằng tích M.

## Chương 4 — Mã số cá nhân, khóa công khai và vùng số nguyên tố

Nếu mọi bản ghi đều dùng cùng ba số nguyên tố, bất kỳ ai biết bộ số đó có thể lấy phần dư của mọi C. Dự án cố gắng tránh điều này bằng cách chọn một vùng số nguyên tố khác nhau theo người dùng và ngữ cảnh học phần.

Mã số cá nhân không trực tiếp trở thành số nguyên tố. Trước hết, hệ thống dùng nó làm số mũ riêng trong một cơ chế trao đổi khóa mô phỏng. Từ số mũ riêng, hệ thống tính khóa công khai.

Hai phía là giảng viên và sinh viên. Khi nhập điểm, phía giảng viên dùng mã số cá nhân của giảng viên cùng khóa công khai của sinh viên. Khi xem điểm, phía sinh viên dùng mã số cá nhân của sinh viên cùng khóa công khai của giảng viên.

Nhờ tính chất lũy thừa theo mô-đun, hai cách tính cho cùng một bí mật dùng chung. Đây là trực giác của cơ chế trao đổi khóa: hai bên không truyền trực tiếp số mũ riêng nhưng có thể đạt cùng một giá trị.

Trong dự án, cơ chế này có vai trò tái lập cùng vùng số nguyên tố từ hai phía. Nó là một mô phỏng phục vụ luồng nghiệp vụ, không nên mặc nhiên được xem là một giao thức trao đổi khóa đã được gia cố cho môi trường vận hành thật.

Bí mật dùng chung được ghép với ngữ cảnh. Ngữ cảnh gồm mã khoa, mã lớp, mã sinh viên, mã học phần và chuỗi phiên bản thuật toán. Thứ tự ghép phải cố định.

Chuỗi ngữ cảnh đi qua hàm băm hai trăm năm mươi sáu bit. Kết quả băm được diễn giải thành một số nguyên lớn B.

Hàm băm có hai tác dụng. Thứ nhất, nó biến đầu vào có độ dài khác nhau thành một giá trị có độ dài cố định. Thứ hai, thay đổi nhỏ trong ngữ cảnh thường làm kết quả thay đổi lớn.

Sau đó B được ánh xạ vào không gian chỉ số của bảng số nguyên tố. Bảng có năm triệu dòng. Hệ thống cần lấy đúng ba dòng liên tiếp.

Một công thức an toàn về chỉ số phải bảo đảm vị trí bắt đầu không vượt quá cuối bảng. Vì cần ba dòng và có một khoảng đẩy ban đầu, hệ thống tính một miền hợp lệ rồi lấy B chia lấy dư theo kích thước miền đó.

Chỉ số bắt đầu bằng kết quả ánh xạ cộng một trăm. Chỉ số kết thúc bằng chỉ số bắt đầu cộng hai. Máy chủ truy vấn ba số nguyên tố theo ba chỉ số liên tiếp.

Việc cộng một trăm nhằm tránh vùng số nguyên tố rất nhỏ ở đầu bảng. Vì điểm chuẩn hóa tối đa là một trăm, các mô-đun phải lớn hơn một trăm.

Máy chủ phải kiểm tra truy vấn trả đúng ba dòng, mỗi giá trị là số hợp lệ và thứ tự được xác định bằng mã chỉ số. Không nên dựa vào thứ tự vật lý ngẫu nhiên của cơ sở dữ liệu.

Phiên bản thuật toán phải là dữ liệu có ý nghĩa, không phải nhãn trang trí. Nếu một bản ghi được tạo bằng công thức vùng số nguyên tố phiên bản một, luồng xem điểm phải chọn lại đúng công thức phiên bản một.

Nếu phiên bản chỉ được lưu nhưng mã nguồn luôn chạy công thức mới nhất, sau một lần nâng cấp, dữ liệu cũ có thể không giải mã được.

Cũng cần định nghĩa cách ghép chuỗi ngữ cảnh. Ví dụ, nối các trường bằng dấu phân cách rõ ràng và chuẩn bộ mã ký tự. Nếu chỉ nối trực tiếp, hai bộ dữ liệu khác nhau có thể vô tình tạo cùng chuỗi.

Mã số cá nhân và mật khẩu là thông tin xác thực có độ dài ngắn, dễ bị đoán hơn một khóa mật mã ngẫu nhiên. Nếu cơ sở dữ liệu chỉ lưu giá trị băm nhanh không có muối, kẻ tấn công có bản sao dữ liệu có thể thử rất nhiều khả năng ngoại tuyến.

Do đó, để bảo vệ thông tin xác thực, cần một hàm dẫn xuất khóa chậm có muối và chi phí bộ nhớ. Mỗi tài khoản phải có muối riêng. Có thể thêm một bí mật phía máy chủ được giữ ngoài cơ sở dữ liệu.

Giới hạn số lần thử cũng cần thiết. Giới hạn theo tài khoản và địa chỉ mạng, tăng dần thời gian chờ và khóa tạm thời sau nhiều lần sai giúp giảm dò đoán trực tuyến.

Khóa công khai cần được quản lý theo phiên bản. Khi đổi mã số cá nhân, khóa công khai thay đổi. Các bản ghi lịch sử phải biết khóa nào đã được dùng hoặc phải được ghép lại nhất quán sang khóa mới.

Bản chụp khóa công khai trong bảng điểm giúp lưu ngữ cảnh lúc tạo C. Tuy nhiên, lưu khóa công khai không bù được việc bảo vệ kém mã số cá nhân. Nếu số mũ riêng có không gian nhỏ, có thể thử các khả năng để tìm giá trị phù hợp.

Một điểm rất quan trọng là vùng số nguyên tố không thật sự bí mật khi toàn bộ thuật toán, C và bảng năm triệu số nguyên tố bị lộ. Kẻ tấn công có thể thử từng bộ ba liên tiếp rồi giữ các vị trí mà ba phần dư đều nằm trong khoảng không đến một trăm.

Miền điểm nhỏ tạo ra một bộ lọc mạnh. Với mỗi bộ sai, xác suất ngẫu nhiên để cả ba phần dư đều nhỏ thường rất thấp khi số nguyên tố lớn. Vì vậy việc thử toàn bộ có thể thu hẹp nhanh đến bộ đúng hoặc một số ít ứng viên.

Phép đo trên mẫu tổng hợp của dự án cho thấy có thể thử không gian năm triệu vị trí trong thời gian ngắn trên một máy thông thường. Con số thời gian cụ thể phụ thuộc phần cứng và cách cài đặt, nhưng kết luận cấu trúc không đổi: không gian hữu hạn và điều kiện phần dư nhỏ làm vùng chỉ số có thể bị dò.

Tóm tắt chương bốn. Hai phía dùng mã số cá nhân và khóa công khai để tạo cùng bí mật dùng chung. Bí mật cùng ngữ cảnh được băm thành B. B chọn ba dòng trong bảng năm triệu số nguyên tố. Cách này giúp tái lập vùng, nhưng không làm vùng trở thành bí mật mạnh trước kẻ có dữ liệu và mã nguồn.

Câu tự kiểm tra: vì sao cùng một sinh viên nhưng hai học phần khác nhau nên nhận hai vùng số nguyên tố khác nhau?

Câu trả lời là vì mã học phần nằm trong ngữ cảnh được băm. Thay mã học phần làm B thay đổi và thường dẫn đến vị trí bắt đầu khác.

## Chương 5 — Ba luồng nghiệp vụ quan trọng

Chương này kể lại ba luồng như ba câu chuyện hoàn chỉnh. Luồng thứ nhất là giảng viên nhập điểm. Luồng thứ hai là sinh viên xem điểm. Luồng thứ ba là đổi mã số cá nhân.

Với luồng nhập điểm, giảng viên đăng nhập và chọn học phần. Giao diện đọc bảng tính, hiển thị các lỗi dễ phát hiện và gửi danh sách dòng lên máy chủ.

Máy chủ lấy danh tính giảng viên từ phiên đăng nhập. Nó không tin mã giảng viên do trình duyệt tự khai báo. Sau đó máy chủ truy vấn học phần theo mã môn, năm học và học kỳ, rồi kiểm tra giảng viên có được phân công hay không.

Máy chủ kiểm tra từng dòng: mã sinh viên tồn tại, sinh viên thuộc phạm vi học phần, điểm nằm trong khoảng, giá trị trung bình đúng quy tắc và không có dòng trùng không được giải quyết.

Ba điểm được nhân mười và làm tròn. Máy chủ lấy bản chụp lớp, khoa và khóa công khai sinh viên.

Máy chủ gọi dịch vụ tính toán để xác định vị trí bắt đầu bằng mã số cá nhân giảng viên và khóa công khai sinh viên. Sau đó máy chủ lấy đúng ba số nguyên tố từ cơ sở dữ liệu.

Máy chủ gửi ba điểm chuẩn hóa và ba số nguyên tố sang chức năng ghép. Dịch vụ tính toán trả C ở dạng chuỗi.

Máy chủ chuẩn bị bản ghi gồm mã sinh viên, mã học phần, mã giảng viên, C, bản chụp ngữ cảnh, phiên bản thuật toán và phiên bản khóa. Bản ghi được thêm mới hoặc cập nhật trong giao dịch.

Nếu một bước thất bại, hệ thống phải biết lô đang ở trạng thái nào. Không nên trả thông báo thành công chung khi một phần dữ liệu chưa được ghi.

Với luồng xem điểm, sinh viên đăng nhập và chọn học phần. Giao diện gửi mã học phần, năm học, học kỳ và mã số cá nhân.

Máy chủ phải lấy mã sinh viên từ phiên đăng nhập. Đây là nguyên tắc quyền sở hữu dữ liệu: người dùng không được tự chọn danh tính đối tượng bằng cách gửi mã sinh viên khác trong phần thân yêu cầu.

Máy chủ kiểm tra mã số cá nhân bằng cơ chế bảo vệ thông tin xác thực. Nếu sai, yêu cầu dừng. Nếu đúng, máy chủ truy vấn bản ghi điểm của chính sinh viên trong đúng lần học.

Từ bản ghi, máy chủ lấy C, mã giảng viên, bản chụp khoa, lớp và phiên bản. Máy chủ lấy đúng khóa công khai giảng viên tương ứng.

Máy chủ gọi chức năng xác định vùng theo chiều sinh viên: mã số cá nhân sinh viên kết hợp với khóa công khai giảng viên. Kết quả phải trùng vùng đã dùng khi giảng viên nhập.

Máy chủ lấy ba số nguyên tố rồi gửi C sang chức năng tách. Dịch vụ trả ba phần dư nguyên.

Máy chủ kiểm tra có đúng ba phần tử và mỗi phần tử nằm trong khoảng không đến một trăm. Sau đó chia từng giá trị cho mười và trả về giao diện.

Nếu bản ghi không tồn tại, thông báo nên là chưa có điểm. Nếu dịch vụ tính toán hỏng hoặc dữ liệu sai cấu trúc, thông báo cho người dùng nên ngắn gọn; chi tiết kỹ thuật đi vào nhật ký đã che dữ liệu nhạy cảm.

Một lỗi quyền truy cập đáng chú ý xuất hiện khi địa chỉ xem điểm ưu tiên mã sinh viên trong yêu cầu hơn mã sinh viên trong phiên đăng nhập. Giao diện bình thường không gửi trường này nên thao tác thông thường không phát hiện lỗi. Nhưng người dùng có thể tự thêm trường để yêu cầu điểm của người khác.

Cách sửa là không nhận mã sinh viên từ người dùng trong luồng sinh viên xem điểm. Máy chủ luôn dùng danh tính đã xác thực. Nếu cần quản trị viên xem thay, phải có một địa chỉ riêng với quyền rõ ràng và nhật ký đầy đủ.

Với luồng đổi mã số cá nhân, người dùng gửi mã cũ và mã mới. Máy chủ kiểm tra mã cũ, chính sách độ mạnh của mã mới và tình trạng tài khoản.

Máy chủ bắt đầu giao dịch và khóa các bản ghi liên quan để tránh bị thay đổi đồng thời.

Đối với từng bản ghi C, máy chủ tái lập vùng cũ bằng mã số cá nhân cũ và ngữ cảnh lịch sử. Nó lấy ba phần dư, kiểm tra miền và giữ ba điểm trong bộ nhớ.

Sau đó máy chủ tạo khóa công khai mới từ mã số cá nhân mới, tái lập vùng mới, lấy ba số nguyên tố mới và ghép lại ba điểm thành C mới.

Máy chủ cập nhật C, khóa công khai, phiên bản khóa và giá trị bảo vệ mã số cá nhân. Trước khi xác nhận giao dịch, nó có thể đọc lại và kiểm tra C mới tách ra đúng ba điểm.

Nếu bất kỳ bản ghi nào thất bại, toàn bộ giao dịch phải hoàn tác. Điều này bảo đảm không xảy ra trạng thái một nửa dữ liệu dùng mã cũ, một nửa dùng mã mới.

Khóa dòng khi cập nhật giúp hạn chế hai tác vụ sửa cùng bản ghi. Kiểm tra phiên bản lạc quan giúp phát hiện C đã thay đổi kể từ lúc đọc. Hai cơ chế có thể kết hợp để bảo vệ luồng dài.

Giao dịch đổi mã số cá nhân có thể kéo dài nếu một sinh viên có nhiều điểm lịch sử. Trong thiết kế vận hành lớn, có thể cần một tác vụ nền có trạng thái, nhưng vẫn phải bảo đảm người dùng không bị rơi vào trạng thái không thể xem điểm.

Cần quyết định hành vi khi máy chủ dừng giữa quá trình. Nếu giao dịch cơ sở dữ liệu chưa xác nhận, cơ sở dữ liệu phải tự hoàn tác. Nếu dùng tác vụ nhiều giai đoạn, cần điểm tiếp tục, khả năng chạy lặp lại an toàn và trạng thái rõ.

Tóm tắt chương năm. Nhập điểm phải kiểm tra quyền và dữ liệu trước khi tạo C. Xem điểm phải lấy mã sinh viên từ phiên đăng nhập. Đổi mã số cá nhân phải giải mã bằng vùng cũ, ghép bằng vùng mới và thực hiện trong một giao dịch có hoàn tác.

Câu tự kiểm tra: điều gì xảy ra nếu hệ thống chỉ thay giá trị băm của mã số cá nhân mà không thay C?

Câu trả lời là lần xem điểm sau sẽ tái lập một vùng số nguyên tố mới. Lấy C cũ chia theo bộ mới sẽ cho các phần dư khác, nên điểm có thể sai hoặc không hợp lệ.

## Chương 6 — Đánh giá bảo mật một cách trung thực

Ta bắt đầu bằng bốn mục tiêu. Tính bí mật nghĩa là người không có quyền không đọc được điểm. Tính toàn vẹn nghĩa là việc sửa C hoặc ngữ cảnh bị phát hiện. Tính xác thực nghĩa là hệ thống biết ai đang thao tác. Khả năng sẵn sàng nghĩa là chức năng vẫn hoạt động khi thành phần gặp lỗi.

Định lý số dư Trung Hoa giải quyết bài toán biểu diễn ba số bằng một số. Nó không tự cung cấp cả bốn mục tiêu.

Về tính bí mật, nếu kẻ tấn công chỉ nhìn một C mà không biết các mô-đun, C khó diễn giải bằng mắt. Nhưng mô hình đe dọa thực tế phải giả sử kẻ tấn công có thể lấy bản sao cơ sở dữ liệu, biết mã nguồn hoặc biết thuật toán.

Trong dự án, bảng số nguyên tố có năm triệu dòng và cách chọn vùng là xác định. Kẻ tấn công có thể thử các bộ ba liên tiếp và kiểm tra phần dư. Vì điểm chỉ từ không đến một trăm, bộ đúng tạo ra một dấu hiệu rất dễ nhận biết.

Do đó, C hiện tại phù hợp để gọi là biểu diễn hoặc che giấu xác định trong một bản thử nghiệm học thuật. Không nên tuyên bố rằng lộ cơ sở dữ liệu vẫn không thể biết điểm.

Một chi tiết khác là cơ sở dữ liệu còn có bảng dữ liệu điểm gốc dùng cho mục đích nghiên cứu hoặc nạp mẫu. Nếu bảng đó chứa điểm rõ và nằm trong cùng phạm vi bảo vệ, câu nói cơ sở dữ liệu hoàn toàn không lưu điểm rõ là sai. Phải giới hạn tuyên bố ở đúng bảng điểm định lý số dư Trung Hoa.

Về tính toàn vẹn, giải mã hiện tại chỉ lấy phần dư. Nếu kẻ tấn công sửa C, hệ thống không có thẻ xác thực để chứng minh C còn nguyên vẹn.

Một C giả vẫn có thể cho ba phần dư nằm trong khoảng hợp lệ. Hệ thống có thể hiển thị điểm giả mà không biết dữ liệu đã bị sửa.

Phiên bản khóa không phải là mã kiểm tra toàn vẹn. Mốc thời gian cũng không phải. Chúng giúp quản lý trạng thái nhưng không tạo bằng chứng mật mã.

Giải pháp hiện đại là mã hóa kèm xác thực. Mỗi bản ghi có bản mã, một giá trị chỉ dùng một lần và thẻ xác thực. Dữ liệu liên kết gồm mã sinh viên, mã học phần, năm, kỳ, lần học và phiên bản.

Nếu bản mã hoặc dữ liệu liên kết bị thay đổi, quá trình mở dữ liệu phải thất bại. Đây là khác biệt quan trọng giữa chỉ che nội dung và vừa giữ bí mật vừa phát hiện sửa đổi.

Khóa mã hóa không nên được suy ra trực tiếp từ một mã số cá nhân ngắn. Hệ thống cần một khóa dữ liệu ngẫu nhiên đủ mạnh, được quản lý bởi hệ thống quản lý khóa hoặc thiết bị bảo mật phù hợp.

Mã số cá nhân có thể tiếp tục là một yếu tố mở khóa nghiệp vụ, nhưng không nên là bí mật duy nhất bảo vệ toàn bộ điểm.

Về tính xác thực, mật khẩu và mã số cá nhân cần hàm dẫn xuất khóa chậm có muối. Băm nhanh không có muối cho phép kẻ tấn công thử nhiều khả năng với tốc độ cao.

Phiên đăng nhập cần một bí mật mạnh lấy từ cấu hình bắt buộc, không dùng giá trị mặc định. Sau khi đăng nhập thành công, máy chủ nên tạo lại mã phiên để giảm nguy cơ chiếm giữ phiên.

Kho lưu phiên trong bộ nhớ chỉ phù hợp với bản trình diễn một tiến trình. Khi chạy nhiều bản máy chủ hoặc khởi động lại, cần kho phiên dùng chung như cơ sở dữ liệu hoặc kho bộ nhớ mạng.

Tệp nhận dạng phiên nên có thuộc tính chỉ gửi qua kết nối bảo mật trong môi trường thật, không cho mã phía trình duyệt đọc và có chính sách gửi liên trang phù hợp.

Về phân quyền, ngoài vai trò còn có quyền trên từng đối tượng. Giảng viên A không được nhập điểm học phần của giảng viên B. Sinh viên A không được xem điểm của sinh viên B.

Lỗi cho phép người dùng thay mã sinh viên trong yêu cầu là ví dụ về truy cập sai đối tượng. Cách phòng ngừa bền vững là truy vấn dữ liệu luôn gắn với danh tính từ phiên đăng nhập.

Về nhật ký, dữ liệu nhạy cảm không được xuất hiện trong nội dung ghi gỡ lỗi. Không ghi toàn bộ phần thân yêu cầu nhập điểm, mã số cá nhân, dữ liệu rõ, khóa hoặc phản hồi nội bộ.

Nhật ký tốt ghi mã yêu cầu, người thao tác, hành động, đối tượng, kết quả, thời gian và mã lỗi. Các giá trị nhạy cảm được che hoặc thay bằng giá trị băm phục vụ đối chiếu.

Dịch vụ tính toán nội bộ cũng cần xác thực. Việc chỉ đặt nó trong mạng nội bộ giảm bề mặt tấn công nhưng không thay thế xác thực giữa các dịch vụ.

Cần có thời gian chờ, giới hạn thử lại và cơ chế ngắt mạch. Nếu dịch vụ tính toán dừng, máy chủ không nên treo vô hạn hoặc gửi cùng yêu cầu lặp lại không kiểm soát.

Về khả năng sẵn sàng, dịch vụ tính toán là một điểm phụ thuộc. Khi nó dừng, người dùng không thể nhập, xem hoặc đổi mã số cá nhân. Cần kiểm tra tình trạng, cảnh báo và quy trình khôi phục.

Sao lưu phải bao gồm không chỉ C mà cả phiên bản thuật toán, ngữ cảnh và dữ liệu khóa cần thiết. Một bản sao C không có đúng ngữ cảnh có thể không khôi phục được điểm.

Rủi ro lớn nhất không phải là C có đủ lớn hay không. Rủi ro lớn nhất là hệ thống có thể chứng minh bí mật, toàn vẹn, quyền sở hữu và khả năng khôi phục trong mô hình đe dọa cụ thể hay không.

Tóm tắt chương sáu. Định lý số dư Trung Hoa không phải mã hóa có xác thực. Vùng số nguyên tố có thể bị thử. C không có thẻ phát hiện sửa đổi. Thông tin xác thực, phiên đăng nhập, quyền đối tượng, nhật ký và xác thực dịch vụ đều cần gia cố.

Câu tự kiểm tra: nếu C là một số dài hàng trăm chữ số, điều đó có tự chứng minh dữ liệu an toàn không?

Câu trả lời là không. Độ dài chỉ cho biết miền số lớn. An toàn phụ thuộc vào bí mật thực sự, tính ngẫu nhiên, khả năng chống thử, thẻ toàn vẹn, quản lý khóa và mô hình đe dọa.

## Chương 7 — Kiểm thử, hiệu năng và khả năng vận hành

Một hệ thống bảo mật không thể chỉ được kiểm tra bằng vài lần bấm trên giao diện. Ta cần kiểm thử theo lớp.

Lớp đầu tiên là kiểm thử toán học. Ghép rồi tách phải trả đúng dữ liệu với điểm bằng không, điểm bằng một trăm, số thập phân và C vượt giới hạn sáu mươi bốn bit.

Phải có trường hợp mô-đun không nguyên tố cùng nhau, mô-đun nhỏ hơn hoặc bằng một trăm, thiếu phần tử và chuỗi C sai định dạng. Kết quả mong đợi là từ chối rõ ràng.

Lớp thứ hai là kiểm thử quy ước giữa máy chủ và dịch vụ tính toán. Đổi tên trường, sai kiểu, mảng sai độ dài, phản hồi thiếu C và quá thời gian chờ đều phải được xử lý.

Lớp thứ ba là kiểm thử nghiệp vụ. Tệp bảng tính thiếu cột, có chữ trong ô điểm, điểm ngoài khoảng, mã sinh viên trùng, sinh viên ngoài lớp và điểm trung bình sai đều cần ca kiểm thử.

Lớp thứ tư là kiểm thử quyền. Với mỗi địa chỉ, cần thử chưa đăng nhập, sai vai trò và đúng vai trò nhưng sai đối tượng.

Ca quan trọng nhất cho xem điểm là đăng nhập sinh viên A rồi cố gửi mã sinh viên B. Kết quả đúng phải là từ chối, không phải trả dữ liệu hoặc tiết lộ bản ghi có tồn tại.

Lớp thứ năm là kiểm thử đồng thời. Hai giảng viên hoặc hai thẻ trình duyệt cùng nhập vào một bản ghi. Một bên nhập điểm trong khi sinh viên đổi mã số cá nhân. Hai yêu cầu đổi mã số cá nhân cùng lúc.

Kết quả cần được xác định trước. Hoặc một giao dịch chờ giao dịch kia, hoặc kiểm tra phiên bản phát hiện xung đột. Không được im lặng để lần ghi cuối cùng thắng.

Lớp thứ sáu là kiểm thử lỗi và khôi phục. Dừng dịch vụ tính toán ở giữa lô. Dừng máy chủ sau khi tạo C nhưng trước khi ghi dữ liệu. Làm lỗi một dòng giữa giao dịch đổi mã số cá nhân.

Sau mỗi lần chèn lỗi, phải kiểm tra cơ sở dữ liệu ở trạng thái nào, người dùng nhận thông báo gì và có thể thử lại an toàn hay không.

Kiểm thử hoàn tác của đổi mã số cá nhân đặc biệt quan trọng. Nếu lỗi ở bản ghi thứ k, giá trị bảo vệ mã số cá nhân, khóa công khai và mọi C phải trở về trạng thái cũ.

Về hiệu năng, cần đo thời gian ghép và tách cho một, một trăm, một nghìn và mười nghìn sinh viên. Báo cáo cả trung vị và phân vị cao, không chỉ thời gian trung bình.

Cần tách thời gian truy vấn cơ sở dữ liệu, thời gian gọi qua mạng nội bộ và thời gian tính số nguyên lớn. Nếu không tách, ta không biết tối ưu ở đâu.

Một dòng nhập hiện có thể tạo nhiều truy vấn và nhiều lời gọi dịch vụ. Với lô lớn, chi phí qua mạng có thể lớn hơn bản thân phép toán.

Có thể cải thiện bằng cách truy vấn dữ liệu nền theo lô, lấy các số nguyên tố theo nhiều vùng trong một lần, và cho dịch vụ tính toán nhận danh sách nhiều bản ghi.

Phép đo dò toàn bộ vùng số nguyên tố là một kiểm thử bảo mật, không chỉ hiệu năng. Nó cần công bố phần cứng, cách đọc bảng số nguyên tố, số ứng viên tìm được và thời gian.

Ngoài thời gian, cần đo tỉ lệ khớp giả. Khớp giả là vị trí sai nhưng cả ba phần dư vẫn tình cờ nằm trong miền điểm. Nếu có nhiều ứng viên, kẻ tấn công có thể kết hợp dữ liệu phụ để chọn.

Về triển khai, thứ tự khởi động phải rõ. Cơ sở dữ liệu sẵn sàng. Lược đồ và dữ liệu số nguyên tố được nạp. Dịch vụ tính toán sẵn sàng. Dữ liệu lịch sử được tạo. Sau đó máy chủ và giao diện mới mở cho người dùng.

Kiểm tra tình trạng không chỉ là cổng đang mở. Dịch vụ tính toán cần thực hiện được một phép kiểm tra nhỏ. Cơ sở dữ liệu cần trả được truy vấn. Tập lệnh lịch sử cần có mã thoát khác không khi thất bại.

Khi sao lưu và khôi phục, cần thử thực tế. Khôi phục một bản sao vào môi trường riêng, đăng nhập bằng tài khoản thử, xem được điểm lịch sử và xác nhận phiên bản thuật toán hoạt động.

Giám sát nên có số yêu cầu thành công, thất bại, thời gian xử lý, số lần sai mã số cá nhân, lỗi dịch vụ tính toán, số giao dịch hoàn tác và độ dài hàng đợi.

Tóm tắt chương bảy. Cần kiểm thử toán, giao tiếp, nghiệp vụ, quyền, đồng thời và khôi phục. Cần đo hiệu năng theo lô và đo khả năng dò vùng số nguyên tố. Triển khai phải kiểm soát thứ tự khởi động, tình trạng dịch vụ, sao lưu và giám sát.

Câu tự kiểm tra: tại sao kiểm thử bấm qua giao diện không đủ để phát hiện lỗi xem điểm của người khác?

Câu trả lời là vì giao diện chính thức không gửi mã sinh viên tùy chọn. Kiểm thử phải chủ động sửa yêu cầu ở mức giao diện lập trình ứng dụng để xác minh máy chủ không tin dữ liệu do người dùng tự khai báo.

## Chương 8 — Cách trình bày dự án và lộ trình cải tiến

Khi thuyết trình, hãy mở đầu bằng phạm vi chính xác. Hệ thống là một bản thử nghiệm quản lý điểm minh họa cách dùng định lý số dư Trung Hoa để ghép ba điểm chuẩn hóa thành một giá trị C.

Không nên mở đầu bằng tuyên bố tuyệt đối như không ai có thể giải mã hoặc cơ sở dữ liệu bị lộ vẫn an toàn. Những câu đó mời hội đồng kiểm tra một mô hình đe dọa mà dự án hiện chưa đáp ứng.

Một câu mô tả tốt là: bảng điểm định lý số dư Trung Hoa không lưu trực tiếp ba cột điểm thành phần; hệ thống tái lập bộ mô-đun theo ngữ cảnh người dùng để lấy lại phần dư. Đây là bản thử nghiệm học thuật và cần mã hóa có xác thực cùng quản lý khóa trước khi dùng thực tế.

Khi giải thích công thức, hãy dùng một ví dụ nhỏ. Ba điểm bảy phẩy năm, tám phẩy năm và tám phẩy một trở thành bảy mươi lăm, tám mươi lăm và tám mươi mốt.

Chọn ba mô-đun một trăm lẻ một, một trăm lẻ ba và một trăm lẻ bảy. C bằng một triệu một trăm lẻ hai nghìn hai trăm tám mươi tám. Lấy C chia lấy dư theo ba mô-đun sẽ trở lại ba điểm chuẩn hóa.

Sau ví dụ, nói ngay điều kiện. Các mô-đun phải đôi một nguyên tố cùng nhau và lớn hơn một trăm. Dịch vụ dùng kiểu số nguyên lớn để tránh tràn.

Khi giải thích khóa, đừng nói mã số cá nhân chính là khóa mã hóa mạnh. Hãy nói mã số cá nhân tham gia cơ chế tạo lại bí mật dùng chung mô phỏng; bí mật và ngữ cảnh được băm để chọn vùng số nguyên tố.

Khi được hỏi tại sao hai phía ra cùng vùng, hãy giải thích bằng tính đối xứng của lũy thừa theo mô-đun: số mũ riêng của giảng viên kết hợp khóa công khai sinh viên và chiều ngược lại dẫn đến cùng bí mật dùng chung.

Khi được hỏi C có phải mã hóa không, câu trả lời tốt là: C là kết quả ghép xác định bằng định lý số dư Trung Hoa. Nó có tác dụng không lưu trực tiếp ba cột trong bảng chính, nhưng chưa đạt tính bí mật và toàn vẹn của mã hóa hiện đại.

Khi được hỏi lộ cơ sở dữ liệu thì sao, hãy thừa nhận bảng số nguyên tố và không gian chỉ số hữu hạn cho phép thử vùng. Miền điểm nhỏ giúp nhận biết ứng viên. Đây là giới hạn đã xác định và là động lực nâng cấp.

Khi được hỏi kẻ tấn công sửa C, hãy nói hệ thống hiện thiếu thẻ xác thực. Kiểm tra phần dư nằm trong miền không chứng minh C nguyên vẹn. Giải pháp là mã hóa kèm xác thực và ràng buộc dữ liệu liên kết.

Khi được hỏi đổi mã số cá nhân có gì khó, hãy mô tả bốn bước: tách bằng vùng cũ, tạo vùng mới, ghép lại, rồi xác minh và xác nhận giao dịch. Nếu một bước lỗi, hoàn tác toàn bộ.

Khi được hỏi vì sao lưu bản chụp lớp và khoa, hãy nêu trường hợp sinh viên chuyển lớp. Ngữ cảnh hiện tại có thể khác ngữ cảnh lúc tạo C.

Khi được hỏi lỗi nghiêm trọng nhất ở quyền truy cập, hãy nói địa chỉ xem điểm không được nhận mã sinh viên tùy ý từ người dùng. Danh tính phải đến từ phiên đăng nhập.

Khi được hỏi dự án thuộc lĩnh vực nào, hãy trả lời dự án chủ yếu thuộc an toàn thông tin ứng dụng, cơ sở dữ liệu và kỹ nghệ phần mềm. Định lý số dư Trung Hoa là thành phần toán học. Nếu chưa có suy diễn, mô hình tri thức hoặc học máy, không nên gán thêm đóng góp không tồn tại.

Lộ trình cải tiến nên chia theo mức ưu tiên. Mức khẩn cấp gồm sửa quyền xem điểm, xóa nhật ký nhạy cảm, thay bảo vệ mật khẩu và mã số cá nhân, bỏ giá trị bí mật mặc định và giới hạn số lần thử.

Mức tiếp theo gồm sửa khóa bảng điểm để có năm, kỳ hoặc lần học; tự tính điểm trung bình; kiểm tra đăng ký học phần; nhập lô nguyên tử; và điều phối thuật toán theo phiên bản.

Mức mật mã gồm dùng mã hóa có xác thực, khóa dữ liệu ngẫu nhiên, hệ thống quản lý khóa và dữ liệu liên kết ràng buộc danh tính bản ghi.

Mức vận hành gồm kho phiên dùng chung, xác thực giữa dịch vụ, kiểm tra tình trạng, thời gian chờ, giám sát, sao lưu và diễn tập khôi phục.

Quá trình chuyển đổi dữ liệu cần thận trọng. Hệ thống phải đọc C theo thuật toán cũ, tái lập và kiểm tra điểm, mã hóa lại theo thuật toán mới, rồi lưu phiên bản và mã định danh khóa.

Trong giai đoạn chuyển đổi, có thể đọc cả hai định dạng nhưng chỉ ghi định dạng mới. Mỗi bản ghi cần trạng thái chuyển đổi và kiểm tra đối chiếu trước khi xóa dữ liệu cũ.

Không được chuyển đổi bằng cách đoán điểm từ C khi không chắc đúng vùng. Mọi bản ghi không tái lập được phải vào vùng cách ly để điều tra.

Nếu buổi bảo vệ cần phần trình diễn, thứ tự tốt là đăng nhập giảng viên, nhập một dòng điểm, truy vấn bảng điểm để thấy C, đăng nhập sinh viên và xem ba điểm, đổi mã số cá nhân, rồi chứng minh C đổi nhưng ba điểm giữ nguyên.

Sau phần trình diễn thành công, chủ động trình bày một trang về giới hạn. Việc tự nêu giới hạn cho thấy nhóm hiểu hệ thống và giảm nguy cơ bị đánh giá là tuyên bố quá mức.

Ba câu chốt có thể dùng. Một: định lý số dư Trung Hoa trong dự án đúng về phép ghép và tách số. Hai: bảo mật của toàn hệ thống phụ thuộc vào quyền truy cập, bảo vệ thông tin xác thực, tính toàn vẹn và quản lý khóa, không phụ thuộc riêng vào độ lớn của C. Ba: phiên bản hiện tại là nền thử nghiệm để đo lường và nâng cấp, chưa phải thiết kế sẵn sàng cho dữ liệu điểm thật.

Tóm tắt chương tám. Trình bày đúng phạm vi, dùng ví dụ tính tay, nói rõ điều kiện và giới hạn. Ưu tiên sửa quyền, thông tin xác thực, nhật ký và lược đồ trước. Sau đó thay cơ chế bảo vệ dữ liệu bằng mã hóa có xác thực và quản lý khóa.

Câu tự kiểm tra cuối: nếu chỉ được nói một câu trung thực nhất về đóng góp của dự án, bạn sẽ nói gì?

Câu trả lời gợi ý là: dự án xây dựng được một luồng quản lý điểm xuyên suốt, trong đó ba điểm chuẩn hóa được ghép và tái lập bằng định lý số dư Trung Hoa theo ngữ cảnh người dùng; đây là một bản thử nghiệm toán học và kỹ nghệ cần tiếp tục gia cố trước khi dùng như cơ chế bảo mật thực tế.

Bạn đã đi hết bài nghe. Hãy thử tự kể lại dự án trong ba phút theo thứ tự sau: bài toán, bốn thành phần, cách tạo C, cách chọn vùng số nguyên tố, ba luồng nghiệp vụ, ba giới hạn lớn và ba ưu tiên sửa.

Nếu bạn kể được mạch này mà không nhìn tài liệu, bạn đã hiểu cấu trúc của dự án. Khi cần đào sâu công thức, hãy dùng phòng mô phỏng trong ứng dụng. Khi cần ôn phản biện, hãy quay lại chương sáu và chương tám của bài nghe.
