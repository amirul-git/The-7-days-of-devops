## The 7 days of devops

> By Amirul-git

> Get taste of becoming a devops engineer with only 7 days or less. From docker, monitoring, to kubernetes and k3s. Based on writer journey on learning docker to monitoring to Kubernetes

> Note: this is the pre-beta roadmap book, expect changes in future but the big idea is the same

#### Docker

- [x] Apa itu docker
- [x] Docker image, dockerfile, dan docker container
  - [x] Docker Image
  - [x] Dockerfile
  - [x] Docker Container
- [x] Docker workflow
- [x] Kontainerisasi aplikasi
  - [x] Requirement
  - [x] Create Dockerfile
  - [x] Build Image
  - [x] Run Image
- [x] Optimasi docker image dengan multi stage build
  - [x] Problem dari docker image yang tidak teroptimasi
  - [x] Requirement
  - [x] Update dockerfile
  - [x] Build and Run
  - [x] v1 (Build) vs v2 (Multi stage build)
- [x] Docker compose
  - [x] Intro
  - [x] Struktur docker compose
  - [x] Docker compose image react

#### ECR, The Container Registry

- [x] Apa itu Container Registry
- [x] Requirement
  - [x] AWS iam, user dan permission
  - [x] AWS CLI
- [x] Push image ke ECR
- [x] Pull image dari ECR ke local
- [x] Run image yang telah di pull dari ECR di local

#### EC2, The Cloud Server

- [x] Apa itu EC2
- [x] Membuat EC2 instance
- [x] Setup `docker`, `aws cli`, login `ecr`
- [ ] Setup docker compose
- [ ] Set up aws security to port 80
- [ ] Access via browser

#### Github Action, The glue of CI/CD

- [ ] GA intro, and what the CI/CD is about
- [ ] Basic walktrhough
  - [ ] secrets, dulu bingung gimana cara buat .pem nya, bahas itu juga
- [ ] What I wish I wkno
  - [ ] don't shy to looks for someone elses implementation
- [ ] main.yaml
- [ ] explanation

#### AWS Route 53

- [ ] Route 53 Intro
- [ ] Buy domain name
- [ ] Linkup domain with route 53
- [ ] Linkup ec2 instance with route 53

#### AWS Load Balanccer

- [ ] AWS load balancer intro
- [ ] Create another app, this time we use simple node server
- [ ] Create load balancer
- [ ] Set up load balancer based on the group
- [ ] Link up load balancer with domain
- [ ] Make it secure with HTTPS

> Ooops, we had a new problem, aplikasi kita dapat banyak pengunjung dan kita harus setup semunya, server, dll dengan cepat, what should we do?, orang yang ngerti infra-nya juga masih cuti liburan ke kalimantan jadi gk bisa dihubungi sama sekali

> Tenang, kan sudah ada automation yang sudah dibuatin oleh infra-eng nya

#### Infra automation with Terraform

- [ ] Terraform intro
- [ ] Terraform sturcture
- [ ] What i wish i know
  - [ ] variable
  - [ ] how to read the docs
  - [ ] don't shy to copy
- [ ] Terraform workflow
- [ ] Write .tf
- [ ] Create a new ec2 instance

#### Config automation with Ansible

- [ ] Ansible Intro
- [ ] Connect ansible with aws, add aws role to create instances
- [ ] Ansible playbook
- [ ] What I wish I know
  - [ ] Just use another person plyabook if it exist
  - [ ] Apply ansible
- [ ] Perbaharui load balancer untuk terhubung ke instance yang baru

#### Logging the server resource with Prometheus - node exporter

- [ ] Intro to node exporter - prometheus
- [ ] Fast setup with docker
- [ ] Setup docker compose in server
- [ ] Your task, automate this one with ansible

#### Monitoring with Grafana

- [ ] Intro to grafana
- [ ] Fast setup with docker
- [ ] Connect and monitor with prometheus

### Kubernetes

