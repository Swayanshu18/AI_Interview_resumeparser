# AWS S3 Setup Guide

This guide will help you set up AWS S3 for storing interview documents (resumes and job descriptions).

## Why S3?

- **Scalable**: Handle unlimited file uploads
- **Reliable**: 99.999999999% durability
- **Cost-effective**: Pay only for what you use (~$0.023/GB/month)
- **Vercel-compatible**: Works perfectly with serverless deployments

## Step-by-Step Setup

### 1. Create AWS Account

1. Go to [AWS Console](https://aws.amazon.com/console/)
2. Sign up for a free account (includes 5GB free S3 storage for 12 months)
3. Verify your email and add payment method

### 2. Create S3 Bucket

1. Go to [S3 Console](https://s3.console.aws.amazon.com/)
2. Click **"Create bucket"**
3. Configure bucket:
   - **Bucket name**: `interview-prep-documents-yourname` (must be globally unique)
   - **AWS Region**: `us-east-1` (or your preferred region)
   - **Block all public access**: ✅ **KEEP CHECKED** (we want private files)
   - **Bucket Versioning**: Disabled (optional, saves costs)
   - **Tags**: Optional
   - **Default encryption**: Enable with SSE-S3
4. Click **"Create bucket"**

### 3. Configure CORS (for browser uploads if needed)

1. Go to your bucket → **Permissions** tab
2. Scroll to **Cross-origin resource sharing (CORS)**
3. Click **Edit** and paste:

\`\`\`json
[
    {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "PUT", "POST", "DELETE"],
        "AllowedOrigins": ["*"],
        "ExposeHeaders": ["ETag"]
    }
]
\`\`\`

4. Click **Save changes**

### 4. Create IAM User for API Access

1. Go to [IAM Console](https://console.aws.amazon.com/iam/)
2. Click **Users** → **Add users**
3. **User name**: `interview-prep-s3-user`
4. **Access type**: Select **"Programmatic access"** (Access key)
5. Click **Next: Permissions**

### 5. Set Permissions

1. Select **"Attach existing policies directly"**
2. Click **"Create policy"**
3. Switch to **JSON** tab and paste:

\`\`\`json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
        }
    ]
}
\`\`\`

4. Replace `YOUR-BUCKET-NAME` with your actual bucket name
5. Click **Next**, name it `InterviewPrepS3Policy`
6. Click **Create policy**
7. Go back to user creation, refresh policies, and attach `InterviewPrepS3Policy`
8. Click **Next** → **Create user**

### 6. Get Access Keys

1. After creating the user, you'll see:
   - **Access key ID**: `AKIA...` (copy this)
   - **Secret access key**: (click **Show** and copy this)

2. ⚠️ **IMPORTANT**: Download the CSV or save these keys securely. You won't be able to see the secret key again!

### 7. Add to Environment Variables

#### Local Development (.env)

Create/update `backend/.env`:

\`\`\`env
AWS_ACCESS_KEY_ID=your-access-key-id-here
AWS_SECRET_ACCESS_KEY=your-secret-access-key-here
AWS_REGION=us-east-1
AWS_S3_BUCKET_NAME=interview-prep-documents-yourname
\`\`\`

#### Vercel Deployment

1. Go to your Vercel project → **Settings** → **Environment Variables**
2. Add these variables:
   - `AWS_ACCESS_KEY_ID` = your access key
   - `AWS_SECRET_ACCESS_KEY` = your secret key
   - `AWS_REGION` = `us-east-1`
   - `AWS_S3_BUCKET_NAME` = your bucket name

3. Redeploy your backend

## Testing S3 Integration

1. Start your backend: `npm run dev`
2. Upload a resume/job description through the frontend
3. Check your S3 bucket - you should see files organized like:
   \`\`\`
   resume_<userId>_<timestamp>.pdf
   job_<userId>_<timestamp>.pdf
   \`\`\`

## Cost Estimate

AWS S3 Pricing (us-east-1):
- **Storage**: $0.023 per GB/month
- **PUT requests**: $0.005 per 1,000 requests
- **GET requests**: $0.0004 per 1,000 requests

**Example for 100 users:**
- 100 resumes (1MB each) = 100MB storage = $0.0023/month
- 200 uploads (resume + job desc) = $0.001
- **Total: ~$0.01/month** 🎉

**Free Tier (first 12 months):**
- 5GB storage
- 20,000 GET requests
- 2,000 PUT requests

## Security Best Practices

✅ **DO:**
- Keep access keys secret (never commit to git)
- Use IAM users with minimal permissions
- Enable S3 bucket encryption
- Block public access to bucket

❌ **DON'T:**
- Share your AWS access keys
- Commit `.env` files to git
- Make your S3 bucket public
- Use root account credentials

## Troubleshooting

### Error: "Access Denied"
- Check IAM policy allows `s3:PutObject` on your bucket
- Verify access keys are correct
- Check bucket name matches

### Error: "NoSuchBucket"
- Verify bucket name is correct
- Check region matches (bucket and credentials)

### Files not appearing in S3
- Check backend logs for upload errors
- Verify AWS credentials are set in environment
- Test S3 connection with AWS CLI: `aws s3 ls`

## Alternative: Use Vercel Blob (Simpler but costs more)

If you prefer not to deal with AWS:

1. Install: `npm install @vercel/blob`
2. Update `backend/utils/s3.js` to use Vercel Blob instead
3. Add `BLOB_READ_WRITE_TOKEN` to Vercel env vars

Cost: $0.15/GB (more expensive than S3 but zero configuration)

## Need Help?

- [AWS S3 Documentation](https://docs.aws.amazon.com/s3/)
- [AWS Free Tier](https://aws.amazon.com/free/)
- [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
