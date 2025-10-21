# Deployment Checklist

## ✅ Pre-Deployment Setup

### 1. AWS S3 Setup (Required)
- [ ] Create AWS account
- [ ] Create S3 bucket (see [AWS_S3_SETUP.md](./AWS_S3_SETUP.md))
- [ ] Create IAM user with S3 permissions
- [ ] Get Access Key ID and Secret Access Key
- [ ] Test S3 upload locally

### 2. MongoDB Atlas Setup (Required)
- [ ] Create MongoDB Atlas account
- [ ] Create free cluster
- [ ] Create database user
- [ ] Whitelist all IP addresses (0.0.0.0/0)
- [ ] Get connection string (MONGODB_URI)

### 3. OpenAI API Setup (Required)
- [ ] Get OpenAI API key
- [ ] Or use MegaLLM endpoint (already configured in `.env.example`)

### 4. Git Repository (Required)
- [ ] Push code to GitHub/GitLab/Bitbucket
- [ ] Ensure `.env` files are gitignored
- [ ] Verify all dependencies are in `package.json`

## 🚀 Vercel Deployment

### Backend Deployment

1. **Go to Vercel Dashboard**
   - Visit: https://vercel.com/dashboard
   - Click "Add New" → "Project"

2. **Import Repository**
   - Select your Git repository
   - Click "Import"

3. **Configure Project Settings**
   - **Project Name**: `ai-interview-resumeparser` (or your choice)
   - **Root Directory**: `backend` ⚠️ **IMPORTANT**
   - **Framework Preset**: Other
   - **Build Command**: Leave empty
   - **Output Directory**: Leave empty
   - **Install Command**: `npm install`

4. **Add Environment Variables**
   Click "Environment Variables" and add:

   ```
   MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/interview-prep

   JWT_SECRET=your-super-secret-random-string-min-32-chars
   JWT_EXPIRE=7d

   AWS_ACCESS_KEY_ID=AKIA...your-key
   AWS_SECRET_ACCESS_KEY=your-secret-key
   AWS_REGION=us-east-1
   AWS_S3_BUCKET_NAME=your-bucket-name

   OPENAI_API_KEY=sk-...your-key
   OPENAI_MODEL=gpt-4o-mini-2024-07-18
   OPENAI_EMBEDDING_MODEL=text-embedding-3-large

   NODE_ENV=production
   FRONTEND_URL=https://your-frontend-url.vercel.app
   ```

5. **Deploy**
   - Click "Deploy"
   - Wait for deployment to complete
   - Copy the backend URL (e.g., `https://ai-interview-resumeparser.vercel.app`)

### Frontend Deployment

1. **Go to Vercel Dashboard** (again)
   - Click "Add New" → "Project"

2. **Import SAME Repository**
   - Select the same repository
   - Click "Import"

