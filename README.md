<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hệ Thống Check-in Khách Mời</title>
    <style>
        /* === PHẦN CSS: THIẾT KẾ GIAO DIỆN === */
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f2f5;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            color: #333;
        }
        .container {
            background-color: white;
            padding: 40px;
            border-radius: 10px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
            width: 90%;
            max-width: 500px;
        }
        h1 {
            color: #1a73e8;
            margin-bottom: 25px;
            text-align: center;
        }
        .form-group {
            margin-bottom: 15px;
            text-align: left;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
            color: #555;
        }
        input[type="text"], input[type="tel"] {
            width: calc(100% - 24px);
            padding: 12px;
            font-size: 16px;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        #searchButton {
            width: 100%;
            padding: 12px;
            margin-top: 10px;
            font-size: 18px;
            font-weight: bold;
            background-color: #1a73e8;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        #searchButton:hover {
            background-color: #1558b8;
        }
        #result {
            margin-top: 20px;
            font-size: 80px;
            font-weight: bold;
            height: 80px;
            line-height: 80px;
            text-align: center;
        }
        #result img {
            max-height: 80px;
            width: auto;
        }
        .info {
            margin-top: 10px;
            font-size: 18px;
            color: #555;
            height: 50px;
            text-align: center;
        }
        .warning {
            color: #ffc107;
            font-size: 80px;
            font-weight: bold;
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>CHECK-IN HỘI THẢO TINH HOA ÁNH SÁNG</h1>
        <form id="checkin-form">
            <div class="form-group">
                <label for="nameInput">Họ và Tên (Không bắt buộc)</label>
                <input type="text" id="nameInput" placeholder="Nhập họ tên...">
            </div>
            <div class="form-group">
                <label for="phoneInput">Số Điện Thoại</label>
                <input type="tel" id="phoneInput" placeholder="Dùng SĐT để xác thực...">
            </div>
            <button type="submit" id="searchButton">Kiểm Tra</button>
        </form>
        <div id="result"></div>
        <div id="info"></div>
    </div>

<script>
    // === PHẦN JAVASCRIPT: LOGIC XỬ LÝ ===

    // DANH SÁCH ĐÃ ĐƯỢC CẬP NHẬT THEO FILE ẢNH VÀ CÓ SẴN STT
    const guestList = [
    	{ stt: 1, name: "Bùi Ngọc Thuý", phone: "0966461546", checkedIn: false },
    	{ stt: 2, name: "Nguyễn Thị Hồng Loan", phone: "0938660663", checkedIn: false },
    	{ stt: 3, name: "Shynh", phone: "", checkedIn: false },
    	{ stt: 4, name: "Nguyễn Thị Thuỷ Mai", phone: "0888546858", checkedIn: false },
    	{ stt: 5, name: "Trương Thái Ngọc", phone: "0776111126", checkedIn: false },
    	{ stt: 6, name: "Hoàng Thu Thuỷ", phone: "0332900098", checkedIn: false },
    	{ stt: 7, name: "Hoàng Văn Minh", phone: "0354626125", checkedIn: false },
    	{ stt: 8, name: "Phan Khánh Linh", phone: "0903161642", checkedIn: false },
    	{ stt: 9, name: "Hồ Nguyễn Khánh Lin", phone: "0935450005", checkedIn: false },
    	{ stt: 10, name: "Dương Võ Mỹ Linh", phone: "0911438768", checkedIn: false },
    	{ stt: 11, name: "Dương Thị Lệ Trang", phone: "0913758647", checkedIn: false },
    	{ stt: 12, name: "Linh Thị Lệ Thu", phone: "0382900679", checkedIn: false },
    	{ stt: 13, name: "Trịnh Minh Hiếu", phone: "0931139886", checkedIn: false },
    	{ stt: 14, name: "Phạm Thị Anh Thư", phone: "0933946653", checkedIn: false },
    	{ stt: 15, name: "Huynh Ny", phone: "0905720227", checkedIn: false },
    	{ stt: 16, name: "Hoàng Ngân", phone: "0946238152", checkedIn: false },
    	{ stt: 17, name: "Phạm Văn Tài", phone: "0842174844", checkedIn: false },
    	{ stt: 18, name: "Trần Oanh Thuỳ Như", phone: "0933222322", checkedIn: false },
    	{ stt: 19, name: "Phạm Thị Mỹ Chi", phone: "0906545848", checkedIn: false },
    	{ stt: 20, name: "Chu Thị Phương Thảo", phone: "0912959179", checkedIn: false },
    	{ stt: 21, name: "Phan Thi Thuy Linh", phone: "0972396789", checkedIn: false },
    	{ stt: 22, name: "Huỳnh Hồ Hải Vân", phone: "0902074849", checkedIn: false },
    	{ stt: 23, name: "Võ Thị Phương", phone: "0847888357", checkedIn: false },
    	{ stt: 24, name: "Nguyễn Thị Giang", phone: "0338029186", checkedIn: false },
    	{ stt: 25, name: "Vũ Thị Thiên Trang", phone: "0827231223", checkedIn: false },
    	{ stt: 26, name: "Võ Thị Bi", phone: "", checkedIn: false },
    	{ stt: 27, name: "Đặng Thị Vân Trang", phone: "0799646898", checkedIn: false },
    	{ stt: 28, name: "Huỳnh Thị Thanh Thuỷ", phone: "0909105604", checkedIn: false },
    	{ stt: 29, name: "Nguyễn Xuân Mai", phone: "0909016607", checkedIn: false },
    	{ stt: 30, name: "Hà Ngọc Trường Quyên", phone: "0962050739", checkedIn: false },
    	{ stt: 31, name: "Hà Ngọc Bảo Trâm", phone: "0869090354", checkedIn: false },
    	{ stt: 32, name: "Hiệu Đẹp", phone: "0904552027", checkedIn: false },
    	{ stt: 33, name: "Nguyễn Hùng Kiệt", phone: "0968552080", checkedIn: false },
    	{ stt: 34, name: "Nguyễn Ngọc Thế", phone: "0939140709", checkedIn: false },
    	{ stt: 35, name: "Vũ Đào Hằng", phone: "0948220672", checkedIn: false },
    	{ stt: 36, name: "Ngọc Anh (R&R)", phone: "0777777787", checkedIn: false },
    	{ stt: 37, name: "Nguyễn Lê Hạnh Trang", phone: "0909870047", checkedIn: false },
    	{ stt: 38, name: "Trần Thị Thanh Hương", phone: "0908751810", checkedIn: false },
    	{ stt: 39, name: "Nguyễn Thị Minh Thi", phone: "0943283950", checkedIn: false },
    	{ stt: 40, name: "Nguyễn Huỳnh Dung", phone: "0943398231", checkedIn: false },
    	{ stt: 41, name: "Dương Thị Ngọc Thanh", phone: "0888116126", checkedIn: false },
    	{ stt: 42, name: "Thái Minh Cảnh", phone: "0918121293", checkedIn: false },
    	{ stt: 43, name: "Khấu Kim Khánh", phone: "0369874990", checkedIn: false },
    	{ stt: 44, name: "Đào Thu Trang", phone: "0989088623", checkedIn: false },
    	{ stt: 45, name: "Trần Quỳnh Duyên", phone: "0941434880", checkedIn: false },
    	{ stt: 46, name: "Nguyễn Ngọc Yên Nghi", phone: "0364788957", checkedIn: false },
    	{ stt: 47, name: "Trần Thị Mỹ Duyên", phone: "0938181590", checkedIn: false },
    	{ stt: 48, name: "Trương Huỳnh Duy", phone: "0944442546", checkedIn: false },
    	{ stt: 49, name: "Trần Chí Quang", phone: "0919900083", checkedIn: false },
    	{ stt: 50, name: "Vie Nguyen", phone: "0792272772", checkedIn: false },
    	{ stt: 51, name: "Nguyễn Thành Nhơn", phone: "0929650678", checkedIn: false },
    	{ stt: 52, name: "Nguyễn Thị Thanh Huyền/ Chị Hân (Viso)", phone: "0374317342", checkedIn: false },
    	{ stt: 53, name: "Thùy Linh", phone: "0936340819", checkedIn: false },
    	{ stt: 54, name: "Anh Toàn", phone: "0796890000", checkedIn: false },
    	{ stt: 55, name: "Lê Bích", phone: "0981718843", checkedIn: false },
    	{ stt: 56, name: "Đỗ Thị Mai Trang", phone: "0326691598", checkedIn: false },
    	{ stt: 57, name: "Lục Ánh Hồng", phone: "0333743785", checkedIn: false },
    	{ stt: 58, name: "Nguyễn Bảo Hoà", phone: "0938687983", checkedIn: false },
    	{ stt: 59, name: "Ho Thi Tuyet Trinh", phone: "0905360275", checkedIn: false },
    	{ stt: 60, name: "Trần Diễm Trinh", phone: "0388762239", checkedIn: false },
    	{ stt: 61, name: "Lê Thị Hoàng Oanh", phone: "0939205468", checkedIn: false },
    	{ stt: 62, name: "Thái Trúc Duy", phone: "0901060466", checkedIn: false },
    	{ stt: 63, name: "Nguyễn Thị Bích Phương", phone: "0912565972", checkedIn: false },
    	{ stt: 64, name: "Nguyễn Công Nam", phone: "0888903424", checkedIn: false },
    	{ stt: 65, name: "Lê Thị Mộng Liên", phone: "0962499576", checkedIn: false },
    	{ stt: 66, name: "Trung Anh", phone: "0977053619", checkedIn: false },
    	{ stt: 67, name: "Lê Hoàng Thịnh", phone: "0849946346", checkedIn: false },
    	{ stt: 68, name: "Huỳnh Thị Kiều Uyên", phone: "0972344523", checkedIn: false },
    	{ stt: 69, name: "Lê Thị Mỹ Linh", phone: "0905258755", checkedIn: false },
    	{ stt: 70, name: "Nguyễn Thị Thanh Thúy", phone: "0333743785", checkedIn: false },
    	{ stt: 71, name: "Thừa Thị Bích Vân", phone: "0937985848", checkedIn: false },
    	{ stt: 72, name: "Nhan Huỳnh Đại Định", phone: "0798639797", checkedIn: false },
    	{ stt: 73, name: "Lê Thảo", phone: "0901669996", checkedIn: false },
    	{ stt: 74, name: "A Thị Kim Khánh", phone: "0971866868", checkedIn: false },
    	{ stt: 75, name: "Trần Thị Bích Tuyền", phone: "0931777449", checkedIn: false },
    	{ stt: 76, name: "Trương Thị Ngọc Bửu", phone: "0905506999", checkedIn: false },
    	{ stt: 77, name: "Trương Thị Thái Bình", phone: "0777423777", checkedIn: false },
    	{ stt: 78, name: "Tran Phu Trung", phone: "0989011295", checkedIn: false },
    	{ stt: 79, name: "Han Beauty", phone: "", checkedIn: false },
    	{ stt: 80, name: "Phương Hoàng", phone: "0969131550", checkedIn: false },
    	{ stt: 81, name: "Lê Phương Du ( Hebe)", phone: "0985998888", checkedIn: false },
    	{ stt: 82, name: "Nguyễn Thị Nhật Linh", phone: "0848281400", checkedIn: false },
    	{ stt: 83, name: "Huỳnh Thị Thanh Thuỷ", phone: "0909105604", checkedIn: false }
    ];

    const form = document.getElementById('checkin-form');
    const nameInput = document.getElementById('nameInput');
    const phoneInput = document.getElementById('phoneInput');
    const resultDiv = document.getElementById('result');
    const infoDiv = document.getElementById('info');

    form.addEventListener('submit', function(event) {
        event.preventDefault(); 
        checkIn();
    });

function checkIn() {
        // SỬA LỖI Ở ĐÂY: Lấy giá trị từ đúng ô input tương ứng
        const nameQuery = nameInput.value.trim().toLowerCase();
        const phoneQuery = phoneInput.value.trim();

        resultDiv.innerHTML = '';
        infoDiv.innerHTML = '';
        
        if (nameQuery === '' && phoneQuery === '') {
            infoDiv.innerText = 'Vui lòng nhập SĐT hoặc Tên để kiểm tra.';
            return;
        }

        let foundGuest = null;

        if (phoneQuery !== '') {
            // Ưu tiên tìm bằng SĐT trước
            foundGuest = guestList.find(guest => guest.phone === phoneQuery);
        } else if (nameQuery !== '') { // Thêm điều kiện kiểm tra nameQuery
            // Nếu không nhập SĐT, tìm bằng tên
            foundGuest = guestList.find(guest => guest.name.toLowerCase().includes(nameQuery));
        }

        if (foundGuest) {
            if (foundGuest.checkedIn) {
                resultDiv.innerHTML = '<span class="warning">!</span>';
                infoDiv.innerHTML = `<b>STT ${foundGuest.stt}: ${foundGuest.name}</b><br>Đã check-in trước đó.`;
            } else {
                foundGuest.checkedIn = true; 
                resultDiv.innerHTML = '<img src="https://sf-static.upanhlaylink.com/img/image_202510020dee8dca441a0402f1e1c0e455ffb9b9.jpg" alt="Thành công">';
                infoDiv.innerHTML = `Check-in thành công!<br><b>STT ${foundGuest.stt}: ${foundGuest.name}</b>`;
            }
        } else {
            resultDiv.innerHTML = '<img src="https://sf-static.upanhlaylink.com/img/image_20251002dd822ef8646d57328513357d74a2b505.jpg" alt="Thất bại">';
            infoDiv.innerText = 'Không tìm thấy thông tin khách mời.';
        }

        // Tự động reset form và focus vào ô nhập tên
        form.reset();
        nameInput.focus(); 

        // Tự động xóa kết quả sau 60 giây 
        setTimeout(() => {
            resultDiv.innerHTML = '';
            infoDiv.innerHTML = '';
        }, 60000); // 60000 mili-giây = 60 giây
    }
    </script>

</body>
</html>
