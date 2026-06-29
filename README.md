# CloudOps Sentinel

CloudOps Sentinel is a hands-on cloud operations lab that simulates a real company problem: detecting a critical application incident, saving the incident record, and notifying the operations team.

This project was built for AWS practice, resume development, and low-cost learning. The AWS version was tested and then destroyed to avoid ongoing charges. The recommended version runs locally with Docker and Moto, so you can practice the AWS workflow without using paid AWS resources.

## Business problem

Companies need a reliable way to respond when important services fail. For example, if a checkout API starts failing, the operations team needs to know quickly, record the incident, and track what happened.

CloudOps Sentinel solves that problem by accepting incident events, storing them, and publishing an alert.

Example incident:

```json
{
  "service": "checkout-api",
  "severity": "critical",
  "summary": "Checkout errors increased above normal threshold"
}
```

## What this project demonstrates

- Infrastructure as Code with Terraform
- Serverless AWS architecture
- Local AWS-style development with Docker and Moto
- DynamoDB incident storage
- SNS-style alert publishing
- Lambda-style application logic
- IAM-authenticated API Gateway routes
- Cost-awareness and safe cleanup practices

## Architecture

AWS version:

```text
Client
  -> API Gateway HTTP API
  -> Lambda incident handler
  -> DynamoDB incidents table
  -> SNS alert topic
```

Local practice version:

```text
Local Node.js test script
  -> Lambda handler code
  -> Moto running in Docker
  -> Local DynamoDB-style table
  -> Local SNS-style topic
```

## Project structure

```text
cloudops-sentinel/
├── docker-compose.local.yml
├── infra/
│   ├── main.tf
│   ├── outputs.tf
│   ├── variables.tf
│   └── versions.tf
├── scripts/
│   ├── bootstrap-local-aws.sh
│   ├── package-lambda.sh
│   └── run-local-incident.cjs
└── src/
    ├── handler.js
    └── package.json
```

## Free local lab

Use this path for practice. It does not create paid AWS resources.

### Prerequisites

- Docker Desktop running
- Node.js installed
- AWS CLI installed

### Run the local lab

From the project folder:

```bash
cd cloudops-sentinel
```

Package the Lambda dependencies:

```bash
bash scripts/package-lambda.sh
```

Start the local AWS simulator:

```bash
docker compose -f docker-compose.local.yml up -d
```

Create the local DynamoDB table and SNS topic:

```bash
bash scripts/bootstrap-local-aws.sh
```

Send a test incident:

```bash
node scripts/run-local-incident.cjs
```

View saved incidents:

```bash
AWS_ACCESS_KEY_ID=test AWS_SECRET_ACCESS_KEY=test aws --endpoint-url http://127.0.0.1:5000 dynamodb scan --table-name cloudops-local-incidents
```

Stop the local lab when finished:

```bash
docker compose -f docker-compose.local.yml down
```

## AWS deployment

The AWS version uses Terraform to define the infrastructure. This creates real AWS resources and may create charges, so only run it if you are comfortable with that.

### AWS resources defined

- API Gateway HTTP API
- Lambda function
- DynamoDB table
- SNS topic and email subscription
- IAM role and permissions
- AWS Budget alert

### Deployment flow

Package the Lambda function:

```bash
bash scripts/package-lambda.sh
```

Go to the Terraform folder:

```bash
cd infra
```

Initialize Terraform:

```bash
terraform init
```

Preview the infrastructure:

```bash
terraform plan
```

Apply the infrastructure:

```bash
terraform apply
```

After deployment, confirm the SNS email subscription from AWS.

### Cleanup

To avoid ongoing AWS charges, destroy the resources when finished:

```bash
terraform destroy
```

Important: AWS Budgets send alerts, but they do not stop spending automatically.

## API behavior

The Lambda handler supports:

- `POST /incidents` to create an incident
- `GET /incidents` to list recent incidents

Required fields for creating an incident:

- `service`
- `severity`
- `summary`

The handler stores each incident with:

- unique incident ID
- service name
- severity
- summary
- open status
- creation timestamp
- TTL expiration timestamp

## Example resume bullet

Built CloudOps Sentinel, a serverless incident-management lab using Terraform, AWS Lambda, API Gateway, DynamoDB, SNS, and IAM. Implemented a free local development workflow with Docker and Moto to simulate AWS services safely for practice and testing.

## LinkedIn summary

I built CloudOps Sentinel, a hands-on cloud operations project that simulates how companies track and alert on critical service incidents. The project uses Terraform Infrastructure as Code, AWS serverless patterns, DynamoDB-style incident storage, SNS-style alerting, and a free local Docker/Moto workflow for safe practice without ongoing cloud costs.

## Lessons learned

- Infrastructure as Code makes cloud environments repeatable.
- Serverless services are powerful, but cost awareness matters.
- Budget alerts are helpful, but they are not spending limits.
- Local cloud simulators are useful for practice before deploying to real AWS.
- Clean teardown is part of responsible cloud engineering.
