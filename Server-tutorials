# Hướng dẫn sử dụng Server AI Lab

> **TL;DR — 10 phút khởi động nhanh**
>
> 1. **Ở đúng mạng lab** (LAN/VPN nội bộ) → mới SSH được.
> 2. SSH: `ssh <username>@<server-ip>` (password **hoặc** SSH key).
> 3. Dùng dữ liệu chung ở: `/data`, mô hình ở: `/models` (đừng copy vào home → dùng **symlink**).
> 4. Bật Conda khi cần: `use_conda` → `conda activate <env>`.
> 5. Cài lib nhanh: `uv pip install <pkg>` (trong env đang active).
> 6. Docker (GPU): `docker run --gpus all -it -v /data:/data -v /models:/models <image> bash`.
> 7. Job dài → luôn chạy trong `screen`/`tmux`.
> 8. **Không xoá/ghi đè** dữ liệu người khác trong `/data` & `/models`.

---

## 1) Giới thiệu & vai trò người dùng

* **Mục đích:** huấn luyện mô hình, thử nghiệm pipeline ML/DL, đóng gói & tái lập môi trường.
* **Điều kiện truy cập:** phải ở **cùng dải mạng nội bộ của lab** (LAN) hoặc **VPN nội bộ của lab**. Không mở SSH ra Internet công cộng.
* **Nhóm người dùng:**

  * `lab_teachers` (Thầy/Cô): có **sudo** cho các tác vụ quản trị.
  * `lab_members` (Thành viên): toàn quyền thực thi trong dự án, truy cập dữ liệu chung.
  * `lab_collab` (Cộng tác viên): quyền tối thiểu, truy cập dữ liệu/mô hình được phép.

> **Lưu ý:** Mọi tài khoản đều nằm trong group chia sẻ `labshared` để dùng `/data` và `/models`.

---

## 2) Cách đăng nhập & thao tác cơ bản (SSH/Terminal)

### 2.1 Kết nối đúng mạng

* **Tại lab:** kết nối Wi-Fi/LAN nội bộ → SSH được ngay.
* **Từ xa:** kết nối **VPN của lab** → nhận IP nội bộ → SSH.

### 2.2 Đăng nhập SSH

* **Password:**

  ```bash
  ssh <username>@<server-ip>
  ```
* **SSH key:**

  ```bash
  ssh -i ~/.ssh/<your_key> <username>@<server-ip>
  ```
* **Đổi mật khẩu (khi đang là user đó hoặc root):**

  ```bash
  passwd        # đổi của chính bạn
  sudo passwd <username>  # root đổi cho user khác
  ```

### 2.3 Upload/Download nhanh

```bash
# Upload
scp local_file.txt <username>@<server-ip>:/data/<project>/

# Download
scp <username>@<server-ip>:/models/<project>/best.pt .
```

### 2.4 Câu lệnh Linux tối thiểu

```bash
ls -lh        # liệt kê
cd /data      # chuyển thư mục
mkdir mydir   # tạo thư mục
rm -rf path   # xoá cẩn thận
du -sh *      # xem dung lượng
```

> **Khuyến cáo:** tránh chạy lệnh xoá (`rm -rf`) trong `/data` hoặc `/models` nếu không chắc 100%.

---

## 3) Cấu trúc thư mục & quy tắc dữ liệu

### 3.1 Thư mục chia sẻ (mọi người cùng dùng)

* **Dữ liệu:** `/data` → chứa **dataset** (train/val/test).
* **Mô hình:** `/models` → chứa **checkpoints**, **pretrained**, **results**.

**Quy ước đặt tên (rõ ràng, có version/date):**

```
/data/<project>/<dataset_version>/
/models/<project>/<model_version>/
```

Ví dụ:

```
/data/retail-detection/v1_2025-08
/models/retail-detection/yolo11x_2025-08-10
```

**Dùng symlink thay vì copy:**

```bash
ln -s /data/retail-detection/v1_2025-08 ~/retail_data
ln -s /models/retail-detection/yolo11x_2025-08 ~/retail_ckpt
```

> **Không** copy dữ liệu từ `/data` vào `~/` (home) → tránh **trùng lặp** và cạn đĩa.

