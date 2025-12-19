# AWS-3-Tier-Web-Application
A production-ready, highly available web application built on AWS using a classic 3-tier architecture with Auto Scaling, Load Balancing, and RDS database.

## 🏗️ Architecture Overview

This project implements a scalable 3-tier architecture:

- **Presentation Tier**: Application Load Balancer (ALB)
- **Application Tier**: EC2 instances with Auto Scaling Group in private subnets
- **Data Tier**: RDS PostgreSQL database in private subnets

### Architecture Diagram
```
                         Internet
                            |
                    Internet Gateway
                            |
                  Application Load Balancer
                      (Multi-AZ, Public)
                            |
            +---------------+---------------+
            |                               |
      EC2 Instance                    EC2 Instance
    (Private Subnet)                (Private Subnet)
         AZ-1a                           AZ-1b
            |                               |
            +---------------+---------------+
                            |
                    RDS PostgreSQL
                   (Multi-AZ, Private)
```

## 📋 Features

- ✅ **High Availability**: Multi-AZ deployment across 2 availability zones
- ✅ **Auto Scaling**: Automatic scaling based on CPU utilization (2-4 instances)
- ✅ **Load Balancing**: Application Load Balancer distributes traffic
- ✅ **Security**: 
  - Private subnets for application and database tiers
  - Security groups with least-privilege access
  - NAT Gateways for outbound internet access
- ✅ **Database**: RDS PostgreSQL with automated backups
- ✅ **Automation**: S3 + IAM roles for automated application deployment
- ✅ **User Management**: Registration, OTP verification, login system

## 🛠️ Technologies Used

- **Cloud Platform**: AWS
- **Compute**: EC2, Auto Scaling Groups, Application Load Balancer
- **Database**: RDS PostgreSQL
- **Networking**: VPC, Subnets, NAT Gateway, Internet Gateway, Route Tables
- **Security**: Security Groups, IAM Roles
- **Storage**: S3
- **Backend**: PHP 8.x, Apache HTTP Server
- **Frontend**: HTML5, CSS3, JavaScript

## 📐 Infrastructure Components

### Networking
- **VPC**: 10.0.0.0/16
- **Public Subnets**: 2 (one per AZ)
- **Private Subnets**: 4 (app and database subnets in each AZ)
- **NAT Gateways**: 2 (one per AZ for high availability)
- **Internet Gateway**: 1

### Compute
- **Auto Scaling Group**: 2-4 t3.micro instances
- **Launch Template**: Automated deployment from S3
- **Application Load Balancer**: Internet-facing, multi-AZ

### Database
- **Engine**: PostgreSQL 17.6
- **Instance Class**: db.t3.micro
- **Storage**: 20GB with autoscaling
- **Multi-AZ**: Optional (can be enabled)

### Security
- **Application Security Group**: Allows HTTP from ALB only
- **ALB Security Group**: Allows HTTP/HTTPS from internet
- **Database Security Group**: Allows PostgreSQL from app tier only
- **IAM Role**: EC2 instances can read from S3 and use SSM
  ### Prerequisites
- AWS Account
- Basic knowledge of AWS services
- Understanding of networking concepts

### Step 1: Network Setup
1. Create VPC with public and private subnets across 2 AZs
2. Configure Internet Gateway
3. Deploy NAT Gateways in each public subnet
4. Configure route tables

### Step 2: Database Setup
1. Create RDS subnet group
2. Create database security group
3. Launch RDS PostgreSQL instance
4. Create database schema

### Step 3: Application Tier
1. Create IAM role for EC2 (S3 + SSM access)
2. Upload application files to S3
3. Create Launch Template with user data script
4. Create Auto Scaling Group
5. Configure scaling policies

### Step 4: Load Balancer Setup
1. Create target group with health checks
2. Create Application Load Balancer
3. Configure listeners and routing

## 📊 Database Schema

### Users Table
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    user_type VARCHAR(20) NOT NULL,
    firstname VARCHAR(100) NOT NULL,
    lastname VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    is_verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### OTP Verification Table
```sql
CREATE TABLE otp_verification (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    otp_code VARCHAR(6) NOT NULL,
    expires_at TIMESTAMP NOT NULL,
    is_used BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 🔒 Security Best Practices Implemented

1. **Network Isolation**: Application and database in private subnets
2. **Least Privilege**: Security groups allow only necessary traffic
3. **No Hardcoded Credentials**: Database credentials in configuration file (production should use AWS Secrets Manager)
4. **Session Management**: PHP sessions with secure configuration
5. **Password Security**: Bcrypt hashing for user passwords
6. **OTP Verification**: Email verification before account activation

## 📈 Auto Scaling Configuration

- **Minimum Instances**: 2
- **Desired Instances**: 2
- **Maximum Instances**: 4
- **Scaling Metric**: Average CPU Utilization
- **Target Value**: 70%
- **Health Check**: ELB health checks with 5-minute grace period

## 🧪 Testing

### Health Check
```bash
curl http:///health.php
```

### Load Balancing Test
Refresh the application multiple times and observe different:
- Server hostnames
- Availability zones
- Instance IDs

### High Availability Test
1. Terminate one EC2 instance
2. Auto Scaling automatically launches replacement
3. Application remains available during replacement

## 📸 Screenshots

1. VPC Architecture
2. EC2 Auto Scaling Group
3. Application Load Balancer
4. RDS Database
5. Running Application

## 💰 Cost Optimization

- Use t3.micro instances (free tier eligible)
- Single NAT Gateway for development (not HA)
- RDS Single-AZ for development
- Delete resources when not in use

**Estimated Monthly Cost** (with optimizations): $15-25/month

## 🎯 Future Enhancements

- [ ] Implement HTTPS with ACM certificate
- [ ] Add CloudFront CDN for static content
- [ ] Use AWS Secrets Manager for credentials
- [ ] Implement ElastiCache for session management
- [ ] Add CloudWatch alarms and monitoring
- [ ] Implement CI/CD pipeline with CodePipeline
- [ ] Add WAF for additional security
- [ ] Enable RDS read replicas for read-heavy workloads

## 📚 Lessons Learned

1. **Multi-AZ deployment** ensures high availability
2. **Auto Scaling** handles traffic fluctuations automatically
3. **Private subnets** provide security through network isolation
4. **NAT Gateways** are essential for private subnet internet access
5. **Health checks** are critical for maintaining application availability
6. **Infrastructure as Code** would simplify future deployments

## 🤝 Contributing

Feel free to fork this project and submit pull requests for improvements.

## 📝 License

This project is open source and available under the MIT License.

## 👨‍💻 Author

Delawoe Amevinya Kwasi
- LinkedIn: (https://www.linkedin.com/in/delawoe-amevinya-kwasi-467612317)

## 🙏 Acknowledgments

- AWS Documentation
- AWS Well-Architected Framework
- Claude AI for technical guidance

---

**Note**: This is a demonstration project. For production use, implement additional security measures including AWS Secrets Manager, WAF, CloudTrail logging, and VPC Flow Logs.
```