3. **Configure Project Settings**
   - **Project Name**: `ai-interview-frontend` (or your choice)
   - **Root Directory**: `frontend` ⚠️ **IMPORTANT**
   - **Framework Preset**: Vite
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`
   - **Install Command**: `npm install`

4. **Add Environment Variables**
   ```
   VITE_API_URL=https://your-backend-url.vercel.app/api
   ```
   ⚠️ **IMPORTANT**: Use the backend URL from step 5 above + `/api`

5. **Deploy**
   - Click "Deploy"
   - Wait for deployment
   - Get your frontend URL

6. **Update Backend CORS**
   - Go back to backend project settings
   - Update `FRONTEND_URL` environment variable with your actual frontend URL
   - Redeploy backend

## 🧪 Testing Deployment

### 1. Test Authentication
- [ ] Visit frontend URL
- [ ] Sign up with new account
- [ ] Verify you can login
- [ ] Check MongoDB - user should be created

### 2. Test Document Upload
- [ ] Upload resume (PDF)
- [ ] Upload job description (PDF)
- [ ] Check S3 bucket - files should appear
- [ ] Check MongoDB - documents should be saved with embeddings

### 3. Test Interview
- [ ] Click "Start Interview"
- [ ] Answer first question
- [ ] Verify second question appears
- [ ] Answer second question
- [ ] Answer third question
- [ ] Verify you get comprehensive feedback with scores
- [ ] Check citations work

### 4. Check Backend Logs
- [ ] Go to Vercel backend project → Deployments
- [ ] Click latest deployment → Runtime Logs
- [ ] Verify no errors

## 🐛 Common Issues & Fixes

### Issue: "Failed to check documents" / "Failed to start interview"
**Fix**: Check MongoDB connection string and verify database is accessible

### Issue: "Failed to upload file to S3"
**Fix**:
- Verify AWS credentials are correct
- Check IAM user has PutObject permission
- Verify bucket name is correct

### Issue: CORS errors in browser console
**Fix**:
- Verify FRONTEND_URL is set correctly in backend
- Check frontend is using correct backend URL
- Redeploy backend after changing CORS settings

### Issue: 404 on all API routes
**Fix**:
- Verify backend root directory is set to `backend`
- Check `backend/vercel.json` exists
- Redeploy backend

### Issue: "MongoServerError: bad auth"
**Fix**:
- Check MongoDB username/password in connection string
- Verify database user is created in MongoDB Atlas
- Check IP whitelist includes 0.0.0.0/0

## 📊 Monitoring

### Check Logs
- **Backend**: Vercel Dashboard → Backend Project → Deployments → Runtime Logs
- **Frontend**: Browser Console (F12)

### Performance
- **Backend**: Vercel Dashboard → Analytics
- **MongoDB**: MongoDB Atlas → Metrics
- **S3**: AWS Console → S3 → Metrics

## 💰 Cost Tracking

### Free Tiers
- **Vercel**: Unlimited deployments, 100GB bandwidth/month
- **MongoDB Atlas**: 512MB storage, shared CPU
- **AWS S3**: 5GB storage, 20K GET, 2K PUT (12 months)
- **OpenAI**: Pay per use (~$0.002/interview)

### Estimated Costs (after free tiers)
- **100 users/month**: ~$1-2
- **1,000 users/month**: ~$10-15
- **10,000 users/month**: ~$100-150

## 🔒 Security Checklist

- [ ] `.env` files are gitignored
- [ ] MongoDB uses strong password
- [ ] AWS IAM user has minimal permissions only
- [ ] JWT_SECRET is strong random string (32+ characters)
- [ ] S3 bucket has "Block all public access" enabled
- [ ] CORS only allows specific origins (update line 41 in server.js if needed)
- [ ] Rate limiting is enabled (100 req/15min)

## 🎉 Post-Deployment

- [ ] Share frontend URL with friends/recruiters
- [ ] Monitor logs for first 24 hours
- [ ] Test from different devices/browsers
- [ ] Set up custom domain (optional)
- [ ] Add to portfolio
- [ ] Update LinkedIn/resume with live link

## 📱 Custom Domain (Optional)

### Vercel Custom Domain
1. Go to Frontend Project → Settings → Domains
2. Add your custom domain
3. Update DNS records as instructed
4. Update FRONTEND_URL in backend
5. Redeploy backend

## 🆘 Need Help?

- **Vercel Issues**: https://vercel.com/support
- **MongoDB Issues**: https://www.mongodb.com/docs/atlas/
- **AWS Issues**: https://docs.aws.amazon.com/s3/
- **Project Issues**: Check [VERCEL_DEPLOYMENT.md](./VERCEL_DEPLOYMENT.md) and [AWS_S3_SETUP.md](./AWS_S3_SETUP.md)

---

## Quick Command Reference

### Redeploy
```bash
# Vercel will auto-deploy on git push
git add .
git commit -m "Update configuration"
git push
```

### View Logs
```bash
# Install Vercel CLI
npm i -g vercel

# Login
vercel login

# View logs
vercel logs [deployment-url]
```

### Test Locally
```bash
# Backend
cd backend
npm install
npm run dev

# Frontend (new terminal)
cd frontend
npm install
npm run dev
```
