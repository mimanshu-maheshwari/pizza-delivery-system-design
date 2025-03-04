# PIZZA DELIVARY SYSTEM (GLOBAL SYSTEM)

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
graph TD

  %% Clients
  A[User] -->|Search / Order| B["Frontend (Web/Mobile)"]
  B -->|API Calls| C[API Gateway & Load Balancer]

  %% Microservices
  subgraph Core Microservices
    D[Auth Service] -->|JWT OAuth2| DB1["User DB (PostgreSQL)"]
    E[Order Service] -->|Transactions| DB2["Order DB (PostgreSQL)"]
    F[Payment Service] -->|Process Payments| DB3["Payment DB (PostgreSQL)"]
    G[Restaurant Service] -->|Menu & Info| DB4["Restaurant DB (DynamoDB)"]
    H[Delivery Service] -->|Real-time Tracking| DB5["Delivery DB (MongoDB)"]
    I[Notification Service] -->|Send SMS/Push| DB6["Notification Logs (NoSQL)"]
  end

  %% Asynchronous Event Processing
  subgraph Event-Driven System ["(Kafka / RabbitMQ)"]
    E -->|Publish Order_Created| K[Kafka Event Bus]
    K -->|Consume Payment_Processed| F
    K -->|Consume Order_Ready| H
    H -->|Delivery_Assigned Event| K
    K -->|Trigger Notification| I
  end

  %% External Services
  subgraph External Services
    P["Payment Gateway (Stripe/PayPal)"]
    Q["SMS/Email (Twilio, SendGrid)"]
    R["Maps & Geo Services (Google Maps)"]
    S["CDN (Cloudflare, AWS CloudFront)"]
  end

  %% Caching Layer
  subgraph Caching & Performance
    T[Redis / Memcached]
    U["Elasticsearch (Full-text Search)"]
  end

  %% Connections
  C --> D
  C --> E
  C --> F
  C --> G
  C --> H
  C --> I
  F --> P
  I --> Q
  G --> R
  B --> S
  DB4 --> T
  DB2 --> T
  G --> U

