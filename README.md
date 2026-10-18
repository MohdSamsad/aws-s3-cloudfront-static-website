# aws-s3-cloudfront-static-website
Static website hosted on Amazon S3, served globally via Amazon CloudFront with Origin Access Control (OAC), custom apex domain on Route 53, and automated TLS via AWS Certificate Manager (ACM).

# Overview
This project demonstrates how to deliver a static website at the edge using core AWS networking and storage services — with zero servers to manage, automatic HTTPS, and a real custom domain.


Layer | Service | Role 
--- | --- | --- 
Storage | Amazon S3 | Private origin;no public bucket access 
CDN | Amazon CloudFront | Global edge delivery (400+ PoPs) 
Security | OAC + ACM	 | Signed requests to S3; managed TLS cert 
DNS | Amazon Route 53 | Public hosted zone; Alias A record at apex 
Domain | GoDaddy (registrar) | Nameservers delegated to Route 53 


# Architecture

```mermaid
flowchart TD
    A([👤 User]) --> R
    R["🌐 Route 53
    DNS resolution only
    not in the data path"]
    R -.->|"IP returned to client\n then Route 53 steps aside"| A
    A -->|"HTTPS direct to CloudFront IP"| C
    C["⚡ CloudFront Distribution
    ─────────────────────────
    ✅ Custom SSL cert · ACM us-east-1
    ✅ Redirect HTTP → HTTPS
    ✅ Default root object: index.html
    ✅ Cache policy: CachingOptimized"]
    C -->|"OAC signed request"| S
    S["🪣 Amazon S3 Bucket · private
    ─────────────────────────
    📄 index.html"]

    style A fill:#1e3a5f,stroke:#5eead4,color:#e8edf7
    style R fill:#1a3a2a,stroke:#34d399,color:#e8edf7
    style C fill:#1e2a4a,stroke:#7c8cff,color:#e8edf7
    style S fill:#3a1a2a,stroke:#f472b6,color:#e8edf7
```

# Prerequisites
- AWS account with console access
- Domain name registered (GoDaddy or any registrar)
- AWS CLI installed and configured (aws configure)

# Step-by-Step Setup
**1. Delegate DNS to Route 53**   
- Route 53 → Hosted zones → Create hosted zone → enter your domain → Public.
- Copy the four NS values from the zone's NS record.
- At your registrar (GoDaddy): Nameservers → Change → I'll use my own → paste all four.
- Verify propagation:
    
    ```
    dig NS yourdomain.com +short
    
    ```
    


