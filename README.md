## The 7 days of devops

> By amirul

> Get taste of becoming a devops engineer with only 7 days or less. From docker, monitoring, to kubernetes and k3s.

> Note: this is the pre-beta roadmap book, expect changes in future but the big idea is the same

#### Docker

- [x] Apa itu docker
- [x] Docker image, dockerfile, dan docker container
  - [x] Docker Image
  - [x] Dockerfile
  - [x] Docker Container
- [ ] docker workflow
- [ ] kontainerisasi react app
- [ ] build
- [ ] docker compose
- [ ] optimasi image dengan multi stage build
- [ ] comparison antara image yang teroptimze dengan yang tidak

#### ECR, The Container Registry

- [ ] Quick intro ECR
- [ ] AWS iam + role + cli
- [ ] Push image ke ECR
- [ ] Pull image ke local
- [ ] Run image dari ECR di local

#### EC2, The Cloud Server

- [ ] EC2 quick intro
- [ ] Update aws iam role
- [ ] Spin up a new EC2 Instance
- [ ] Set up deps, docker, aws cli, login ecr
- [ ] Run docker coompose
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

### Docker

#### 1. Apa itu docker

Docker adalah salah satu teknologi kontainerisasi yang memudahakan developer untuk upload aplikasinya ke server dengan MUDAH

Kenapa diklaim mudah?

Ayok kita coba bandingan dengan cara tradisional dalam mendeploy aplikasi.

Saat kita mau deploy aplikasi ke server dengan cara tradisional, kita harus
setup dan install berbagai library, package, dan dependensi yang dibtutuhkan oleh aplikasi
di server agar aplikasi yang kita deploy itu dapat berjalan dengan baik

![Traditional method of deployment](img/1-traditional-method.png)

Kelemaahan dari cara seperti ini apa?

kita mungkin akan memperoleh masalah "it works on my machine" problem. di localhost codenya jalan, tapi di server enggak

Nah dengan adanya docker kita akan membungkus semua keperluan yang dibutuhkan olehaplikasi kita ke dalam suatu IMAGE. dan server yang telah terinstall docker engine dapat langsung menjalankan IMAGE tersebut

![Deployment with docker](img/2-docker-deployment.png)

Melalui cara ini, kita akan terhindar dari masalah it works on my machine problem
karena keperluan untuk menjalankan aplikasi sudah kita definisikan di awal
dan orang lain yang ingin gabung ngerjain tinggal minta dockerfile + code app nya

> don't worry, kita akan bahas dockerfile dan Image kok kedepannya

#### 2. Docker Image and Docker Container

##### Docker Image

Docker image adalah hasil penggabungan antara source code program kita dengan berbagai dependensi yang dibutuhkan agar code tersebut dapat dijalankan

Dependensi tersebut dapat berupa base image tempat aplikasi akan dijalankan dan juga config seperti command apa yang dipakai untuk menjalankan aplikasi tersebut

![Docker image](img/3-docker-image.png)

> Masih bingung tentang apa itu base image? jadi base image itu seperti jalan tempat kendaraan (app) kita akan berjalan. misal base image node-alpine:latest adalah jalan tempat kendaraan (app) bertipe node.js akan dijalankan

> Linux OS + App environtment (exemple, node.js) = base image

> App + Dependesinya yang dibutuhkan agar app bisa berjalan = Docker image

##### Dockerfile

Dockerfile adalah file berisi script yang kita tulis untuk membuat docker image. sebeluunya kan kita sudah tau bahwa docker image itu sebenarnya adalah source code app kita + dependensi yang diperlukan agar app kita tersebut dapat berjalan, nah untuk membuat docker image tersebut disini kita harus membuat docker file

![Docker image](img/4-dockerfile.png)

Dockerfile sendiri terbagi ke dalam 3 struktur besar.

1. Dimana harus dijalankan (base image)
2. Dimana letak source code appnya di local lalu mau di taruh dimana pada base image
3. bagaimana cara menjalankan aplikasinya

```

FROM node-alpine:18

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 4000

CMD ["npm", "run", "dev"]

```

##### Docker Container

Docker container adalah docker image yang telah dijalanakan. kita bisa membuat banyak container dari suatu docker image

![Docker Container](img/5-docker-container.png)

Masih bingung? coba bayangkan kaalu docker image adalah gudang penyimpanan kendaraan,
maka docker container adalah salah satu kendaraan yang telah dijalankan keluar gudang

![Docker Container](img/6-docker-container-2.png)

Masih bingung juga? ayok kita coba praktek aja langsung ya
