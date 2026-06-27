# Planly — Ruang Kerja Akademik Mahasiswa (Web & Mobile Backend)

<!-- Banner Section -->
<div align="center">
  <img src="assets/banner.svg" alt="Planly Banner" width="100%" style="border-radius: 12px; margin-bottom: 20px;" />
</div>

<!-- Badges Section -->
<div align="center">

### Frontend Ecosystem
[![Vite](https://img.shields.io/badge/Vite-6.2.x-646CFF?style=for-the-badge&logo=vite&logoColor=white)](https://vite.dev/)
[![React](https://img.shields.io/badge/React-19.0.x-61DAFB?style=for-the-badge&logo=react&logoColor=white)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.8.x-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![TailwindCSS](https://img.shields.io/badge/TailwindCSS-v4.1.x-38B2AC?style=for-the-badge&logo=tailwindcss&logoColor=white)](https://tailwindcss.com/)
[![Framer Motion](https://img.shields.io/badge/Framer_Motion-12.4.x-FF007F?style=for-the-badge&logo=framer&logoColor=white)](https://www.framer.com/motion/)
[![KaTeX](https://img.shields.io/badge/KaTeX-0.17.x-37A779?style=for-the-badge&logo=katex&logoColor=white)](https://katex.org/)

### Backend & Database
[![Laravel](https://img.shields.io/badge/Laravel-11.x-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)](https://laravel.com/)
[![Sanctum](https://img.shields.io/badge/Sanctum-Sanctum-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)](https://laravel.com/docs/sanctum)
[![MySQL](https://img.shields.io/badge/MySQL-8.x-4479A1?style=for-the-badge&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![Composer](https://img.shields.io/badge/Composer-Dependency-885630?style=for-the-badge&logo=composer&logoColor=white)](https://getcomposer.org/)

### AI & Biometrics
[![Gemini AI](https://img.shields.io/badge/Gemini_AI-Flash--2.4-8E75C2?style=for-the-badge&logo=google-gemini&logoColor=white)](https://deepmind.google/technologies/gemini/)
[![Face API](https://img.shields.io/badge/Face--API-Biometrics-0d9488?style=for-the-badge&logo=webcam&logoColor=white)](https://github.com/vladmandic/face-api)

</div>

---

## 🌟 Tentang Planly

**Planly** adalah platform web perencana akademik premium yang dirancang khusus untuk meningkatkan produktivitas belajar mahasiswa. Aplikasi ini menggabungkan pengelolaan jadwal kuliah otomatis, manajemen tugas kuliah, catatan materi berformat Markdown, verifikasi presensi kehadiran menggunakan kamera (Face Biometrics), asisten kuliah pintar menggunakan Gemini AI, hingga ekspor jadwal ke Google Calendar secara instan.

Platform ini mendukung **Dual-Mode API** (Simulasi Lokal / Mock vs Koneksi Laravel Backend) untuk kemudahan pengembangan dan paritas penuh dengan target pengembangan aplikasi mobile Flutter.

---

## 🗺️ Alur & Arsitektur Sistem

Berikut adalah arsitektur aliran data di dalam ekosistem **Planly** yang menghubungkan modul sisi klien, mesin deteksi biometrik/AI, jembatan data, hingga API Backend Laravel:

```mermaid
graph TB
    subgraph Client [Sisi Klien - React 19 SPA]
        A[App.tsx - Router & State Global]
        B[Custom Hooks: useAcademicData, useAppAuth, useFocusTimer, useAppTheme]
        C["Modul Fitur: Today, Attendance, AI Companion, Tasks, Calendar, Notes"]
        D["Pustaka UI: Lucide, Framer Motion, KaTeX"]
    end
    
    subgraph CoreEngine [Mesin Deteksi & AI - Sisi Klien]
        E["vladmandic/face-api: Face Recognition"]
        F["Web Audio API: WAV 16kHz Downsampler"]
        G["@google/genai: Gemini API Client"]
    end

    subgraph DataBridge [Jembatan Data]
        H{"VITE_USE_MOCK == true?"}
        I["Mock Engine: LocalStorage"]
        J["Axios HTTP Client"]
    end

    subgraph Backend [Server API - Laravel 11]
        K["Sanctum Auth Middleware"]
        L["Controllers: Course, Task, Note, Attendance, Profile"]
        M["Eloquent ORM"]
    end

    subgraph Database [Penyimpanan Data]
        N[("MySQL / MariaDB")]
    end

    %% Hubungan Aliran
    A --> B
    B --> C
    C --> D
    C --> E
    C --> F
    C --> G
    
    C --> H
    H -- Ya --> I
    H -- Tidak --> J
    
    J --> K
    K --> L
    L --> M
    M --> N
    
    style Client fill:#1e1b4b,stroke:#4f46e5,stroke-width:2px,color:#fff
    style CoreEngine fill:#311042,stroke:#8e75c2,stroke-width:2px,color:#fff
    style DataBridge fill:#06201b,stroke:#0d9488,stroke-width:2px,color:#fff
    style Backend fill:#2d1316,stroke:#ff2d20,stroke-width:2px,color:#fff
    style Database fill:#172554,stroke:#3b82f6,stroke-width:2px,color:#fff
```

---

## 📊 Tabel Alur Kerja Pengguna (User Journey)

Tabel berikut merinci aktivitas pengguna pada setiap halaman, logika state yang menyokongnya, integrasi API, serta efek animasi interaktif yang diterapkan:

| Halaman / Fitur | Deskripsi Alur & Interaksi Pengguna | Custom Hook / State | API Endpoint (Live) | Animasi & Efek UI |
| :--- | :--- | :--- | :--- | :--- |
| **Landing & Auth** | Landing page edukatif untuk pengguna baru, dialihkan ke form login/register. | `useAppAuth` | `POST /auth/login`<br>`POST /auth/register` | Fade transition (200ms) & scaling modal (0.95 -> 1) |
| **Dasbor Hari Ini** | Memantau kuliah hari ini secara realtime, melacak status kelas, tugas mendesak, dan widget Pomodoro. | `useFocusTimer`<br>`useAcademicData` | `GET /courses` | **Pulsing Indicator** untuk kelas aktif, checklist coret dinamis |
| **Absensi Wajah** | Membuka kamera depan, mengambil koordinat GPS, memindai wajah, mencocokkan Euclidean distance. | `useFaceScanner`<br>(attendance) | `POST /attendance`<br>`GET /attendance/history` | **Scanning Radar Loop** (garis laser hijau vertikal), indikator sukses berkedip |
| **Asisten AI** | Mengunggah video rekaman kuliah, downsample audio client-side, transkripsi Gemini, tanya jawab materi RAG. | `useFocusTimer`<br>(workspace) | Direct Gemini API (Client SDK) | **Soundwave Pulse** (tinggi bar audio acak dinamis), chatting fade-in |
| **Kalender Jadwal** | Menampilkan timeline kelas mingguan, navigasi tanggal horizontal, modifikasi sesi terjadwal (*reschedule*). | `useAcademicData` | `GET /courses`<br>`POST /reschedules` | Slide tab horizontal, border transition warna khusus per kelas |
| **Daftar Tugas** | CRUD tugas kuliah dengan prioritas tinggi, tanggal batas waktu relatif, melampirkan file materi tugas. | `useAcademicData`<br>`useDeadlineMonitor` | `POST /tasks`<br>`PUT /tasks/{id}` | Slide-over panel drawer dari sisi kanan (300ms ease-out) |
| **Catatan LaTeX** | CRUD catatan perkuliahan mendukung pratinjau matematika LaTeX (KaTeX) dan checklist masonry. | `useAcademicData` | `POST /notes`<br>`DELETE /notes/{id}` | Masonry grid transition, render instan formula matematika KaTeX |
| **Profil Pengguna** | Mengubah info profil, upload wajah referensi, ganti API Key Gemini, ekspor kalender .ics. | `useAppAuth`<br>`useAppTheme` | `PUT /profile`<br>ICal Generator | Dark/Light Mode adaptif transition (warna memudar halus) |

---

## ⚙️ Detail Alur Fitur Kunci

### 1. Verifikasi Kehadiran Cerdas (Face Biometrics & GPS Geofencing)
Proses validasi kehadiran mahasiswa dilakukan secara ketat di sisi klien sebelum data dikirim ke server backend untuk mencegah manipulasi kehadiran.

```mermaid
graph TD
    Start["Mulai Presensi Kehadiran"] --> GetGPS["Ambil Koordinat GPS Pengguna"]
    GetGPS --> CompareGPS{"Koordinat dalam Radius Kelas?"}
    
    CompareGPS -- Gagal / Di Luar Radius --> ErrorGPS["Tampilkan Error: 'Di Luar Lokasi Kelas'"]
    CompareGPS -- Berhasil / Dalam Radius --> OpenCam["Aktifkan Kamera & WebCam Stream"]
    
    OpenCam --> LoadModels["Muat Model Face-API TinyFaceDetector & FaceLandmark"]
    LoadModels --> DetectFace["Ekstrak Live Face Descriptor"]
    DetectFace --> CompareFace{"Bandingkan dengan Profile Descriptor"}
    
    CompareFace -- Euclidean Distance <= 0.6 --> Success["Presensi Berhasil: Hadir"]
    CompareFace -- Euclidean Distance > 0.6 --> Fail["Presensi Gagal: Wajah Tidak Cocok"]
    
    Success --> SendAPI["Kirim Log Kehadiran ke Database via POST /attendance"]
    Fail --> ShowRetry["Tampilkan Kesalahan & Opsi Coba Lagi"]
    
    style Start fill:#1e1b4b,stroke:#4f46e5,stroke-width:1px,color:#fff
    style Success fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#fff
    style Fail fill:#7f1d1d,stroke:#f87171,stroke-width:2px,color:#fff
```

### 2. Pemrosesan Rekaman Kuliah Client-Side & Gemini AI RAG
Untuk meminimalkan beban bandwidth jaringan, audio diekstrak dan didownsample di sisi klien sebelum diunggah ke Gemini AI.

```mermaid
graph TD
    Upload["Unggah File Rekaman Video/Audio Kuliah"] --> CheckFormat{"Cek Durasi & Format"}
    CheckFormat --> WebAudio["Web Audio API Client-Side Processing"]
    WebAudio --> Downsample["Downsample ke WAV 16kHz Mono 16-bit"]
    Downsample --> UploadGemini["Kirim file audio biner ke Gemini API"]
    
    UploadGemini --> GeminiProc{"Analisis Gemini AI"}
    GeminiProc --> Transcribe["Generasi Transkrip bertimestamp"]
    GeminiProc --> Takeaways["Ekstraksi Poin Utama & Bab Ringkasan"]
    GeminiProc --> GoogleSearch["Pencarian Eksternal untuk Pengayaan Materi"]
    
    Transcribe & Takeaways & GoogleSearch --> RAGStore["Simpan Konteks di State Catatan/Session"]
    RAGStore --> ChatBot["Aktifkan RAG Chatbot untuk Tanya Jawab Terbatas Materi"]
    
    style WebAudio fill:#311042,stroke:#8e75c2,stroke-width:1px,color:#fff
    style GeminiProc fill:#172554,stroke:#3b82f6,stroke-width:1px,color:#fff
    style ChatBot fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#fff
```

---

## 🛠️ Pustaka Pendukung Utama (Third-Party Libraries)

Aplikasi ini menggunakan berbagai pustaka pilihan untuk mengoptimalkan kinerja di sisi frontend dan backend:

| Nama Pustaka | Kategori | Peran & Kegunaan Utama dalam Planly | Dokumentasi Resmi |
| :--- | :--- | :--- | :--- |
| **@vladmandic/face-api** | Biometrik | Menjalankan model deteksi dan ekstraksi fitur wajah berbasis TensorFlow.js langsung di browser untuk verifikasi kehadiran. | [GitHub Repo](https://github.com/vladmandic/face-api) |
| **@google/genai** | Artificial Intelligence | Integrasi resmi ke model Gemini Flash untuk transkripsi audio kuliah, analisis RAG chatbot, dan kartu pencarian pengayaan materi. | [Docs](https://deepmind.google/technologies/gemini/) |
| **framer-motion** / **motion** | Animasi UI | Mengontrol animasi transisi halaman, efek slide-over panel pada manajemen tugas, dan micro-interactions pada tombol/kartu. | [Framer Motion Docs](https://www.framer.com/motion/) |
| **katex** | Math Rendering | Pustaka rendering formula matematika berkecepatan tinggi berbasis LaTeX untuk menulis rumus di catatan kuliah. | [KaTeX Docs](https://katex.org/) |
| **tw-animate-css** | Utilitas Animasi | Pustaka pendukung integrasi animasi css kustom yang terikat dengan kelas TailwindCSS untuk widget status visual. | [GitHub Repo](https://github.com/d-exclaimation/tw-animate-css) |
| **axios** | HTTP Client | Melakukan request asinkron (RESTful API calls) dari React Klien ke Laravel Backend dengan penanganan interceptor token. | [Axios Docs](https://axios-http.com/) |
| **laravel-sanctum** | Keamanan Backend | Menyediakan sistem autentikasi API berbasis token bearer (JWT-like) yang ringan untuk mengamankan endpoint data mahasiswa. | [Sanctum Docs](https://laravel.com/docs/sanctum) |

---

## 🚀 Panduan Instalasi & Kloning

### 1. Kloning Repositori
```bash
git clone https://github.com/username/planly-website.git
cd planly-website
```

### 2. Jalankan Frontend (React)
Instal dependensi dan jalankan server lokal:
```bash
npm install
npm run dev
```
*Aplikasi frontend berjalan di alamat `http://localhost:3000`.*

### 3. Jalankan Backend (Laravel API)
Masuk ke folder backend, instal dependensi, dan nyalakan server lokal:
```bash
cd planly-api
composer install
php artisan key:generate
php artisan migrate --seed
php artisan serve
```
*API Server berjalan di alamat `http://localhost:8000`.*

### 4. Konfigurasi Environment Variables (`.env`)
Pastikan berkas `.env` pada root directory proyek disesuaikan:
```env
# Set 'true' untuk database lokal (localStorage), set 'false' untuk menggunakan API Laravel
VITE_USE_MOCK=false
VITE_API_BASE_URL=http://localhost:8000/api
```

---

## 📬 Postman API Collection

Proyek ini menyediakan file konfigurasi Postman Collection untuk mempermudah pengujian REST API Laravel secara langsung. Konfigurasi ini disimpan pada root proyek di dalam file [planly_postman_collection.json](file:///d:/PROJECT/Planly Website/planly_postman_collection.json).

### Fitur Utama Koleksi Postman:
1. **Autentikasi Otomatis (Bearer Token)**: Dilengkapi dengan *Test Script* otomatis pada request **Login User**. Script ini secara otomatis mengekstrak token dari respons API dan menyimpannya ke variabel environment/collection `{{token}}`. Anda tidak perlu melakukan penyalinan token secara manual setelah melakukan login.
2. **Penggunaan Variabel Dinamis**: Menggunakan variabel `{{base_url}}` (default: `http://localhost:8000/api`) dan `{{token}}` untuk fleksibilitas pengujian di server lokal maupun staging.
3. **Payload Request Lengkap**: Setiap request sudah dilengkapi dengan data dummy JSON yang valid, termasuk payload rumit seperti format biometrik wajah (Base64), file lampiran tugas, dan koordinat GPS geofencing.

### Cara Mengimpor & Menggunakan:
1. **Impor ke Postman**:
   - Buka aplikasi Postman.
   - Klik tombol **Import** di bagian atas menu.
   - Seret atau pilih file [planly_postman_collection.json](file:///d:/PROJECT/Planly Website/planly_postman_collection.json) dari root folder proyek.
2. **Menyiapkan Aplikasi**:
   - Pastikan server API Laravel Anda sudah aktif (silakan jalankan `php artisan serve` di folder `planly-api`).
3. **Alur Pengujian**:
   - Jalankan request **Register User** di folder `Auth` untuk membuat akun mahasiswa baru (atau gunakan credential default yang ada).
   - Jalankan request **Login User**. Setelah sukses, variabel `{{token}}` akan terisi otomatis.
   - Anda kini dapat langsung menjalankan endpoint privat lainnya (seperti mengambil data mata kuliah, membuat tugas, mencatat materi kuliah, atau presensi kehadiran) tanpa perlu mengatur token secara manual lagi.

<details>
<summary><b>Klik untuk melihat isi JSON Postman Collection lengkap</b></summary>

```json
{
	"info": {
		"_postman_id": "8b9e6fa4-8977-4cf0-bb46-a4c3f59e51dc",
		"name": "Planly API Collection",
		"description": "Koleksi endpoint REST API lengkap untuk aplikasi Planly (Ruang Kerja Akademik Mahasiswa). Menggunakan Laravel Sanctum untuk autentikasi Bearer Token.",
		"schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
	},
	"item": [
		{
			"name": "Auth",
			"item": [
				{
					"name": "Register User",
					"request": {
						"auth": {
							"type": "noauth"
						},
						"method": "POST",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"name\": \"Arief Sidik Wijayanto\",\n    \"email\": \"arfwjn@gmail.com\",\n    \"password\": \"ariefsidikpassword\",\n    \"password_confirmation\": \"ariefsidikpassword\",\n    \"nim\": \"STI202303494\"\n}"
						},
						"url": {
							"raw": "{{base_url}}/auth/register",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"auth",
								"register"
							]
						}
					},
					"response": []
				},
				{
					"name": "Login User",
					"event": [
						{
							"listen": "test",
							"script": {
								"exec": [
									"var jsonData = pm.response.json();",
									"if (jsonData.token) {",
									"    pm.environment.set(\"token\", jsonData.token);",
									"    pm.collectionVariables.set(\"token\", jsonData.token);",
									"}"
								],
								"type": "text/javascript"
							}
						}
					],
					"request": {
						"auth": {
							"type": "noauth"
						},
						"method": "POST",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"email\": \"arfwjn@gmail.com\",\n    \"password\": \"ariefsidikpassword\"\n}"
						},
						"url": {
							"raw": "{{base_url}}/auth/login",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"auth",
								"login"
							]
						}
					},
					"response": []
				},
				{
					"name": "Logout User",
					"request": {
						"method": "POST",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/logout",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"logout"
							]
						}
					},
					"response": []
				}
			]
		},
		{
			"name": "Profile",
			"item": [
				{
					"name": "Get Profile",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/profile",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"profile"
							]
						}
					},
					"response": []
				},
				{
					"name": "Update Profile (POST)",
					"request": {
						"method": "POST",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"name\": \"Arief Sidik Wijayanto\",\n    \"nim\": \"STI202303494\",\n    \"semester\": 6,\n    \"major\": \"Teknik Informatika\",\n    \"gpa_current\": 3.60,\n    \"gpa_target\": 3.80,\n    \"target_study_hours\": 2,\n    \"address\": \"Purwokerto, Jawa Tengah\"\n}"
						},
						"url": {
							"raw": "{{base_url}}/profile/update",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"profile",
								"update"
							]
						}
					},
					"response": []
				},
				{
					"name": "Update Profile (PUT)",
					"request": {
						"method": "PUT",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"name\": \"Arief Sidik Wijayanto\",\n    \"nim\": \"STI202303494\",\n    \"semester\": 6,\n    \"major\": \"Teknik Informatika\",\n    \"gpa_current\": 3.60,\n    \"gpa_target\": 3.80,\n    \"target_study_hours\": 2,\n    \"address\": \"Purwokerto, Jawa Tengah\"\n}"
						},
						"url": {
							"raw": "{{base_url}}/profile",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"profile"
							]
						}
					},
					"response": []
				}
			]
		},
		{
			"name": "Courses",
			"item": [
				{
					"name": "Get All Courses",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/courses",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"courses"
							]
						}
					},
					"response": []
				},
				{
					"name": "Create Course",
					"request": {
						"method": "POST",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"course_code\": \"SWU001\",\n    \"course_name\": \"Website Programming Lanjut\",\n    \"sks\": 4,\n    \"lecturer_name\": \"Sunaryono M.Kom\",\n    \"room\": \"KB. Ruang 2.3\",\n    \"day_of_week\": \"Wednesday\",\n    \"start_time\": \"17:00\",\n    \"end_time\": \"18:00\",\n    \"color_hex\": \"#3525cd\"\n}"
						},
						"url": {
							"raw": "{{base_url}}/courses",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"courses"
							]
						}
					},
					"response": []
				},
				{
					"name": "Get Course Detail",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/courses/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"courses",
								"1"
							]
						}
					},
					"response": []
				},
				{
					"name": "Update Course",
					"request": {
						"method": "PUT",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"course_code\": \"SWU001\",\n    \"course_name\": \"Website Programming Lanjut (Updated)\",\n    \"sks\": 4,\n    \"lecturer_name\": \"Sunaryono M.Kom\",\n    \"room\": \"KB. Ruang 2.5\",\n    \"day_of_week\": \"Wednesday\",\n    \"start_time\": \"16:30\",\n    \"end_time\": \"18:00\",\n    \"color_hex\": \"#3525cd\"\n}"
						},
						"url": {
							"raw": "{{base_url}}/courses/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"courses",
								"1"
							]
						}
					},
					"response": []
				},
				{
					"name": "Delete Course",
					"request": {
						"method": "DELETE",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/courses/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"courses",
								"1"
							]
						}
					},
					"response": []
				}
			]
		},
		{
			"name": "Tasks",
			"item": [
				{
					"name": "Get All Tasks",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/tasks",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"tasks"
							]
						}
					},
					"response": []
				},
				{
					"name": "Get Tasks by Course",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/tasks?course_id=1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"tasks"
							],
							"query": [
								{
									"key": "course_id",
									"value": "1"
								}
							]
						}
					},
					"response": []
				},
				{
					"name": "Create Task",
					"request": {
						"method": "POST",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"course_id\": 1,\n    \"task_title\": \"Membuat UI Login & Register di Flutter\",\n    \"description\": \"Implementasi halaman login dan register menggunakan Flutter dengan validasi form, integrasi API, dan state management Provider.\",\n    \"deadline\": \"2026-06-10 23:59:00\",\n    \"is_priority\": true,\n    \"attachments\": [\n        {\n            \"name\": \"mock_flutter_guidelines.pdf\",\n            \"type\": \"application/pdf\",\n            \"size\": 14520,\n            \"data_url\": \"data:application/pdf;base64,JVBERi...\"\n        }\n    ]\n}"
						},
						"url": {
							"raw": "{{base_url}}/tasks",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"tasks"
							]
						}
					},
					"response": []
				},
				{
					"name": "Get Task Detail",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/tasks/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"tasks",
								"1"
							]
						}
					},
					"response": []
				},
				{
					"name": "Update Task",
					"request": {
						"method": "PUT",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"task_title\": \"Membuat UI Login & Register di Flutter (REVISI)\",\n    \"description\": \"Implementasi halaman login dan register menggunakan Flutter dengan validasi form, integrasi API, dan state management Provider.\",\n    \"deadline\": \"2026-06-10 23:59:00\",\n    \"is_priority\": false\n}"
						},
						"url": {
							"raw": "{{base_url}}/tasks/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"tasks",
								"1"
							]
						}
					},
					"response": []
				},
				{
					"name": "Finish Task (Checklist Status)",
					"request": {
						"method": "PATCH",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/tasks/1/finish",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"tasks",
								"1",
								"finish"
							]
						}
					},
					"response": []
				},
				{
					"name": "Delete Task",
					"request": {
						"method": "DELETE",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/tasks/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"tasks",
								"1"
							]
						}
					},
					"response": []
				}
			]
		},
		{
			"name": "Notes",
			"item": [
				{
					"name": "Get All Notes",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/notes",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"notes"
							]
						}
					},
					"response": []
				},
				{
					"name": "Create Note",
					"request": {
						"method": "POST",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"course_id\": 3,\n    \"title\": \"Catatan Materi AI - Pertemuan 4\",\n    \"content\": \"Algoritma pencarian: BFS (Breadth-First Search) dan DFS (Depth-First Search) adalah dasar dari algoritma pencarian pada graf. Heuristik digunakan pada informed search seperti A* dan Greedy Best-First Search untuk meningkatkan efisiensi pencarian.\"\n}"
						},
						"url": {
							"raw": "{{base_url}}/notes",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"notes"
							]
						}
					},
					"response": []
				},
				{
					"name": "Get Note Detail",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/notes/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"notes",
								"1"
							]
						}
					},
					"response": []
				},
				{
					"name": "Update Note",
					"request": {
						"method": "PUT",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"title\": \"Catatan Materi AI - Pertemuan 4 (Revisi)\",\n    \"content\": \"Algoritma pencarian: BFS (Breadth-First Search) dan DFS (Depth-First Search) revisi.\"\n}"
						},
						"url": {
							"raw": "{{base_url}}/notes/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"notes",
								"1"
							]
						}
					},
					"response": []
				},
				{
					"name": "Delete Note",
					"request": {
						"method": "DELETE",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/notes/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"notes",
								"1"
							]
						}
					},
					"response": []
				}
			]
		},
		{
			"name": "Events",
			"item": [
				{
					"name": "Get All Events",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/events",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"events"
							]
						}
					},
					"response": []
				},
				{
					"name": "Create Event",
					"request": {
						"method": "POST",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"event_name\": \"Seminar Nasional AI & Web Development\",\n    \"category\": \"seminar\",\n    \"description\": \"Seminar nasional mengenai masa depan Web Development di era kecerdasan buatan.\",\n    \"event_date\": \"2026-06-09\",\n    \"start_time\": \"09:00\",\n    \"end_time\": \"12:00\",\n    \"location\": \"Auditorium SWU Lantai 3\",\n    \"organizer\": \"Himpunan Mahasiswa Informatika\",\n    \"color_hex\": \"#6366F1\",\n    \"is_important\": true\n}"
						},
						"url": {
							"raw": "{{base_url}}/events",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"events"
							]
						}
					},
					"response": []
				},
				{
					"name": "Update Event",
					"request": {
						"method": "PUT",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"location\": \"Auditorium Gedung Utama Lantai 2\",\n    \"is_important\": false\n}"
						},
						"url": {
							"raw": "{{base_url}}/events/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"events",
								"1"
							]
						}
					},
					"response": []
				},
				{
					"name": "Delete Event",
					"request": {
						"method": "DELETE",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/events/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"events",
								"1"
							]
						}
					},
					"response": []
				}
			]
		},
		{
			"name": "Reschedules",
			"item": [
				{
					"name": "Get All Reschedules",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/reschedules",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"reschedules"
							]
						}
					},
					"response": []
				},
				{
					"name": "Create Reschedule",
					"request": {
						"method": "POST",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"course_id\": 1,\n    \"original_date\": \"2026-06-09\",\n    \"new_date\": null,\n    \"new_start_time\": null,\n    \"new_end_time\": null,\n    \"is_canceled\": true,\n    \"note\": \"Pertemuan perdana dibatalkan karena dosen rapat rektorat\"\n}"
						},
						"url": {
							"raw": "{{base_url}}/reschedules",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"reschedules"
							]
						}
					},
					"response": []
				},
				{
					"name": "Delete Reschedule (Restore Normal)",
					"request": {
						"method": "DELETE",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/reschedules/1/2026-06-09",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"reschedules",
								"1",
								"2026-06-09"
							]
						}
					},
					"response": []
				}
			]
		},
		{
			"name": "Attendance",
			"item": [
				{
					"name": "Get All Attendance",
					"request": {
						"method": "GET",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/attendance",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"attendance"
							]
						}
					},
					"response": []
				},
				{
					"name": "Submit Attendance",
					"request": {
						"method": "POST",
						"header": [
							{
								"key": "Content-Type",
								"value": "application/json"
							},
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"body": {
							"mode": "raw",
							"raw": "{\n    \"course_id\": 1,\n    \"course_code\": \"SWU001\",\n    \"course_name\": \"Website Programming Lanjut\",\n    \"date\": \"2026-06-09\",\n    \"time\": \"17:05:12\",\n    \"status\": \"Hadir\",\n    \"latitude\": -6.9825,\n    \"longitude\": 110.4091,\n    \"image_base64\": \"data:image/jpeg;base64,/9j/4AAQSkZJRg...\"\n}"
						},
						"url": {
							"raw": "{{base_url}}/attendance",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"attendance"
							]
						}
					},
					"response": []
				},
				{
					"name": "Delete Attendance",
					"request": {
						"method": "DELETE",
						"header": [
							{
								"key": "Accept",
								"value": "application/json"
							}
						],
						"url": {
							"raw": "{{base_url}}/attendance/1",
							"host": [
								"{{base_url}}"
							],
							"path": [
								"attendance",
								"1"
							]
						}
					},
					"response": []
				}
			]
		}
	],
	"auth": {
		"type": "bearer",
		"bearer": [
			{
				"key": "token",
				"value": "{{token}}",
				"type": "string"
			}
		]
	},
	"event": [
		{
			"listen": "prerequest",
			"script": {
				"type": "text/javascript",
				"exec": [
					""
				]
			}
		},
		{
			"listen": "test",
			"script": {
				"type": "text/javascript",
				"exec": [
					""
				]
			}
		}
	],
	"variable": [
		{
			"key": "base_url",
			"value": "http://localhost:8000/api",
			"type": "string"
		},
		{
			"key": "token",
			"value": "your_bearer_token_here",
			"type": "string"
		}
	]
}
```

</details>

---

## 📂 Struktur Folder Proyek

```txt
planly-website/
├── assets/                    # Aset statis & ilustrasi (termasuk banner.svg)
├── planly-api/                # Backend API Laravel 11 & DB Migrations
│   ├── app/                   # Controller, Model, & Request API
│   ├── config/                # Konfigurasi laravel (auth, database, dll)
│   ├── database/              # Migrasi tabel database & seeders data awal
│   └── routes/                # Defini endpoint API (/routes/api.php)
├── src/
│   ├── components/            # Komponen visual modular per-fitur
│   │   ├── attendance/        # Verifikasi wajah & riwayat absen
│   │   ├── auth/              # Halaman masuk/daftar
│   │   ├── calendar/          # Timeline jadwal & grid bulanan
│   │   ├── courses/           # Pendaftaran mata kuliah & input SKS
│   │   ├── discussion/        # Ruang Diskusi Kampus (Forum & Komentar)
│   │   ├── events/            # Agenda non-kuliah kampus
│   │   ├── notes/             # Catatan materi & editor Markdown (LaTeX)
│   │   ├── profile/           # Bento settings layout & ekspor kalender
│   │   ├── tasks/             # Pengelola tugas & file uploader
│   │   ├── today/             # Dasbor ringkasan hari ini & Pomodoro timer
│   │   └── ui/                # Reusable UI (InteractiveEmptyState, ApiKeyModal, dll.)
│   ├── hooks/                 # Custom Hooks (useFaceScanner, useAcademicData, useAppAuth)
│   ├── services/              # Modul REST API Laravel & Helper HTTP
│   ├── utils/                 # Utility helpers (security, iCal exporter, dll.)
│   ├── types.ts               # Interface Types TypeScript (snake_case)
│   ├── mockData.ts            # Dummy Data Awal Mahasiswa (Arief Sidik W.)
│   ├── App.tsx                # Entry point UI & Router Navigasi (Tab persistence)
│   └── main.tsx               # Bootstrapper React utama
├── API.md                     # Panduan endpoint REST API Laravel
├── BACKEND_INTEGRATION.md     # Panduan migrasi database & controller backend
├── planly_postman_collection.json # File JSON Postman Collection untuk uji coba API
└── PRD.md                     # Product Requirement Document (Paritas Fitur Mobile Flutter)
```