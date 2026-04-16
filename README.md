# ToolVault — Full Stack AI Tools Directory

## Project Structure
```
toolvault/
├── backend/
│   ├── server.js       ← Express API + serves frontend
│   ├── db.js           ← JSON file database
│   ├── auth.js         ← JWT authentication
│   ├── data/
│   │   └── db.json     ← Auto-created on first run (your database)
│   └── package.json
├── frontend/
│   ├── public/
│   │   └── index.html  ← Your public website (copy toolvault-pro.html here)
│   └── admin/
│       └── index.html  ← Admin dashboard
└── start.sh
```

## Setup (Local)

### 1. Install Node.js
Download from https://nodejs.org (v18 or higher)

### 2. Install dependencies
```bash
cd toolvault/backend
npm install
```

### 3. Copy your public site
Copy `toolvault-pro.html` to `frontend/public/index.html`
Then update the `fetch` calls in the public site to use the API:
- Replace static TOOLS array with: `fetch('/api/tools')`

### 4. Start the server
```bash
node backend/server.js
# or
bash start.sh
```

### 5. Open in browser
- Public site:  http://localhost:3001
- Admin panel:  http://localhost:3001/admin
- Default login: admin / admin123
- ⚠️ CHANGE PASSWORD immediately after first login!

---

## Deploy to Production (Render.com — Free)

1. Push this folder to a GitHub repo
2. Go to https://render.com → New Web Service
3. Connect your GitHub repo
4. Settings:
   - Build command: `cd backend && npm install`
   - Start command: `cd backend && node server.js`
   - Environment: Node
5. Add environment variable: `JWT_SECRET=your-random-secret-here`
6. Deploy → your site is live!

## Deploy to Railway (easiest)

1. Install Railway CLI: `npm install -g @railway/cli`
2. `railway login`
3. `cd toolvault && railway init`
4. `railway up`
5. Done! 

## Deploy to VPS (DigitalOcean, Hetzner, etc.)

```bash
# On your server
git clone your-repo
cd toolvault/backend && npm install
# Install PM2 for process management
npm install -g pm2
pm2 start server.js --name toolvault
pm2 save && pm2 startup

# Install nginx for reverse proxy
# Point your domain to the server IP
# Configure nginx to proxy :80 → :3001
```

---

## API Reference

### Public Endpoints
| Method | Path | Description |
|--------|------|-------------|
| GET | /api/tools | List all approved tools (supports ?q=, ?category=, ?sort=) |
| GET | /api/tools/:id | Get single tool |
| POST | /api/tools/:id/click | Track outbound click |
| GET | /api/categories | List categories with counts |
| POST | /api/submit | Submit a new tool |
| GET | /api/settings/public | Public settings |

### Admin Endpoints (require Bearer token)
| Method | Path | Description |
|--------|------|-------------|
| POST | /api/admin/login | Login → get JWT token |
| GET | /api/admin/analytics?days=30 | Full analytics data |
| GET | /api/admin/tools | All tools (incl. unapproved) |
| POST | /api/admin/tools | Create tool |
| PUT | /api/admin/tools/:id | Update tool |
| DELETE | /api/admin/tools/:id | Delete tool |
| PATCH | /api/admin/tools/:id/featured | Toggle featured |
| PATCH | /api/admin/tools/:id/approved | Toggle visibility |
| GET | /api/admin/submissions | All submissions |
| POST | /api/admin/submissions/:id/approve | Approve → publish |
| PATCH | /api/admin/submissions/:id/reject | Reject |
| GET | /api/admin/settings | Get all settings |
| PUT | /api/admin/settings | Update settings |
| GET | /api/admin/export | Download full DB backup |
| POST | /api/admin/change-password | Change admin password |

---

## Connecting the Public Frontend to the API

In your `frontend/public/index.html`, replace the static TOOLS array 
with a fetch call. Find the `render()` function and add at the top:

```javascript
// Replace static render call with:
async function init() {
  const res  = await fetch('/api/tools');
  const data = await res.json();
  TOOLS = data.tools;
  render();
}
init();

// Track clicks on tool visit:
// In card click handler, add:
await fetch(`/api/tools/${tool.id}/click`, { method: 'POST' });
```

---

## Monetization Integration

### Google AdSense
1. Sign up at https://adsense.google.com
2. Get your Publisher ID (ca-pub-XXXXXXXXXX)
3. Add it in Admin → Settings → AdSense ID
4. Replace the ad placeholder divs in index.html with real `<ins>` tags

### Affiliate Links
Add affiliate tracking params to tool URLs in the database:
- Jasper: `?fpr=your-code`
- ElevenLabs: `?via=your-code`
Update via Admin → Tools Manager → Edit tool → URL field

---

## Security Checklist Before Going Live
- [ ] Change admin password from admin123
- [ ] Set JWT_SECRET environment variable (long random string)
- [ ] Enable HTTPS (automatic on Render/Railway, manual on VPS with Let's Encrypt)
- [ ] Set up regular db.json backups (Admin → Export or cron job)
- [ ] Review CORS settings in server.js for your domain

---

## Support
Built with: Node.js + Express + JSON DB + Chart.js
No external database required — db.json IS your database.
To migrate to PostgreSQL/MySQL later, swap db.js only.
