# Membuat-repository-infinix-
Untuk membuat repository pribadi di Termux yang menyimpan tools milikmu dan bisa berfungsi sebagai jaringan pribadi, kita akan lakukan beberapa tahap:


🛠️ 1. Persiapan Awal di Termux

# Update dan install dependencies dasar
pkg update && pkg upgrade -y
pkg install git python nginx openssh curl -y


---

📁 2. Buat Struktur Repository

# Buat direktori dasar untuk repo pribadi
mkdir -p $HOME/mytermuxrepo/{packages,configs,scripts}
cd $HOME/mytermuxrepo

Kamu akan menyimpan .deb package, script .sh, dan file konfigurasi di sini.


---

🔧 3. Buat Script Tools Sendiri

Contoh: tools/update.sh untuk update semua package

cat > scripts/update.sh << 'EOF'
#!/bin/bash
apt update && apt upgrade -y
EOF

chmod +x scripts/update.sh


---

📦 4. Buat Server HTTP Pribadi

# Set nginx ke direktori repo
mkdir -p ~/nginx_root
cp -r $HOME/mytermuxrepo/* ~/nginx_root/

# Edit konfigurasi nginx
termux-setup-storage
mkdir -p ~/.termux/nginx
cat > ~/.termux/nginx/nginx.conf <<EOF
events {}
http {
    server {
        listen 8080;
        location / {
            root /data/data/com.termux/files/home/nginx_root;
            autoindex on;
        }
    }
}
EOF

# Jalankan nginx
nginx -c ~/.termux/nginx/nginx.conf

Akses di browser HP atau PC: http://<IP-kamu>:8080/


---

🛜 5. Setup SSH agar bisa diakses sebagai jaringan pribadi

# Start SSH
sshd

# Lihat IP kamu
ip a | grep inet

Gunakan scp atau ssh dari perangkat lain ke Termux.


---

🚀 6. Tambahkan Repo ke Termux Sendiri (Paket Custom)

Untuk install script sendiri via pkg install, butuh skema lebih lanjut (build .deb package), tapi kamu bisa jalankan tool dengan:

bash ~/mytermuxrepo/scripts/update.sh
