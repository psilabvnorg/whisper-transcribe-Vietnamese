# TRANSCRIBE-AUDIO-TO-TEXT-WHISPER

<div align="center">

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.10-blue.svg)](https://www.python.org/)
[![PhoWhisper](https://img.shields.io/badge/PhoWhisper-Vietnamese-green.svg)](https://huggingface.co/vinai/PhoWhisper)

*Chuyển đổi âm thanh thành văn bản chất lượng cao cho tiếng Việt*

</div>

---

## 📖 Giới thiệu

**TRANSCRIBE-AUDIO-TO-TEXT-WHISPER** là một hệ thống Speech-to-Text (STT) tiên tiến cho tiếng Việt, sử dụng mô hình [PhoWhisper](https://huggingface.co/vinai/PhoWhisper) được phát triển bởi VinAI Research. Hệ thống hỗ trợ chuyển đổi âm thanh thành văn bản với độ chính xác cao và timestamps chi tiết.

### ✨ Tính năng nổi bật

- 🎯 **Độ chính xác cao**: Sử dụng mô hình PhoWhisper được tối ưu cho tiếng Việt
- ⏱️ **Word-level timestamps**: Hỗ trợ timestamps chi tiết đến từng từ
- 🔤 **Khôi phục dấu câu**: Tự động thêm dấu câu vào văn bản
- 📹 **Tải video YouTube**: Tích hợp công cụ tải và xử lý video từ YouTube
- 🎬 **Xử lý video**: Điều chỉnh tốc độ video, cắt video, chuyển đổi định dạng
- 🌐 **API Server**: FastAPI để dễ dàng triển khai dịch vụ STT
- 🔧 **Scripts tiện ích**: Nhiều công cụ xử lý âm thanh và video

### 🖥️ Yêu cầu hệ thống

- **Hệ điều hành**: Ubuntu (hoặc các bản phân phối Linux khác)
- **Python**: 3.10
- **CUDA**: 12.4 (khuyến nghị cho GPU acceleration)
- **RAM**: Tối thiểu 8GB
- **GPU**: NVIDIA GPU với ít nhất 6GB VRAM (khuyến nghị cho xử lý nhanh)
- **FFmpeg**: Cần thiết cho xử lý âm thanh/video

## 📦 Các bước cài đặt

### 1. Tạo môi trường ảo
```bash
python3.10 -m venv venv
source venv/bin/activate
```

### 2. Cài đặt PyTorch
```bash
pip install torch==2.4.0+cu124 torchaudio==2.4.0+cu124 --extra-index-url https://download.pytorch.org/whl/cu124
```

### 3. Cài đặt FFmpeg
```bash
sudo apt update
sudo apt install ffmpeg
```

### 4. Cài đặt các thư viện khác
```bash
pip install -r requirements.txt
```

### 5. Tải mô hình PhoWhisper
Mô hình sẽ được tự động tải về khi chạy lần đầu tiên. Mô hình được lưu tại folder `model/`.

## 🚀 Hướng dẫn sử dụng

### 1. Chuyển đổi âm thanh thành văn bản

#### Sử dụng script

**Cách nhanh - Chạy 1 dòng:**
```bash
python scripts/transcribe_phowhisper.py "input/your_audio.wav"
```

**Cách đầy đủ với tham số:**
```bash
python scripts/transcribe_phowhisper.py \
    --audio_path input/your_audio.wav \
    --output_dir temp/transcriptions \
    --model_path model/snapshots/[model_id]
```

**Tham số:**
- `--audio_path`: Đường dẫn đến file âm thanh (WAV, MP3, MP4, ...)
- `--output_dir`: Thư mục lưu kết quả
- `--model_path`: Đường dẫn đến mô hình PhoWhisper
- `--restore_punctuation`: Khôi phục dấu câu (mặc định: True)
- `--return_timestamps`: Trả về timestamps (mặc định: True)

**Kết quả đầu ra:**
- File JSON chứa văn bản và timestamps chi tiết
- File TXT chứa văn bản thuần túy

### 2. Tải video từ YouTube

**Cách nhanh - Chạy 1 dòng:**
```bash
python scripts/download_youtube_video.py "https://youtube.com/watch?v=..."
```

**Cách đầy đủ với tham số:**
```bash
python scripts/download_youtube_video.py \
    --url "https://youtube.com/watch?v=..." \
    --output_dir temp/downloads
```

**Tính năng:**
- Tải video chất lượng cao nhất
- Tải riêng audio (MP3)
- Hỗ trợ playlists

### 3. Xử lý video

#### Điều chỉnh tốc độ video

**Cách nhanh - Chạy 1 dòng:**
```bash
python scripts/adjust_speed_video.py video.mp4 output.mp4 1.5
```

**Cách đầy đủ với tham số:**
```bash
python scripts/adjust_speed_video.py \
    --input video.mp4 \
    --output output.mp4 \
    --speed 1.5
```

#### Tổng hợp từ transcription

**Cách nhanh - Chạy 1 dòng:**
```bash
python scripts/synthesize_from_transcription.py transcription.json
```

**Cách đầy đủ với tham số:**
```bash
python scripts/synthesize_from_transcription.py \
    --transcription transcription.json \
    --output_audio output.wav
```

### 4. API Server
Folder `fast_api/` chứa script để tạo API cho server

### Cách tạo public URL dến localhost:
- Khởi động project: python fast_api/main.py (Ví dụ được url: http://localhost:8000)
- Chạy câu lệnh ở terminal khác: cloudflared tunnel --url http://localhost:8000
- Kết quả thu được 1 URL ngẫu nhiên (Ví dụ: https://liquid-gravity-epinions-pine.trycloudflare.com)
- Mỗi lần chạy lại sẽ ra 1 URL khác nhau.

#### Khởi động API Server
```bash
cd fast_api
chmod +x start.sh
./start.sh
```

API sẽ chạy tại `http://localhost:8000`

#### Sử dụng API

**Endpoint chính:** `POST /transcribe`

**Cách nhanh - Chạy 1 dòng:**
```bash
curl -F "file=@your_audio.wav" http://localhost:8000/transcribe
```

**Cách đầy đủ với headers:**
```bash
curl -X POST "http://localhost:8000/transcribe" \
    -H "Content-Type: multipart/form-data" \
    -F "file=@your_audio.wav"
```

**Response:**
```json
{
    "text": "Văn bản đã được chuyển đổi",
    "segments": [
        {
            "text": "văn bản",
            "start": 0.0,
            "end": 0.5
        }
    ],
    "duration": 10.5
}
```

**API Documentation:**
- Swagger UI: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc

#### Test API
```bash
cd fast_api
chmod +x test_api.sh
./test_api.sh
```

## 📁 Cấu trúc thư mục

```
TRANSCRIBE-AUDIO-TO-TEXT-WHISPER/
├── fast_api/              # FastAPI server
│   ├── main.py           # Main API application
│   ├── requirements.txt  # API dependencies
│   ├── start.sh          # Script khởi động server
│   └── test_api.sh       # Script test API
├── scripts/              # Các script tiện ích
│   ├── transcribe_phowhisper.py       # Script chuyển đổi âm thanh
│   ├── download_youtube_video.py      # Tải video YouTube
│   ├── adjust_speed_video.py          # Điều chỉnh tốc độ video
│   └── synthesize_from_transcription.py  # Tổng hợp từ transcription
├── input/                # Thư mục chứa file đầu vào
├── temp/                 # Thư mục tạm
│   ├── downloads/        # Video/audio đã tải
│   └── transcriptions/   # Kết quả transcription
├── model/                # Thư mục chứa mô hình PhoWhisper
└── requirements.txt      # Dependencies chính

```

## 🛠️ Scripts tiện ích

### `transcribe_phowhisper.py`
Chuyển đổi âm thanh thành văn bản với timestamps chi tiết

**Tính năng:**
- Hỗ trợ nhiều định dạng audio
- Word-level timestamps
- Khôi phục dấu câu tự động
- Export JSON và TXT

### `download_youtube_video.py`
Tải video/audio từ YouTube

**Tính năng:**
- Chất lượng cao nhất có sẵn
- Tải riêng audio (MP3)
- Hỗ trợ playlists
- Metadata đầy đủ

### `adjust_speed_video.py`
Điều chỉnh tốc độ phát video

**Tính năng:**
- Thay đổi tốc độ video (0.5x - 2.0x)
- Giữ nguyên chất lượng
- Tự động điều chỉnh audio

### `synthesize_from_transcription.py`
Tổng hợp audio từ transcription với timestamps

**Tính năng:**
- Tạo audio từ văn bản
- Đồng bộ với timestamps
- Tích hợp với F5-TTS

## 🔧 Cấu hình nâng cao

### Thay đổi model path trong API
Chỉnh sửa file `fast_api/main.py`:

```python
MODEL_PATH = "/path/to/your/model/snapshots/[model_id]"
```

### Tùy chỉnh cài đặt transcription
Chỉnh sửa các tham số trong `scripts/transcribe_phowhisper.py`:

- `chunk_length_s`: Độ dài mỗi chunk (mặc định: 30s)
- `batch_size`: Kích thước batch cho xử lý
- `return_timestamps`: Bật/tắt timestamps

## 🐛 Xử lý lỗi thường gặp

### Lỗi: "FFmpeg not found"
```bash
sudo apt update
sudo apt install ffmpeg
```

### Lỗi: "Model not found"
Đảm bảo đường dẫn model đúng hoặc để mô hình tự động tải về lần đầu

### Lỗi: "CUDA out of memory"
Giảm `batch_size` hoặc `chunk_length_s` trong script transcription

### Lỗi: "Module not found"
```bash
pip install -r requirements.txt
```

## 📝 Ví dụ sử dụng

### Ví dụ 1: Transcribe video YouTube

**Cách nhanh - Chạy 2 dòng:**
```bash
python scripts/download_youtube_video.py "https://youtube.com/watch?v=..."
python scripts/transcribe_phowhisper.py "temp/downloads/video.mp3"
```

**Cách đầy đủ với tham số:**
```bash
# Bước 1: Tải video
python scripts/download_youtube_video.py \
    --url "https://youtube.com/watch?v=..." \
    --output_dir temp/downloads

# Bước 2: Chuyển đổi thành văn bản
python scripts/transcribe_phowhisper.py \
    --audio_path temp/downloads/video.mp3 \
    --output_dir temp/transcriptions
```

### Ví dụ 2: Sử dụng qua API
```python
import requests

url = "http://localhost:8000/transcribe"
files = {"file": open("audio.wav", "rb")}
response = requests.post(url, files=files)
result = response.json()

print(f"Text: {result['text']}")
print(f"Duration: {result['duration']}s")
```

## 📚 Tài liệu tham khảo

- [PhoWhisper](https://huggingface.co/vinai/PhoWhisper) - Mô hình Whisper cho tiếng Việt
- [Whisper](https://github.com/openai/whisper) - Mô hình gốc từ OpenAI
- [FastAPI](https://fastapi.tiangolo.com/) - Framework API
- [yt-dlp](https://github.com/yt-dlp/yt-dlp) - YouTube downloader

## 📄 License

MIT License - Xem file [LICENSE](LICENSE) để biết thêm chi tiết

## 🤝 Đóng góp

Mọi đóng góp đều được chào đón! Vui lòng tạo issue hoặc pull request.

---

<div align="center">
Made with ❤️ for Vietnamese Speech Recognition
</div>
