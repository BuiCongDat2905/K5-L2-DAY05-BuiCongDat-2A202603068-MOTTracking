# Chuẩn bị dữ liệu MOT17-09 và CVAT trên máy cá nhân

## 1. Mở CVAT trên máy

Mở `http://localhost:8080` trong trình duyệt và đăng nhập bằng tài khoản CVAT trên máy của bạn. Nếu CVAT đã được cấu hình ở cổng khác, mở địa chỉ bạn đang dùng.

Nếu chưa cài, làm theo [hướng dẫn cài CVAT Community cho hệ điều hành của bạn](https://docs.cvat.ai/docs/administration/community/basics/installation/). Với Windows, xem phần Windows/WSL2. Sau khi cài Docker, khởi động CVAT và tạo tài khoản theo hướng dẫn đó.

## 2. Tải 150 ảnh MOT17-09

Bạn cần đúng các ảnh `000001.jpg` đến `000150.jpg` (1920×1080) từ thư mục `train/MOT17-09/img1/` trong gói [MOT17Det.zip của MOTChallenge](https://motchallenge.net/data/MOT17Det.zip). Lưu ảnh và ZIP ở ngoài repo bài nộp.

**Tải riêng 150 ảnh (khoảng 30 MB ảnh).** Mở Terminal hoặc PowerShell trong thư mục Downloads. Chạy hai lệnh sau. Trên macOS/Linux, dùng `python3` nếu máy dùng tên lệnh đó. Trên Windows, có thể dùng `py`.

```bash
python -m pip install "fsspec[http]"
python -c "
from zipfile import ZipFile
import fsspec

url = 'https://motchallenge.net/data/MOT17Det.zip'
with fsspec.open(url, 'rb') as download, ZipFile(download) as source:
    with ZipFile('MOT17-09-150.zip', 'w') as output:
        for frame in range(1, 151):
            name = '%06d.jpg' % frame
            output.writestr(name, source.read('train/MOT17-09/img1/' + name))
print('Da tao MOT17-09-150.zip')
"
```

Mở `MOT17-09-150.zip` để kiểm tra có đủ 150 ảnh, rồi giải nén vào một thư mục riêng.

**Tải bằng trình duyệt.** Nếu lệnh trên gặp lỗi, tải [MOT17Det.zip](https://motchallenge.net/data/MOT17Det.zip) (khoảng 2 GB). Mở ZIP, vào `train/MOT17-09/img1/`, sắp xếp theo tên tăng dần và giải nén đúng `000001.jpg` đến `000150.jpg` vào một thư mục riêng.

## 3. Tạo task và kiểm tra job

1. Trong CVAT local, vào **Tasks → + → Create new task**. Đặt tên, ví dụ `DAY05-HoVaTen-MOT17-09`.
2. Chọn **Add label**, đặt tên nhãn là `person` và lưu nhãn.
3. Trong **My computer → Select files**, chọn đủ 150 file JPG đã giải nén. Chỉ chọn ảnh để task bắt đầu với nhãn trống.
4. Trong **Advanced configuration**, chọn **Sorting method → Lexicographical** và đặt **Segment size = 150**. Để trống **Start frame**, **Stop frame** và **Frame step**. Bấm **Submit & Open**.
5. Mở job duy nhất của task. Kiểm tra job chạy từ frame **0 đến 149**. CVAT frame **0** ứng với `000001.jpg`, frame **149** ứng với `000150.jpg`.
6. Dùng bảng giao việc của coach để xác định 5 người `P1`–`P5`, rồi bắt đầu [CP0](../CHECKPOINTS.md).

Nếu sai số frame, tạo task mới với đúng 150 ảnh trước khi gán. Nếu thiếu nhãn `person`, thêm nhãn ở trang task. Khi làm xong, xuất hai file theo [hướng dẫn nộp bài](../SUBMISSION.md) và bỏ chọn **Save images**.