```

## **1. API Gateway & Load Balancer**

- **API Gateway (Kong / AWS API Gateway / Nginx)**
  - Handles authentication, rate limiting, request routing, and caching.
- **Global Load Balancer (AWS ALB / GCP Load Balancer)**
  - Distributes traffic across multiple regional instances.

---

## **2. Frontend Clients**

- **Mobile App (React Native / Flutter)**
- **Web App (React / Angular)**
- **Admin Dashboard (React / Angular)**
- **Third-Party API Clients (Partner Integration for Orders)**

---

## **3. Core Microservices**

### **User & Authentication**

- **User Service** (Manages user profiles, authentication, and authorization)
  - **Auth0 / Firebase Auth / AWS Cognito**
  - PostgreSQL for user data storage.
  - JWT / OAuth2 for authentication.

### **Order Management**

- **Order Service** (Handles order creation, updates, and tracking)
  - PostgreSQL (ACID compliance for transactions).
  - Uses Kafka for event-driven updates.

### **Payment Processing**

- **Payment Service** (Handles transactions, refunds, and fraud detection)
  - Integrates **Stripe, PayPal, Razorpay, UPI**.
  - Ensures compliance with **PCI DSS**.
  - Uses **Kafka** for asynchronous order updates.

### **Restaurant & Menu Management**

- **Restaurant Service** (Stores restaurant details and menus)
  - DynamoDB / MongoDB for fast access.
  - Elasticsearch for search functionality.

### **Kitchen & Order Fulfillment**

- **Kitchen Service** (Manages order preparation, estimated time, and workflow)
  - Uses WebSockets for real-time order status updates.

### **Delivery & Rider Management**

- **Delivery Service** (Manages riders and order tracking)
  - Uses **Firebase Firestore / MongoDB** for real-time updates.
  - WebSockets / gRPC for real-time communication.

### **Notification & Customer Engagement**

- **Notification Service** (Sends push, SMS, email notifications)
  - Uses **Twilio, Firebase Push, SendGrid**.

### **Review & Feedback**

- **Review Service** (Manages customer ratings and reviews)
  - Uses PostgreSQL or DynamoDB.

### **Search & Recommendation**

- **Search Service** (Helps users find restaurants and dishes)
  - **Elasticsearch** for full-text search.
  - **Redis / Memcached** for caching.

### **Loyalty & Rewards**

- **Loyalty Service** (Handles discounts, coupons, and rewards)
  - PostgreSQL / DynamoDB for storage.

### **Customer Support**

- **Support Service** (Chatbots, helpdesk, and complaint resolution)
  - Uses **Zendesk / ChatGPT API**.

---

## **4. Backend Infrastructure**

### **Database & Storage**

- **PostgreSQL / MySQL** (For transactional data: orders, users, payments)
- **DynamoDB / Cassandra** (For restaurant data, menu caching)
- **MongoDB / Firestore** (For real-time delivery tracking)
- **Elasticsearch** (For fast search queries)
- **Redis / Memcached** (For caching frequently accessed data)
- **BigQuery / Snowflake** (For analytics & reporting)

### **Event-Driven Architecture**

- **Apache Kafka / RabbitMQ** (For asynchronous communication)
  - Order events, payment processing, restaurant updates.

### **Distributed Transaction Management**

- **Saga Pattern (Camunda / Temporal / Orchestration Engine)**

### **Caching & Performance Optimization**

- **Redis / Memcached** (Reduces DB load)
- **CDN (Cloudflare / AWS CloudFront)** (For static assets)

### **Rate Limiting & Security**

- **API Gateway Throttling**
- **DDoS Protection (Cloudflare / AWS Shield)**
- **Data Encryption (AES-256, TLS 1.2/1.3)**

---

## **5. DevOps & Deployment**

### **Containerization & Orchestration**

- **Kubernetes (K8s) / Docker** (Manages microservices)
- **Helm Charts** (For Kubernetes deployment)

### **Continuous Integration & Deployment (CI/CD)**

- **GitHub Actions / Jenkins / ArgoCD** (Automates deployments)

### **Logging & Monitoring**

- **Prometheus & Grafana** (Metrics collection & visualization)
- **ELK Stack (Elasticsearch, Logstash, Kibana)** (Log analysis)
- **Jaeger / OpenTelemetry** (Distributed tracing)

---

## **6. Third-Party Integrations**

- **Payment Gateways:** Stripe, PayPal, Razorpay, UPI.
- **SMS & Email Notifications:** Twilio, SendGrid.
- **Geo Services:** Google Maps API, OpenStreetMap.
- **Analytics & Reporting:** Snowflake, AWS QuickSight.

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
erDiagram
    USER {
        string user_id PK
        string name
        string email
        string phone
        string address
    }
    ORDER {
        string order_id PK
        string user_id FK
        string restaurant_id FK
        string status
        timestamp order_time
        float total_price
    }
    RESTAURANT {
        string restaurant_id PK
        string name
        string address
        string menu_id FK
    }
    MENU {
        string menu_id PK
        string restaurant_id FK
        string items
    }
    PAYMENT {
        string payment_id PK
        string order_id FK
        string status
        string payment_method
        timestamp payment_time
    }
    DELIVERY {
        string delivery_id PK
        string order_id FK
        string rider_id FK
        string status
        timestamp estimated_time
    }
    NOTIFICATION {
        string notification_id PK
        string user_id FK
        string type
        string content
        timestamp sent_time
    }

    USER ||--o{ ORDER : places
    ORDER ||--|{ RESTAURANT : from
    RESTAURANT ||--o{ MENU : has
    ORDER ||--o{ PAYMENT : has
    ORDER ||--o{ DELIVERY : assigned_to
    USER ||--o{ NOTIFICATION : receives
```

