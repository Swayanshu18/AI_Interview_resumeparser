# Final Deployment Guide for Vercel

This is the complete, step-by-step guide to deploy your AI Interview Prep App to Vercel.

## Prerequisites Checklist

Before deploying, ensure you have:

- [ ] MongoDB Atlas account with cluster created
- [ ] AWS account with S3 bucket created (see [AWS_S3_SETUP.md](./AWS_S3_SETUP.md))
- [ ] GitHub/GitLab account with repository
- [ ] Vercel account (free tier is fine)

---

## Part 1: Environment Variables You Need

### For Backend Deployment

You'll need these **8 environment variables** for the backend:

#### 1. MongoDB Configuration

**MONGODB_URI**
```
mongodb+srv://username:password@cluster.mongodb.net/interview-prep
```
- Get this from: MongoDB Atlas → Databases → Connect → Connect your application
- Replace `username` and `password` with your database user credentials
- **Important**: Use URL encoding if password contains special characters (e.g., `@` → `%40`)

---

#### 2. JWT Configuration

**JWT_SECRET**
```
your-super-secret-random-string-min-32-chars
```
- Generate a strong random string (at least 32 characters)
- Example: `8f7e6d5c4b3a2190f8e7d6c5b4a39281`
- You can generate one at: https://randomkeygen.com/

**JWT_EXPIRE**
```
7d
```
- Keep as `7d` (7 days) - this is how long users stay logged in

---

#### 3. AWS S3 Configuration

**AWS_ACCESS_KEY_ID**
```
AKIA...your-access-key
```
- Get this from: AWS IAM → Users → Your S3 user → Security credentials → Create access key
- See [AWS_S3_SETUP.md](./AWS_S3_SETUP.md) for detailed setup

**AWS_SECRET_ACCESS_KEY**
```
your-secret-access-key
```
- You'll get this when creating the access key (only shown once!)
- Save it securely - you won't be able to see it again

**AWS_REGION**
```
us-east-1
```
- Use the region where you created your S3 bucket
- Common options: `us-east-1`, `us-west-2`, `eu-west-1`

**AWS_S3_BUCKET_NAME**
```
interview-prep-documents-yourname
```
- Your S3 bucket name (must be globally unique)

---

#### 4. OpenAI API Configuration

**OPENAI_API_KEY**
```
sk-mega-99f979b4d669b7ebfc52e5426cacfb9c12c9fee2dde064404c44741996b13a31
```
- **You can use the MegaLLM key above** (already included in .env.example)
- Or get your own OpenAI API key from: https://platform.openai.com/api-keys

**OPENAI_BASE_URL** (Optional - only if using MegaLLM)
```
https://ai.megallm.io/v1
```
- **Only add this if using the MegaLLM key above**
- If using official OpenAI key, **DO NOT add this variable**

---

#### 5. Production Configuration

**NODE_ENV**
```
production
```
- Keep as `production` for Vercel deployment

**FRONTEND_URL**
```
https://your-frontend-url.vercel.app
```
- **Important**: You'll update this AFTER deploying frontend
- For now, you can use `*` or leave it blank, then update later

---

### For Frontend Deployment

You'll need **1 environment variable** for the frontend:

**VITE_API_URL**
```
https://your-backend-url.vercel.app/api
```
- **Important**: Use the backend URL from deployment + `/api`
- Example: `https://ai-interview-backend.vercel.app/api`
- **Important**: Make sure it ends with `/api`

---

## Part 2: Deploy Backend to Vercel

### Step 1: Push Code to GitHub

If you haven't already:

```bash
cd d:\upivot_assignment\interview-prep-app
git init
git add .
git commit -m "Initial commit - AI Interview Prep App"
git branch -M main
git remote add origin https://github.com/yourusername/interview-prep-app.git
git push -u origin main
```

### Step 2: Deploy Backend

1. **Go to Vercel Dashboard**
   - Visit: https://vercel.com/new
   - Login with GitHub

2. **Import Repository**
   - Click "Import Project"
   - Select your `interview-prep-app` repository
   - Click "Import"

