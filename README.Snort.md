# Instalasi dan Konfigurasi Snort 3

## Persiapan Awal

Petunjuk ini untuk Debian/Ubuntu. Jalankan perintah dengan akses root (gunakan `sudo` atau `sudo su`).

1. Opsional: beralih ke root

```bash
sudo su
```

Atau jalankan setiap perintah dengan `sudo` jika tidak ingin masuk shell root.

2. Perbarui paket sistem

```bash
sudo apt-get update
sudo apt-get upgrade -y
```

3. Instal dependensi

```bash
sudo apt-get install -y build-essential libpcap-dev libpcre3-dev \
libnet1-dev zlib1g-dev luajit libdumbnet-dev bison flex \
liblzma-dev libssl-dev pkg-config cmake cpputest \
libsqlite3-dev uuid-dev libcmocka-dev libnetfilter-queue-dev \
libmnl-dev autotools-dev libluajit-5.1-dev libunwind-dev \
git hwloc libhwloc-dev libnet1-dev
```

Jika hanya ingin memasang `git` secara terpisah:

```bash
sudo apt-get install -y git
```

4. Siapkan direktori sumber

```bash
mkdir -p ~/snort3-src
cd ~/snort3-src
```

## Instalasi Snort DAQ (libdaq)

Jalankan perintah dengan `sudo` jika diperlukan.

1. Klon repositori libdaq:

```bash
git clone https://github.com/snort3/libdaq.git
cd libdaq
```

2. Kompilasi dan instal:

```bash
./bootstrap
./configure
make
sudo make install
```

3. Kembali ke direktori sumber:

```bash
cd ..
```

## Instal gperftools (opsional)

1. Unduh dan ekstrak:

```bash
wget https://github.com/gperftools/gperftools/releases/download/gperftools-2.8/gperftools-2.8.tar.gz
tar -xzf gperftools-2.8.tar.gz
cd gperftools-2.8
```

2. Kompilasi dan instal:

```bash
./configure
make
sudo make install
```

3. Kembali ke direktori sumber:

```bash
cd ..
```

## Instalasi Snort 3

1. Unduh rilis Snort 3 (contoh 3.9.6.0) dan ekstrak:

```bash
wget https://github.com/snort3/snort3/archive/refs/tags/3.9.6.0.tar.gz
tar -xvzf 3.9.6.0.tar.gz
cd snort3-3.9.6.0
```

2. Konfigurasi (aktifkan tcmalloc jika menggunakan gperftools):

```bash
./configure_cmake.sh --prefix=/usr/local --enable-tcmalloc
# Jika muncul error, pastikan libpcre2-dev terpasang:
# sudo apt-get install -y libpcre2-dev
```

3. Masuk ke direktori build, kompilasi dan instal:

```bash
cd build
make
sudo make install
```

4. Perbarui cache library:

```bash
sudo ldconfig
```

5. Verifikasi instalasi:

```bash
snort -V
# atau
snort --version
```

## Konfigurasi Network Interface Card (NIC)

Langkah ini memastikan NIC dapat melihat semua lalu lintas (promiscuous mode) dan menonaktifkan offload tertentu untuk Snort. Ganti `ensxx` dengan nama NIC Anda (cek dengan `ip addr` atau `ifconfig`).

![ensxx](https://github.com/anas-shn/skuriti/blob/main/docs/ensxx.png)

Pengaturan NIC sementara:

```bash
# Aktifkan promiscuous mode
sudo ip link set dev ensxx promisc on

# Cek status receive-offload
sudo ethtool -k ensxx | grep receive-offload

# Nonaktifkan GRO dan LRO
sudo ethtool -K ensxx gro off lro off
```

Catatan: jika `ethtool` belum terpasang, instal dengan `sudo apt install -y ethtool`.

Pengaturan NIC permanen (systemd service):

1. Buat file service:

```bash
sudo nano /etc/systemd/system/snort3-nic.service
```

2. Isi dengan (ganti `ensxx`):

```ini
[Unit]
Description=Set Snort 3 NIC to promiscuous mode and disable GRO/LRO on boot
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ip link set dev ensxx promisc on
ExecStart=/usr/sbin/ethtool -K ensxx gro off lro off
TimeoutStartSec=0
RemainAfterExit=yes

[Install]
WantedBy=default.target
```

3. Aktifkan service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now snort3-nic.service
```

Service ini akan memastikan pengaturan NIC diterapkan setiap boot.
