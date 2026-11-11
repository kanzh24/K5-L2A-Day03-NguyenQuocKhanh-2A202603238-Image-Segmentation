# Báo cáo thực hành Day 3 — điền trực tiếp trong fork của bạn

- Mã học viên theo lớp: 2A202603238
- Ngày / CVAT local: 12/11/2026 / CVAT local
- Công cụ đã dùng: SAM 3  + CVAT SDK

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh LMS đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh `000000181542.jpg`, chiếc xe ô tô (car) đỗ bên lề đường ở góc trái tiền cảnh.
- Class và quy tắc tôi dùng để chọn biên: Class `car`. Quy tắc chọn biên: bám sát ranh giới pixel nhìn thấy của thân xe và bánh xe tiếp giáp mặt đường; giữ nguyên phần kính chắn gió và kính cửa xe bên trong mask (không khoét lỗ); dừng biên tại mép bị che bởi người đi bộ phía trước mà không tự suy đoán phần sau bị che.
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Đề xuất SAM 3 nhận diện chính xác hình dáng xe và bánh xe; ban đầu vùng chân lốp xe có xu hướng lan nhẹ vào bóng đổ trên mặt đường (shadow); hành động sửa: cắt tỉa mask sát chân lốp xe loại bỏ bóng mặt đường để tuân thủ quy tắc chỉ gán mask cho pixel thuộc vật thể thật.
- Nếu không dùng gợi ý: (Đã dùng gợi ý AI SAM 3 sau khi tự gán nhãn object đầu tiên).

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: Task `cp2_slice`, ảnh `000000017627.jpg`, khu vực hai xe ô tô (car) đỗ sát nhau song song.
- Lỗi thuộc loại: gộp-tách (Gộp hai xe cùng class sát nhau thành một instance).
- Bằng chứng tôi nhìn thấy: SAM 3 ở ngưỡng mặc định tạo ra một polygon bao trùm cả hai xe sát nhau do khoảng cách sườn xe rất hẹp.
- Quy tắc và hành động sửa: Quy tắc: Cùng class không đồng nghĩa cùng instance; hai vật thể cá thể phải có hai ID/polygon riêng biệt. Hành động: Áp dụng thuật toán Mask NMS với ngưỡng IoU 0.55 và hàm resolve_instance_overlaps để tách đường phân chia giữa hai xe theo khe hở sườn xe, tạo thành 2 polygon riêng biệt không chồng lấn.
- Sau sửa đã Save và export lại chưa? Đã Save và export lại thành `submissions/cp2_slice.zip`, kiểm tra hợp lệ với 20 polygon instances.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `cp4_curb` (`7d83710e-4697c3b2.jpg`) | (A) Tô toàn bộ là road theo màu sắc bề mặt. (B) Phân tách theo chức năng và vệt bó vỉa (curb). | Hướng dẫn nhấn mạnh: Không phân chia class chỉ theo màu sắc; ranh giới road-sidewalk là ranh giới chức năng. | Vẽ theo đường viền gờ mép bó vỉa để phân định vỉa hè dành cho người đi bộ và lòng đường cho phương tiện giao thông. |
| 2. `cp1_holes` (`000000144300.jpg`) | (A) Khoét rỗng phần kính vì nhìn thấy nền phía sau. (B) Giữ nguyên vùng kính bên trong mask xe. | Quy tắc task: Kính, cửa sổ, khe hở thuộc cấu trúc vật thể không được khoét rỗng. | Dùng `fill_mask_holes` để giữ toàn bộ vùng kính xe nằm trọn vẹn trong polygon của xe, không khoét thủng. |
| 3. `cp5_occlusion` (`000000336232.jpg`) | (A) Tách thành 2 object khác nhau với 2 ID riêng. (B) Giữ nguyên là 1 instance có đa polygon (multi-polygon). | Quy tắc task: Không vẽ phần bị che, nhưng các mảng nhìn thấy của cùng một vật thể vẫn thuộc về một instance duy nhất. | Gom các mảnh nhìn thấy vào chung một annotation ID theo chuẩn COCO đa polygon. |

