# Vercel Deployment Guide

This guide explains how to deploy the Interview Prep AI application to Vercel.

## Architecture

This is a monorepo with two separate applications:
- **Backend (API)**: Node.js/Express server with AWS S3 storage
- **Frontend**: React/Vite application

## Important: File Storage with AWS S3

✅ **This app uses AWS S3 for file storage**, which provides:
- Persistent, reliable file storage
- Scalable for production use
- Works perfectly with Vercel serverless functions
- Cost-effective (~$0.023/GB/month)

📖 **See [AWS_S3_SETUP.md](./AWS_S3_SETUP.md) for complete S3 setup instructions**

## Prerequisites

Before deploying, set up:
1. ✅ MongoDB Atlas account (free tier)
2. ✅ AWS S3 bucket (see [AWS_S3_SETUP.md](./AWS_S3_SETUP.md))
3. ✅ OpenAI API key
4. ✅ Git repository pushed to GitHub/GitLab

## Deployment Steps

### 1. Deploy Backend (API)

1. Go to [Vercel Dashboard](https://vercel.com/dashboard)
2. Click "Add New" → "Project"
3. Import your Git repository
4. Configure the project:
   - **Root Directory**: `backend`
   - **Framework Preset**: Other
   - **Build Command**: (leave empty)
   - **Output Directory**: (leave empty)

5. Add Environment Variables:
   ```
   MONGODB_URI=your_mongodb_connection_string
   OPENAI_API_KEY=your_openai_api_key
   OPENAI_MODEL=gpt-4o-mini-2024-07-18
   OPENAI_EMBEDDING_MODEL=text-embedding-3-large
   JWT_SECRET=your_jwt_secret_key
   NODE_ENV=production
   FRONTEND_URL=https://your-frontend-url.vercel.app
   AWS_ACCESS_KEY_ID=your_aws_access_key_id
   AWS_SECRET_ACCESS_KEY=your_aws_secret_access_key
   AWS_REGION=us-east-1
   AWS_S3_BUCKET_NAME=your-bucket-name
   ```

6. Deploy!

7. Copy the deployed backend URL (e.g., `https://your-backend.vercel.app`)

### 2. Deploy Frontend

1. Go to [Vercel Dashboard](https://vercel.com/dashboard)
2. Click "Add New" → "Project"
3. Import the SAME Git repository
4. Configure the project:
   - **Root Directory**: `frontend`
   - **Framework Preset**: Vite
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`

5. Add Environment Variables:
   ```
   VITE_API_URL=https://your-backend.vercel.app/api
   ```
   (Use the backend URL from step 1.7)

6. Deploy!

## MongoDB Setup (MongoDB Atlas)

1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a free cluster
3. Create a database user
4. Whitelist all IP addresses (0.0.0.0/0) for Vercel
5. Get your connection string and use it as `MONGODB_URI`

## Important Notes

### File Uploads
⚠️ **Vercel has a 50MB limit for serverless functions and doesn't support persistent file storage.**

For production, you should:
1. Use a cloud storage service (AWS S3, Cloudinary, or Vercel Blob)
2. Update the document upload logic to store files in cloud storage
3. Store only the file URL in MongoDB

### Rate Limiting
The current rate limiting is set to 100 requests per 15 minutes per IP. Adjust as needed in `backend/server.js`.

### Environment Variables Security
- Never commit `.env` files
- Always use Vercel's environment variables UI
- Generate a strong JWT_SECRET (use a random string generator)

## Testing Deployment

1. Visit your frontend URL
2. Try signing up/logging in
3. Test document upload
4. Start an interview

## Troubleshooting

### Backend Errors
- Check Vercel logs: Project → Deployments → Click deployment → Runtime Logs
- Verify MongoDB connection string is correct
- Ensure all environment variables are set

### Frontend Can't Connect to Backend
- Check VITE_API_URL is correct (must include `/api`)
- Verify CORS is allowing your frontend URL in backend

### Upload Errors
- If you see "ENOENT: no such file or directory, mkdir '/var/task/backend/uploads'", you need to implement cloud storage (Vercel doesn't support local file storage)

## Alternative: Deploy Backend on Railway/Render

If you need persistent file storage, consider:
- **Railway.app**: Supports persistent volumes
- **Render.com**: Free tier with persistent disks
- **Heroku**: Traditional hosting with file system

Then just update VITE_API_URL in your Vercel frontend to point to that backend.

## Cost Estimate

- **Vercel**: Free tier (Hobby plan) - Frontend + Backend
- **MongoDB Atlas**: Free tier (512MB storage)
- **OpenAI API**: Pay per use (~$0.002 per request)

Total: **~Free** for testing/portfolio (except OpenAI costs)
