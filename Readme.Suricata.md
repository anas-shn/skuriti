# README — Instalasi Suricata (Debian/Ubuntu)

Panduan singkat untuk memasang Suricata dari PPA resmi (oisf/suricata-stable).

## Langkah instalasi (bash)

```bash
# 1. Perbarui daftar paket dan sistem
sudo apt update
sudo apt upgrade -y

# 2. Pasang tools untuk menambah repository
sudo apt-get install -y software-properties-common

# 3. Tambahkan repository Suricata yang stabil
sudo add-apt-repository -y ppa:oisf/suricata-stable

# 4. Perbarui daftar paket lagi dan pasang Suricata
sudo apt-get update
sudo apt-get install -y suricata

# 5. Verifikasi build dan status layanan
suricata --build-info
systemctl status suricata

# 6. Edit konfigurasi jika perlu
sudo nano /etc/suricata/suricata.yaml
```

## Catatan

- Setelah mengubah konfigurasi, restart layanan dengan:
  sudo systemctl restart suricata
- Untuk log dan debugging, periksa /var/log/suricata/

## Referensi

- Dokumentasi resmi: https://docs.suricata.io/en/latest/quickstart.html