---

### **Final Architecture Categories**

✅ **Frontend Clients** – Apps, Website, Dashboard  
✅ **Core Microservices** – Orders, Payments, Users, etc.  
✅ **Infrastructure** – Database, Messaging, API Gateway  
✅ **DevOps & Monitoring** – CI/CD, Logging, Kubernetes  
✅ **Third-Party Integrations** – Payment, Maps, Notifications

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
sequenceDiagram
    participant User
    participant API_Gateway
    participant Order_Service
    participant Payment_Service
    participant Delivery_Service
    participant Kafka
    participant Notification_Service

    User ->> API_Gateway: Place Order
    API_Gateway ->> Order_Service: Validate & Create Order
    Order_Service ->> Kafka: Publish Order_Created Event

    Kafka -->> Payment_Service: Consume Order_Created Event
    Payment_Service ->> Payment Gateway: Process Payment
    Payment Gateway -->> Payment_Service: Payment Confirmed
    Payment_Service ->> Kafka: Publish Payment_Success Event

    Kafka -->> Order_Service: Consume Payment_Success Event
    Order_Service ->> Delivery_Service: Assign Delivery
    Delivery_Service ->> Kafka: Publish Delivery_Assigned Event

    Kafka -->> Notification_Service: Consume Order & Delivery Events
    Notification_Service ->> User: Send Order & Delivery Updates