### 3.2 Thư mục cá nhân

* `~/` (home của bạn): code, notebook, file tạm (nhỏ/gọn).
* Không bắt buộc backup — chủ động tự sao lưu phần quan trọng.

> **Quyền thư mục chia sẻ:** đã bật **setgid** & sticky bit → file mới luôn thuộc group `labshared`, hạn chế xoá nhầm file của người khác.

---

## 4) Môi trường GPU & kiểm tra tài nguyên

### 4.1 NVIDIA & CUDA

* Driver + CUDA đã cài; Docker có **nvidia-container-toolkit**.
* Kiểm tra GPU:

  ```bash
  nvidia-smi
  watch -n 2 nvidia-smi   # theo dõi real-time
  ```
* Chọn GPU cụ thể khi chạy Python:

  ```bash
  CUDA_VISIBLE_DEVICES=0 python train.py
  ```

> **Lưu ý:** không chiếm hết tất cả GPU nếu không cần; nếu chạy lớn, lên lịch & thông báo nhóm.

---

## 5) Quản lý môi trường Python: **Conda** & **uv**

### 5.1 Conda (cài chung tại `/opt/conda`, **không auto-activate**)

* **Bật conda khi cần:**

  ```bash
  use_conda
  conda activate <env-name>
  ```

  > Nếu `use_conda` báo không tìm thấy, báo quản trị viên.

* **Tạo môi trường riêng cho dự án:**

  ```bash
  conda create -n proj310 python=3.10
  conda activate proj310
  ```

* **Cài gói vào env đang active:**

  ```bash
  pip install -U numpy pandas
  # hoặc
  conda install -c conda-forge pytorch torchvision torchaudio
  ```

* **Xuất/khôi phục môi trường:**

  ```bash
  conda env export > environment.yml
  conda env create -f environment.yml
  ```

> **Quy ước:** không cài gói nặng vào **env chung** (nếu có). Mỗi dự án nên có **env riêng**.

### 5.2 uv (cài gói Python **nhanh**)

* Dùng như pip, chạy trong env Conda đang active:

  ```bash
  use_conda
  conda activate proj310
  uv pip install -r requirements.txt
  uv pip install lightning rich
  ```
* Có thể dùng **venv nhẹ** nếu không cần Conda (dự án thuần Python):

  ```bash
  python -m venv .venv
  source .venv/bin/activate
  uv pip install -r requirements.txt
  ```

> **Khi nào dùng gì?**
>
> * **Conda**: quản lý phiên bản Python, CUDA/BLAS, gói có native deps (PyTorch, OpenCV…).
> * **uv**: tăng tốc cài **pip** trong env đã mở (conda/venv).

---

## 6) Docker & Docker Compose (đóng gói/tái lập môi trường)

### 6.1 Chạy container GPU nhanh

```bash
docker pull nvidia/cuda:12.3.2-cudnn-runtime-ubuntu22.04

docker run --gpus all -it --rm \
  -v /data:/data \
  -v /models:/models \
  -v $PWD:/workspace \
  -w /workspace \
  --user $(id -u):$(id -g) \
  nvidia/cuda:12.3.2-cudnn-runtime-ubuntu22.04 bash
```

> **Giải thích nhanh:**
> `--gpus all` bật GPU; `-v` mount thư mục chung; `--user` tránh file root-owned.

### 6.2 Dockerfile mẫu (Python + PyTorch)

```dockerfile
# Dockerfile
FROM nvidia/cuda:12.3.2-cudnn-runtime-ubuntu22.04

RUN apt-get update && apt-get install -y \
    python3 python3-pip git && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

# Cài deps (ví dụ)
COPY requirements.txt .
RUN pip3 install --no-cache-dir -r requirements.txt

COPY . .

CMD ["bash"]
```

### 6.3 docker-compose.yml (GPU + volumes)

> **Lưu ý:** hỗ trợ GPU trong Compose phụ thuộc phiên bản Docker/Compose. Nếu file dưới không nhận GPU, hãy dùng `docker run --gpus ...` như mục 6.1 hoặc nâng cấp Compose.

