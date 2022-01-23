# Prerequisites

To set up and run this lab, you must first complete these tasks:

* Create a SQS Standard Queue
* Create a Lambda Funciton (Runtime Python 3.9)

---
**Note**

Please attach 'AWSLambdaSQSQueueExecutionRole' to your lambda role in IAM

---

# Send Message to SQS Queue&#x20;

Using the `send_message` method from Lambda to send a message to the SQS queue.&#x20;

## **Parameters**

* **`QueueUrl`\[Required]**
  * The URL of the AWS SQS queue to which a message is sent.
  * Type: String
  * Queue URLs and names are case-sensitive.
* **`MessageBody`\[Required]**
  * The message to send.&#x20;
  * Type: String
  * A message can include only XML, JSON, and unformatted text.

## Example

1. AWS lambda function window&#x20;

```python
import json
import boto3

def lambda_handler(event, context):
    # Create SQS client
    client = boto3.client('sqs')
    
    # messagebody
    message = {
               "date":"today",
               "content":"how are you"
    }
    
    # Send message to SQS queue
    response = client.send_message(
        QueueUrl='https://sqs.ap-southeast-2.amazonaws.com/*********/testqueue',
        MessageBody=json.dumps(message)
    )
    
    print(response)
```

2\.  Click 'Deploy' button

3\.  Click 'Test' button&#x20;

![](https://github.com/miaaaalu/Aws/blob/readme/.gitbook/assets/Screen%20Shot%202022-01-22%20at%203.51.49%20PM.png?raw=true)

4\. Check 'Execution results' window or open your CloudWatch Log for more details if has any error.

5\. Go to AWS SNS

* Click 'Send and receive messages' button
* Click 'Poll for messages' under Receive Messages Window

Your message should be successfully sent to SNS Queue by Lambda.

![image](https://user-images.githubusercontent.com/90841192/150666779-87e3f8d2-9f89-474f-9f0d-a528f2af372a.png)

# Receive message from SQS Queue

Using the `receive_message` method from Boto3 to retrieves one or more messages (up to 10), from the specified queue.

## Important Parameters&#x20;

*   **`QueueUrl`\[REQUIRED]**

    The URL of the AWS SQS queue from which messages are received.

    * Type: String
    * Queue URLs and names are case-sensitive.
*   **`MaxNumberOfMessages`:**&#x20;

    The maximum number of messages to return.

    * Type: Integer
    * Valid values: 1 to 10

## Example

```python
import json
import boto3

def lambda_handler(event, context):
    # Create SQS client
    client = boto3.client('sqs')
    
    # Receive message from SQS queue
    response = client.receive_message(
        QueueUrl='https://sqs.ap-southeast-2.amazonaws.com/*********/testqueue',
        AttributeNames=['All'],
        MaxNumberOfMessages=10,
        MessageAttributeNames=['All'],
        VisibilityTimeout=0,
        WaitTimeSeconds=0
    )
    
    message = response['Messages'][0]
    receipt_handle = message['ReceiptHandle']
    
    print(f"Number of messages received: {len(response.get('Messages', []))}")
    print(message)
```

# Deleted receive message from SQS Queue

&#x20;Using `delete_message` to delete the message from the SQS queue.

---
**Note**

It is important to keep in mind that receiving a message from the SQS queue doesn’t automatically delete it. Any other user can also retrieve the same message once the `VisibilityTimeout` period expires. To ensure, no other consumer retrieves the same message, it needs to be deleted within the `VisibilityTimeout` time period.

---

## Example

```python
import json
import boto3

def lambda_handler(event, context):
    # Create SQS client
    client = boto3.client('sqs')
    
    # Receive message from SQS queue
    response = client.receive_message(
        QueueUrl='https://sqs.ap-southeast-2.amazonaws.com/330964433409/testqueue',
        AttributeNames=['All'],
        MaxNumberOfMessages=10,
        MessageAttributeNames=['All'],
        VisibilityTimeout=0,
        WaitTimeSeconds=0
        )
    
    message = response['Messages'][0]
    receipt_handle = message['ReceiptHandle']
    
    # Delete received message from queue
    client.delete_message(
        QueueUrl='https://sqs.ap-southeast-2.amazonaws.com/330964433409/testqueue',
        ReceiptHandle=receipt_handle
    )
    
    print('Received and deleted message: %s' % message)
```