- [ ] Kubernetes intro and use minikube
- [ ] How should we learn it? get taste of it as soon as possible
- [ ] The big picture of kubernetes. Kubernetes manifest file here
- [ ] Write the manifest file, I mean copy from docs
- [ ] Kubernetes deployments
  - [ ] Create -f \*
- [ ] Kubernetes services
- [ ] Kubernetes Ingress
- [ ] Horizontal Pods Autoscaller

#### K3S

---

# Docker

### 1. Apa itu docker

Docker adalah salah satu teknologi kontainerisasi yang memudahakan developer untuk upload aplikasinya ke server

Kenapa diklaim mudah?

Ayok kita coba bandingan dengan cara tradisional dalam mendeploy aplikasi.

Saat kita mau deploy aplikasi ke server dengan cara tradisional, kita harus setup dan install berbagai library, package, dan dependensi yang dibutuhkan oleh aplikasi di server agar aplikasi yang kita deploy itu dapat berjalan dengan baik

![Traditional method of deployment](img/1-traditional-method.png)

Kelemaahan dari cara seperti ini apa?

Kita mungkin akan mendapatkan masalah "it works on my machine" problem. Di localhost codenya jalan, tapi di server enggak

Nah dengan adanya docker kita akan membungkus semua keperluan yang dibutuhkan olehaplikasi kita ke dalam suatu IMAGE. dan server yang telah terinstall docker engine dapat langsung menjalankan IMAGE tersebut

![Deployment with docker](img/2-docker-deployment.png)

Melalui cara ini, kita akan terhindar dari masalah it works on my machine problem karena keperluan untuk menjalankan aplikasi sudah kita definisikan di awal dan orang lain yang ingin gabung ngerjain tinggal minta dockerfile + code app nya

> Don't worry, kita akan bahas Dockerfile dan docker Image kok kedepannya

### 2. Docker Fundamental

#### Docker Image

Docker image adalah hasil penggabungan antara source code program kita dengan berbagai dependensi yang dibutuhkan agar code aplikasi tersebut dapat dijalankan.

Dependensi tersebut dapat berupa base image tempat aplikasi akan dijalankan dan juga config seperti command apa yang dipakai untuk menjalankan aplikasi tersebut.

![Docker image](img/3-docker-image.png)

> Masih bingung tentang apa itu base image? jadi base image itu seperti jalan tempat kendaraan (app) kita akan berjalan. Misal base image node-alpine:latest adalah jalan tempat kendaraan (app) bertipe node.js akan dijalankan

> Linux OS + App environtment (example, node.js) = base image

> App + Dependesinya yang dibutuhkan agar app bisa berjalan = Docker image

#### Dockerfile

Dockerfile adalah file berisi script yang kita tulis untuk membuat docker image. Sebelumnya kan kita sudah tau bahwa docker image itu sebenarnya adalah source code app kita + dependensi yang diperlukan agar app kita tersebut dapat berjalan, nah untuk membuat docker image tersebut disini kita harus membuat dockerfile-nya

![Docker image](img/4-dockerfile.png)

Dockerfile sendiri terbagi ke dalam 3 struktur besar.

1. Dimana harus dijalankan (base image)
2. Dimana letak source code appnya di local lalu mau di taruh dimana pada base image (code setup)
3. bagaimana cara menjalankan aplikasinya (run)

```
###### base image ######

FROM node-alpine:18

###### base image ######

### code setup ###

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

###### code setup ######

###### run ######

EXPOSE 4000

CMD ["npm", "run", "dev"]

###### run ######

```

#### Docker Container

Docker container adalah docker image yang telah dijalanakan. kita bisa membuat banyak container dari suatu docker image

![Docker Container](img/5-docker-container.png)

Masih bingung? coba bayangkan kalau docker image adalah gudang penyimpanan kendaraan, maka docker container adalah salah satu kendaraan yang telah dijalankan keluar gudang

![Docker Container](img/6-docker-container-2.png)

Masih bingung juga? ayok kita coba praktek aja langsung ya, tapi sebelum itu kita perlu tau dulu bagaimana cara kita bekerja dengan docker (docker workflow)

