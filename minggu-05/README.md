# Orchestration using Docker Compose
---
Semua konfigurasi untuk membuat docker compose berada pada file docker-compose.yml. Semua perintah yang ada pada saat menjalankan
container `docker run` dapat di definisikan di file tersebut. Format file tersebut berdasarakan YAML (YEt Another Markup Language)
Format penulisan kurang lebih sebagai berikut :
```
container_name:
  property: value
    - or options
```
Dalam katakoda, terdapat skenario dimana kita memiliki aplikasi node.js yang memerlukan koneksi ke redis. Untuk memulainya pertama,
seting terlebih dahulu file `docker-compose.yml`. Definisikan container yang akan dibuat, disini untuk node.js digunakan container
bernama web yang akan mem-build container berdasarkan direktorinya. Agar lebih paham, maka membuat file `docker-compose.yml` ini
secara step-by-step. Pertama, definisikan container web dan perintah build untuk menjalankan segala properti yang ada dalam folder
tersebut. 
Identasi tepat dibawah peritah `build`, tambahkan perintah :
```
links:
    - redis
```
Perintah diatas untuk menghubungkan antara dua kontainer agar dapat saling berinteraksi satu sama lain. Pada contoh diatas,
menghubungkan antara kontainer web (aplikasi node.js) dengan kontainer database redis.
Tambahkan properti yang lain, yaitu port indentasi persis dibawah perintah `links`:
```
 ports:
    - "3000"
    - "8000"
```
Selanjutnya mendefiniskan container kedua yaitu redis yang akan disambungkan dengan container web (aplikasi node.js) :
```
redis:
  image: redis:alpine
  volumes:
    - /var/redis/data:/data
```
Untuk definisi container baru, maka indentasi sejajar dengan perintah `web`. Untuk mendefinisikan container baru tinggal
menuliskan di line baru di file yang sama. Mendefinisikan container kedua, berbeda dengan mendefinisikan container pada
container pertama sebab di container pertama membuat langsung dari current direktori yaitu memanfaatkan `Dockerfile` &
'Makefile'. Untuk container kedua ini, dibuat berdasarkan image yang sudah tersedia di docker hub.
Setelah file `docker-compose.yml` sudah terseting dengan baik, lalu jalankan docker-compose dengan perintah :
```
docker-compose up -d
```
Perintah diatas akan menjalankan docker-compose secara daemon (latar belakang) dengan option -d. Maka kedua container yang
di define sebelumnya akan berjalan secara bersamaan.
Untuk mengeceknya menggunakan perintah :
```
docker-compose ps
```
Maka akan muncul semua container yang berjalan menggunakan docker-compose tadi.
Untuk melihat semua log, menggunakan perintah :
```
docker-compose logs
```
Untuk melihat semua perintah yang ada pada docker-compose, gunakan :
```
docker-compose
```
Selanjutnya docker-compose dapat juga untuk me-manage berapa container yang dijalankan dengan perintah `scale`. Diikuti dengan nama container yang akan di scale serta jumlah containernya. Jika jumlahnya lebih besar dari jumlah container yang sedang berjalan maka akan menjalankan container yang sama sebanyak jumlah container yang di definisikan. Jika jumlahnya lebih kecil dari jumlah container yang sedang berjalan, maka container yang sedang berjalan tersebut akan di kurangi jumlahnya sesuai jumlah container yang di definisikan. Contoh perintah :
```
docker-compose scale web=3
```
Maka akan menjalankan 3 container web secara bersamaan. Jika dijalankan perintah :
```
docker-compose scale web=1
```
Maka akan kembali menjalankan 1 container web saja. Cek dengan perintah :
```
docker-compose ps
```

Selanjutnya, untuk menghentikan container ketikkan perintah :
```
docker-compose stop
```
Maka container akan berhenti, cek statusnya dengan `docker-compose ps` terlihat status menjadi `exited`.
Selanjutnya untuk menghapus container-container yang ada, ketikkan perintah :
```
docker-compose rm
```
Cek dengan `docker-compose ps`, maka container yang sebelumnya ada dilist akan hilang dari list.



# Docker Orchestration - Getting Started With Swarm Mode
---
Swarm mode memungkinkan untuk mendeploy container pada multiple docker host atau node. Menggunakan jaringan overlay untuk
mendeteksi layanan dan juga sudah terdapat load balancer di dalamnya untuk membuat sevice yang scalable.

Dalam swarm mode terdapat 3 konsep penting :
* **Node** : Sebuah Node merupakan instance dari Docker Engine yang terhubung dengan Swarm. Yang termasuk Node adalah worker ataupun manager. Manager menjadwalkan dimana suatu container akan dijalankan, sedangkan worker akan mengeksekusi perintah yang ada pada container. Secara otomatis manager juga sebagai worker.

* **Service** : Sebuah service merupakan konsep tingkat tinggi yan berhubungan dengan kumpulan dari perintah yang akan dieksekusi oleh worker. Sebagai contoh service yaitu, sebuah HTTP server yang berjalan sebagai docker container pada 3 nodes.

