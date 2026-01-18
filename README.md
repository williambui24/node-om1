# Triển khai Robot trên OpenMind Fabric Linux
Hướng dẫn này giúp chạy node openmind với chi phí thấp nhất (có thể). Triển khai 1 node OpenMind OM1 trên Google Cloud VM (GCE), viết theo kiểu step by step, phù hợp với người mới. Sử dụng Ubuntu 22.04 LTS.
Mục tiêu: Node chạy ổn định, không lỗi PEP 668, không lỗi audio, không lỗi git
## 1- TẠO VM TRÊN GOOGLE CLOUD
- Truy cập https://console.cloud.google.com
- Chọn Select a project → New Project
- Đặt tên (vd: openmind-om1) rồi bấm Create
- Tạo VM Instance: Vào Compute Engine → VM instances rồi Bấm Create Instance
## 2- CHUẨN BỊ MÔI TRƯỜNG HỆ THỐNG ĐỂ CHẠY NODE OM1
## 2.1- Update & nâng cấp hệ thống
```bash
sudo apt update -y
sudo apt upgrade -y
sudo reboot
```
➡ SSH lại sau reboot
## 2.2- Cài các gói hệ thống bắt buộc
```bash
sudo apt install -y \
git \
curl \
build-essential \
python3 \
python3-pip \
python3-venv \
python3-all-dev \
ffmpeg \
portaudio19-dev \
alsa-utils
```
## 2.3- Load dummy audio (VM không có sound card)
```bash
sudo modprobe snd-dummy
```
👉 Thấy Dummy là OK. Nếu không thì chuyển sang 2.4.
## 2.4- Dùng ALSA NULL
👉 Phù hợp Cloud / VM / Robot headless
👉 OM1 vẫn chạy BÌNH THƯỜNG

1️⃣ Tạo cấu hình ALSA null
```bash
nano ~/.asoundrc
```
Dán nội dung sau:
```bash
pcm.!default {
    type null
}
ctl.!default {
    type null
}
```
Lưu lại.

2️⃣ Kiểm tra
```bash
aplay -l
```
👉 Có thể vẫn báo no soundcards found
⚠️ KHÔNG SAO – đây là bình thường

Test:
```bash
aplay /usr/share/sounds/alsa/Front_Center.wav
```
➡ Không báo lỗi = OK

3️⃣ Export biến môi trường cho OM1
```bash
export ALSA_PCM_CARD=0
export ALSA_CTL_CARD=0
```

➡ OM1 sẽ không cần sound card thật

## 2.5- Tránh lỗi PEP 668
1️⃣ Tạo virtual environment cho OM1
```bash
python3 -m venv om1-env
```
Kích hoạt:
```bash
source om1-env/bin/activate
```

Kiểm tra:
```bash
which python
```

➡ Phải trỏ tới om1-env/bin/python

2️⃣ Nâng pip trong venv
```bash
pip install --upgrade pip setuptools wheel
```
## 3- TRIỂN KHAI OPENMIND OM1
1️⃣ Clone source OM1
```bash
git clone https://github.com/openmind/OM1.git
cd OM1
```
2️⃣ Thiết lập UV, Git và biến môi trường
```bash
pip install uv
git submodule update --init
uv venv
```
➡ còn tiếp >>>>
