# MASTER-SLAVE-SERVER

*by Rifky D.R. Prakoso*


[![Instagram](https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://instagram.com/rifky.prakoso_)

Built with the tools and technologies:

![JSON](https://img.shields.io/badge/JSON-000000?style=for-the-badge&logo=json&logoColor=white)
![JAVA](https://img.shields.io/badge/Java-000000?style=for-the-badge&logo=openjdk&logoColor=white)
![MAVEN](https://img.shields.io/badge/MAVEN-000000?style=for-the-badge&logo=apachemaven&logoColor=white)
![MONGODB](https://img.shields.io/badge/-MongoDB-000000?style=for-the-badge&logo=mongodb&logoColor=white)
![UBUNTU SERVER](https://img.shields.io/badge/Ubuntu-000000?style=for-the-badge&logo=Ubuntu&logoColor=white)

Remote server menggunakan program Java ke server mongodb dengan konfigurasi master dan slave. Berikut langkah-langkahnya:

## 1. Siapkan Resources:
* Windows (10/11) sebagai Host
* [Virtual Box](https://www.virtualbox.org/wiki/Downloads)
* [ISO Ubuntu Server 20.04.6 sebagai Primary](https://kartolo.sby.datautama.net.id/ubuntu-cd/focal/ubuntu-20.04.6-live-server-amd64.iso)
* [ISO Ubuntu Server 20.04.6 sebagai Secondary](https://kartolo.sby.datautama.net.id/ubuntu-cd/focal/ubuntu-20.04.6-live-server-amd64.iso)
* [JDK Adoptium 8](https://adoptium.net/download/)
* [JRE Adoptium 8](https://adoptium.net/download/)
* [Maven](https://dlcdn.apache.org/maven/maven-3/3.9.10/binaries/apache-maven-3.9.10-bin.tar.gz)

## 2. Instalasi
### 1. Ubuntu Server
Setup menggunakan VirtualBox dengan konfigurasi jaringan ter-Bridge. Buat dengan menggunakan openssh, untuk mempermudah saat proses pengerjaan kita akan gunakan perintah `ssh` pada windows powershell yang berfungsi untuk meremote OS ubuntu server dari OS client(Windows)

### 2. Install Java

### 3. Install Maven
Ekstrak ke folder yang diinginkan (Contoh: C:\apache-maven-3.9.10)
copy path yang di bin (C:\apache-maven-3.9.10\bin), lalu buka aplikasi "Edit the system environtment variables", cari 'path' lalu pilih 'new' dan paste path yang disalin dan klik OK

## 3. Setup Project
buka CMD di path yang diinginkan (Contoh: D:\Maven), lalu jalankan:
```
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.5 -DinteractiveMode=false
```
Anda akan melihat bahwa tujuan generate membuat direktori dengan nama yang sama dengan artifactId. Ubahlah direktori tersebut.
```bash
cd my-app
```
buka vscode dengan command:
```
code .
```

## 3. Install Mongo 4.4 pada masing-masing Server
### Ikuti satu per satu command dibawah ini:
```
curl -fsSL https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
```
This command will return `OK` if the key was added successfully

### Kemudian:
```
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
```

### After running this command, update your server’s local package index so APT knows where to find the mongodb-org package:
```
sudo apt update
```

### Following that, you can install MongoDB:
```
sudo apt install mongodb-org -y
```

### Then check the service’s status. Notice that this command doesn’t include .service in the service file definition. systemctl will append this suffix to whatever argument you pass automatically if it isn’t already present, so it isn’t necessary to include it:
```
sudo systemctl status mongod
```

### Run the following systemctl command to start the MongoDB service:
```
sudo systemctl start mongod.service
```

### After confirming that the service is running as expected, enable the MongoDB service to start up at boot:
```
sudo systemctl enable mongod
```

### Referensi:
```
https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04
```

## 5. Setup MongoDB untuk Replica Set
### Lihat IP Address dari masing-masing server:
```
hostname -I
```

### Edit IP & Replicatiton pada kedua server
```
sudo nano /etc/mongod.conf
```
Cari
`# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1`
lalu tambahkan IP dari `hostname -I` menjadi seperti:
`# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1, 192.168.x.x
`
Cari
`#replication`
Kemudian ubah menjadi
```
replication:
  replSetName: rs0
```
lalu untuk exit jalankan `ctrl+x` dan `y` lalu `ENTER`

Agar `mongod.conf` berjalan, kita perlu menjalankan satu per satu command berikut:
```
sudo systemctl restart mongod
```
```
mkdir -p /data/db0
```
```
sudo mongod --replSet rs0 --dbpaath /data/db0 --port 27017
```
```
sudo systemctl restart mongod
```
```
sudo mongod --replSet rs0 --dbpaath /data/db0 --port 27017
```
```
sudo systemctl restart mongod
```

## 6. Inisiasi Replica Set
Langkah ini dilakukan 'HANYA' dari Server Linux 1 (Primary).
1. Masuk ke Shell MongoDB di primary:
```
mongo
```
2. Inisiasi Replica Set:
Di dalam shell mongo, jalankan perintah berikut menggunakan IP Address Anda:
```
rs.initiate(
  {
    _id: "rs0",
    members: [
      { _id: 0, host: "GANTI_IP_PRIMARY" },
      { _id: 1, host: "GANTI_IP_SECONDARY" }
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
Pastikan kedua anggota (IP_PRIMARY:27017 dan IP_SECONDARY:27017) muncul, satu sebagai PRIMARY dan satu lagi sebagai SECONDARY, keduanya dengan `health: 1`.

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



 
