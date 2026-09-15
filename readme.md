# Event Ticketing - Infrastructure

Infrastructure setup untuk Event Ticketing Microservice menggunakan Docker Compose.

## Architecture

```
infrastucture/
├── docker-compose.yml      # PostgreSQL container setup
├── database-init.sh        # Database initialization script
├── .env                    # Environment variables (tidak di-push)
├── .gitignore              # Git ignore rules
├── migrations/             # (opsional) SQL migration files
├── seed/                   # (opsional) Seed data
└── spec/                   # API specifications
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

### 2. Start Database

```bash
chmod +x database-init.sh
docker-compose up -d
```

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
