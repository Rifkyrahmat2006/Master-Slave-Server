# MASTER-SLAVE-SERVER

*by Rifky D.R. Prakoso*


[![Instagram](https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://instagram.com/rifky.prakoso_)
[![Languages](https://img.shields.io/github/languages/count/username/repo)](https://github.com/username/repo)
[![Top Language](https://img.shields.io/github/languages/top/username/repo)](https://github.com/username/repo)

Built with the tools and technologies:

![JSON](https://img.shields.io/badge/JSON-000000?style=for-the-badge&logo=json&logoColor=white)
![Markdown](https://img.shields.io/badge/Markdown-000000?style=for-the-badge&logo=markdown&logoColor=white)
![C](https://img.shields.io/badge/C-000000?style=for-the-badge&logo=c&logoColor=white)

Remote server menggunakan program C ke server mongodb dengan konfigurasi master dan slave. Berikut langkah-langkahnya:

## 1. Siapkan Resources:
* Windows (10/11) sebagai Host
* [Virtual Box](https://www.virtualbox.org/wiki/Downloads)
* ISO Ubuntu Server sebagai Primary
* ISO Ubuntu Server sebagai Secondary
* [MSYS2](https://github.com/msys2/msys2-installer/releases/download/2025-02-21/msys2-x86_64-20250221.exe)
* [CMake](https://github.com/Kitware/CMake/releases/download/v4.0.2/cmake-4.0.2-windows-x86_64.msi)
* [Mongo C Driver](https://github.com/mongodb/mongo-c-driver/releases/download/2.0.1/mongo-c-driver-2.0.1.tar.gz)

## 2. Instalasi
### 1. Ubuntu Server

Untuk OS Linux yang support docker: 
- [Ubuntu Server Noble 24.04 (LTS)](https://ubuntu.com/download/server/thank-you?version=24.04.2&architecture=amd64&lts=true)
- [Ubuntu Server Jammy 22.04 (LTS)](https://ubuntu.com/download/server/thank-you?version=22.04.5&architecture=amd64&lts=true)

Disini saya gunakan Ubuntu Server Jammy 22.04 (LTS). Setup menggunakan VirtualBox dengan konfigurasi jaringan ter-Bridge

### 2. Install MSYS2 MINGW64 (dengan GCC 15.1.0).
### 3. Install Mongo C Driver
Unduh dan Ekstrak Source Code MongoDB C Driver:
- Ekstrak ke direktori di Windows, misalnya C:\mongo-c-driver-2.0.1.
- Buka MSYS2 MINGW64 Shell di Windows Anda.

Navigasi ke Direktori Source Code Driver:
```
cd /c/mongo-c-driver-2.0.1
```
Buat Direktori Build Bersih:
```
mkdir cmake-build
cd cmake-build
```
Jalankan CMake untuk Mengkonfigurasi Build:
```
cmake -G "MinGW Makefiles" -DCMAKE_INSTALL_PREFIX=/c/mongodb_c_driver ..
```
Kompilasi Driver:
```
mingw32-make
```
Instal Driver:
```
mingw32-make install
```

## 3. Install Docker pada masing-masing Ubuntu
### 1. Set up Docker's apt repository.
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
### 2. Install the Docker packages.
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
### 3. Verify that the installation is successful by running the hello-world image:
```
sudo docker run hello-world
```
This command downloads a test image and runs it in a container. When the container runs, it prints a confirmation message and exits.

## 4. Install Mongo 4.4 pada masing-masing Ubuntu
```
sudo docker pull mongo:4.4
```
## 5. Menjalankan Kontainer MongoDB untuk Replica Set
Lihat IP Address dari masing-masing server:
```
hostname -I
```
### Di Sever Linux Primary/Master
Jalankan Kontainer mongo_primary
```
sudo docker run -d \
  --name mongo_primary \
  -p GANTI_IP_PRIMARY/MASTER:27017:27017 \
  -v mongo_primary_data:/data/db \
  mongo:4.4 \
  --replSet "rs0" --bind_ip_all
```
(GANTI_IP_PRIMARY/MASTER dengan IP Address server)

### Di Server Linux 2 Secondary/Slave
Jalankan Kontainer mongo_secondary
```
sudo docker run -d \
  --name mongo_secondary \
  -p GANTI_IP_SECONDARY/SLAVE:27017:27017 \
  -v mongo_secondary_data:/data/db \
  mongo:4.4 \
  --replSet "rs0" --bind_ip_all
```
(GANTI_IP_SECONDARY/SLAVE dengan IP Address server)

Verifikasi: Di kedua server, jalankan 
```
sudo docker ps
``` 
untuk memastikan kontainer `mongo_primary` dan `mongo_secondary` berjalan.

## 6. Inisiasi Replica Set
Langkah ini dilakukan 'HANYA' dari Server Linux 1 (Primary/Master).
1. Masuk ke Shell MongoDB di mongo_primary:
```
sudo docker exec -it mongo_primary mongo
```
2. Inisiasi Replica Set:
Di dalam shell mongo, jalankan perintah berikut menggunakan IP Address Anda:
```
rs.initiate(
  {
    _id: "rs0",
    members: [
      { _id: 0, host: "GANTI_IP_PRIMARY/MASTER" },
      { _id: 1, host: "GANTI_IP_SECONDARY/SLAVE" }
    ]
  }
)
```
Tunggu hingga prompt berubah menjadi `rs0:PRIMARY>`.

3. Verifikasi Status:
Masih di shell mongo pada mongo_primary:
```
rs.status()
```
Pastikan kedua anggota (IP_PRIMARY/MASTER:27017 dan IP_SECONDARY/SLAVE:27017) muncul, satu sebagai PRIMARY dan satu lagi sebagai SECONDARY, keduanya dengan `health: 1`.

## 7. Menyiapkan Proyek Program C di Windows
1. Buat dan Masuk ke Direktori Proyek Baru(Anda bisa menggunakan direktori lainnya):
Buka MSYS2 MINGW64 Shell dan masuk. Contoh:
```
cd /c/Users/rifky/Documents/ProyekMongoReplicaSet
```
2. Clone Repo Ini
```
git clone https://github.com/Rifkyrahmat2006/Master-Slave-Server.git
```
## 8. Mengkompilasi Program C
1. Pastikan Anda Masih Berada di Direktori Proyek. Contoh:
(/c/Users/rifky/Documents/ProyekMongoReplicaSet) di MSYS2 MINGW64 shell.
2. Jalankan Perintah Kompilasi GCC:
```
gcc kirim_data_final.c -o kirim_data_final.exe \
    -I/c/mongodb_c_driver/include/mongoc-2.0.1 \
    -I/c/mongodb_c_driver/include/bson-2.0.1 \
    -L/c/mongodb_c_driver/lib \
    -lmongoc2 -lbson2 \
    -lws2_32 -lsecur32 -lcrypt32 -lbcrypt -ldnsapi -lzstd
```
Jika berhasil, `kirim_data_final.exe` akan dibuat.

## 9. Menyiapkan File DLL yang Diperlukan
Pastikan anda masih di direktori yang sama (/c/Users/rifky/Documents/ProyekMongoReplicaSet) 
```
# Cek nama file DLL yang benar di /c/mongodb_c_driver/bin/
cp /c/mongodb_c_driver/bin/libmongoc2.dll . 
cp /c/mongodb_c_driver/bin/libbson2.dll .
# Salin DLL dependensi dari MinGW
cp /mingw64/bin/libzstd.dll .
cp /mingw64/bin/zlib1.dll .
```
## 10. Menjalankan Program C di Windows
1. Pastikan Replica Set MongoDB Anda Berjalan dan sehat.
2. Jalankan Program dari MSYS2 MINGW64 Shell: Di direktori tempat anda meng-clone repo ini
```
./kirim_data_replset.exe data_input_replset.json
```
3. Periksa Output Program: Anda akan melihat pesan koneksi dan (semoga) pesan sukses.

## 11. Memverifikasi Data di MongoDB Replica Set
1. Hubungkan ke PRIMARY MongoDB: Di terminal Server Linux PRIMARY/MASTER:
```
sudo docker exec -it mongo_primary mongo
```
2. Periksa Data: Di dalam shell mongo:
```
use NAMA_DATABASE_ANDA; // Ganti dengan nama database yang Anda gunakan di C
db.NAMA_COLLECTION_ANDA.find().pretty(); // Ganti dengan nama collection
```
Anda seharusnya melihat data baru. Anda juga bisa terhubung ke SECONDARY/SLAVE (Server Linux 2, kontainer mongo_secondary) jalankan `rs.slaveOk()`, lalu periksa datanya menggunakan perintah diatas untuk melihat replikasi.



 
