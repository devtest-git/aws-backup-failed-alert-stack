# AWS Backup Monitoring & Alerting Stack

This project provides a CloudFormation-based monitoring solution for AWS Backup jobs.  
It detects **FAILED** and **EXPIRED** backup jobs and sends alerts using CloudWatch, EventBridge, and SNS integrated with external notification systems like ServiceNow.

---

## 🚀 Overview

This stack automates backup observability in AWS by:

- Capturing AWS Backup job events via **EventBridge**
- Sending events to **CloudWatch Logs**
- Creating **custom CloudWatch metrics**
- Triggering **CloudWatch Alarms**
- Sending notifications via **SNS**

---

## 🏗️ Architecture Flow

```
AWS Backup
   ↓
EventBridge Rule (Backup Job State Change)
   ↓
CloudWatch Log Group
   ↓
Metric Filters
   ↓
CloudWatch Alarms
   ↓
SNS Topic
   ↓
HTTPS Endpoint (ServiceNow / external system)
```

---

## 📦 Features

### Event Monitoring
- Captures AWS Backup job state changes
- Filters events using EventBridge rule

### Metrics Creation
- `NumberOfBackupJobsFailed`
- `NumberOfBackupJobsExpired`

### Alerting
- CloudWatch Alarms for:
  - FAILED backup jobs
  - EXPIRED backup jobs
- Threshold-based detection (>= 1 event)

### Notifications
- SNS topic for alert distribution
- HTTPS integration with external systems (e.g., ServiceNow)

---

## 📁 Project Structure

```text
aws-backup-monitoring-alarms/
│
├── cloudformation/
│   └── backup-monitoring.yaml
│
├── docs/
│   ├── architecture.md
│   └── setup-guide.md
│
├── README.md
└── .gitignore
```

---

## ⚙️ Resources Created

### CloudWatch Log Group
```
/aws/events/AWS-Backup-Monitoring-Log-group
```

### EventBridge Rule
- Name: `AWS-Backup-Monitoring-Rule`
- Event Source: `aws.backup`
- Event Type: `Backup Job State Change`

### Metrics
| Metric Name | Namespace |
|-------------|----------|
| NumberOfBackupJobsFailed | BackupMonitoring |
| NumberOfBackupJobsExpired | BackupMonitoring |

---

### SNS Topic
- `Backup-Monitoring-Topic`

### SNS Subscription
- Protocol: HTTPS  
- Endpoint: ServiceNow integration URL

---

## 🚨 CloudWatch Alarms

### 1. Failed Backup Jobs
- Metric: `NumberOfBackupJobsFailed`
- Condition: `>= 1`
- Period: 60 seconds

### 2. Expired Backup Jobs
- Metric: `NumberOfBackupJobsExpired`
- Condition: `>= 1`
- Period: 60 seconds

---

## 🔐 IAM Requirements

This stack requires permissions for:

- `events:*`
- `logs:*`
- `cloudwatch:*`
- `sns:*`
- `iam:PassRole` (if extended)

---

## 🚀 Deployment

### Deploy using AWS CLI

```bash
aws cloudformation deploy \
  --template-file cloudformation/backup-monitoring.yaml \
  --stack-name backup-monitoring-stack \
  --capabilities CAPABILITY_NAMED_IAM
```

---

## 📊 Use Cases

- AWS Backup failure monitoring
- Enterprise backup compliance tracking
- Automated incident creation (ServiceNow integration)
- Backup SLA enforcement
- Centralized observability for backup operations

---

## ⚠️ Notes

- Ensure AWS Backup is enabled in the account
- SNS HTTPS endpoint must be reachable publicly or via approved network (Change endpoint according to your organisation)
- Metric filters depend on logs being delivered correctly from EventBridge

---

## 🔮 Future Improvements

- Multi-account backup monitoring (AWS Organizations)
- Slack / Teams integration
- Advanced anomaly detection
- Dashboard using CloudWatch Metrics or Grafana
- Dead-letter queue (DLQ) for failed notifications
