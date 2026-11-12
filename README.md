# Lab Ngày 5 — Gán track ID trên 150 frame bằng keyframe và nội suy

> **Loại bài: CÁ NHÂN.** Mỗi học viên tự tạo một task trên CVAT chạy trên máy cá nhân, tự review và tự nộp một repo riêng.

Bạn theo dõi **5 người đi bộ** trên **150 frame liên tiếp** (5 giây, 30 fps) của chuỗi MOT17-09 bằng **track mode** trong CVAT. Bạn đặt keyframe ở chỗ nội suy tuyến tính sai, xử lý một đoạn bị che bằng cờ `occluded` / `outside`, tự review ID hai lượt và xuất file **MOT 1.1**.

Nhãn này chính là loại nhãn mà tracker (SORT, DeepSORT, ByteTrack…) dùng để học ReID và để được chấm bằng MOTA, IDF1. Sai một ID là sai ở cả hai chỗ.

## Mục tiêu học tập

Sau bài lab, bạn có thể:

1. Gán 5 người trên 150 frame bằng **track mode**, keyframe và nội suy tuyến tính, mỗi người **một track ID** suốt đoạn thấy được.
2. Đặt keyframe đúng chỗ: đổi hướng, đổi tốc độ, đổi kích thước, quanh đoạn bị che. Giải thích được vì sao nội suy sai ở đó.
3. Dùng đúng cờ `occluded` và `outside`; quyết định **cùng ID hay ID mới** theo ngưỡng 2 giây của lớp.
4. Tự soát lỗi ID và gọi đúng tên: `id_switch`, `fragmentation`, `lost`, `merge`, `false_track`, `drift`, `flag`.
5. Xuất file MOT 1.1, đọc được từng cột, và giải thích MOTA khác IDF1 ở đâu.

## Thông tin nhanh

| Mục | Giá trị |
|---|---|
| Loại bài | Cá nhân |
| Thời lượng | 240 phút (195′ làm bài + 15′ nghỉ + 30′ buffer) |
| Dữ liệu | MOT17-09, frame 1–150, 1920×1080, 30 fps ([cách tải 150 ảnh](docs/CVAT_LOCAL_SETUP.md#2-tải-150-ảnh-mot17-09)) |
| Công cụ | CVAT Community chạy trên máy cá nhân, Python 3.9+ để chạy script tự kiểm (`src/check_submission.py`, không cần cài thư viện) |
| Deadline | **23:59 GMT+7 ngày làm lab**, trừ khi coach thông báo khác trong 48h sau lab. Xem `SUBMISSION.md` |
| Điểm | 100 điểm chính + tối đa 10 điểm bonus. Xem `RUBRIC.md` |

## Chuẩn bị trước buổi

- [ ] Tải 150 ảnh, mở CVAT trên máy và tạo task có một job từ frame 0 đến 149 theo [hướng dẫn CVAT local](docs/CVAT_LOCAL_SETUP.md).
- [ ] Đã xem slide lý thuyết Ngày 5, nhất là phần keyframe, `occluded` / `outside`, năm lỗi ID, MOTA / IDF1.
- [ ] Ôn phím `N` `F` `D` `V` `C` `K` `O` `Q` `M` `Ctrl+S` (bảng ở `docs/CVAT_CHEATSHEET.md`).
- [ ] Máy có Python 3.9+ (`python --version`).
- [ ] Nhận từ coach: **bảng giao việc** (`P1`–`P5`, frame xuất hiện đầu, ảnh khoanh vùng từng người).

## Cách bắt đầu

1. Tạo repo bài nộp từ repo này, đặt tên theo mẫu (chi tiết ở `SUBMISSION.md`):

   ```text
   K5-L2-DAY05-HoVaTen-MSSV-MOTTracking
   ```

2. Đổi tên file báo cáo `report/individual/MSSV_HoTen.md` thành MSSV và họ tên của bạn, ví dụ `report/individual/20241234_NguyenVanAn.md`.
3. Đọc theo thứ tự: `RULES.md` → `docs/GUIDELINE.md` → `CHECKPOINTS.md` → `src/README.md` → `RUBRIC.md` → `SUBMISSION.md`.
4. Mở task vừa tạo trên CVAT máy cá nhân, làm lần lượt CP0 → CP4 trong [CHECKPOINTS.md](CHECKPOINTS.md).

## Lịch buổi thực hành

Mốc tính bằng phút kể từ lúc bắt đầu buổi thực hành (phút 0), áp dụng cho cả lab sáng lẫn lab chiều.

| Mốc phút | Thời lượng | Nội dung | Checkpoint |
|---|---:|---|---|
| 0–5 | 5′ | Chữa exit ticket | |
| 5–20 | 15′ | Luật, dữ liệu, quy ước nhãn; mở job, cấu hình player | CP0 |
| 20–45 | 25′ | Coach demo track mode trên một người mẫu, bạn làm theo | CP0 |
| 45–100 | 55′ | Gán 5 track trên 150 frame. **Mốc phút 100: xong ít nhất 4 người** | CP1 |
| 100–115 | 15′ | Nghỉ (bấm `Ctrl+S` trước khi nghỉ) | |
| 115–140 | 25′ | Đoạn bị che | CP2 |
| 140–170 | 30′ | Tự review ID hai lượt | CP3 |
| 170–195 | 25′ | Xuất file, chạy script tự kiểm, viết báo cáo | CP4 |
| 195–225 | 30′ | Buffer: làm bù hoặc làm bonus | Bonus |
| 225–240 | 15′ | Nộp bài, tổng kết | |

## Cấu trúc repo

```text
K5-L2-DAY05-HoVaTen-MSSV-MOTTracking/
├── README.md            # trang này
├── RULES.md             # luật làm bài, AI, dữ liệu, nộp muộn
├── CHECKPOINTS.md       # CP0–CP4: làm gì, nộp gì, tự kiểm thế nào
├── RUBRIC.md            # cách chấm 100 điểm + bonus
├── SUBMISSION.md        # tên repo, file phải nộp, deadline, checklist
├── docs/
│   ├── GUIDELINE.md     # quy ước nhãn của lớp (box toàn thân, ngưỡng 2 giây…)
│   ├── CVAT_LOCAL_SETUP.md  # tải dữ liệu và tạo task trên máy cá nhân
│   ├── CVAT_CHEATSHEET.md
│   └── MOT_FORMAT.md    # đọc file MOT 1.1, MOTA, IDF1
├── src/
│   ├── README.md
│   ├── check_submission.py   # script tự kiểm file xuất
│   └── exports/              # BẠN ĐẶT 2 FILE XUẤT TỪ CVAT VÀO ĐÂY
└── report/individual/
    ├── MSSV_HoTen.md         # ĐỔI TÊN rồi điền báo cáo
    └── images/               # ảnh chụp màn hình cho báo cáo
```

## Dữ liệu và giấy phép

MOT17 (MOTChallenge, Milan et al. 2016), giấy phép **CC BY-NC-SA 3.0**. Chỉ dùng trong lớp. **Không commit ảnh, video hay `gt.txt` gốc của MOT17 vào repo.** Khi xuất từ CVAT, bỏ chọn "Save images". Ảnh chụp màn hình trong báo cáo ghi nguồn: "MOT17, MOTChallenge (Milan et al., 2016)".