## Docker Workflow

Ada 2 workflow (proses kerja) yang bisa kita terapkan dalam menggunakan docker. Workflow production (code nya selesai dulu semua lalu buat dockerfile), Workflow hybrid (dockerfile dan code dibuat secara bersamaan dan diubah sesuai kebutuhan)

#### 1. Production

Workflow production itu mengutamakan code nya agar selesai dulu, setelah itu kita baru membuat dockerfile sesuai dengan kebutuhan kode app kita.

![Workflow production](img/7-workflow-production.png)

#### 2. Hybrid

Workflow hybrid berfokus pada perkembangan sistem, dan umumnya diterapkan pada project baru yang memang direncanakan untuk menggunakan docker. Dalam workflow ini dockerfile dan code dibuat bersamaan dan dikembangkan sesuai kebutuhan.

![Workflow production](img/8-workflow-hybrid.png)

## Kontainerisasi Aplikasi

Setelah kita mengatahui dasar dari docker, kali ini kita akan coba menerapkan pemahaman kita untuk mengkontainerisasi lalu build docker image dari suatu aplikasi.

Untuk studi kasusnya kita akan pakai aplikasi hello world react.

#### 1. Requirement

Untuk dapat mengkontainerisasi aplikasi. kita sebelumnya harus tau dulu bagaimana cara menjalankan aplikasi tersebut di local, lalu setelah itu kita bisa mulai dan faham cara untuk menulis dockerfile nya.

Misal, karena kita akan menggunakan react, maka kita harus mengerti bagaimana menjalankan aplikasi tersebut di localhost dan apa saja dependensi yang dibutuhkan agar aplikasi tersebut dapat berjalan.

```
# menjalankan aplikasi react, port nya di 5173
npm run dev
```

Di react, dependensi aplikasi tersimpan di package.json, dan kita bisa gunakan `npm install` untuk menginstall dependensi tersebut sebelum menjalankan aplikasi agar aplikasnya bisa dijalankan.

#### 2. Create Dockerfile

Sekarang kita ngerti kalau react ternyata perlu dependensi yang harus di install, cara installnya kita udah tau, dan kita juga udah tau gimana cara menjalankan aplikasi react kita tersebut, jadi sekarang kita bisa lanjut untuk membuat dockerfilenya.

```
# task: buat file dengan nama Dockerfile

FROM node:18-alpine

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 5173

CMD [ "npm", "run", "dev" ]

```

#### 3. Build Image

Untuk membuat membuat image dari dockerfile tersebut kita bisa gunakan `docker build` command di terminal

```
docker build -t devopsbook:1.0.0 .
```

Tapi sebelum di build teman-teman harus ubah dulu `viteconfig.js` nya agar bisa menggunakan docker.

```
// dari code seperti ini
export default defineConfig({
  plugins: [react()],
});

// jadi seperti ini

export default defineConfig({
  plugins: [react()],
  server: {
    watch: {
      usePolling: true,
    },
    host: true, // needed for the Docker Container port mapping to work
    strictPort: true,
    port: 5173, // you can replace this port with any port
  }

```

Selain itu kita akan membuat file `.dockerignore` untuk file-file yang tidak seharusnya kita tambahkan ke dalam image

```
# task: buat docker ignore dan tambahkan node_module

node_modules
```

#### 4. Run Image

Dan untuk menjalan image tersebut menjadi kontainer kita bisa gunakan `docker run` command

> Sebenarnya jauh lebih mudah untuk melihat image yang kita buat dengan menggunakan app docker desktop, jadi teman-teman tidak harus menggunakan terminal. karena yang penting teman-teman ngerti dulu alur workflow nya.

```
docker run -d -p 5173:5173 devopsbook:1.0.0
```

Sekarang kita bisa mengakses aplikasi react kita yang telah terkontainerisasi tersebut di `localhost:5173`

![Workflow production](img/9-app.png)

