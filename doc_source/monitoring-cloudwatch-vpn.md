# Monitoring VPN Tunnels Using Amazon CloudWatch<a name="monitoring-cloudwatch-vpn"></a>

You can monitor VPN tunnels using CloudWatch, which collects and processes raw data from the VPN service into readable, near real\-time metrics\. These statistics are recorded for a period of 15 months, so that you can access historical information and gain a better perspective on how your web application or service is performing\. VPN metric data is automatically sent to CloudWatch as it becomes available\.

**Important**  
CloudWatch metrics are not supported for AWS Classic VPN connections\. For more information, see [Site\-to\-Site VPN Categories](vpn-categories.md)\.

For more information, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

## VPN Tunnel Metrics and Dimensions<a name="metrics-dimensions-vpn"></a>

The following metrics are available for your VPN tunnels\.


| Metric | Description | 
| --- | --- | 
|  `TunnelState`  |  The state of the tunnel\. For static VPNs, 0 indicates DOWN and 1 indicates UP\. For BGP VPNs, 1 indicates ESTABLISHED and 0 is used for all other states\.  Units: Boolean  | 
|  `TunnelDataIn`  |  The bytes received through the VPN tunnel\. Each metric data point represents the number of bytes received after the previous data point\. Use the Sum statistic to show the total number of bytes received during the period\. This metric counts the data after decryption\. Units: Bytes  | 
|  `TunnelDataOut`  |  The bytes sent through the VPN tunnel\. Each metric data point represents the number of bytes sent after the previous data point\. Use the Sum statistic to show the total number of bytes sent during the period\. This metric counts the data before encryption\. Units: Bytes  | 

To filter the metric data, use the following dimensions\.


| Dimension | Description | 
| --- | --- | 
| `VpnId` |  Filters the metric data by the Site\-to\-Site VPN connection ID\.  | 
| `TunnelIpAddress` |  Filters the metric data by the IP address of the tunnel for the virtual private gateway\.  | 

## Viewing VPN Tunnel CloudWatch Metrics<a name="viewing-metrics"></a>

When you create a new Site\-to\-Site VPN connection, the VPN service sends the following metrics about your VPN tunnels to CloudWatch as it becomes available\. You can view the metrics for VPN tunnels as follows\.

**To view metrics using the CloudWatch console**

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Under **All metrics**, choose the **VPN** metric namespace\.

1. Select the metric dimension to view the metrics \(for example, for the Site\-to\-Site VPN connection\)\.

**To view metrics using the AWS CLI**  
At a command prompt, use the following command:

```
aws cloudwatch list-metrics --namespace "AWS/VPN"
```

## Creating CloudWatch Alarms to Monitor VPN Tunnels<a name="creating-alarms-vpn"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period you specify, and sends a notification to an Amazon SNS topic based on the value of the metric relative to a given threshold over a number of time periods\. 

For example, you can create an alarm that monitors the state of a VPN tunnel and sends a notification when the tunnel state is DOWN for 3 consecutive 5\-minute periods\.

**To create an alarm for tunnel state**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **Create Alarm**\.

1. Choose **VPN Tunnel Metrics**\.

1. Choose the IP address of the VPN tunnel and the **TunnelState** metric\. Choose **Next**\.

1. Configure the alarm as follows, and choose **Create Alarm** when you are done:
   + Under **Alarm Threshold**, enter a name and description for your alarm\. For **Whenever**, choose **<=** and enter `0`\. Enter **3** for the consecutive periods\.
   + Under **Actions**, select an existing notification list or choose **New list** to create a new one\. 
   + Under **Alarm Preview**, select a period of 5 minutes and specify a statistic of **Maximum**\.

You can create an alarm that monitors the state of the Site\-to\-Site VPN connection\. For example, you can create an alarm that sends a notification when the status of one or both tunnels is DOWN for 1 consecutive 5\-minute period\.

**To create an alarm for Site\-to\-Site VPN connection state**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **Create Alarm**\.

1. Choose **VPN Connection Metrics**\.

1. Select your Site\-to\-Site VPN connection and the **TunnelState** metric\. Choose **Next**\.

1. Configure the alarm as follows, and choose **Create Alarm** when you are done:
   + Under **Alarm Threshold**, enter a name and description for your alarm\. For **Whenever**, choose **<=** and enter `0` \(or `0.5` for when at least one tunnel is down\)\. Enter **1** for the consecutive periods\.
   + Under **Actions**, select an existing notification list or choose **New list** to create a new one\. 
   + Under **Alarm Preview**, select a period of 5 minutes and specify a statistic of **Maximum**\.

     Alternatively, if you've configured your Site\-to\-Site VPN connection so that both tunnels are up, you can specify a statistic of **Minimum** to send a notification when at least one tunnel is down\.

You can also create alarms that monitor the amount of traffic coming in or leaving the VPN tunnel\. For example, the following alarm monitors the amount of traffic coming into the VPN tunnel from your network, and sends a notification when the number of bytes reaches a threshold of 5,000,000 during a 15 minute period\.

**To create an alarm for incoming network traffic**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **Create Alarm**\.

1. Choose **VPN Tunnel Metrics**\.

1. Select the IP address of the VPN tunnel and the **TunnelDataIn** metric\. Choose **Next**\.

1. Configure the alarm as follows, and choose **Create Alarm** when you are done:
   + Under **Alarm Threshold**, enter a name and description for your alarm\. For **Whenever**, choose **>=** and enter `5000000`\. Enter **1** for the consecutive periods\.
   + Under **Actions**, select an existing notification list or choose **New list** to create a new one\. 
   + Under **Alarm Preview**, select a period of 15 minutes and specify a statistic of **Sum**\.

The following alarm monitors the amount of traffic leaving the VPN tunnel to your network, and sends a notification when the number of bytes is less than 1,000,000 during a 15 minute period\.

**To create an alarm for outgoing network traffic**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **Create Alarm**\.

1. Choose **VPN Tunnel Metrics**\.

1. Select the IP address of the VPN tunnel and the **TunnelDataOut** metric\. Choose **Next**\.

1. Configure the alarm as follows, and choose **Create Alarm** when you are done:
   + Under **Alarm Threshold**, enter a name and description for your alarm\. For **Whenever**, choose **<=** and enter `1000000`\. Enter **1** for the consecutive periods\.
   + Under **Actions**, select an existing notification list or choose **New list** to create a new one\. 
   + Under **Alarm Preview**, select a period of 15 minutes and specify a statistic of **Sum**\.

For more examples of creating alarms, see [Creating Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) in the *Amazon CloudWatch User Guide*\.