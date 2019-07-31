>Docker - Orchestration using Docker Compose

Saat bekerja dengan banyak docker, bisa jadi sulit untuk mengelola permulaan bersama dengan konfigurasi variabel dan tautan. 

Untuk mengatasi masalah ini, Docker memiliki alat yang disebut Docker Compose untuk mengelola orkestrasi, peluncuran, docker.

> Docker Orchestration - Getting Started With Swarm Mode

n 1.12, Docker memperkenalkan Mode Swarm. Mode Swarm memungkinkan kemampuan untuk menyebarkan kontainer di beberapa host Docker, menggunakan jaringan overlay untuk penemuan layanan dengan penyeimbang beban bawaan untuk meningkatkan skala layanan.

Mode Swarm dikelola sebagai bagian dari Docker CLI, menjadikannya pengalaman yang mulus bagi ekosistem Docker.

Konsep Kunci
Mode Docker Swarm memperkenalkan tiga konsep baru yang akan kami eksplorasi dalam skenario ini.

Node: Node adalah turunan dari Mesin Docker yang terhubung ke Swarm. Node adalah manajer atau pekerja. Manajer menjadwalkan docker mana yang akan dijalankan. Pekerja melaksanakan tugas. Secara default, Manajer juga adalah pekerja.

services: service adalah konsep tingkat tinggi yang berkaitan dengan kumpulan tugas yang akan dieksekusi oleh pekerja. Contoh service adalah Server HTTP yang berjalan sebagai Docker Container pada tiga node.

Load Balancing: Docker menyertakan penyeimbang beban untuk memproses permintaan di semua kontainer dalam service.


Yang akan kita bahas dari [Skenario Katacoda](https://www.katacoda.com/) adalah sebagai berikut :

 * [Orchestration using Docker Compose](01-Orchestration_using_Docker_Compose.md) 

 Link Skenario  [Katacoda](https://www.katacoda.com/courses/docker/11)
   Orchestration using Docker Composee
  * [ Orchestration - Getting Started With Swarm Mode](02-Orchestration_Getting_Started_With_Swarm_Mode.md) 

  Link Skenario [Katacoda](https://www.katacoda.com/courses/docker/getting-started-with-swarm-mode) Getting Started With Swarm Mode