```

### **Indexing & Sharding Strategy for the Global Pizza Delivery System**

When designing a **global-scale pizza delivery system**, handling **high query loads, low-latency search, and massive datasets** is crucial. Below, I'll cover **indexing, sharding, partitioning, replication, and why specific choices are made**.

---

## **1️⃣ Indexing Strategy**

Indexing helps in **fast lookups** for frequently accessed data. Let's break it down by service.

### **📌 Order Service (PostgreSQL)**

- **Indexes**:

  - **B-Tree Index** on `order_id` (Primary Key) – Faster lookups.
  - **Hash Index** on `user_id` – Efficient filtering by user.
  - **GIN Index** on `status` – Fast lookups for `Pending`, `Completed`, etc.
  - **Partial Index** on `created_at` for last `7 days` – Optimized queries.

- **Why B-Tree?** PostgreSQL defaults to **B-Tree** because it works well for **range queries** (`BETWEEN`, `<`, `>`).
- **Why Hash Index?** Hash indexes are efficient for **exact match lookups**, such as finding all orders by a `user_id`.

### **📌 Restaurant & Menu Service (DynamoDB)**

- **Indexes**:

  - **Partition Key** → `restaurant_id`
  - **Global Secondary Index (GSI)** on `city_name`
  - **Local Secondary Index (LSI)** on `cuisine_type`
  - **Elasticsearch Sync for Full-Text Search on Menus**

- **Why GSI & LSI?**
  - **GSI** allows querying restaurants by `city_name`, helpful for geo-based lookups.
  - **LSI** allows efficient filtering by `cuisine_type` within a restaurant.
  - **Elasticsearch** enhances **fuzzy search** for menu items like `"Cheese Pizza" ~ "Cheesy Pizza"`.

### **📌 Delivery Service (MongoDB)**

- **Indexes**:

  - **2D GeoSpatial Index** on `(latitude, longitude)`
  - **Compound Index** on `rider_id, status`
  - **TTL Index** on `delivery_time` (Expire after 24 hours)

- **Why GeoSpatial Index?**
  - Helps find **nearest delivery agents** within a given radius.
  - Enables **fast searches based on real-time location updates**.
- **Why TTL Index?**
  - Automatically deletes old records to **keep DB size manageable**.

---

## **2️⃣ Sharding Strategy**

Sharding ensures the system can handle **millions of queries per second**. Here’s how we’ll distribute data.

### **📌 Order Service (PostgreSQL)**

- **Sharded by `user_id` (Range-Based Sharding)**

  - Each shard contains **orders of a range of users** (`1-10M`, `10M-20M`).
  - Reduces **hotspotting** since users place orders across different shards.
  - **Replication per region** for **fault tolerance**.

- **Alternative: Hash-Based Sharding**
  - **Pro:** Uniform distribution of load.
  - **Con:** Can’t efficiently query **"all orders of a city"**.

### **📌 Restaurant & Menu Service (DynamoDB)**

- **Sharded by `restaurant_id` (Partition Key)**

  - **Each restaurant has its menu data** stored separately.
  - **GSI on `city_name`** ensures we can query "restaurants in a city".
  - **Read replicas** in **multiple regions** ensure **faster local access**.

- **Why Not Range-Based?**
  - Restaurant names aren't **evenly distributed** (e.g., many start with "Pizza").
  - **Hashing avoids hotspots** in a distributed database.

### **📌 Delivery Service (MongoDB)**

- **Sharded by `city_name` (Geo-Sharding)**

  - Each **city has its delivery tracking database**.
  - Within each city, **GeoSpatial indexing** helps find riders.

- **Why Not Hash-Based?**
  - **Location-based lookups** would be inefficient.
  - **GeoPartitioning keeps data close to riders** and improves **real-time updates**.

---

## **3️⃣ Partitioning & Replication Strategy**

Partitioning splits large tables into smaller **manageable units**.

### **📌 Order Service (PostgreSQL)**

- **Time-Based Partitioning** (`Monthly`)

  - Orders older than **6 months** move to **cold storage (S3, Glacier)**.
  - Speeds up recent order lookups.

- **Read Replicas per Continent**
  - US, EU, Asia each have a **read-only replica** to **reduce query latency**.

### **📌 Restaurant Service (DynamoDB)**

- **Partitioned by Region**
  - US-East, US-West, Europe, Asia.
  - **Multi-active replication** ensures **restaurants can update data from any location**.

### **📌 Delivery Service (MongoDB)**

- **GeoPartitioned & Read-Replicated**
  - India’s deliveries stay in India, US in US.
  - **Read-optimized replicas for mobile clients**.

---

## **4️⃣ Caching Strategy**

We need **caching** to avoid **hitting the database too often**.

| Service               | Cache Strategy                | Why?                                   |
| --------------------- | ----------------------------- | -------------------------------------- |
| **Order Service**     | Redis (LRU) for recent orders | Users check recent orders often        |
| **Restaurant Menu**   | Redis / Memcached             | Speeds up restaurant & menu queries    |
| **Delivery Tracking** | In-Memory Cache (10s TTL)     | Reduces DB calls for real-time updates |
| **Search & Filters**  | Elasticsearch                 | Full-text search for restaurant names  |

---

## **5️⃣ Load Balancing & API Gateway**

To handle **millions of requests per second**, we use:

| Component        | Load Balancer / Proxy                            |
| ---------------- | ------------------------------------------------ |
| API Gateway      | **Nginx / AWS API Gateway**                      |
| Backend Services | **Kubernetes Ingress + Internal Load Balancers** |
| Caching Layer    | **Redis Cluster Mode**                           |
| Database         | **Read Replicas & Auto-Scaling**                 |

---

## **6️⃣ Deployment & Scaling Strategy**

### **📌 Horizontal Scaling**

- **Kubernetes (K8s) & Auto-Scaling**
- Each service runs **independently in containers**.
- **Event-driven scaling** → Auto-scale when high load detected.

### **📌 Global Deployment**

- **Multi-Region AWS / GCP Setup**
- **Load Balancers** route traffic to nearest server.
- **Edge caching (CDN)** for static assets.

---

### **🛠 Why This Design?**

✅ **Optimized Reads** – Using **indexes, caching, partitioning**.  
✅ **Low Latency** – **Sharding, Replication, CDN, GeoPartitioning**.  
✅ **Scalable** – **Event-Driven, Auto-Scaling, Kubernetes**.  
✅ **Fault-Tolerant** – **Redundant regions, Replication, Kafka**.  
✅ **Real-time Capable** – **GeoSpatial Indexing, WebSockets, Event Bus**.

---

### **1️⃣ Database Indexing Strategy**

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
erDiagram
    ORDER_DB {
        int order_id PK
        int user_id FK
        int restaurant_id FK
        datetime created_at
        varchar status
        float total_amount
    }
    RESTAURANT_DB {
        int restaurant_id PK
        varchar name
        varchar city_name
        varchar cuisine_type
    }
    DELIVERY_DB {
        int delivery_id PK
        int order_id FK
        int rider_id
        geo latitude
        geo longitude
        datetime delivery_time
    }
    PAYMENT_DB {
        int payment_id PK
        int order_id FK
        int user_id FK
        float amount
        varchar status
    }

    ORDER_DB ||--|| RESTAURANT_DB : "Orders from"
    ORDER_DB ||--|| DELIVERY_DB : "Delivery Details"
    ORDER_DB ||--|| PAYMENT_DB : "Payment Transactions"

```

