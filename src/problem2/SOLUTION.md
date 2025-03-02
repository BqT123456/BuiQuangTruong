Provide your solution here:
![System](https://github.com/user-attachments/assets/979054ed-9076-4877-bd87-5d3cadf5b387)
	
PNG
Components and Their Roles:

- Users:

	+ Role: Traders and clients using the platform to place orders and view market data.

- Route 53 + Application Load Balancer (ALB):

	+ Role: DNS & Traffic Routing.

	+ Reason to choose: Global traffic distribution, fault tolerance.<br/>
	<ins>**=> Alternative: Cloudflare DNS**</ins>
	
- Autoscaling Group EC2: 
	
	+ Role: Runs the server (for authentication) and order matching system (Executes buy/sell orders).
	+ Reason to choose: Maintain high ability. Easy to deploy.
	
	<ins>**=> Alternative:**</ins><br/>
	+ ECS (Fargate) - Microservices Layer to scale automatically serverlessly, move from based on demand to pay-as-you-go to save cost.
  	+ Amazon EKS (Elastic Kubernetes Service) to run the order matching system using container orchestration for easier to manage resources, maintain high availability and scalability for the matching engine workloads.
	+ Add AWS Lambda to handle short-duration functions.
	
- Amazon MSK (Managed Streaming for Apache Kafka):

	+ Role: Acts as an event streaming platform for high-throughput data. 
	+ Reason to choose: Handles real-time streams of orders and market events, low-latency data streams for trading platforms.
	
	<ins>**=> Alternative: AWS Kinesis if the system does not need to be compatible with Kafka eco-system and the system has low data throughput.**</ins>

- Aurora PostgreSQL:

	+ Role: Serves as the relational database for storing order books and trade data. Performance and Scalability: Delivers up to 5x throughput of standard PostgreSQL on the same hardware. 
		    Replicates data across multiple Availability Zones with fast failover.

	+ Reason to choose: Offers high performance, scalability, and durability for transactional data. Using serverless Database to gain cost effective.
	
	<ins>**=> Alternative: AWS DynamoDB: Fully managed NoSQL database with high performance.**</ins>
	
- Amazon S3 + CloudFront:

	+ Role: Stores and delivers static assets for market data display and logs from services.

	+ Reason to choose: S3 provides durable storage; CloudFront accelerates content delivery globally. S3 buckets are located in different AZ to maintain backup (resilient to failures)**</ins>
	
- CloudWatch:

	+ Role: Collects logs and metrics across the system.

	+ Reason to choose: Aggregates logs and metrics from across AWS services and custom applications. 
						Allows setting thresholds to trigger alarms and automate responses.
						Provides dashboards and insights to facilitate proactive management.

	<ins>**=> Alternative: third-party tools which have advanced features and cross-environment monitoring.**</ins>
--------------------------------------------------------------------------------------------------------
Plans for Scaling When the Product Grows Beyond Current Setup

- Apply GitOps for version integrity.
- Implement caching (Amazon ElastiCache) to reduce database load
- Implement message queue (Amazon SQS) to decouple the system, adhence scalability and fault tolerance.
- Apply IaC to automate deploying environment.
- Scaling Compute Resources: using ECS Fargate (Microservices Layer)

- Increase Task Count: Use ECS Service Auto Scaling to adjust the number of running tasks based on CPU/memory utilization or custom metrics.

- Optimize Task Sizes: Right-size CPU and memory allocations to improve efficiency and reduce costs.

- Employ Multiple Regions: Deploy services across multiple regions for redundancy

- Amazon S3:

	+ Lifecycle Policies: Move data to cheaper storage classes (e.g., Glacier) as it ages to reduce costs.

- Load Balancing Enhancements:

- Global Accelerator:

	+ Use AWS Global Accelerator to direct user traffic to optimal endpoints based on network performance.

- Security and Compliance

	+ Implement AWS WAF:
----------------------------------------------------------------------------------
Performance Optimization

- Profiling Tools:

	+ Use AWS X-Ray and other APM tools to identify performance bottlenecks.
--------------------------------------------------------------------------------
Cost Management
- Monitoring and Reporting:

	+ AWS Cost Explorer and Budgets: Track spending patterns and set alerts for unexpected cost increases.

- Resource Tagging:

	+ Apply tags to resources for granular cost allocation and analysis.

- Utilize Savings Plans:

	+ Compute Savings Plans: Commit to consistent compute usage across AWS to receive discounts. Mix Spot Instance and Reserved instance for reducing cost.

