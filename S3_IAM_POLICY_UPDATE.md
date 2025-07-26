# AWS IAM Policy Update Required

## Issue
The S3 file recovery mechanism is failing with an `AccessDenied` error because the `curriculux-s3-user` lacks the `s3:ListBucket` permission on the `curriculux-media-noah` bucket.

## Current Error
```
AccessDenied: User: arn:aws:iam::307122262825:user/curriculux-s3-user is not authorized to perform: s3:ListBucket on resource: "arn:aws:s3:::curriculux-media-noah" because no identity-based policy allows the s3:ListBucket action
```

## Required IAM Policy Update

Add this policy to your `curriculux-s3-user` IAM user (or update the existing policy):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowS3BucketOperations",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::curriculux-media-noah"
        },
        {
            "Sid": "AllowS3ObjectOperations", 
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::curriculux-media-noah/*"
        }
    ]
}
```

## Steps to Apply
1. Go to AWS IAM Console
2. Navigate to Users → `curriculux-s3-user`
3. Go to the Permissions tab
4. Either:
   - Edit the existing inline policy to add the `s3:ListBucket` permission, OR
   - Attach a new policy with the above JSON

## Why This is Needed
The file recovery mechanism needs to list objects in the S3 bucket to find orphaned files that weren't properly linked to submissions. The `s3:ListBucket` permission allows the application to enumerate objects with a specific prefix (e.g., `submissions/classId/assignmentId/submissionId/`).

## After Applying the Policy
Once you've updated the IAM permissions, the file recovery should work automatically. You should see recovered files appear in the assignment submissions view with a "Recovered" badge. 