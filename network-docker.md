# 1. Tổng quan

Docker có một hệ thống network riêng cho từng container, không hệ liên quan tới hệ thống network của máy tính ngoài.

Khi sử dụng container, chúng ta thường chỉ quan tâm chính tới ứng dụng trong container có chạy không, còn nhiều mục khác rất ít khi để ý tới.
Đơn giản là network. Bạn có thể xem bằng lệnh sau:
```sh
root@ldapclient1:~# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
dfc51b4498cf        bridge              bridge              local
51088c073ed0        host                host                local
249730a893fd        none                null                local
```

Để xem nhiều thông tin hơn, ta sử dụng lệnh
```sh
root@ldapclient1:~# docker network inspect dfc51b4498cf
[
    {
        "Name": "bridge",
        "Id": "dfc51b4498cf15cc64dfc38aa47f8fa525684159212bed8c5dd8b3f1a21cf51a",
        "Created": "2017-03-16T10:30:16.095571331+07:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {
            "3951ded2d13ffb239af3414663b63839dd56ce40b8699f23e603092e48b9347c": {
                "Name": "my-redis",
                "EndpointID": "6a81c707422592dac02af2777a9049a9a58d23f410f8da0b909b49d18b9ecfff",
                "MacAddress": "02:42:ac:11:00:04",
                "IPv4Address": "172.17.0.4/16",
                "IPv6Address": ""
            },
            "47718f81a5b284359a8ed0158aca0616cdd142604e4e2c08f6106ce35062f85d": {
                "Name": "tannt-ubuntu1",
                "EndpointID": "b7c6f1a6803a85d70dc381964a18886646addf801ac2be71afed002fd1e5d5d3",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```

Native, docker hỗ trợ 03 loại network như đã chạy bằng lệnh `docker network ls`

# Tham khảo
- [https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-getting-started](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-getting-started)
- [https://kipalog.com/posts/Toi-da-dung-Docker-nhu-the-nao](https://kipalog.com/posts/Toi-da-dung-Docker-nhu-the-nao)