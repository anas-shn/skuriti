# README — Instalasi Config Suricata (Debian/Ubuntu)

Panduan singkat untuk memasang Suricata dari PPA resmi (oisf/suricata-stable).

## Langkah instalasi (bash)

```bash
git clone https://github.com/anas-shn/skuriti.git

cd skuriti/suri

sudo mv dvwa.rules /etc/suricata/rules/

sudo mv suricata.yaml /etc/suricata

sudo nano /etc/suricata/suricata.yaml
#Ganti ip HOME_NET menjadi ipmu

sudo systemctl restart suricata
sudo systemctl status suricata


```

## Jalankan Suricata (eth0 diganti dari ip a)

```bash
sudo suricata -c /etc/suricata/suricata.yaml -i eth0
```

## Jalankan log Suricata

```bash
tail -f /var/log/suricata/fast.log
```
