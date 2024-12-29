# Deteksiobjek
# Step 1: Clone YOLOv5 Repository dan Install Dependencies
import os
import shutil

# Clone YOLOv5 repository
if not os.path.exists('yolov5'):
    !git clone https://github.com/ultralytics/yolov5.git

%cd yolov5

# Install dependencies
!pip install -qr requirements.txt

# Step 2: User Upload File
from google.colab import files
import torch

print("Silakan unggah file gambar atau video...")
uploaded = files.upload()

# Ambil file pertama yang diunggah
if uploaded:
    input_file = list(uploaded.keys())[0]
    input_path = os.path.join('/content/yolov5', input_file)  # Path lengkap untuk file
    shutil.move(input_file, input_path)  # Pindahkan file ke dalam folder YOLOv5
    print(f"File berhasil diunggah: {input_file}")
else:
    print("Tidak ada file yang diunggah. Silakan coba lagi.")

# Step 3: Jalankan Deteksi Objek Menggunakan YOLOv5
device = "cuda" if torch.cuda.is_available() else "cpu"
print(f"Using device: {device}")

# Menyiapkan perintah bash untuk menjalankan deteksi
command = f"python detect.py --weights yolov5s.pt --source {input_path} --device {device}"
!{command}

# Step 4: Tampilkan Hasil Deteksi
from IPython.display import Image, display
import glob

# Cari folder hasil deteksi (runs/detect/)
output_dirs = sorted(glob.glob('runs/detect/*'), key=os.path.getmtime)
if output_dirs:
    latest_dir = output_dirs[-1]  # Folder hasil deteksi terbaru
    print(f"Folder hasil deteksi: {latest_dir}")

    # Tampilkan hasil jika input berupa gambar
    if input_file.lower().endswith(('.jpg', '.jpeg', '.png')):
        result_img_path = os.path.join(latest_dir, input_file)
        if os.path.exists(result_img_path):
            display(Image(filename=result_img_path))
            print(f"Hasil deteksi ditampilkan. File tersimpan di: {result_img_path}")
        else:
            print("Hasil deteksi tidak ditemukan. Periksa folder output.")
    else:
        print("Deteksi selesai untuk video. Periksa folder output untuk hasilnya.")
else:
    print("Hasil deteksi tidak ditemukan. Periksa log error.")
     
