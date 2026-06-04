# KUBERNETES_FULLSTACK_REACT-NODEJS
Sistem Fullstack berbasis React.js (Frontend) dan Node.js/Express (Backend) yang di-deploy dan diorkestrasi menggunakan Kubernetes. Lingkungan kluster lokal yang digunakan adalah **Docker Desktop** (bukan Minikube).

Sistem ini memisahkan layanan menjadi dua kontainer mandiri yang saling terhubung melalui *Service* Kubernetes:
1. **Frontend (React.js + Nginx)** = Berjalan di Pod K8s, diakses melalui NodePort 30080
2. **Backend (Node.js)** = Berjalan di Pod K8s, diakses melalui NodePort 30011

## Arsitektur Sistem

Aplikasi tidak lagi dijalankan secara *native* di host, melainkan di dalam kluster Kubernetes melalui alur berikut:

Client (Browser/Postman) 
  ├── NodePort (30080) → K8s Service Frontend → Pod Frontend (Port 80)
  └── NodePort (30011) → K8s Service Backend  → Pod Backend (Port 3011)

## Teknologi yang Digunakan

Pastikan perangkat lunak berikut sudah terinstall dan aktif:
1. Docker Desktop (Fitur **Kubernetes** wajib di-enable di Settings)
2. `kubectl` (Kubernetes CLI)
3. Node.js & React (Untuk *development* lokal)
4. Nginx (Sebagai *web server* Frontend di dalam Docker)

## Struktur Folder
```bash
fullstack/
│
├── backend/
│   ├── Dockerfile
│   ├── package.json
│   └── server.js
│
├── frontend/
│   ├── Dockerfile
│   ├── package.json
│   ├── public/
│   └── src/
│       └── App.js
│
└── klaster/
    ├── backend-deployment.yaml
    ├── backend-service.yaml
    ├── frontend-deployment.yaml
    └── frontend-service.yaml
```

## Instalasi & Build Image (Docker)
Masuk ke folder project utama (fullstack), lalu jalankan perintah build image berikut di terminal. Karena menggunakan Docker Desktop, image ini akan otomatis terbaca oleh Kubernetes lokal tanpa perlu di-push ke Docker Hub.

1. Build Image Backend
``` bash
docker build -t (namabebas)/backend:v1 ./backend
```

2. Build Image Frontend
``` bash
docker build -t (namabebas)/frontend:v1 ./frontend
```

## Konfigurasi & Menjalankan Kluster Kubernetes
Pastikan context terminal sudah mengarah ke Docker Desktop (`kubectl config use-context docker-desktop`).

Terapkan semua konfigurasi YAML sekaligus untuk menyalakan Pod dan Service:
``` bash
kubectl apply -f klaster/
```

### Untuk memverifikasi bahwa kluster sudah berjalan, gunakan perintah:
``` bash
kubectl get pods
kubectl get svc
```
Pastikan status Pod menampilkan keterangan Running.

## ENDPOINT API & AKSES APLIKASI
1. Service Backend (Node.js)
Berjalan di http://localhost:30011

|Method|	  Endpoint	                  |      Deskripsi                  |
|------|-------------------------------|---------------------------------|
|GET	  |http../api |	 Menampilkan response JSON: `{"message": "tes backend di kubernetes"}`

2. |Service Frontend (React UI)
Berjalan di http://localhost:30080

Buka URL tersebut di browser. Frontend akan secara otomatis melakukan fetch data ke Backend dan menampilkan pesan hasil tangkapan API di antarmuka web.

## ⚠️ Notes Penting

1. Akses dari luar (komputer host) wajib menggunakan NodePort (30011 & 30080), bukan port container internal (3011 & 80).

2. Jika ada perubahan kode aplikasi (source code), image Docker harus di-build ulang, dan Pod lama harus di-delete agar Kubernetes menarik image yang baru.

## Fitur Utama
1. Containerization: Isolasi environment menggunakan Docker untuk Frontend dan Backend.

2. Kubernetes Orchestration: Pengelolaan lifecycle kontainer (Pod) secara otomatis menggunakan Deployment.

3. Built-in Load Balancing & Exposing: Penggunaan K8s Service tipe NodePort untuk mendistribusikan trafik dan membuka akses ke host.

4. Multi-stage Docker Build: Pemanfaatan Nginx ringan (nginx:alpine) untuk melayani file statis hasil build React.

## Author
- Nama: Andito Dwi Wicaksono

- NIM: 2410511115

- Mata Kuliah: Pembangunan Perangkat Lunak Berorientasi Service (SE-2)