3. **Configure Backend Project**

   **CRITICAL SETTINGS:**
   - **Project Name**: `ai-interview-backend` (or your choice)
   - **Root Directory**: Click "Edit" and select `backend` ⚠️ **MUST BE "backend"**
   - **Framework Preset**: Other
   - **Build Command**: Leave empty (or `npm install`)
   - **Output Directory**: Leave empty
   - **Install Command**: `npm install`

4. **Add Environment Variables**

   Click "Environment Variables" section and add ALL these:

   ```
   MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/interview-prep

   JWT_SECRET=your-super-secret-random-string-min-32-chars
   JWT_EXPIRE=7d

   AWS_ACCESS_KEY_ID=AKIA...your-key
   AWS_SECRET_ACCESS_KEY=your-secret-key
   AWS_REGION=us-east-1
   AWS_S3_BUCKET_NAME=interview-prep-documents-yourname

   OPENAI_API_KEY=sk-mega-99f979b4d669b7ebfc52e5426cacfb9c12c9fee2dde064404c44741996b13a31

   NODE_ENV=production
   FRONTEND_URL=*
   ```

   **If using MegaLLM**, also add:
   ```
   OPENAI_BASE_URL=https://ai.megallm.io/v1
   ```

5. **Deploy**
   - Click "Deploy"
   - Wait 2-3 minutes for deployment
   - ✅ Copy your backend URL (e.g., `https://ai-interview-backend.vercel.app`)

6. **Test Backend**
   - Visit: `https://your-backend-url.vercel.app/api/health`
   - You should see: `{"status":"OK","timestamp":"..."}`
   - If you see this, backend is working! 🎉

---

## Part 3: Deploy Frontend to Vercel

### Step 1: Deploy Frontend

1. **Go to Vercel Dashboard Again**
   - Visit: https://vercel.com/new

2. **Import SAME Repository**
   - Click "Import Project"
   - Select the SAME `interview-prep-app` repository
   - Click "Import"

3. **Configure Frontend Project**

   **CRITICAL SETTINGS:**
   - **Project Name**: `ai-interview-frontend` (or your choice)
   - **Root Directory**: Click "Edit" and select `frontend` ⚠️ **MUST BE "frontend"**
   - **Framework Preset**: Vite
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`
   - **Install Command**: `npm install`

4. **Add Environment Variable**

   Click "Environment Variables" and add:

   ```
   VITE_API_URL=https://your-backend-url.vercel.app/api
   ```

   ⚠️ **IMPORTANT**:
   - Use YOUR actual backend URL from Part 2, Step 5
   - Make sure it ends with `/api`
   - Example: `https://ai-interview-backend.vercel.app/api`

5. **Deploy**
   - Click "Deploy"
   - Wait 2-3 minutes
   - ✅ Copy your frontend URL (e.g., `https://ai-interview-frontend.vercel.app`)

---

## Part 4: Update Backend CORS

**IMPORTANT**: Now that you have your frontend URL, update the backend:

1. **Go to Backend Project Settings**
   - Vercel Dashboard → Your backend project → Settings → Environment Variables

2. **Update FRONTEND_URL**
   - Find `FRONTEND_URL` variable
   - Change value from `*` to your actual frontend URL
   - Example: `https://ai-interview-frontend.vercel.app`

3. **Redeploy Backend**
   - Go to Deployments tab
   - Click "..." on latest deployment → Redeploy

---

## Part 5: Testing Your Deployment

### Test 1: Health Check
- Visit: `https://your-backend-url.vercel.app/api/health`
- Expected: `{"status":"OK",...}`

### Test 2: Frontend Access
- Visit: `https://your-frontend-url.vercel.app`
- Expected: Landing page with "AI-Powered Interview Prep" loads

### Test 3: User Registration
1. Click "Sign Up"
2. Create account with email and password
3. Expected: Redirected to upload page

### Test 4: Document Upload
1. Upload a resume PDF
2. Expected: Progress bar → "Resume uploaded and processed!" toast
3. Upload a job description PDF
4. Expected: Both documents show in UI with filenames
5. Expected: "Begin Interview Now" button appears

