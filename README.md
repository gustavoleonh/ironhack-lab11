# ironhack-lab11

Lab Overview:
-------------

This lab guides participants through the theoretical design of a secure and scalable web application architecture using AWS services. It will explore the integration of core AWS services, IAM configurations, and strategies for optimizing resource management and cost.

Objectives:
-----------

By the end of this lab, participants will be able to:

1.  Outline an AWS cloud architecture using core services like EC2, S3, and VPC.
2.  Design IAM roles and policies that ensure security and compliance with the principle of least privilege.
3.  Propose resource management strategies to optimize performance and cost.


Cloud architecture for eCommerce platform:
-----------

![Ecomerce](https://github.com/gustavoleonh/ironhack-lab11/assets/116121540/a3d249a9-beb5-49cf-ad6d-994158538ee8)

#### Rational:

The architecture involves deploying microservices on Amazon Elastic Kubernetes Service (EKS). Each microservice handles a specific domain such as products, orders, and users, and each has its own database. Communication between services happens both synchronously (using REST or gRPC) and asynchronously (using messaging queues).

#### Key Components

1.  **Amazon EKS (Elastic Kubernetes Service)**

    -   **Microservices Deployment**: Each microservice (products, orders, users) runs in its own Kubernetes pod.
    -   **Scaling and Management**: EKS manages the scaling and orchestration of containers.
2.  **Amazon RDS (Relational Database Service) or DynamoDB**

    -   Each microservice has its own dedicated database instance. For example:
        -   **Products Service**: Could use Amazon RDS (PostgreSQL/MySQL) for relational data or DynamoDB for high throughput.
        -   **Orders Service**: Amazon RDS for complex transactions and joins.
        -   **Users Service**: DynamoDB for fast access and scalability or Amazon RDS for relational data.
3.  **Amazon API Gateway**

    -   Serves as the entry point for external clients (web/mobile apps) to communicate with the microservices.
    -   Manages and routes incoming API requests to the appropriate microservice.
4.  **Amazon SQS (Simple Queue Service)**

    -   **Asynchronous Communication**: Used for decoupled and reliable message passing between microservices. For example, the Orders service can place an order message in a queue that the Inventory service consumes to update stock levels.
5.  **Amazon SNS (Simple Notification Service)**

    -   **Event Broadcasting**: Used for broadcasting events (e.g., new order placed) to multiple subscribers (other services or external systems).
6.  **Amazon CloudWatch**

    -   **Monitoring and Logging**: Tracks the performance and health of the microservices, and logs errors and other important metrics.
7.  **Amazon IAM (Identity and Access Management)**

    -   Manages access and permissions for services and users.
8.  **Amazon VPC (Virtual Private Cloud)**

    -   Provides isolated network environments for the microservices ensuring secure communication.

#### Service Interaction

1.  **Products Service**

    -   **Database**: Stores product details and inventory information.
    -   **Synchronous Communication**: Exposes APIs for product catalog management.
    -   **Asynchronous Communication**: Publishes events when product inventory is updated.
2.  **Orders Service**

    -   **Database**: Manages orders, payment details, and order status.
    -   **Synchronous Communication**: Exposes APIs for order creation, updates, and status checks.
    -   **Asynchronous Communication**: Listens to product availability events and payment confirmation.
3.  **Users Service**

    -   **Database**: Stores user profiles, authentication data, and preferences.
    -   **Synchronous Communication**: Exposes APIs for user registration, login, and profile management.
    -   **Asynchronous Communication**: Publishes user-related events (e.g., new user registration).

#### Example Workflow

1.  **Order Placement**

    -   A user places an order via the frontend.
    -   The frontend calls the Orders Service API via the API Gateway.
    -   The Orders Service validates the order, updates its database, and sends a message to the Inventory Service via SQS.
    -   The Inventory Service updates the product stock and confirms the update.
    -   The Orders Service updates the order status and notifies the user.
2.  **User Registration**

    -   A new user registers via the frontend.
    -   The frontend calls the Users Service API via the API Gateway.
    -   The Users Service creates a new user entry in its database.
    -   An event is published to SNS to notify other services of the new user.
3.  **Product Update**

    -   An admin updates product details.
    -   The frontend calls the Products Service API via the API Gateway.
    -   The Products Service updates its database and publishes an event.
    -   The Orders Service subscribes to this event and updates any pending orders accordingly.

#### Security Considerations

-   **IAM Roles and Policies**:Each component requires an specific IAM role and also cognito ensures that only allowed microservices could get connected to other one.
-   **VPC Security Groups**: Control network access to and from services.
-   **API Gateway**: Use throttling and rate limiting to protect against abuse.

#### Monitoring and Logging

-   **CloudWatch Logs**: Collect logs from each service for debugging and auditing.
-   **CloudWatch Alarms**: Set up alarms for critical metrics like CPU usage, memory usage, and error rates.
-   **X-Ray**: For tracing requests and understanding the flow across microservices.

This architecture ensures scalability, flexibility, and resilience by leveraging AWS's managed services and best practices for microservices deployment in the cloud.