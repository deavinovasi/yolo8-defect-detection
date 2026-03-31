# YOLOv8 Defect Detection
Proyek deteksi seal cacat (defect) pada produk menggunakan YOLOv8n. Model dilatih menggunakan dataset dari Roboflow dan mampu mendeteksi objek pada gambar maupun video.

## Classes
- Good : Seal bagus
- Defect : Seal cacat

## Dataset
Dataset diambil dari Roboflow:  
🔗 https://app.roboflow.com/ds/SXzmhI1jqt?key=0AiSHS2PVt 

## Model
File model tersedia di repository ini: `best.pt`  
Dilatih dengan konfigurasi:
- Base model: `yolov8n.pt`
- Epochs: 100
- Image size: 640

---

## Cara Pakai (Google Colab)

### 1. Persiapan Awal
Mount Google Drive dan install dependencies:
```python
from google.colab import drive
drive.mount('/content/drive')

!pip install ultralytics roboflow
```

### 2. Download Dataset dari Roboflow
```python
from roboflow import Roboflow

rf = Roboflow(api_key="YOUR_API_KEY")
project = rf.workspace("WORKSPACE").project("PROJECT-ID")
dataset = project.version(1).download("yolov8")
```
> Ganti `YOUR_API_KEY` dengan API key Roboflow kamu.

### 3. Simpan Dataset ke Google Drive
```python
!cp -r /content/test-2-1 /content/drive/MyDrive/
```

### 4. Training Model
```python
!yolo detect train \
  data=/content/drive/MyDrive/test-2-1/data.yaml \
  model=yolov8n.pt \
  epochs=100 \
  imgsz=640 \
  project=/content/drive/MyDrive/yolo-training \
  name=seal
```
Hasil training tersimpan di `/content/drive/MyDrive/yolo-training/seal/weights/best.pt`

### 5. Prediksi pada Gambar
Untuk upload gambar, jalankan:
```python
from google.colab import files
uploaded = files.upload()

!yolo detect predict \
  model=/content/drive/MyDrive/best.pt \
  source=/content/NAMA_GAMBAR.jpg
```
Tampilkan hasil:
```python
from IPython.display import Image
Image('/content/runs/detect/predict/NAMA_GAMBAR.jpg')
```

### 6. Prediksi pada Video
Untuk upload video, jalankan:
```python
from google.colab import files
uploaded = files.upload()

!yolo detect predict \
  model=/content/drive/MyDrive/best.pt \
  source=/content/NAMA_VIDEO.mp4
```

### 7. Konversi Video Output ke MP4
Hasil deteksi YOLO berformat `.avi` yang tidak kompatibel dengan browser. Konversi ke MP4 menggunakan FFmpeg:
```bash
!ffmpeg -y -i /content/runs/detect/predict/NAMA_VIDEO.avi \
  -vcodec libx264 -crf 23 -pix_fmt yuv420p \
  /content/runs/detect/predict/output.mp4
```

### 8. Tampilkan Video Hasil Deteksi
```python
from IPython.display import Video
Video('/content/runs/detect/predict/output.mp4', embed=True, width=640)
```

### 9. Download Video Hasil
```python
from google.colab import files
files.download('/content/runs/detect/predict/output.mp4')
```

---

## Requirements
Lihat `requirements.txt`
