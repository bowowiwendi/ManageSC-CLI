# ManageSC CLI

CLI manajemen penyewaan VPS, diadaptasi dari aplikasi Android
[bowowiwendi/ManageSC-Android](https://github.com/bowowiwendi/ManageSC-Android).

Repo ini **terpisah** dari versi aplikasi Android — hanya berisi tool CLI (Python,
tanpa dependensi). Data & kredensial tersimpan lokal di `data/*.json`.

## Fitur
- CRUD VPS (username, tipe limit/unli, masa aktif, IP, email, RAM, pesan, SSH)
- PIN tidak disertakan (dihapus dari versi CLI)
- **TUI interaktif** (curses) + fallback menu teks bila non-TTY
- Remote **SSH**: test koneksi, jalankan perintah, setup otomatis VPS
- **Cloudflare DNS**: pilih domain (zone id auto), CRUD record A/CNAME/TXT/MX
- **GitHub**: pull/push daftar `### username expiry ip` (kredensial SSH lokal dipertahankan)

## Instal (Termux)
```sh
pkg install -y python3
curl -L -o ~/managesc https://raw.githubusercontent.com/bowowiwendi/ManageSC-CLI/main/msc \
  && chmod +x ~/managesc
cp ~/managesc $PREFIX/bin/managesc; hash -r 2>/dev/null
managesc
```

## Instal (Alpine)
```sh
apk add --no-cache python3 ncurses
wget -qO /usr/local/bin/managesc https://raw.githubusercontent.com/bowowiwendi/ManageSC-CLI/main/msc \
  && chmod +x /usr/local/bin/managesc
managesc
```

## Jalankan
```sh
managesc            # TUI curses (terminal asli) ATAU fallback menu teks (non-TTY)
managesc --help     # mode perintah (argparse)
```
Catatan: `msc` adalah compiler C# Mono, hence command di sini `managesc`.

## Menu
Lihat Daftar, Tambah, Cari, Update, Hapus, Perpanjang, Cek Kadaluarsa,
SSH Remote, DNS Cloudflare, GitHub.

Di setiap input bisa mengetik `batal` (atau `cancel`/`q`/`exit`) untuk
membatalkan tanpa error / keluar tiba-tiba. Tampilan daftar menggunakan
tabel bercorak (box-drawing) dengan ringkasan Total/Kadaluarsa.

## Mode perintah
```bash
managesc add --username budi --tipe limit --masa-aktif 30 --ip 1.2.3.4 \
        --ssh-user root --ssh-pass rahasia --ram 2GB
managesc list --search budi
managesc renew budi --days 10
managesc check --days 7
managesc ssh budi --cmd "uptime"
managesc ssh budi --shell      # masuk terminal VPS (interaktif, seperti pindah terminal)
managesc dns config --email a@b.com --key <global_api_key>
managesc dns zones
managesc github config --username bowowiwendi --repo ipvps --file-path main/ip --token TOKEN --enabled true
managesc github sync
```

## Keamanan
Kredensial akses remote (SSH `userSsh`/`passSsh`, Cloudflare key, GitHub token)
hanya disimpan di file lokal `data/*.json` dan **tidak pernah di-upload** ke GitHub
(push hanya mengirim `### username expiry ip`).

## Struktur Data
`id, username, tipeAkun, masaAktif, ipVps, emailMember, ram, pesan,
userSsh, passSsh, serverAktif`
