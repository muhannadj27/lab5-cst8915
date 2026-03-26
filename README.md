# CST8915 Lab 5 - Containerizing the Algonquin Pet Store

## Demo Video
[Click here to watch the demo](https://youtu.be/7ceDVDlGadw)

## Final Docker Compose (using Docker Hub images)
```yaml
version: '3'

services:
  rabbitmq:
    image: "rabbitmq:3-management"
    ports:
      - "5672:5672"
      - "15672:15672"
    environment:
      - RABBITMQ_DEFAULT_USER=myuser
      - RABBITMQ_DEFAULT_PASS=mypassword

  order-service:
    image: muhannadj27/order-service:latest
    ports:
      - "3000:3000"
    environment:
      - RABBITMQ_CONNECTION_STRING=amqp://myuser:mypassword@rabbitmq:5672/
      - PORT=3000
    depends_on:
      - rabbitmq

  product-service:
    image: muhannadj27/product-service:latest
    ports:
      - "3030:3030"
    environment:
      - PORT=3030

  store-front:
    image: muhannadj27/store-front:latest
    ports:
      - "80:80"
    environment:
      - VUE_APP_ORDER_SERVICE_URL=http://order-service:3000
      - VUE_APP_PRODUCT_SERVICE_URL=http://product-service:3030
    depends_on:
      - product-service
      - order-service
```

## Notes
- Used Python version for the product-service
- RabbitMQ handles messaging between order-service and the backend
- Multi-stage build used for store-front (Node.js builds, Nginx serves)
