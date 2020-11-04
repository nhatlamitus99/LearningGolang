# MicroServices

### API gateway
### Message: sync message vs asyn message
### Message Queue: RabbitMQ vs Kafka
### gRPC
### CQRS

1. Microservices:
  + Đặc điểm: Chia ứng dụng thành những services nhỏ, độc lập nhau (có server riêng, database riêng). Mỗi service đảm nhận 1 chức năng riêng biệt, testing và deploy độc lập.

2. API gateway:
  + Đặc điểm: là entry point tiếp nhận request từ client sau đó forward đến service đích vì các service chỉ có thể truy cập nội bộ (service gọi lẫn nhau hoặc api gateway gọi service, client không thể truy cập trực tiếp đến mỗi service).
  + Vai trò của API gateway:
    - proxy request, analytics
    - load balancer
    - caching
    - authentication

  ![pic_1](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-11-04%20API%20Gateway%20l%C3%A0%20g%C3%AC%20T%E1%BA%A1i%20sao%20m%E1%BB%99t%20h%E1%BB%87%20th%E1%BB%91ng%20microservices%20l%E1%BA%A1i%20c%E1%BA%A7n%20API%20Gateway%20.png)
