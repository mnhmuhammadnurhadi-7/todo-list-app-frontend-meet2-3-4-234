# Backend Todo List API

REST API untuk aplikasi Todo List yang dibangun menggunakan Node.js, Express, TypeScript, dan database MySQL. Proyek ini menerapkan pola arsitektur MVC (Model-View-Controller) serta autentikasi menggunakan JSON Web Token (JWT).

## Prasyarat
Sebelum menjalankan proyek, pastikan beberapa software berikut sudah terpasang:
- Node.js (disarankan v18 atau lebih baru)
- MySQL Server atau XAMPP
- Postman (opsional, untuk pengujian endpoint)

## Struktur Direktori

```text
backend/
├── src/
│   ├── config/
│   │   └── db.ts             # Koneksi pool ke MySQL
│   ├── controllers/
│   │   ├── authController.ts # Logika login dan register
│   │   └── todoController.ts # Logika CRUD todo
│   ├── middlewares/
│   │   ├── authMiddleware.ts # Verifikasi JWT token
│   │   └── validator.ts      # Validasi input request body
│   ├── models/
│   │   ├── todoModel.ts      # Query SQL tabel todos
│   │   └── userModel.ts      # Query SQL tabel users
│   ├── routes/
│   │   └── api.ts            # Definisi rute API
│   ├── app.ts                # Konfigurasi Express
│   └── server.ts             # File utama untuk menjalankan server
├── .env.example              # Contoh variabel environment
├── package.json
└── tsconfig.json
```

## Persiapan Database

1. Nyalakan service MySQL (misalnya lewat XAMPP Control Panel).
2. Buka phpMyAdmin di browser (`http://localhost/phpmyadmin`) atau buka terminal MySQL.
3. Masuk ke tab **SQL** dan jalankan query berikut untuk membuat database serta tabel yang dibutuhkan:

```sql
DROP DATABASE IF EXISTS todo_db;
CREATE DATABASE todo_db;
USE todo_db;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) UNIQUE NOT NULL,
    email VARCHAR(150) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL
);

CREATE TABLE todos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    task VARCHAR(255) NOT NULL,
    is_completed BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- Data awal (dummy data)
INSERT INTO users (username, email, password) VALUES 
('Andi', 'andi@example.com', 'hashed_pass_1'),
('Budi', 'budi@example.com', 'hashed_pass_2'),
('Citra', 'citra@example.com', 'hashed_pass_3'),
('Doni', 'doni@example.com', 'hashed_pass_4');

INSERT INTO todos (user_id, task, is_completed) VALUES
(1, 'Mengerjakan tugas sebelum deadline', FALSE),
(1, 'Merapikan folder tugas kuliah', TRUE),
(1, 'Belajar untuk ujian besok', FALSE),
(2, 'Mengerjakan laporan praktikum', FALSE),
(2, 'Push project ke GitHub', TRUE),
(2, 'Jangan lupa tidur sebelum jam 3 pagi!', FALSE),
(3, 'Menyelesaikan tugas kelompok', FALSE),
(3, 'Membuat slide presentasi', FALSE),
(3, 'Cari tempat nyaman untuk nugas', TRUE),
(4, 'Mengerjakan tugas yang sudah ditunda seminggu', FALSE),
(4, 'Review materi sebelum praktikum', FALSE),
(4, 'Membalas chat kelompok yang sudah menunggu', TRUE);
```

## Setup dan Instalasi

1. Buka terminal dan masuk ke folder `backend`:
   ```bash
   cd backend
   ```

2. Pasang semua dependensi yang dibutuhkan:
   ```bash
   npm install
   ```

3. Buat file `.env` baru di folder backend. Anda dapat menduplikat dari `.env.example`:
   ```bash
   cp .env.example .env
   ```
   Lalu sesuaikan isinya dengan kredensial database lokal Anda:
   ```env
   DB_HOST=localhost
   DB_USER=root
   DB_PASSWORD=
   DB_NAME=todo_db
   PORT=5000

   JWT_SECRET=pwf_2026
   ```

## Menjalankan Server

Untuk menjalankan server dalam mode pengembangan (development):

```bash
npm run dev
```

Jika berjalan dengan normal, akan muncul pesan:
```text
Server berjalan di http://localhost:5000
```

## Daftar Endpoint

| Method | Endpoint | Keterangan | Autentikasi | Body Request |
|---|---|---|---|---|
| GET | `/` | Cek status server | Tidak | - |
| POST | `/api/auth/register` | Mendaftarkan akun user baru | Tidak | `username`, `email`, `password` |
| POST | `/api/auth/login` | Login untuk mendapatkan token JWT | Tidak | `username`, `password` |
| POST | `/api/todos` | Menambahkan tugas baru | Ya (Bearer Token) | `task` |
| GET | `/api/todos` | Menampilkan seluruh tugas milik user | Ya (Bearer Token) | - |