---

### **2️⃣ Sharding & Partitioning Strategy**

```mermaid
graph TD

    A[Global Load Balancer] -->|User ID Hash| B1[Shard 1: Users 1M - 10M]
    A -->|User ID Hash| B2[Shard 2: Users 10M - 20M]
    A -->|User ID Hash| B3[Shard 3: Users 20M - 30M]
    A -->|User ID Hash| B4[Shard N: Users 100M - 110M]

    subgraph Orders Sharded by User ID
        B1 --> DB1[PostgreSQL Shard 1]
        B2 --> DB2[PostgreSQL Shard 2]
        B3 --> DB3[PostgreSQL Shard 3]
        B4 --> DBN[PostgreSQL Shard N]
    end

    C[Restaurant Search] -->|City Partitioning| D1["City Partition 1 (US-East)"]
    C -->|City Partitioning| D2["City Partition 2 (Europe)"]
    C -->|City Partitioning| D3["City Partition 3 (Asia)"]
    C -->|City Partitioning| D4["City Partition N (Australia)"]

    subgraph Restaurant Database ["(Partitioned by City)"]
        D1 --> DB5[DynamoDB US-East]
        D2 --> DB6[DynamoDB Europe]
        D3 --> DB7[DynamoDB Asia]
        D4 --> DB8[DynamoDB Australia]
    end

    E[Delivery Service] -->|GeoHash| F1["Delivery Shard 1 (New York)"]
    E -->|GeoHash| F2["Delivery Shard 2 (London)"]
    E -->|GeoHash| F3["Delivery Shard 3 (Tokyo)"]
    E -->|GeoHash| F4["Delivery Shard N (Mumbai)"]

    subgraph Delivery Tracking ["(GeoPartitioned)"]
        F1 --> DB9[MongoDB NY]
        F2 --> DB10[MongoDB London]
        F3 --> DB11[MongoDB Tokyo]
        F4 --> DB12[MongoDB Mumbai]
    end
```

---

### **3️⃣ Event-Driven Architecture & Replication**

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
sequenceDiagram
    participant User as Customer
    participant API as API Gateway
    participant OrderService as Order Service
    participant Kafka as Event Bus (Kafka)
    participant PaymentService as Payment Service
    participant DeliveryService as Delivery Service
    participant NotificationService as Notification Service
    participant DB as Order DB (Sharded)

    User->>API: Places an Order
    API->>OrderService: Create Order Request
    OrderService->>DB: Write to Order DB
    OrderService->>Kafka: Publish Order_Created Event
    Kafka->>PaymentService: Consume Order_Created Event
    PaymentService->>Kafka: Publish Payment_Success Event
    Kafka->>DeliveryService: Consume Payment_Success Event
    DeliveryService->>Kafka: Publish Delivery_Assigned Event
    Kafka->>NotificationService: Send Push/SMS Notification