```yaml
# docker-compose.yml
services:
  app:
    build: .
    volumes:
      - ./:/workspace
      - /data:/data
      - /models:/models
    working_dir: /workspace
    tty: true
    stdin_open: true
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: ["gpu"]
```

* Chạy:

  ```bash
  docker compose up -d --build
  docker compose exec app bash
  ```
* Nếu GPU **không** được nhận trong compose của bạn → chạy theo **6.1**.

> **Quy tắc Docker:**
>
> * Luôn mount `/data` & `/models` thay vì copy.
> * Dùng `--user $(id -u):$(id -g)` để file sinh ra thuộc về bạn.
> * Không push image chứa dữ liệu nhạy cảm.

---

## 7) Chạy job dài, không sợ rớt SSH

### 7.1 `screen`

```bash
screen -S train_exp1
# (chạy lệnh training)
python train.py --cfg config.yaml
# tách phiên: Ctrl + A, sau đó nhấn D
# quay lại:
screen -r train_exp1
```

### 7.2 `tmux` (nếu quen)

```bash
tmux new -s train_exp1
# detach: Ctrl + B, sau đó D
tmux attach -t train_exp1
```

> **Bắt buộc:** job dài phải chạy trong screen/tmux để tránh SSH rớt là dừng job.

---

## 8) Quy định sử dụng tài nguyên & an toàn dữ liệu

* **GPU/CPU/RAM**: chia sẻ hợp lý; xin phép khi cần chạy **full máy**.
* **Dữ liệu dùng chung**:

  * Không xoá/ghi đè file người khác.
  * Đặt tên thư mục có `project`/`version`/`date`.
  * Sử dụng **symlink** để tái dùng dữ liệu.
* **Môi trường**: mỗi dự án 1 env riêng; không phá env chung.
* **Docker**: không lưu dữ liệu thật *bên trong* image/container; luôn mount.
* **Bảo mật**: tuyệt đối không chia sẻ tài khoản; đổi mật khẩu khi nghi ngờ.
* **Dọn dẹp**: xoá file tạm sau khi xong; định kỳ làm sạch `~/Downloads`, `~/tmp`.

---

## 9) Sự cố thường gặp & cách xử lý nhanh

* **SSH được bằng key nhưng “đổi password không tác dụng”**
  → SSH đang **tắt mật khẩu**. Dùng key hoặc liên hệ quản trị viên bật tạm.

* **`conda: command not found`**
  → Chưa `use_conda`. Chạy:

  ```bash
  use_conda
  ```

* **Container ghi file bị quyền root**
  → Thêm `--user $(id -u):$(id -g)` khi `docker run`.

* **GPU không xuất hiện trong container**
  → Kiểm tra `nvidia-smi` trên host; kiểm tra `nvidia-container-toolkit`; hoặc dùng `docker run --gpus all ...` thay vì compose.

* **Thiếu dung lượng**
  → Dọn thư mục tạm ở `~/`, xoá checkpoint cũ ở `/models/<project>/old_*`, bàn với nhóm trước khi xoá lớn.

---

## 10) Phụ lục – Cheatsheet nhanh

### 10.1 SSH / SCP / RSYNC

```bash
ssh <u>@<ip>                    # SSH
scp a.txt <u>@<ip>:/data/p/     # Upload
rsync -avP data/ <u>@<ip>:/data/p/v2/   # Đồng bộ (khuyến nghị cho thư mục lớn)
```

### 10.2 Conda

```bash
use_conda
conda create -n proj310 python=3.10
conda activate proj310
pip install -r requirements.txt
conda env export > environment.yml
conda env create -f environment.yml
```

### 10.3 uv

```bash
uv pip install -r requirements.txt
uv pip install "torch==2.3.*" "torchvision==0.18.*"
```

### 10.4 Docker

```bash
docker build -t my_img .
docker run --gpus all -it --rm -v /data:/data -v /models:/models my_img bash
docker compose up -d --build
docker compose exec app bash
```

---

## 11) Kênh hỗ trợ & liên hệ

* Báo lỗi/sự cố: ghi lại **lệnh đã chạy + thông báo lỗi** → gửi quản trị.
* Liên hệ: cngvng123@gmail.com.
