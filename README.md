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
    A([👤 User]) --> B

    B["🌐 Route 53
    Alias A Record → CloudFront"]

    B --> C

    C["⚡ CloudFront Distribution
    ─────────────────────────
    ✅ Custom SSL cert · ACM us-east-1
    ✅ Redirect HTTP → HTTPS
    ✅ Default root object: index.html
    ✅ Cache policy: CachingOptimized"]

    C -->|OAC signed request| D

    D["🪣 Amazon S3 Bucket · private
    ─────────────────────────
    📄 index.html"]

    style A fill:#1e3a5f,stroke:#5eead4,color:#e8edf7
    style B fill:#1a3a2a,stroke:#34d399,color:#e8edf7
    style C fill:#1e2a4a,stroke:#7c8cff,color:#e8edf7
    style D fill:#3a1a2a,stroke:#f472b6,color:#e8edf7
```
