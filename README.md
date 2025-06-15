# MASTER-SLAVE-SERVER

*by Rifky D.R. Prakoso & Arzaq Vincent P. Prasetyo*


[![Instagram](https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://instagram.com/rifky.prakoso_)
[![Instagram](https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://instagram.com/notorfix2)

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
* [JDK Adoptium 8](https://adoptium.net/temurin/releases/?os=any&arch=any&version=8)
* [JRE Adoptium 8](https://adoptium.net/temurin/releases/?os=any&arch=any&version=8)
  ![Adoptium](https://i.imgur.com/9PqBLlWl.png)
* [Maven](https://dlcdn.apache.org/maven/maven-3/3.9.10/binaries/apache-maven-3.9.10-bin.tar.gz)

## 2. Instalasi
### 1. Ubuntu Server
Setup menggunakan VirtualBox dengan konfigurasi jaringan ter-Bridge. Buat dengan menggunakan openssh, untuk mempermudah saat proses pengerjaan kita akan gunakan perintah `ssh` pada windows powershell yang berfungsi untuk meremote OS ubuntu server dari OS client(Windows)

### 2. Install Java

### 3. Install Maven
Ekstrak ke folder yang diinginkan (Contoh: C:\apache-maven-3.9.10)
copy path yang di bin (C:\apache-maven-3.9.10\bin)
![path bin](https://i.imgur.com/pEss87wl.png)
lalu buka aplikasi "Edit the system environment variables"
![Edit the system environment variables](https://i.imgur.com/AyRbGYtl.png)
pilih Environment Variables
![Environment Variables](https://i.imgur.com/uvt07rRl.png)
cari 'path' dan klik 2 kali
![path](https://i.imgur.com/RgHjkFgl.png)
lalu pilih 'new' 
![new](https://i.imgur.com/4spVmscl.png)
paste path yang disalin dan klik OK
![paste](https://i.imgur.com/K7HSnIBl.png)

## 3. Setup Project
buka CMD di path yang diinginkan (Contoh: D:\Maven), lalu jalankan:
```bash
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.5 -DinteractiveMode=false
```
Anda akan melihat bahwa tujuan generate membuat direktori dengan nama yang sama dengan artifactId. Ubahlah direktori tersebut.
```bash
cd my-app
```
buka vscode dengan command:
```bash
code .
```
buka file `pom.xml`, kemudian ganti semua dengan yang ada dibawah ini:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>my-app</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.release>17</maven.compiler.release>
  </properties>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.junit</groupId>
        <artifactId>junit-bom</artifactId>
        <version>5.11.0</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <dependencies>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <scope>test</scope>
    </dependency>
    <!-- Optionally: parameterized tests support -->
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-params</artifactId>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.mongodb</groupId>
      <artifactId>mongodb-driver-sync</artifactId>
      <version>4.11.0</version>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <!-- clean lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#clean_Lifecycle -->
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.4.0</version>
        </plugin>
        <!-- default lifecycle, jar packaging: see https://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_jar_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.3.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.13.0</version>
          <configuration>
              <verbose>true</verbose>
              <fork>true</fork>
              <executable><!-- Paste Path nya Disini --></executable>
              <compilerVersion>1.3</compilerVersion>
          </configuration>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>3.3.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-jar-plugin</artifactId>
          <version>3.4.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>3.1.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>3.1.2</version>
        </plugin>
        <!-- site lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#site_Lifecycle -->
        <plugin>
          <artifactId>maven-site-plugin</artifactId>
          <version>3.12.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-project-info-reports-plugin</artifactId>
          <version>3.6.1</version>
        </plugin>

        <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <version>3.7.1</version>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
     
           <archive>
            <manifest>
              <addClasspath>true</addClasspath>
              <mainClass>com.mycompany.app.App</mainClass>
            </manifest>
          </archive>
         
        </configuration>
        <executions>
          <execution>
            <id>make-assembly</id> <!-- this is used for inheritance merges -->
            <phase>package</phase> <!-- bind to the packaging phase -->
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```


Setelah itu kembali ke CMD tadi, lalu install dependensi dengan menjalankan command berikut:
```bash
mvn install
```
Jika terjadi error setelah menjalankan `mvn install` yang bertuliskan
```
No compiler is provided in this environment. Perhaps you are running on a JRE rather than a JDK?
```
pertama jalankan perintah berikut:
```
where javac
```
Perintah tersebut berfungsi untuk menacri dimana loaksi compiler java. setelah itu copy output dari perintah tersebut.
Setelah itu kembali ke file `pom.xml` dan cari bari yang bertuliskan berikut:
```xml
<executable><!-- Paste Path nya Disini --></executable>
```
kemudian ubah isi dari executable menjadi path yang dicopy tadi, contoh:
```xml
<executable>C:\Program Files\Eclipse Adoptium\jdk-8.0.452.9-hotspot\bin\javac.exe</executable>
```
Setelah itu jalankan ulang perintah `mvn install`

Jika sudah selesai, kemudian jalankan command berikut untuk membuat jar file:
```bash
mvn clean compile assembly:single
```
Tunggu hingga muncul `BUILD SUCCESS`, lalu jalankan command berikut:
```bash
java -jar target\my-app-1.0-SNAPSHOT-jar-with-dependencies.jar
```
Jika muncul `Hello World`, maka anda telah berhasil men-setup Java


## 3. Install Mongo 4.4 pada masing-masing Server
### Ikuti satu per satu command dibawah ini:
```bash
curl -fsSL https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
```
This command will return `OK` if the key was added successfully

### Kemudian:
```bash
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
```

### After running this command, update your server’s local package index so APT knows where to find the mongodb-org package:
```bash
sudo apt update
```

### Following that, you can install MongoDB:
```bash
sudo apt install mongodb-org -y
```

### Then check the service’s status. Notice that this command doesn’t include .service in the service file definition. systemctl will append this suffix to whatever argument you pass automatically if it isn’t already present, so it isn’t necessary to include it:
```bash
sudo systemctl status mongod
```

### Run the following systemctl command to start the MongoDB service:
```bash
sudo systemctl start mongod.service
```

### After confirming that the service is running as expected, enable the MongoDB service to start up at boot:
```bash
sudo systemctl enable mongod
```

### Referensi:
```bash
https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04
```

## 5. Setup MongoDB untuk Replica Set
### Lihat IP Address dari masing-masing server:
```bash
hostname -I
```

### Edit IP & Replicatiton pada kedua server
```bash
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
```bash
replication:
  replSetName: rs0
```
lalu untuk exit jalankan `ctrl+x` dan `y` lalu `ENTER`

Agar `mongod.conf` berjalan, kita perlu menjalankan satu per satu command berikut:
```bash
sudo systemctl restart mongod
```
```bash
mkdir -p /data/db0
```
```bash
sudo mongod --replSet rs0 --dbpath /data/db0 --port 27017
```
```bash
sudo systemctl restart mongod
```
```bash
sudo mongod --replSet rs0 --dbpath /data/db0 --port 27017
```
```bash
sudo systemctl restart mongod
```

## 6. Inisiasi Replica Set
Langkah ini dilakukan 'HANYA' dari Server Linux 1 (Primary).
1. Masuk ke Shell MongoDB di primary:
```bash
mongo
```
2. Inisiasi Replica Set:
Di dalam shell mongo, jalankan perintah berikut menggunakan IP Address Anda:
```js
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
```js
rs.status()
```
Pastikan kedua anggota (IP_PRIMARY:27017 dan IP_SECONDARY:27017) muncul, satu sebagai PRIMARY dan satu lagi sebagai SECONDARY, keduanya dengan `health: 1`.

## 7. Menyiapkan Program Java di Client(Windows)
### Ubah semua App.java, menjadi seperti di bawah ini:
```java
package com.mycompany.app;

import com.mongodb.MongoClientSettings;
import com.mongodb.MongoException;
import com.mongodb.client.*;
import com.mongodb.client.model.InsertOneOptions;
import org.bson.Document;
import static com.mongodb.client.model.Filters.regex;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.*;

public class App {
	String PORT_MASTER = "192.x.x.x:27017"; // IP_MASTER:PORT
	String PORT_SLAVE = "192.x.x.x:27017"; // IP_SLAVE:PORT 

	public void insert() {
        	String uri = "mongodb://" + PORT_MASTER + "," + PORT_SLAVE;
        	String dbName = "test"; // nama database;
        	String collName = "data_apalah"; // nama collection/table;

        	try (MongoClient mongoClient = MongoClients.create(uri)) {
            		MongoDatabase database = mongoClient.getDatabase(dbName);
            		MongoCollection<Document> collection = database.getCollection(collName);

			Document doc = new Document();
			doc.append("name", "buidanto");
			doc.append("nim", "123456");

			try {
				collection.insertOne(doc, new InsertOneOptions());
				System.out.println("Insert data success");
			} catch (MongoException me) {
                		System.err.println("Insert failed: " + me.getMessage());
            		}
        	} catch (Exception e) {
			System.err.println("[ERROR]: " + e.getMessage());
		}
    	}

    	public static void main(String[] args) {
		App app = new App();
		app.insert();
    	}
}
```
2. Menjalankan Program
Setelah mengubah isi `App.java`, jalankan ulang perintah dibawah ini untuk membersihkan cache sebelumnya dan men-compile ulang project
```bash
mvn clean compile assembly:single
```
Kemudian jalankan jar file:
```bash
java -jar target\my-app-1.0-SNAPSHOT-jar-with-dependencies.jar
```
Apabila setelah dijalankan memunculkan output `Insert data success` maka program telat berhasil dijalankan dan data berhasil di masukan kedalam mongo server

## 8. Memverifikasi Data di MongoDB Replica Set
Periksa Data: Di dalam shell mongo:
```
use NAMA_DATABASE_ANDA; // Ganti dengan nama database yang Anda gunakan di Java
db.NAMA_COLLECTION_ANDA.find().pretty(); // Ganti dengan nama collection
```
Anda seharusnya melihat data baru. Anda juga bisa terhubung ke SECONDARY (Server Linux 2) jalankan `rs.slaveOk()` atau `rs.secondaryOk()`, lalu periksa datanya menggunakan perintah diatas untuk melihat replikasi.



 
