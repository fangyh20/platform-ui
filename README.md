# RapidBuild Platform UI

Web-based dashboard for the RapidBuild platform. Allows users to create, manage, and deploy web applications.

## Tech Stack

- **React 18** with TypeScript
- **Vite** for fast development and optimized builds
- **TailwindCSS** for styling
- **React Router** for navigation
- **React Query** for server state management
- **Zustand** for client state management
- **Axios** with retry logic for API calls

## Environment Variables

Create a `.env` file based on `.env.example`:

```bash
# Development - uses local backend
VITE_API_BASE_URL=http://localhost:8092/api/v1

# Production - uses deployed backend
VITE_API_BASE_URL_PROD=https://api.rapidbuild.app/api/v1
```

## Development

```bash
# Install dependencies
npm install

# Start development server (http://localhost:5173)
npm run dev

# Type checking
npm run build

# Linting
npm run lint
```

**Note:** During development, the Vite proxy forwards `/api/*` requests to `http://localhost:8092`. Make sure the Platform API is running locally on port 8092.

## Building for Production

```bash
# Build optimized production bundle
VITE_API_BASE_URL=$VITE_API_BASE_URL_PROD npm run build

# Output will be in ./dist directory
```

The build is optimized with:
- Code splitting for React and UI libraries
- Minified with esbuild
- No source maps (for security)

## Deployment

### Option 1: Cloudflare Pages (Recommended)

**Why Cloudflare:**
- Global CDN for fast loading worldwide
- Automatic HTTPS
- Zero-downtime deployments
- Free tier available

**Steps:**

1. **Build the app:**
   ```bash
   npm run build
   ```

2. **Deploy with Wrangler:**
   ```bash
   npx wrangler deploy
   ```

3. **Configure custom domain:**
   - Go to Cloudflare Pages dashboard
   - Add custom domain: `app.rapidbuild.app`
   - Update DNS records as instructed

### Option 2: Self-Hosted with Nginx

1. **Build for production:**
   ```bash
   npm run build
   ```

2. **Copy build to web server:**
   ```bash
   sudo cp -r dist/* /var/www/app.rapidbuild.app/
   sudo chown -R www-data:www-data /var/www/app.rapidbuild.app
   ```

3. **Configure nginx:**
   See `DEPLOYMENT_GUIDE.md` in the root directory for full nginx configuration.

4. **Obtain SSL certificate:**
   ```bash
   sudo certbot --nginx -d app.rapidbuild.app
   ```

## Production URLs

- **Platform UI:** https://app.rapidbuild.app
- **Platform API:** https://api.rapidbuild.app
- **Auth Service:** https://auth.rapidbuild.app
- **Data Service:** https://data.rapidbuild.app

## Project Structure

```
platform-ui/
├── src/
│   ├── lib/           # Core libraries
│   │   ├── api.ts     # API client & types
│   │   ├── auth.ts    # Authentication client
│   │   ├── store.ts   # Zustand state management
│   │   └── utils.ts   # Utility functions
│   ├── pages/         # Page components
│   │   ├── Dashboard.tsx        # Main dashboard
│   │   ├── AppDetail.tsx        # App detail & versions
│   │   ├── NewApp.tsx           # Create new app
│   │   ├── Login.tsx            # Login page
│   │   ├── Signup.tsx           # Signup page
│   │   ├── ForgotPassword.tsx   # Password reset request
│   │   ├── ResetPassword.tsx    # Password reset form
│   │   ├── VerifyEmail.tsx      # Email verification
│   │   └── GoogleCallback.tsx   # OAuth callback
│   ├── App.tsx        # Root component with routing
│   └── main.tsx       # Entry point
├── public/            # Static assets
├── dist/              # Production build (generated)
└── wrangler.jsonc     # Cloudflare deployment config
```

## Features

### App Management
- Create new apps with AI code generation
- View all apps in dashboard
- Deploy new versions
- Promote versions to production
- Delete apps

### Version Control
- Track all app versions
- View build logs and status
- Preview versions before deploying
- Rollback to previous versions

### Collaboration
- Add comments to app versions
- Track change requests
- Review and approve changes

### Authentication
- Email/password authentication
- Google OAuth integration
- Password reset flow
- Email verification
- Session management with token refresh

## API Integration

The UI communicates with three backend services:

1. **Platform API** (`api.rapidbuild.app`)
   - App CRUD operations
   - Version management
   - Build & deployment
   - File uploads to S3

2. **Auth Service** (`auth.rapidbuild.app`)
   - User authentication
   - OAuth flows
   - Password reset

3. **Data Service** (`data.rapidbuild.app`)
   - User-created app data (not used by Platform UI directly)
   - Used by deployed apps via SDK

## Development Notes

- The app uses React Query for server state caching
- Axios retry logic handles transient network errors
- Token refresh happens automatically on 401 responses
- SSE (Server-Sent Events) for real-time build progress

## Troubleshooting

### Build fails with TypeScript errors
```bash
# Run type checking
npm run build
```

### API calls fail in development
- Ensure Platform API is running on `http://localhost:8092`
- Check `.env` file has correct `VITE_API_BASE_URL`
- Check browser console for CORS errors

### Production build doesn't load
- Verify `VITE_API_BASE_URL_PROD` is set correctly
- Check nginx configuration for correct routing
- Ensure SSL certificates are valid

---

**Deployed at:** https://app.rapidbuild.app
**Repository:** github.com/fangyh20/platform-ui
**Version:** 1.0.0