> Tips: kalau bingung isi dockerfile untuk framework x misalnya, gpp untuk lihat dockerfile orang lain di github/google

## Optimasi docker image dengan multi stage build

#### Problem dari docker image yang tidak teroptimasi

Image yang telah kita build sebelumnya mungkin bisa digunakan untuk membuat container yang works. Akan tetapi sebenarnya image tersebut memiliki beberapa kelemahan

1. Masih menggunakan dev `npm run dev`
   Seharusnya kita tidak menggunakan `npm run dev` untuk production karena command tersebut digunakan untuk mode development
2. Size nya yang sangat besar karena belum di optimasi

Sebenarnya aplikasi yang kita buat di react itu bisa kita optimasi untuk production, dan command yang kita gunakan sebenarnya bukan `npm run dev`, tapi `npm run build` sehingga aplikasi kita akan di build sebagai `html+css+js` biasa yang teroptimasi

#### Requirement

Sebelum kita mengubah dockerfile kita akan menggunakan `npm run dev`, kita perlu tau bahwa hasil build dari command tersebut adalah `html+css+js`. Tanpa webserver sama sekali. Oleh karena itu kita perlu menggunakan baseimage tambahan sebagai webserver.

Jadi kita akan punya 2 base image, base image `node` untuk build aplikasi kita, dan base image `nginx` sebagai server yang akan serve aplikasi kita.

Oleh karena kita akan menggunakan `nginx`, kita perlu membuat file config untuk `nginx`.

```
taks: buat file /docker/nginx/conf.d/default.conf

server {
    listen 80;
    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

#### Update dockerfile

Sekarang kita memfungsikan base image `node` untuk build image nya, dan base image `nginx` untuk server yang akan serve hasil build dari aplikasi react kita ke user

```
FROM node:18-alpine as build

WORKDIR /usr/app

COPY package.json .

RUN npm install

COPY . .

RUN npm run build

FROM nginx:1.23.1-alpine

EXPOSE 80

COPY ./docker/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf

COPY --from=build /usr/app/dist /usr/share/nginx/html
```

Sekarang dockerfile kita sudah bisa digunakan untuk build image yang telah teropsimasi. kalau sebelumnya kita aplikasinya menggunakan vite server, sekarang sudah pakai nginx

#### Build dan Run

```
# build
docker build -t devopsbook:2.0.0 .

# run
docker run -d -p 5173:5173 devopsbook:2.0.0
```

#### v1 vs v2

Mungkin teman-teman bertanya-tanya kenapa sih kita repot-repot untuk melakukan multi stage build. Kan build biasa dengan `npm run dev` sudah bisa jalan aplikasinya.

Ya memang benar aplikasinya dapat berjalan, tapi sebenarnya image yang kita buat tanpa multi stage build tadi belum teroptimasi. Size image yang belum teroprimasi bisa mencapai ratusan mb sedangkan yang teroptimasi hanya puluhan mb. Selain itu multi stage build yang telah kita lakukan sudah sesuai dengan environment production karena memang seharusnya kita tidak boleh run app yang environment nya dikhusukan untuk development ke server production.

## Docker compose

#### Intro

Docker compose sebenarnya adalah suatu file script yang ditulis dan difungsikan sebagai kumpulan aturan bagi docker engine untuk menjalankan docker image.

Docker compose sendiri umumnya digunakan di server untuk running berbagai macam image dengan satu kali command maupun bisa juga digunakan di local untuk setup dev environment.

Misal kita butuh untuk menjalankan image `app` kita, butuh menjalankan image `mysql` untuk database dan juga `redis` untuk chaching.

Kalau kita lakukan lakukan docker run satu-satu kan bisa capek banget ya, oleh karena itu kita butuh docker compose agar bisa menjalankan seluruh image yang kita inginkan dengan sekali command.

#### Struktur docker compose

Docker compose ditulis dengan bahasa yaml dan penamaan yang dipakai adalah `docker-compose.yaml`

Untuk struktur docker compose sendiri dipisahkan berdasarkan service. Service sendiri berisi image apa yang dipakai beserta dengan confignya seperti port, volume, dll.

```
# Struktur docker compose
services:
  name:
    build:
    image:
    port:
  name:
    ...