### Test 5: Interview Flow
1. Click "Begin Interview Now"
2. Expected: First question appears
3. Answer the question
4. Expected: Second question appears (no feedback yet)
5. Answer second question
6. Expected: Third question appears (still no feedback)
7. Answer third question
8. Expected: Comprehensive feedback with:
   - Individual scores for all 3 answers
   - Overall score
   - Summary
   - Citations with relevance percentages

### Test 6: Check S3
1. Go to AWS S3 Console
2. Open your bucket
3. Expected: Files named like `resume_<userId>_<timestamp>.pdf`

### Test 7: Check MongoDB
1. Go to MongoDB Atlas → Browse Collections
2. Expected:
   - `users` collection has your user
   - `documents` collection has 2 documents with embeddings
   - `chats` collection has your interview session

---

## Common Issues and Solutions

### Issue 1: "Failed to check documents" on backend

**Symptoms**: Error when clicking "Start Interview"

**Causes & Fixes**:
1. **MongoDB connection failed**
   - Check `MONGODB_URI` is correct
   - Verify IP whitelist includes `0.0.0.0/0` in MongoDB Atlas
   - Check database user exists with correct password

2. **No documents uploaded**
   - Upload both resume and job description first

### Issue 2: "Network Error" or "Failed to fetch" on frontend

**Symptoms**: Red error toasts on signup/login/upload

**Causes & Fixes**:
1. **Wrong API URL**
   - Check `VITE_API_URL` in frontend settings
   - Must be `https://your-backend-url.vercel.app/api` (with `/api`)

2. **Backend not deployed**
   - Visit backend health endpoint to verify it's running

### Issue 3: CORS errors in browser console

**Symptoms**: Console shows "CORS policy blocked" errors

**Causes & Fixes**:
1. **FRONTEND_URL not set**
   - Update `FRONTEND_URL` in backend environment variables
   - Redeploy backend after changing

2. **Using preview URL**
   - Use production URL instead of preview URL with hash

### Issue 4: "Failed to upload file to S3"

**Symptoms**: Upload fails with S3 error

**Causes & Fixes**:
1. **AWS credentials wrong**
   - Double-check `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`
   - Verify IAM user has `s3:PutObject` permission

2. **Bucket name wrong**
   - Verify `AWS_S3_BUCKET_NAME` matches your actual bucket name
   - Check bucket exists in correct region

3. **Region mismatch**
   - Verify `AWS_REGION` matches where bucket was created

### Issue 5: 404 on all API routes

**Symptoms**: All backend requests return 404

**Causes & Fixes**:
1. **Root directory not set**
   - Verify backend root directory is set to `backend`
   - Check `backend/vercel.json` exists

2. **Redeploy**
   - Go to Deployments → Redeploy with correct root directory

### Issue 6: Questions asked all together (not sequential)

**Symptoms**: All 3 questions show at once

**Fixes**:
- This should be fixed in the latest code
- Check `backend/routes/chat.js` has the updated sequential logic
- If still happening, redeploy backend

### Issue 7: Feedback showing after each answer

**Symptoms**: Score and feedback after answers 1 and 2

**Fixes**:
- This should be fixed in the latest code
- Feedback should ONLY show after question 3
- If still happening, redeploy backend

### Issue 8: Preview deployment requires login

**Symptoms**: 401 errors, Vercel SSO login page

**Fixes**:
- Use production URL (without hash/preview)
- Production URLs look like: `https://project-name.vercel.app`
- Preview URLs look like: `https://project-name-git-branch-user.vercel.app`

---

## Monitoring and Maintenance

### Check Backend Logs
1. Vercel Dashboard → Backend Project → Deployments
2. Click latest deployment
3. Click "Runtime Logs" tab
4. Look for errors or warnings

### Check Frontend Console
1. Open frontend in browser
2. Press F12 to open Developer Tools
3. Check Console tab for errors
4. Check Network tab for failed requests

### MongoDB Monitoring
1. MongoDB Atlas → Metrics tab
2. Monitor:
   - Connections (should be low in serverless)
   - Operations per second
   - Storage size