* **Load Balancing** : Dalam docker sudah terdapat load balancer yang berguna untuk memproses request untuk semua container dalam service tersebut.

**Initialise Swarm Mode**
Swarm mode terdapat pada Docker CLI, ketikan perintah berikut untuk mengetahui command apa saja yang bisa dijalankan dengan Swarm mode :
```
docker swarm --help
```
Maka akan muncul panduan tentang perintah Swarm mode. Lalu untuk meng-inisialisasi Swarm mode, gunakan perintah :
```
docker swarm init
```
Hasil dari perintah diatas yaitu, current node akan menjadi manager. Selain itu, akan menghasilkan sebuah token yang digunakan untuk menambah node tambahan dengan aman. Token tersebut nantinya bisa digunakakn untuk kebutuhan selanjutnya untuk men-scale cluster.

**Join Cluster**

Setelah Swarm mode aktif, maka kita dapat menambahkan node serta dapat memberi perintah untuk semua node yang ada. Jika node menghilang karena hal terntetu, misalnya crash. container yang berjalan pada host tersebut akan menjadwalkan ulang ke node yang lain untuk menggantikan node yang hilang tersebut. Penjadwalan ulang ini, memastikan agar kita tidak kehilangan kapasitas dan memberi layanan yang baik.
Pada setiap node tambahan kita harus menjoinkan node baru ke group yang sudah ada menggunakan Docker CLI. Menjoinkan node tidak bisa sembarangan harus menggunakan token yang didapat dari perintah :
 ```
 token=$(docker -H 172.17.0.31:2345 swarm join-token -q worker) && echo $token
 ```
 Contoh diatas hostnya adalah 172.17.0.31:2345, tinggal sesuaikan dengan port yan sedang kita jalankan. 
 Lalu pada host yang lain kita gunakan token tersebut untuk menjoinkan, dengan perintah :
 ```
 docker swarm join 172.17.0.31:2377 --token $token
 ```
Dengan perintah diatas node baru akan meminta access ke node manager, untuk bergabung dengan current node. By default,node manager akan mengijinkan node baru tersebut untuk bergabung. Untuk melihat semua node, gunakan perintah :
```
docker node ls
```
Kita cek apakah node baru sudah tersedia atau belum. Akan terdapat informasi: ID, HOSTNAME, STATUS, AVAILABILITY, dan MANAGER STATUS.

**Create Overlay Network**

Perintah berikut akan membuat overlay network dengan nama skynet :
```
docker network create -d overlay skynet
```
Semua container yang terdaftar dalam network tersebut dapat berkomunikasi satu sama lain, terlepas di node mana container tersebut di deploy.

**Deploy Service**

Pada contoh di katakoda, kita akan mendeploy image "katacoda/docker-http-server". Lalu membuat service bernama http yang akan di attach di network skynet yan sudah dibuat sebelumnya. Selanjutnya me-load balance kedua container secara bersamaan meggunakan port 80. Lalu mengirim sebuah HTTP request ke beberapa node yang ada di dalam cluster. 
Perintah membuat service :
```
docker service create --name http --network skynet --replicas 2 -p 80:80 katacoda/docker-http-server
```
Untuk melihat service sudah berjalan atau belum :
```
docker service ls
```
Karena menjalankan service akan sekaligus menjalankan container, maka cek juga container yang berjalan pada masing-masing host :
```
docker ps
```
Jika kita menggunakan HTTP request ke port public, maka akan di proses oleh kedua container. Jalankan perintah berikut :
```
curl docker
```

**Inspect State**

Dengan feature Service, kita dapat mengecek kondisi dari cluster dan apilkasi yang sedang berjalan. Untuk melihat list semua task yang berkaitan dengan Service tersebut dengan asumsi setiap task adalah container, gunakan perintah :
```
docker service ps http
```

Untuk melihat detail dan konfigurasi dari Service :
```
docker service inspect --pretty http
```

Untuk melihat task apa yang sedang dikerjakan oleh suatu node :
```
docker node ps self
```
Self pada perintah diatas adalah manager atau node leadernya.

Menggunakan ID dari sebuah node, kita dapat meng-query suatu hosts :
```
docker node ps $(docker node ls -q | head -n1)
```

**Scale Service**

Dengan Service kita dapat men-scale banyak instance dari sebuah task yang berjalan di berbagai cluster. 
Sebelumnya kita memiliki load-balance container yang berjalan, jalankan perintah :
```
curl docker
```
Lalu kita akan men-scale Service "http" untuk berjalan di 5 container, jalankan perintah :
```
docker service scale http=5
```
Di masing-masing host kita akan melihat tambahan node yang sudah terbuat :
```
docker ps
```
Load balancer juga akan sekaligus terupdate. Sekarang request akan dijalankan oleh container-container baru. Dapat kita coba dengan perintah :
```
curl docker
```