```

#### Docker compose image react

Sekarang kita akan coba untuk build dan run image menggunakan docker compose

```
version: "3.8"
services:
  frontend:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - 9000:80
```

Untuk menjalan file docker compose yang telah dibuat, teman-teman bisa menggunakan command `docker compose up` untuk, dan untuk mendestroy bisa menggunakan command `docker compose down`

Untuk di server sendiri, umumnya kita sudah memiliki image yang telah di build, oleh karena itu docker compose nya tidak perlu build lagi, tapi langsung consume imagenya

```
version: "3.8"
services:
  frontend:
    image: devops-book:2.0.0
    ports:
      - 9000:80
```

# ECR, The Container Registry

### Apa itu container registry

Container registry itu bisa diibartkan sebagai git-nya docker image, berfungsi sebagai version control bagi image yang telah di build. Dan ECR (Elastic Container Registry) adalah salah satu container registry ini.

![Workflow production](img/10-container-registry.png)

Kenapa kita harus pakai container registry? agar memudahkan kita dalam melakukan penyimpanan, version control. dan distribusi image yang telah kita build. Karena kan kedepannya kita akan deploy image tersebut ke berbagai server ya.

### Requirement

Sebelum kita menggunakan ECR sebagai container registry, sebelumnya kita harus menyiapkan `role iam` yang memiliki akses untuk membuat container registry

Bagi yang belum punya account AWS bisa login aja dulu ya. Kita bisa pakai beberapa resource secara gratis selama setahunan. [**_<u>Create a new aws account</u>_**](https://aws.amazon.com/resources/create-account/)

#### AWS iam, user dan permissions

> AWS iam digunakan untuk melakukan manajemen terhadap akun aws teman-teman, seperti membuat user baru, dan memberikan hak akses kepada user tersebut.

Setelah itu teman-teman bisa mengakses halaman aws iam dan melanjutkan untuk membuat user baru serta memberikan permission (hak akses) kepada user tersebut sehingga dapat melakukan push ataupun pull image ke container registry `AmazonEC2ContainerRegistryFullAccess`

![Workflow production](img/11-user-permission.png)

#### AWS CLI

> AWS cli merupakan cli app yang digunakan untuk melakukan manajemen resource AWS melalui terminal. Kenapa adanya CLI? karena server linux tidak ada UI GUI nya, jadi mau tidak mau kita harus menggunakan terminal.

Sebelumnya pastikan AWS cli nya sudah terinstal ya. [**_<u>Install AWS CLI</u>_**](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

Lalu selanjutnya kita perlu mengatur AWS CLI untuk menggunakan user dengan permission (`AmazonEC2ContainerRegistryFullAccess`) yang telah kita buat sebelumnya dan juga meng set region yang ingin kita pakai.

> region adalah lokasi datacenter aws yang ingin teman-teman gunakan. aws datacenter sendiri tersebar di beberapa negara, dan yang paling dekat dengan indonesia adalah `ap-southeast-1` di singapura dan `ap-southeast-3` di jakarta

![Workflow production](img/12-set-awscli.png)

Berikut adalah tampilan AWS user yang telah memiliki permission `AmazonEC2ContainerRegistryFullAccess`

![User with permissions](img/13-aws-iam-permissions.png)

#### Push image ke ECR

Untuk bisa upload image ke ECR, kita perlu untuk login terlebih dahulu di terminal dengan user IAM yang telah kita buat sebelumnya. pastikan user nya sudah memiliki permission `AmazonEC2ContainerRegistryFullAccess` ya.

Selanjutnya kita akan configure terminal kita agar bertindak sebagai IAM role yang telah kita buat, kita bisa ketikkan `aws configure` dan isi semua field yang dibutukan.

![User with permissions](img/14-configure-aws-cli.png)

> AWS Access Key ID dapat di lihat di detail IAM users, tinggal klik create access key dan pilih CLI
> AWS Secret Key akan teman-teman dapatkan saat membuat AWS Access Key
> Default region name itu nama access point dari

Berikutnya kita akan coba untuk push image kita yaitu `devops-book:2.0.0` ke ECR. Tapi sebelum itu kita harus buat terlebih dahulu online reponya di page AWS ECR. teman-teman bisa akses halaman ECR lalu klik create repository

Setelah berhasil dibuat, teman-teman bisa klik view push commands button dan ikuti instruksi push image nya ke ECR

![Created a ECR repository](img/16-repository-created.png)

```
<!-- build dulu image nya jika belum -->
docker build -t devops-book:2.0.0 .

