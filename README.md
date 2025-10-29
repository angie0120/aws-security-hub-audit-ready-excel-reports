# AWS Security Hub Audit-Ready Excel Reports

Automates AWS Security Hub findings into audit-ready Excel reports with summary dashboards, and KPIs for GRC teams and auditors.

---

## About this project

This project demonstrates how GRC engineers can bridge the gap between automated security findings and audit teams’ preferred workflows.

The AWS Security Hub Excel Reporting tool automatically collects security findings, transforms them into structured Excel workbooks, and provides:
- Detailed summaries of findings
- Severity and compliance status dashboards
- Executive-ready KPI summaries for audit reporting

> Why this matters:
> Audit teams rely on Excel for accessibility, offline use, and analysis. This project demonstrates a scalable way for GRC teams to automate Security Hub reporting while delivering familiar, actionable outputs to auditors.

---

## Overview

| Area                   | Description                                                         |
| ---------------------- | ------------------------------------------------------------------- |
| **Purpose**            | Automate Security Hub reporting into audit-ready Excel workbooks    |
| **Focus**              | Present structured, actionable security findings for GRC & auditors |
| **Output Format**      | Excel (.xlsx) with pivot tables and dashboards                      |
| **Key Outcome**        | Detailed findings + summary KPIs + remediation guidance            |
| **Compliance Context** | SOC 2, ISO 27001, PCI DSS                                           |
| **Tech Stack**         | Python • AWS Lambda • Security Hub • S3 • CloudFormation            |

---

## Logic flowchart

![flowchart](./assets/automated-excel-report-flowchart.png)

---

## Quick Start & Deployment

1. Configure your AWS credentials:
`aws configure sso`

Verify your credentials:
`aws sts get-caller-identity --profile profilename'

2. Activate Security Hub:
`aws securityhub enable-security-hub --region us-east-1 --profile profilename`

Verify Security Hub is enabled:
`aws securityhub describe-hub --region us-east-1 --profile profilename`

3. Create your S3 bucket:
`aws s3 mb s3://security-hub-reports-1755129821-axl --region us-east-1 --profile profilename`

4. Upload your Lambda source code to S3:
`aws s3 cp lambda-source.zip s3://security-hub-reports-1755129821-axl/source/lambda-source.zip --profile profilename`

5. Deploy your CloudFormation stack:

```bash
aws cloudformation deploy \
  --stack-name security-hub-excel-pipeline \
  --template-file cloudformation-template.yaml \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM \
  --parameter-overrides \
      S3BucketName=security-hub-reports-1755129821-axl \
      SourceS3Key=source/lambda-source.zip \
  --region us-east-1 \
  --profile profilename
```

You should see this is your terminal:
```bash
Waiting for changeset to be created..
Waiting for stack create/update to complete
Successfully created/updated stack - security-hub-excel-pipeline
```

6. Invoke the Lambda manually:
```bash
 aws lambda invoke \
  --function-name security-hub-excel-generator-cf \
  --region us-east-1 \
  --profile profilename \
  response.json
```

> This will execute the Lambda and save the output metadata in `response.json`.

You should see this appear is your terminal:
```bash
{
    "StatusCode": 200,
    "ExecutedVersion": "$LATEST"
}
```

7. Check S3 for the report. Once the Lambda finishes (usually < 30 seconds), check your S3 bucket. You should see a new .xlsx file created (naming may include timestamp or date).

8. Inspect Logs (optional but helpful). If no file appears or you want to verify what happened, CloudWatch will stream the Lambda’s log output in real time.

9. After you've run the Lambda invoke command, check the contents of response.json. The output should confirm your entire Security Hub → Excel → S3 pipeline is working exactly as designed.

#### Execution Summary

| Field                | Meaning                                                                    |
| -------------------- | -------------------------------------------------------------------------- |
| `statusCode`: `200`  | Lambda ran successfully with no errors                                     |
| `message`            | Confirms the report was generated and uploaded                             |
| `bucket`             | Target S3 bucket: `security-hub-reports-1755129821-axl`                    |
| `key`                | Report location: `reports/security_hub_report_20251029_172423.xlsx`        |
| `findings_count`     | 15 findings pulled from AWS Security Hub                                   |
| `worksheets_created` | 3 sheets: **Executive Summary**, **Detailed Findings**, **Pivot Analysis** |


---

## Sample output

1. Executive Summary

![execsummary](./assets/executive-summary.png)

2. Detailed Findings

![findings](./assets/detailed-findings.png)

3. Pivot Analysis

![pivotanalysis](./assets/pivot-analysis.png)

---

## How it works

1. Deploy CloudFormation stack to create required AWS resources (Lambda, S3 bucket, IAM roles).
2. Lambda function collects Security Hub findings on schedule.
3. Findings are transformed into structured data.
4. Excel workbook is generated with:
- Detailed findings
- Pivot tables for severity and compliance
- Summary KPIs
5. Workbook is uploaded to S3 for audit-ready distribution.

---

## Core Building Blocks

1. AWS CloudFormation: defines the infrastructure (Lambda, S3, IAM roles) for reproducible deployments.
2. AWS Lambda: collects findings, transforms data, generates Excel reports.
3. Amazon S3: stores reports securely with lifecycle policies for automatic cleanup.

---

## Compliance Context

| Framework | Description                                                  |
| --------- | ------------------------------------------------------------ |
| SOC 2     | Demonstrates continuous monitoring and control effectiveness |
| ISO 27001 | Supports ISMS evidence collection and reporting              |
| PCI DSS   | Provides audit-ready evidence for security controls          |

---

## Governance & Security

- Reports stored securely in S3 with encryption at rest.
- IAM permissions follow least privilege principle. Lambda execution roles are scoped to only the permissions required for reading Security Hub findings, writing Excel reports to the target S3 bucket, and logging to CloudWatch. No broader permissions are granted, supporting security best practices and audit readiness.
- Structured reporting reduces audit errors and speeds review cycles.

---

## Skills Demonstrated

| Skill Area                    | Description                                                              |
| ----------------------------- | ------------------------------------------------------------------------ |
| **GRC Automation**            | Built serverless pipelines to automate Security Hub reporting            |
| **Audit Reporting**           | Produced Excel workbooks with pivot dashboard                            |
| **Data Transformation**       | Converted raw AWS API data into structured, actionable formats           |
| **AWS Infrastructure**        | Designed CloudFormation stacks with Lambda, S3, and IAM roles            |
| **Stakeholder Communication** | Delivered executive-ready KPIs and summaries for auditors and management |

---