```

---

### **Why This Design?**

✔ **Indexes** optimize search queries across **PostgreSQL, DynamoDB, MongoDB**.  
✔ **Sharding by User ID, GeoPartitioning, and City-Based Partitions** distribute load.  
✔ **Event-Driven Architecture with Kafka** reduces synchronous calls & improves scalability.  
✔ **Read Replicas in Multiple Regions** reduce database load.

## **Deep Dive into Data Storage & Architecture for a Global Pizza Delivery System** 🍕

A **global-scale pizza delivery system** needs a highly efficient **data architecture** that balances:  
✔ **Scalability** (handling billions of orders)  
✔ **Low Latency** (fast search & tracking)  
✔ **Reliability** (ACID for payments, eventual consistency for tracking)  
✔ **Cost Efficiency** (hot vs. cold storage)

---

# **📌 1. Key Data Entities & Size Estimations**

### **1️⃣ Orders**

- **Contains:** Order details, status, payment, delivery info, tracking, etc.
- **Scale:** 500M+ orders/day → ~1TB/day (~365TB/year)
- **Storage Choice:** **PostgreSQL (Sharded) + Cold Storage (S3/GCS)**
- **Partitioning Strategy:**
  - **Sharded by `user_id`** (even distribution of load)
  - **Partitioned by `created_at`** (for fast retrieval & archival)
- **Indexing:**
  - **B-Tree index on `order_id`, `user_id`** (fast lookup)
  - **GIN index on `status` (JSONB column)** (query orders by status efficiently)
  - **GeoIndex on `delivery_location`** (for nearest driver assignment)

---

### **2️⃣ Users**

- **Contains:** Name, email, phone, preferences, addresses, loyalty points
- **Scale:** 500M users (~1.5TB)
- **Storage Choice:** **PostgreSQL + Redis Cache**
- **Indexing:**
  - **Primary Key: `user_id`**
  - **B-Tree index on `phone_number, email`**
  - **Cache recent lookups in Redis** (low latency reads)

---

### **3️⃣ Restaurants**

- **Contains:** Name, location, cuisine, ratings, menus, operating hours
- **Scale:** 50M restaurants (~250GB)
- **Storage Choice:** **DynamoDB (NoSQL, GSI on `city_name`)**
- **Indexing:**
  - **Partition Key: `restaurant_id`**
  - **Global Secondary Index (GSI) on `city_name`**
  - **Local Secondary Index (LSI) on `cuisine_type`**
- **Replication:** Multi-region to keep search fast worldwide

---

### **4️⃣ Menu**

- **Contains:** Menu items, descriptions, prices, availability
- **Scale:** 5B menu items (~2.5TB)
- **Storage Choice:** **MongoDB (NoSQL, Document DB)**
- **Indexing:**
  - **Index on `restaurant_id` (fast lookups by restaurant)**
  - **Text search indexing for menu search**

---

### **5️⃣ Live Order Tracking**

- **Contains:** Delivery status, driver location, estimated arrival time
- **Scale:** 10M+ concurrent deliveries → **360GB/hour**
- **Storage Choice:** **MongoDB (GeoSharded) + Redis for hot lookups**
- **Indexing:**
  - **GeoIndex on `current_location` (fast lookups by area)**
  - **TTL Index (auto-delete records after 1 hour)**

---

### **6️⃣ Payments**

- **Contains:** Payment transactions, refunds, fraud detection data
- **Scale:** 100M+ transactions/day
- **Storage Choice:** **PostgreSQL (ACID Compliance) + Kafka for async processing**
- **Indexing:**
  - **Primary Key: `transaction_id`**
  - **Index on `user_id`, `order_id`**

---

### **7️⃣ Reviews & Ratings**

- **Contains:** User reviews, restaurant ratings
- **Scale:** 1B+ reviews
- **Storage Choice:** **Elasticsearch (Full-text search) + PostgreSQL**
- **Indexing:**
  - **Full-text search index on `review_text`**
  - **B-Tree index on `restaurant_id`**

---

# **📌 2. Storage Optimization Techniques**

### **🗂️ 1. Sharding Strategy**

- **PostgreSQL (Orders, Users, Payments)**
  - **Horizontal sharding by `user_id`**
  - **Logical partitions on `created_at`**
- **MongoDB (Live Tracking, Menus)**
  - **GeoSharding by `latitude, longitude`**
- **DynamoDB (Restaurants, Menus)**
  - **Partition by `city_name`** (ensures load balancing)

### **🚀 2. Caching Strategy**

- **Redis for user profiles, session management**
- **CDN (Cloudflare/Akamai) for menu images, static assets**
- **Elasticsearch for fast menu & restaurant search**

### **📜 3. Archival & Data Retention**

- **Orders older than 1 year → Cold Storage (S3/GCS)**
- **Live tracking data auto-deleted after 1 hour (TTL Index)**

---

# **📌 3. Event-Driven Architecture (Async Processing)**

To improve **scalability & performance**, use **event-driven architecture** with Kafka.

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
sequenceDiagram
    User->>+API Gateway: Places Order
    API Gateway->>+Order Service: Create Order
    Order Service->>+Kafka: Publish "ORDER_PLACED" Event
    Kafka->>+Payment Service: Process Payment
    Payment Service->>Kafka: Publish "PAYMENT_SUCCESSFUL"
    Kafka->>+Delivery Service: Assign Delivery
    Delivery Service->>MongoDB: Store Tracking Info
    Kafka->>+Notification Service: Notify User
    Notification Service->>+User: "Your order is on the way!"
```