<!-- rename build image sesuai nama repo -->
docker tag devops-book:2.0.0 nama-repo-aws-ecr:latest

<!-- push docker image ke repo -->
docker push nama-repo-aws-ecr:latest
```

Saat push image berhasil teman-teman akan dapat melihat ada image baru yang terupload.

![Image created](img/17-docker-image-pushed.png)

#### Pull image dari ECR ke local

Karena kita telah berhasil upload image kita di ECR, sekarang kita bisa pull image tersebut baik ke local maupun ke server untuk di run.

![Pull image from ECR](img/18-pull-image-ecr.png)

Untuk pull image dari ECR kita bisa gunakan `docker pull nama-image-nya:tag`

Misal nama image kita di repo ecr adalah 123/devops-book:latest, maka untuk pull image tersebut dari ECR ke local adalah sebagai berikut

```
docker pull 123/devops-book:latest
```

#### Run image yang telah di pull dari ECR di local

Untuk run docker image, kita bisa gunakan `docker run nama-image:tag`

Jadi kalau nama image kita adalah `123/devops-book:latest` maka untuk run image tersebut adalah

```
docker run 123/devops-book:latest
```

Berikut adalah tampilan app yang kita run dari image yang telah kita pull dari ECR sebelumnya.

![Pull image from ECR](img/19-run-image-in-local.png)

# ECR, The Container Registry

#### Apa itu EC2

EC2 (Elastic Compute Cloud) merupakan server virtual. Jadi ibaratnya kita memiliki komputer yang dapat kita jadikan apapun baik itu untuk serve aplikasi web, untuk training AI, ataupun untuk nambang bitcoin juga bisa loh (canda gaess, kita gk boleh nambang bitcoin pakai `ec2` ya, pelanggaran)

Umumnya kita akan menggunakan EC2 untuk serve aplikasi web maupun API, dan enaknya kita bisa milih beragam spesifikasi yang cocok untuk kebutuhan kita dan harga paling murah itu sekitar $3+, free satu tahun kalau teman-teman cuma pengen test-test ombak dan kalau mau lebih murah lagi teman-teman bisa pakai `spot instance`

#### Membuat `ec2` instance

Untuk membuat `ec2` baru teman-teman bisa search `ec2` di search bar aws nya teman-teman, klik aja. Lalu saat sudah di dashboard `ec2` klik launch instance

![Pull image from ECR](img/20-launch-instances.png)

Lalu di bagian OS, bisa pilih ubuntu aja dulu, jangan aneh aneh, kenapa pilih ubuntu? karena yang paling familiar :)

![Pull image from ECR](img/21-chose-ubuntu.png)

Terus teman-teman buat dulu ya keypair-nya, namanya bebas aja gpp dan formatnya pilih yang .pem dan typenya rsa. key pair yang kita buat ini akan ke pakai untuk akses server melalui `ssh`

![Pull image from ECR](img/22-key-pair.png)

Sip, itu dulu yang perlu kita set, teman-teman bisa lanjut create instancenya. `ec2` instance yang udah dibuat akan muncul di dashboard `ec2` ya dan untuk akses nya teman-teman bisa akses melalui built-in terminal nya.

Tapi lebih nyamannya kita akan pakai terminal kita sendiri aja ya, file .pem tadi akan jadi key untuk ssh kita. step penggunannya sebenarnya sudah diberikan. teman-teman tinggal klik tombol connect dan pilih ssh client

![Pull image from ECR](img/23-terminal-ssh.png)

Berikutnya kita akan lanjut untuk setup `ec2` instance kita tadi agar bisa jalanin docker dan run image react kita, yeay.

#### Setup `docker`, `aws cli`, login `ecr` di `ec2 instance`

Sekarang kan kita udah punya `ec2` instance untuk cloud server kita ya, nah cloud server ini dalamnya masih kosongan gaess, isinya cuma os ubuntu-server yang kita pilih diawal pembuatan tadi.

Agar kita jalankan aplikasi react terkontainerisasi yang telah kita push di `ecr`, kita perlu install dan setup beberapa hal ini. kita perlu install `docker`, install `aws cli`, login dengan `iam role` yang telah kita buat dan pull image kita tadi ke `ec2` instance. Terakhir kita akan buat `docker compose` agar run/stop aplikasinya lebih mudah.

kita jadikan todolist aja kali ya biar gampang :), nanti teman-teman bisa centang deh apa aja yang selesai dan belum selesai

- [ ] Install `docker`
- [ ] Install `aws cli`
- [ ] Login ke aws cli dengan `iam` yang sudah kita buat
- [ ] Login ke `ecr` agar bisa pull image
- [ ] Pull image react kita dari `ecr`
- [ ] Buat `docker compose`
- [ ] Run container melalui `docker compose`

#### Install `docker`

Ok, kita mulai dari install `docker` nya ya, teman-teman bisa search di google `install docker engine` lalu klik yang mengarah ke dokumentasi dockernya aja ya. jangan lupa pilih linux dan variannya ubuntu. ikuti step by step nya dari docs.

> Jangan sampai salah ya gess, yang akan kita pakai disini adalah docker engine, bukan docker desktop.

Hapus package yang kemungkinan bikin conflict

```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

