# Amazon CloudWatch Incident Investigation

## Project Overview

Amazon CloudWatch Logs Insights is used to monitor, investigate, detect, and alert on application incidents.

The project collects application logs from an Amazon EC2 instance and sends them to Amazon CloudWatch Logs. CloudWatch Logs Insights is used to investigate ERROR and CRITICAL events. A metric filter counts application errors, and a CloudWatch alarm triggers an Amazon SNS email notification when an error is detected. A CloudWatch dashboard provides a visual monitoring view.

## Problem Statement

Application failures and service disturbances need to be detected quickly so that incidents can be investigated and appropriate alerts can be generated.

## Objectives

- Collect application logs from an EC2 instance
- Centralize logs in CloudWatch
- Investigate incidents using Logs Insights
- Detect ERROR events automatically
- Trigger CloudWatch alarms
- Send email notifications through SNS
- Monitor incidents using a CloudWatch dashboard

## AWS Services Used

- Amazon EC2
- Amazon CloudWatch Logs
- Amazon CloudWatch Logs Insights
- Amazon CloudWatch Metric Filter
- Amazon CloudWatch Alarm
- Amazon SNS
- IAM

## Architecture

``text
EC2 Instance
     |
     v
CloudWatch Agent
     |
     v
CloudWatch Log Group
     |
     v
CloudWatch Logs Insights
     |
     v
Metric Filter
     |
     v
CloudWatch Alarm
     |
     v
Amazon SNS
     |
     v
Email Notification

CloudWatch Dashboard
        ^
        |
ApplicationErrorCount
-----------------------------------------------------------------------------------------------------------------------------------------------------------
## Implementation

### 1. EC2 Instance
An Amazon Linux 2023 EC2 instance was created to generate and collect application incident logs.

### 2. IAM Configuration
An IAM role named `CloudWatchAgentEC2Role` was created and the `CloudWatchAgentServerPolicy` policy was attached. The role was then attached to the EC2 instance.

### 3. CloudWatch Agent
The Amazon CloudWatch Agent was installed on the EC2 instance and configured to collect the log file:

`/var/log/incident-app.log`

### 4. Incident Log Generation
A sample application log was created containing INFO, WARNING, ERROR, and CRITICAL events to simulate real application incidents.

### 5. CloudWatch Logs
The logs were sent to the CloudWatch log group:

`/incident-investigation/application`

### 6. Logs Insights
CloudWatch Logs Insights was used to search and investigate application logs and identify ERROR and CRITICAL incidents.

### 7. Metric Filter
A metric filter named `ApplicationErrorFilter` was created with the filter pattern:

`ERROR`

The filter publishes values to:

`CloudWatchIncidentProject / ApplicationErrorCount`

### 8. CloudWatch Alarm
A CloudWatch alarm named `CloudWatch-Incident-Alarm` was configured with:

`ApplicationErrorCount >= 1`

for one datapoint within 5 minutes.

### 9. SNS Notification
An SNS topic named `CloudWatchIncidentAlerts` was configured to send an email notification whenever the alarm enters the ALARM state.

### 10. CloudWatch Dashboard
A dashboard named `CloudWatch-Incident-Dashboard` was created to display the ApplicationErrorCount metric and alarm status.

-------------------------------------------------------------------------------------------------------------------------------------------------------------
## Testing

The complete incident detection and notification workflow was tested successfully.

### Test Procedure

1. An ERROR event was generated on the EC2 instance.
2. The event was written to `/var/log/incident-app.log`.
3. The CloudWatch Agent sent the log to `/incident-investigation/application`.
4. Logs Insights displayed the ERROR and CRITICAL events.
5. The `ApplicationErrorFilter` metric filter detected the ERROR event.
6. `ApplicationErrorCount` reached the configured threshold.
7. `CloudWatch-Incident-Alarm` changed to the ALARM state.
8. Amazon SNS sent an email notification.
9. The CloudWatch dashboard displayed the error metric and alarm status.

### Test Result

The end-to-end incident detection workflow worked successfully:

`EC2 → CloudWatch Logs → Logs Insights → Metric Filter → CloudWatch Alarm → SNS Email → Dashboard`

The alarm notification was successfully received by email.
