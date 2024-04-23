## JSON Script for policies:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecs:UpdateService"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        }
    ]
}
```
## Code for lambda function
```
import boto3

def lambda_handler(event, context):
    services = [
        {'cluster': 'cluster_name_1', 'service': 'service_name_1'},
        {'cluster': 'cluster_name_1', 'service': 'service_name_2'},
        {'cluster': 'cluster_name_2', 'service': 'service_name_3'}
    ]
    
    ecs = boto3.client('ecs')
    
    if event['action'] == 'stop':
        # Stop each ECS service
        for service in services:
            response = ecs.update_service(
                cluster=service['cluster'],
                service=service['service'],
                desiredCount=0
            )
        message = 'All ECS services stopped successfully!'
    elif event['action'] == 'start':

        for service in services:
            response = ecs.update_service(
                cluster=service['cluster'],
                service=service['service'],
                desiredCount=1
            )
        message = 'All ECS services started successfully!'
    else:
        message = 'Invalid action specified. Please specify either \'stop\' or \'start\'.'
    
    print(response)
    
    return {
        'statusCode': 200,
        'body': message
    }
```
## JSON text for ECS start event rule
```
{
  "action": "start"
}
```
## JSON text for ECS stop event rule
```
{
  "action": "stop"
}
```
