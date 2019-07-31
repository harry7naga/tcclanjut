Katakoda Docker Tutorial
Docker Network
Tutorial ini menjelaskan bagaimana membuat network untuk docker container. Sehingga container satu dengan container yang lain dapat saling berhubungan. Sebelumnya, untuk membuat jaringan untuk docker container ada dua pendekatan yaitu link dan network. Di tutorial akan menggunakan network. Pertama, buat network beserta nama network. Contoh :

docker network create backend-network
Network sudah terbuat, bisa di cek menggunakan :

docker network ls
Maka akan muncul list network yang tersedia, termasuk network yang baru saja dibuat.

Selanjutnya untuk menspesifikasi container agar menggunakan network tertentu. Bisa di define saat membuat container. Contoh :

docker run -d --name=redis --net=backend-network redis
Perintah di atas membuat container redis menggunakan network: backend-network. Option --net mendefinisikan network yang digunakan. Untuk mengetest koneksi antara dua container, gunakan perintah :

docker run --net=backend-network alpine ping -c1 redis
Dimana, network yang digunakan adalah backend-network. Container yang di test adalah alpine mengakses jaringan ke redis. Dengan list satu line saja, -c1 mendefinisikan jumlah test yang dilakukan.

Dalam satu container docker memungkinkan dapat memiliki lebih dari satu network. Contoh, buat network baru :

docker network create frontend-network
Cek network baru di list :

docker network ls
Lalu tambahkan network baru ke dalam container redis :

docker network connect frontend-network redis
Inspect container redis, untuk melihat network sudah di embed atau belum :

docker inspect redis
Lihat di bagian, "Netwok" seharusnya sudah ada backend-network & frontend-network.

Kemudian buat container baru yang menggunakan network yang sama :

docker run -d -p 3000:3000 --net=frontend-network katacoda/redis-node-docker-example
Cek jaringan container redis-docker-example :

curl docker:3000
Selanjutnya, memberi alias container. Tapi sebelum itu buat dulu container baru :

docker network create frontend-network2
Lalu beri nama aliasnya :

docker network connect --alias db frontend-network2 redis
Jadi saat ingin mengakses jaringan dari container redis menggunakakn jaringan frontend-network2, dapat seperti berikut :

docker run --net=frontend-network2 alpine ping -c1 db
Perintah diatas, dari container alpine mencoba mengakses redis dengan nama lainnya yaitu db. Karena sebelumnya sudah di deklarasikan alias untuk redis adalah db, seharusnya test ping berhasil.

Selanjutnya mencoba men-disconnect network yang sudah di embed ke suatu container. Sebelumnya bisa dilihat dulu list network yang tersedia :

docker network ls
Setelah tau list network yang tersedia, pilih salah satu network untuk di inspect, misal :

docker network inspect frontend-network 
Pada bagian "Containers", terlihat container apa saja yang menggunakakn network tersebut. Misal akan men-disconnect container redis dari frontend-network :

docker network disconnect frontend-network redis
Maka redis akan ter-disconnect dari frontend-network. Untuk memastikannya bisa di inspect lagi, frontend-network :

docker network inspect frontend-network
Lihat bagian "Containers", seharusnya redis tidak ada lagi di dalam list.

Communicating Between Containers
Di praktik ini, mencoba menghubungkan satu container dengan container lain menggunakan pendekatan yang kedua yaitu link. Jalankan dulu containernya, dengan nama yang mendeskripsikan isi dari container tersebut :

docker run -d --name redis-server redis
Untuk menggunakan link, cukup tambahkan option --link saat akan membuat container. Contohnya :

docker run --link redis-server:redis alpine ping -c 1 redis
Perintah diatas membuat link antara container redis-server / redis dengan container alpine. Sekaligus mengecek koneksi antara keduanya. Saat link terbuat, docker akan menjalankan dua hal yaitu pertama docker akan mengeset environment variabel berdasarkan container yang di link kan. Environment variabel ini berisikan informasi Port dan IP Address yang digunakan. Saat menjalankan container dapat sekaligus menampilkan environment variabel nya :

docker run --link redis-server:redis alpine env
Yang kedua, docker akan mengupdate file HOST dari container dari entri yang berasal dari source container dengan tiga nama yaitu nama asli (original), nama alias (alias), nama hash-id (hash-id). Dapat menampilkannya dengan perintah :

docker run --link redis-server:redis alpine cat /etc/hosts
Contoh aplikasi sederhana node.js yang tersambung dengan redis yang menggunakan hostname 'redis' :

docker run -d -p 3000:3000 --link redis-server:redis katacoda/redis-node-docker-example
Lalu test koneksinya :

curl docker:3000
Maka seharusnya kedua container sudah terhubung.

Selanjutnya, untuk mengakses redis CLI. Dapat menggunakan langkah yang sama seperti diatas dengan tambahan option redis-cli, seperti berikut :

docker run -it --link redis-server:redis redis redis-cli -h redis
Maka akan muncul CLI dari redis, perintah KEYS * akan menampilkan semua data yang tersimpan. Perintah QUIT, untuk keluar dari CLI.
