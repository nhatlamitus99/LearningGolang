# MicroServices

### API gateway
### Message: sync message vs asyn message
### Message Queue: RabbitMQ vs Kafka
### gRPC
### CQRS
### Monitoring, Log

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

3. Message: 
  + sync message: yêu cầu service phản hồi ngay (cơ chế đông bộ) => sử dụng REST API hoặc gRPC
  + asyn message: không yêu cầu phản hồi ngay (cơ chế bất đồng bộ) => sử dụng Message Queue
  
4. Service Registry & Service Discovery:
  + Service Registry: Nơi chứa metadata của các services => tìm kiếm thông tin service thông qua service registry.
  + Service Discovery: Tìm kiếm các service đang hoạt động
    - client-side: truy vấn vào service registry để lấy thông tin service
    ![pic_2](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-11-04%20%5BMicroservice%5D%20C%C3%A1c%20kh%C3%A1i%20ni%E1%BB%87m%20ch%C3%ADnh%20trong%20microservice.png)
    - server-side: gọi đến load balancer => service registry => lấy thông tin service
    ![pic_3](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-11-04%20%5BMicroservice%5D%20C%C3%A1c%20kh%C3%A1i%20ni%E1%BB%87m%20ch%C3%ADnh%20trong%20microservice(1).png)

5. Message Queue:
  + RabbitMQ: gởi hàng nghìn messages / giây
  + Kafka: gởi hàng triệu messages / giây
