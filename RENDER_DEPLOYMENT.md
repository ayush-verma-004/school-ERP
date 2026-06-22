# Render Deployment Guide

This guide provides step-by-step instructions on how to deploy the School ERP application (Backend & Frontend) to **Render**.

---

## 🛠️ Prerequisites

1. A **Render** account. Sign up at [render.com](https://render.com/).
2. A **MongoDB Atlas** cluster (or any MongoDB database instance).
3. The codebase pushed to your GitHub repository: `https://github.com/ayush-verma-004/school-ERP`.

---

## 🖥️ Part 1: Backend Deployment (Web Service)

The backend is an Express server located in the `backend/` directory.

### Step 1: Create a New Web Service
1. Log in to your [Render Dashboard](https://dashboard.render.com/).
2. Click the **New +** button and select **Web Service**.
3. Connect your GitHub repository (`school-ERP`).

### Step 2: Configure Web Service Settings
Fill in the following details in the creation form:

| Field | Value |
| :--- | :--- |
| **Name** | `school-erp-backend` (or your preferred name) |
| **Region** | Select the region closest to your users (e.g., `Singapore` or `Oregon`) |
| **Branch** | `main` |
| **Root Directory** | `backend` |
| **Runtime** | `Node` |
| **Build Command** | `npm install` |
| **Start Command** | `node server.js` |
| **Instance Type** | `Free` (or any tier of your choice) |

### Step 3: Add Environment Variables
Scroll down and click **Advanced** -> **Add Environment Variable**. Add the following variables:

| Key | Value / Description |
| :--- | :--- |
| `PORT` | `5000` (or leave empty, Render automatically configures `PORT`) |
| `DB_URL` | Your MongoDB connection string (e.g., `mongodb+srv://...`) |
| `JWT_SECRET` | A secure random string for signing JWT tokens |
| `EMAIL_USER` | Your email address (for Nodemailer notifications) |
| `EMAIL_PASS` | Your email App Password (not your primary password) |
| `RAZORPAY_KEY_ID` | Your Razorpay API Key ID |
| `RAZORPAY_KEY_SECRET` | Your Razorpay API Key Secret |
| `FRONTEND_URL` | (Optional) Your deployed frontend URL (e.g. `https://school-erp-frontend-r8qc.onrender.com`) to allow CORS requests |

> [!NOTE]
> Make sure your MongoDB Atlas Database has Network Access configured to allow access from anywhere (`0.0.0.0/0`), as Render's outbound IP addresses are dynamic.

### Step 4: Deploy
Click **Create Web Service**. Render will build and deploy your backend. Once complete, copy the backend URL provided by Render (e.g., `https://school-erp-backend.onrender.com`).

---

## 🌐 Part 2: Frontend Deployment (Static Site)

The frontend is a React application located in the `frontend/` directory.

### Step 1: Update the API URL in Frontend
Before deploying the frontend, verify the backend base URL in your frontend code.
Open [frontend/src/api/axios.js](file:///d:/spidy/GitHub-Spidy/school-ERP/frontend/src/api/axios.js) and make sure the production URL matches your newly deployed Render Backend URL:

```javascript
const API = axios.create({
  baseURL: isLocalhost 
    ? 'http://localhost:5000' 
    : 'https://your-deployed-backend-url.onrender.com', // Replace with your backend URL
});
```

*Don't forget to push this change to GitHub before deploying the frontend.*

### Step 2: Create a New Static Site on Render
1. Go to your [Render Dashboard](https://dashboard.render.com/).
2. Click **New +** and select **Static Site**.
3. Connect the same GitHub repository (`school-ERP`).

### Step 3: Configure Static Site Settings
Fill in the following details:

| Field | Value |
| :--- | :--- |
| **Name** | `school-erp-frontend` |
| **Branch** | `main` |
| **Root Directory** | `frontend` |
| **Build Command** | `npm run build` |
| **Publish Directory** | `build` |

### Step 4: Add Rewrite Rules (For React Router Support)
Since React uses client-side routing (React Router), you must configure Render to redirect all requests to `index.html` to avoid `404 Not Found` errors when refreshing subpages.

1. Go to your **Static Site Dashboard** on Render.
2. Select **Redirects/Rewrites** from the left sidebar.
3. Click **Add Rule** and configure:
   - **Source**: `/*`
   - **Destination**: `/index.html`
   - **Action**: `Rewrite`
4. Click **Save Changes**.

---

## 🚀 Part 3: Deploying Changes to GitHub

Whenever you make updates locally, run the following commands to push them to GitHub and trigger automatic redeployments on Render:

```bash
# Add all changes
git add .

# Commit changes
git commit -m "docs: add render deployment guide"

# Push to Main branch
git push origin main
```