### AWS S3 Monitoring
1. AWS Console → S3 → Metrics
2. Monitor:
   - Bucket size
   - Number of objects
   - Request count

---

## Cost Estimates

### With Free Tiers:
- **Vercel**: Free (100GB bandwidth/month)
- **MongoDB Atlas**: Free (512MB storage)
- **AWS S3**: Free first 12 months (5GB storage)
- **OpenAI (MegaLLM)**: Free API key provided

**Total Monthly Cost**: $0 🎉

### After Free Tiers (estimated):
- **100 interviews/month**: ~$1-2
- **1,000 interviews/month**: ~$10-15
- **10,000 interviews/month**: ~$100-150

Main cost is OpenAI API usage (~$0.002 per interview with gpt-4o-mini)

---

## Quick Reference: All Environment Variables

### Backend (10 variables):
```bash
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/interview-prep
JWT_SECRET=your-super-secret-random-string-min-32-chars
JWT_EXPIRE=7d
AWS_ACCESS_KEY_ID=AKIA...your-key
AWS_SECRET_ACCESS_KEY=your-secret-key
AWS_REGION=us-east-1
AWS_S3_BUCKET_NAME=interview-prep-documents-yourname
OPENAI_API_KEY=sk-mega-99f979b4d669b7ebfc52e5426cacfb9c12c9fee2dde064404c44741996b13a31
OPENAI_BASE_URL=https://ai.megallm.io/v1
NODE_ENV=production
FRONTEND_URL=https://your-frontend-url.vercel.app
```

### Frontend (1 variable):
```bash
VITE_API_URL=https://your-backend-url.vercel.app/api
```

---

## Deployment Checklist

### Before Deploying:
- [ ] MongoDB Atlas cluster created
- [ ] MongoDB database user created
- [ ] MongoDB IP whitelist set to 0.0.0.0/0
- [ ] AWS S3 bucket created
- [ ] AWS IAM user created with S3 permissions
- [ ] AWS access keys saved
- [ ] Code pushed to GitHub/GitLab

### Backend Deployment:
- [ ] Vercel project created
- [ ] Root directory set to "backend"
- [ ] All 10 environment variables added
- [ ] Deployment successful
- [ ] Health endpoint returns 200 OK
- [ ] Backend URL copied

### Frontend Deployment:
- [ ] Vercel project created (separate from backend)
- [ ] Root directory set to "frontend"
- [ ] VITE_API_URL set to backend URL + /api
- [ ] Deployment successful
- [ ] Landing page loads
- [ ] Frontend URL copied

### Post-Deployment:
- [ ] FRONTEND_URL updated in backend
- [ ] Backend redeployed
- [ ] Test user registration
- [ ] Test document upload (both files)
- [ ] Test interview flow (all 3 questions)
- [ ] Verify feedback shows at end
- [ ] Check S3 has uploaded files
- [ ] Check MongoDB has data

---

## Support and Documentation

- **Vercel Issues**: https://vercel.com/docs
- **MongoDB Atlas**: https://www.mongodb.com/docs/atlas/
- **AWS S3**: https://docs.aws.amazon.com/s3/
- **Project Setup**: See [VERCEL_DEPLOYMENT.md](./VERCEL_DEPLOYMENT.md)
- **S3 Setup**: See [AWS_S3_SETUP.md](./AWS_S3_SETUP.md)
- **Full Checklist**: See [DEPLOYMENT_CHECKLIST.md](./DEPLOYMENT_CHECKLIST.md)

---

## Next Steps After Deployment

1. **Test thoroughly** from different devices
2. **Share the link** with friends/recruiters
3. **Monitor logs** for first 24-48 hours
4. **Set up custom domain** (optional)
5. **Add to your portfolio**
6. **Update LinkedIn** with live project link

---

## Need Help?

If you encounter issues:

1. Check "Common Issues and Solutions" section above
2. Check backend Runtime Logs in Vercel
3. Check browser Console (F12) for frontend errors
4. Verify all environment variables are correct
5. Try redeploying both backend and frontend

Good luck with your deployment! 🚀
