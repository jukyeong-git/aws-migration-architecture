# Atlassian Jira / Confluence — AWS Cloud Migration

Architecture design for migrating Atlassian Jira and Confluence Data Center from on-premises (IDC) to AWS, achieving **99.9% uptime** with a high-availability, multi-AZ architecture.

## AS-IS: On-Premises (IDC)

![AS-IS Architecture](diagrams/Atlassian%20JIRA_Confluence-AS-IS.drawio.png)

### Infrastructure
- **Load Balancing:** L4 Switch Load Balancer with 1:1 NAT
- **Application:** Jira Data Center (4-node cluster) + Confluence Server
- **Database:** MySQL with DR Center sync
- **Storage:** Shared File Storage + MySQL Backup
- **Networking:** NAT Gateway (Public subnet) for external system integration

### Challenges
- Confluence running as a single server (no HA), causing frequent service outages during business hours
- Jira server reaching End of Support (EOS), requiring infrastructure replacement
- No separation between API and user-facing servers, causing heavy API traffic to impact end-user experience with noticeable delays

---

## TO-BE: AWS Cloud

![TO-BE Architecture](diagrams/Atlassian%20JIRA_Confluence-TO-BE.drawio.png)

### Infrastructure
- **VPC:** Multi-AZ deployment for high availability
- **Load Balancing:** ELB (external) + ALB (internal) with Security Group separation
- **Compute:** EC2 R6i instances
  - User Web Server: 4 nodes (2 per AZ)
  - API Web Server: 2 nodes (1 per AZ)
- **Database:** MySQL with cross-AZ DB Replication
- **Storage:** EFS (NFS mounted shared home), Amazon S3 (logs, backups)
- **Security:** Layered Security Groups (WEB → Cluster → DB)

### External Integrations
Zendesk, Workato, TestRail, ServiceNow, Slack, VictorOps — connected via Public subnet ALB

### Key Improvements
| Challenge | AS-IS (IDC) | TO-BE (AWS) |
|-----------|-------------|-------------|
| Confluence HA | Single server, frequent outages | Multi-AZ clustered deployment |
| Jira EOS | End of Support server | New EC2 R6i instances |
| API Performance | API & user traffic shared, causing delays | Separated User Web Servers + Dedicated API Web Servers |
| Load Balancing | L4 Switch | ELB + ALB |
| Storage | Shared File Storage | EFS + S3 |
| DR | Manual failover | Automated cross-AZ DB Replication |
| Uptime | ~99.5% | **99.9%** |

## Tools Used
- **Diagramming:** Draw.io
- **IaC:** Terraform (provisioning & version control)
- **CI/CD:** GitHub Actions

## Author
**Ryan Jukyeong Kim**
- LinkedIn: [in/jukyeong-kim](https://linkedin.com/in/jukyeong-kim)
- GitHub: [jukyeong-git](https://github.com/jukyeong-git)