Setup docker apt

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

```

Install docker package

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Cek apakah instalasi kita berhasil dengan run image hello world nya

```
sudo docker run hello-world

```

Berikut message yang muncul setelah kita berhasil run image hello-world

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete
Digest: sha256:ac69084025c660510933cca701f615283cdbb3aa0963188770b54c31c8962493
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

```

Yeay, docker sudah berhasil teristall :)

#### Install `aws cli`

karena kita akan berinteraksi dengan produk cloud aws, maka kita juga perlu install aws cli di cloud ya gess. [Install aws cli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

Sebelum install `aws cli` kita perlu perlu install unzip terlebih dahulu

```
sudo apt install unzip
```

Lalu kita bisa lanjut install aws cli

```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Setelah itu kita bisa login ke iam role kita di aws cli, agar kita bisa pull image react kita

#### Setup `aws cli` sampai pull image dari `ecr`

Kita perlu mengatur `aws cli` agar menggunakan `iam role` yang telah kita buat sebelumnya sehingga kita bisa pull image react dari repo `ecr`

```
aws configure
```

Input semua data yang diperlukan, dan kita bisa coba pull image kita dari `ecr`

Untuk pull image kita dari `ecr` ke `ec2` instance, kita harus login ke ecr dulu ya

> untuk commandnya, teman-teman bisa akses halaman push image dan ambil yang bagian login :)

```
aws ecr get-login-password --region ap-southeast-1 | sudo docker login --username AWS --password-stdin 123.dkr.ecr.ap-southeast-1.amazonaws.com
```

Setelah berhasil login, bisa lanjut pull deh image react nya.

```
docker pull 123.dkr.ecr.ap-southeast-1.amazonaws.com/devops-book:latest
```

Setelah proses pull selesai teman-teman bisa cek image yang teman-teman miliki

```
sudo docker images
```

![Pull image from ECR](img/24-success-pull.png)
Voila, sekarang image react kita yang dari `ecr` sudah ter-pull di `ec2`

#### Membuat `docker-compose`