✅ **Advantages of Kafka-based event processing**:  
✔ **Decoupling Services** (Order, Payment, Delivery work independently)  
✔ **Scalability** (Millions of orders per second)  
✔ **Fault Tolerance** (Kafka retains events for replay)

---

# **📌 4. High-Level System Architecture**

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
graph TD
    User["Customer"] --> API["API Gateway"]
    API -->|GraphQL/REST| OrderService["Order Service"]
    OrderService -->|Write| PostgreSQL["PostgreSQL (Sharded)"]
    OrderService -->|Publish Event| Kafka["Kafka Event Bus"]

    Kafka --> PaymentService["Payment Service"]
    PaymentService -->|Update| PostgreSQL

    Kafka --> DeliveryService["Delivery Service"]
    DeliveryService -->|Store| MongoDB["MongoDB (GeoSharded)"]

    API --> RestaurantService["Restaurant Service"]
    RestaurantService -->|Read| DynamoDB["DynamoDB (GSI)"]

    API --> UserService["User Service"]
    UserService -->|Read/Write| PostgreSQL["PostgreSQL (Sharded)"]

    API --> MenuService["Menu Service"]
    MenuService -->|Read| MongoDB["MongoDB (NoSQL)"]

    API --> Cache["Redis Cache"]
    Cache -->|Fast Reads| UserService
```

---

# **📌 5. Deployment & Scaling**

### **🛠️ Deployment Model**

- **Multi-region AWS/GCP Deployment** for low latency
- **Microservices in Kubernetes (EKS/GKE)**
- **Auto-scaling with HPA (Horizontal Pod Autoscaler)**
- **Log aggregation with ELK stack**

### **🔧 Load Balancing**

- **Global Load Balancer (Cloudflare, AWS ALB)**
- **Separate read replicas for PostgreSQL**

---

# **✅ Final Thoughts**

### **Key Takeaways**

✔ **Hybrid SQL + NoSQL** for best scalability  
✔ **Sharding & Partitioning** to handle billions of records  
✔ **Kafka-based async processing** for performance  
✔ **GeoIndexing for live tracking**  
✔ **Redis & CDN for ultra-fast access**
