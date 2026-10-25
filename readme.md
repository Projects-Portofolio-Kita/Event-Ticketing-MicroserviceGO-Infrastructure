# Event Ticketing - Infrastructure

Infrastructure setup untuk Event Ticketing Microservice menggunakan Docker Compose.

> **Satu definisi database.** File ini adalah satu-satunya definisi Postgres dan
> di-`include` oleh `backend/docker-compose.yml`, jadi kedua file memakai
> project name yang sama (`event-ticketing`) dan **tidak pernah berebut port
> 5432**. Jalankan stack penuh dari `backend/`, atau cukup database saja dari
> sini — keduanya mengelola container yang sama.

## Architecture

```
infrastucture/
├── docker-compose.yml      # PostgreSQL container setup (di-include backend/docker-compose.yml)
├── database-init.sh        # Database initialization script
├── .env                    # Environment variables (tidak di-push)
├── .gitignore              # Git ignore rules
├── migrations/             # (opsional) SQL migration files
├── seed/                   # (opsional) Seed data
└── spec/                   # API specifications (arsip — kontrak resmi di /contracts)
```

## Services

| Service | Port | Description |
|---------|------|-------------|
| PostgreSQL | 5432 | Database server |

## Database

| Key | Value |
|-----|-------|
| Host | `localhost` |
| Port | `5432` |
| Database | `event_ticketing_db` |
| Username | `admin` |
| Password | (lihat `.env`) |

## Cara Jalankan

### 1. Setup Environment Variables

Copy `.env.example` ke `.env` dan isi credentials:

```bash
cp .env.example .env
nano .env
```

> Default di `docker-compose.yml` sudah disamakan dengan
> `backend/docker-compose.yml` (`admin` / `tiketid` / `event_ticketing_db`),
> jadi `.env` opsional — hanya dibuat bila ingin mengganti kredensial.

### 2. Start Database

```bash
chmod +x database-init.sh
docker compose up -d
```

Atau langsung stack penuh (database + semua service + portal) dari folder backend:

```powershell
docker compose -f ../backend/docker-compose.yml up -d --build
```

Karena project name-nya sama (`event-ticketing`), perintah di atas tidak akan
menabrak container yang dijalankan dari folder ini.

### 3. Cek Status

```bash
docker-compose ps
```

### 4. Stop Database

```bash
docker-compose down
```

### 5. Stop & Hapus Data

```bash
docker-compose down -v
```

## Database Init

`database-init.sh` akan otomatis jalan saat pertama kali container start. Script ini membuat database dari variabel `POSTGRES_DB` di `.env`.

> **Note:** Migrations (pembuatan tables) dilakukan di masing-masing backend service, bukan di infrastructure.

## Network

Semua service tergabung dalam network `app-network` (bridge) agar bisa saling berkomunikasi.

## Troubleshooting

### Database sudah ada, ingin reset

```bash
docker-compose down -v
docker-compose up -d
```

### Cek logs

```bash
docker-compose logs -f postgres
```

### Masuk ke psql

```bash
docker exec -it event-ticketing-postgres psql -U admin -d event_ticketing_db
```
