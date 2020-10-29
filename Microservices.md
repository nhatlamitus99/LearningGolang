# MicroServices

### API gateway
### Message: sync message vs asyn message
### Message Queue: RabbitMQ vs Kafka
### gRPC
### CQRS

1. Microservices:
  + Đặc điểm: Chia ứng dụng thành những services nhỏ, độc lập nhau (có server riêng, database riêng). Mỗi service đảm nhận 1 chức năng riêng biệt, testing và deploy độc lập.

2. API gateway:
  + Đặc điểm: là entry point tiếp nhận request từ client sau đó forward đến service đích vì client không thể truy cập trực tiếp đến mỗi service.
