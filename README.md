# Cài đặt và triển khai Hyperledger Fabric trên Swarm

🏨 Cài đặt và triển khai Hyperledger Fabric trên Swarm



## Nội dung
* [Giới thiệu](#Giới-thiệu)
* [Môi trường](#Môi-trường)
* [Cài đặt](#Cài-đặt)
* [Tài liệu tham khảo](#Tài-liệu-tham-khảo)
* [Bugs và các vấn đề](#Bugs-và-các-vấn-đề)
* [Tác giả](#Tác-giả)
* [Giấy phép](#Giấy-phép)

## Giới thiệu
Khi triển khai một mạng Hyperledger thường cần tương tác giữa nhiều máy với nhau. Các component của mạng hoạt động trong các container của docker. Chính vì vậy mà thường sẽ không tương tác được với nhau. Nên bắt buộc phải triển khai bằng Docker Swarm hoặc Kubernetes


## Môi trường

Cài đặt môi trường theo hướng dẫn:
* [Installing pre-requisites](https://hyperledger.github.io/composer/latest/installing/installing-prereqs)
* [Installing the development environment](https://hyperledger.github.io/composer/latest/installing/development-tools)
* [Yarn](https://yarnpkg.com/lang/en/docs/install/)
* [Updating components](https://hyperledger.github.io/composer/latest/installing/update-dev-env)
* [Uninstalling components](https://hyperledger.github.io/composer/latest/installing/uninstall-dev-env)

## Cài đặt

1. Clone this git:
```
  git clone https://github.com/blockchainvn/hyperledger-fabric-swarm.git
```

2. Mở các cổng cho Swarm:
```
    firewall-cmd --permanent --zone=public --add-port=2377/tcp --add-port=7946/tcp --add-port=7946/udp --add-port=4789/udp
  firewall-cmd --reload
```

3. Khởi tạo một swarm:
```
    docker swarm init
```

4. Thêm các máy khác vào swarm: 
Nếu chỉ triển khai trên một máy thì không cần lệnh này
  
Các máy khi tham gia vào một swarm thì có thể có vai trò là manager hoặc worker.

Lấy token để join máy vào:

a. Nếu là join vào với vài trò manager
```
docker swarm join-token manager
```
b. Nếu là join vào với vài trò worker
```
docker swarm join-token worker
```
* Join máy vào swarm:
```
docker swarm join --token <đoạn token sinh ra từ lệnh ở trên>
```

1. Tạo một overlay network:
```
docker network create --attachable --driver overlay --subnet=10.200.1.0/24 hyperledger-ov
```
6. Khởi tạo các file config:
Mục đích của bước này là tạo ra các file .yaml để khởi chạy các docker. 
```
yarn genConfig -domain example.com -Kafka 3 -Orderer 2 -Zookeeper 3 -Orgs 2 -Peer 1 -Tag :latest
```
Các thông số trên có thể thay đổi. Ví dụ khởi tạo một mạng gồm 2 orgs, mỗi org gồm 2 peer. Khởi tạo như vậy là để tương tự với mạng [Hyperledger Fabric First Network](https://hyperledger-fabric.readthedocs.io/en/release-1.4/build_network.html) trong các demo để thuận tiện cho các bài hướng dẫn khác. 
```
yarn genConfig -domain example.com -Orderer 1 -Orgs 2 -Peer 2 -Tag :latest
```

7. Khởi tạo channel artifacts:
```
 yarn genArtifacts -c <CHANNEL-NAME> -d <DOMAIN-NAME> -o <NUM-ORGS>
```
Cụ thể cho ví dụ trên:
```
 mkdir -p channel-artifacts
 yarn genArtifacts -c mychannel -d example.com -o 2
```
8. Khởi chạy:
* On Master host,
  * If you choose to configure with multiple orderers,

```
  yarn start
```

* If you choose to configure in 'solo' mode (without kafka, zookeeper). Chúng ta sẽ thực hiện lệnh này cho ví dụ trên.

```
  yarn start-solo
```

* I recommend you to check services' status after every deploy.

```
  docker service ls
```
* Khởi chạy lệnh khởi động portainer
  ```
  yarn portainer
  ```
  Đây là lệnh dùng để khởi động portainer, sẽ được truy cập thông qua localhost:9000. Ứng dụng này sẽ hỗ trợ quản lý docker tối ưu và dễ sử dụng hơn các câu lệnh.


9. Khởi chạy lệnh ở container cli của hyperledger fabric:
```
docker exec -it $(docker ps --format "table {{.ID}}" -f "label=com.docker.stack.namespace=hyperledger-cli" | tail -1) bash
```

10. Khởi chạy script để join vào mạng:
Đây là script của byfn (build first fabric network) được tùy biến để phù hợp với mạng ví dụ này:
```
./script/script.sh
```
Lệnh 10 chỉ chạy khi đã chạy xong lệnh số 9.

11. Quản lý mạng này với Hypereledger Composer:
Truy cập tài liệu này [đây](https://hyperledger.github.io/composer/latest/tutorials/deploy-to-fabric-multi-org)

## Tài liệu tham khảo

* [Tài liệu hướng dẫn Hyperledger Composer](https://hyperledger.github.io/composer/latest/introduction/introduction.html)
* [Tài liệu hướng dẫn Hyperledger Fabric](https://hyperledger-fabric.readthedocs.io/en/release-1.3/)
* [Video demo](https://youtu.be/hy-zgeZ8Es0)
* [Bài gốc](https://github.com/blockchainvn/hyperledger-fabric-swarm)

## Bugs và các vấn đề

Bạn có câu hỏi hoặc vấn đề với dự án? [Đặt câu hỏi](https://github.com/phuctu1901/create-fabric-on-swarm/issues) tại đây trên Github.

## Ảnh chụp màn hình


## Tính năng đang phát triển


## Tác giả

* **Nguyễn Phúc Tú** - [phuctu1901](https://github.com/phuctu1901)


## Giấy phép

[Apache](https://www.apache.org/licenses/LICENSE-2.0)