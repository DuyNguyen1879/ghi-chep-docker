# 1. Giới thiệu

- Khi mới bắt đầu với Docker bạn có thể bị nhầm lẫn giữa một rừng các công cụ với tên kiểu Docker-something. 
Có một công cụ Docker Client, một thứ gọi là Docker Engine, rồi lại có một công cụ tên là Docker Machine, rồi build cluster với Docker Swarm, 
rồi lại đóng gói multi-container với Docker Compose và ti tỉ thứ khác... Mới nghe đã choáng hết cả đầu rồi

# 2. Docker Engine

Đây là công cụ mặc định khi cài đặt package docker. nó giúp chúng ta build các container. Bản chất docker là một ứng dụng client-server. 
Docker client nói chụng với Docker Engine thông qua một RESTfull API, để thực thi các lệnh build, run, ship một container. Ví dụ:
```sh
# docker version
Client:
 Version:      17.03.0-ce
 API version:  1.26
 Go version:   go1.7.5
 Git commit:   60ccb22
 Built:        Thu Feb 23 10:57:47 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.03.0-ce
 API version:  1.26 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   60ccb22
 Built:        Thu Feb 23 10:57:47 2017
 OS/Arch:      linux/amd64
 Experimental: fals
 
 # echo -e "GET /version HTTP/1.0\r\n" |sudo netcat -U /var/run/docker.sock
HTTP/1.0 200 OK
Api-Version: 1.26
Content-Type: application/json
Docker-Experimental: false
Server: Docker/17.03.0-ce (linux)
Date: Mon, 20 Mar 2017 04:06:55 GMT
Content-Length: 225

{"Version":"17.03.0-ce","ApiVersion":"1.26","MinAPIVersion":"1.12","GitCommit":"60ccb22","GoVersion":"go1.7.5","Os":"linux","Arch":"amd64","KernelVersion":"4.4.0-59-generic","BuildTime":"2017-02-23T10:57:47.731437667+00:00"}
```

ở đây, lệnh docker chính là docker client. khi ta gõ lệnh docker version thì bản chất là docker client gọi tới API của docker engine với phương thức GET 
thông qua unix-socket. Điều này có nghĩa là ta hoàn toàn có thể giao tiếp với docker engine thông qua một công cụ khác, miễn là nó biết cách gọi tới
các API của docker engine. Các API được cung cấp tại [đây](https://docs.docker.com/engine/api/v1.20/)

Có 4 đối tượng lớn trong thế giới của docker engine và tất cả chúng đều có ID.
- *Images*: image được sử dụng để đóng gói ứng dụng và các thành phần phụ thuộc của ứng dụng. 
Image có thể được lưu trữ ở local hoặc trên một registry. 
Registry là một dịch vụ giúp tổ chức và cung cấp các kho chứa các image
- *Container*: container là một running instance của một Docker Images. Nếu thấy quá khó hiểu bạn có thể liên tưởng nó với một virtual machine về mặt chức năng
- *Network*: Cung cấp một private network mà chỉ tồn tại giữa container và host. Bắt đầu từ phiên bản 1.09 thì private network có thể mở rộng trên multi-host
- *Volume*: volume được thiết kể để lưu trữ các dữ liệu độc lập với vòng đời của container

Chúng ta có thể nhìn mối liên kết giữa các thành phần qua hình dưới:

![container-relation](/images/container-relation.png)

Ngoài ra, docker engine còn cho phép load plugin của bên thứ 3 để mở rộng chức năng. Như Flocker là một volume-plugin cung cấp cơ chế volume cho 
multi-host hoặc Weave là một network-plugin cho phép tạo một mạng ảo, kết nối các docker container trên nhiều host lại với nhau, cho phép tự động
discovery các ứng dụng.

# 2. Distribution tools

Công cụ giúp chúng ta lưu trữ và quản lý các docker image. có thể tự dụng một private registry, chúng ta sử dụng các công cụ sau:
- *Docker Registry*: một open source image distribution tool giúp lưu trữ và quản lý image
- *Docker Trusted Registry*: một công cụ trả phí, có thêm giao diện quản lý
- *Docker Hub*: mặc định docker client sẽ sử dụng docker hub nếu không có registry nào được cấu hình.

# 3. Orchestration tools

### Docker Machine

Một công cụ giúp dễ dàng tiếp cập docker. Docker Machine sẽ tạo các máy ảo và cài docker engine lên chúng, cuối cùng cấu hình docker client để giao 
tiếp với docker engine một cách bảo mật.

### Docker Swarm

là một công cụ giúp chúng ta tạo ra một clustering Docker. Nó giúp chúng ta gom nhiều Docker Engine lại với nhau và 
ta có thể "nhìn" nó như duy nhất một virtual Docker Engine. Tất nhiên là bất cứ công cụ nào có thể giao tiếp với 
Docker Engine thì cũng sẽ giao tiếp với Docker Swarm bình thường theo đúng chuẩn Docker API, và tất nhiên là trong suốt. 
Một cụm Swarm có thể được cấu hình và deploy thông qua Docker-Machine. Và theo như bài test của Docker thì họ có thể scale ra 30.000 container 
trên 1000 node AWS với chỉ một Swarm Manager. Bạn có thể xem nó tương tự như Kubernetes, nhưng theo Docker thì công cụ Swarm của họ lightweight 
và nhanh hơn gấp 5 lần so với Kubernetes.

### Docker Compose

là một orchestration tool giúp ta tạo multi-container một cách dễ dàng (lưu ý đối tượng ở đây là container chứ không phải host hay engine nhé). 
Tất nhiên Docker Compose cũng có thể hoạt động với Swarm để có thể tạo multi-container. Mặc định thì Docker Compose sẽ tạo một single-network 
cho các ứng dụng của bạn và các container có thể truy cập lẫn nhau thông qua mạng này. Bạn có thể xem ví dụ tại 
[đây](https://github.com/docker/example-voting-app/blob/master/docker-compose.yml). Đại khái nó sẽ giúp ta tạo container service vote, container redis, 
container worker, container db, container result nodejs. Với Docker Compose ta sẽ không cần tạo thủ công từng container như trước nữa

# 4. Management tools

Phần này mình sẽ không nói nhiều, vì 3 công cụ ở đầy đều là công cụ trả phí, mình thì không có nhu cầu với các công cụ này nên nếu bạn tò mò 
thì có thể tìm hiểu với các keyword như ở dưới

- Docker Universal Control Plane
- Docker Datacenter
- Docker Cloud

# 5. Tổng kết

Docker cho phép chúng ta build, ship, và run bất kỳ ứng dụng nào ở bất kỳ đâu.

- *Build*: Docker Engine (`docker buid -t`) và Docker Compose (`docker-compose build`, dành cho multi-container)
- *Ship*: Docker Registry, Docker Trusted Registry, Docker Hub (SaaS)
- *Run*: Docker Engine (`docker run`) , Docker Swarm (cụm Docker Engines), Docker Compose (`docker-compose up`)
- *Manage*: Docker Universal Control Plane và Docker Cloud (CaaS)

# Tham khảo
- [https://kipalog.com/posts/He-sinh-thai-Docker](https://kipalog.com/posts/He-sinh-thai-Docker)
