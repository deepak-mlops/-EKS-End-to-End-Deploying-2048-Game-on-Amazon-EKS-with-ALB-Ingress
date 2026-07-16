# Fix AWS Error: CloudFormation Stack Already Exists

## Error

    Request failed

    A CloudFormation stack already exists for a failed cluster with the same name.
    Choose a different cluster name or delete the
    Infra-ECS-Cluster-demo-ecs-cluster-74e63f60 stack
    through the CloudFormation console.

## Cause

An earlier ECS cluster creation failed, but the CloudFormation stack
still exists.

## Solution 1 (Recommended): Delete the Failed Stack

1.  Open **AWS Console**.
2.  Go to **CloudFormation**.
3.  Select the stack: `Infra-ECS-Cluster-demo-ecs-cluster-74e63f60`
4.  Click **Delete**.
5.  Wait until the status is `DELETE_COMPLETE`.
6.  Create the ECS cluster again.

## If Deletion Fails

Check the **Events** tab for the resource blocking deletion.

Common fixes: - Empty S3 buckets. - Delete ECS services/tasks first. -
Remove dependent load balancers or security groups.

Retry deleting the stack.

## Solution 2: Use a Different Cluster Name

Examples: - demo-ecs-cluster-v2 - demo-ecs-cluster-01 -
deepak-ecs-cluster

## AWS CLI

List stacks:

``` bash
aws cloudformation list-stacks --stack-status-filter CREATE_FAILED ROLLBACK_COMPLETE
```

Delete stack:

``` bash
aws cloudformation delete-stack \
  --stack-name Infra-ECS-Cluster-demo-ecs-cluster-74e63f60
```

Check status:

``` bash
aws cloudformation describe-stacks \
  --stack-name Infra-ECS-Cluster-demo-ecs-cluster-74e63f60
```

## Best Practices

-   Delete failed CloudFormation stacks before retrying.
-   Use unique stack and cluster names.
-   Review CloudFormation Events for root cause.
-   Clean up unused AWS resources.

## References

-   AWS CloudFormation
-   Amazon ECS
