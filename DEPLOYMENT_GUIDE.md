# Hostinger Deployment Guide for WebTraffic Analyzer

## Prerequisites
- Hostinger hosting account with Node.js support
- FTP/SFTP access to your hosting account
- Domain pointed to your hosting server

## Step-by-Step Deployment

### 1. Prepare Your Hostinger Account
1. Log into your Hostinger control panel (hPanel)
2. Go to **Advanced > SSH Access** and enable SSH if available
3. Note your hosting details:
   - Server IP address
   - FTP/SFTP credentials
   - Your domain name

### 2. Upload Files to Hostinger

#### Option A: Using File Manager (Recommended)
1. In hPanel, go to **Files > File Manager**
2. Navigate to your domain's `public_html` folder
3. Upload the entire project zip file
4. Extract the zip file in the `public_html` directory
5. Make sure all files are directly in `public_html` (not in a subfolder)

#### Option B: Using FTP Client
1. Download an FTP client like FileZilla
2. Connect using your FTP credentials
3. Upload all project files to the `public_html` directory

### 3. Install Dependencies
1. Access your hosting via SSH (if available) or use Hostinger's Terminal
2. Navigate to your project directory:
   ```bash
   cd public_html
   ```
3. Install Node.js dependencies:
   ```bash
   npm install
   ```

### 4. Configure Environment
1. Create a `.env` file in your project root:
   ```
   NODE_ENV=production
   PORT=3000
   ```
2. If using a database, add your database connection details

### 5. Build the Application
Run the build command:
```bash
npm run build
```

### 6. Start the Application

#### For Shared Hosting:
If Node.js apps aren't directly supported, you'll need to:
1. Use the static files from the `dist/public` folder
2. Set up a simple PHP redirect or use static hosting

#### For VPS/Cloud Hosting:
1. Start the application:
   ```bash
   npm start
   ```
2. Use PM2 for process management:
   ```bash
   npm install -g pm2
   pm2 start dist/index.js --name "webtraffic-analyzer"
   pm2 startup
   pm2 save
   ```

### 7. Configure Web Server

#### For Apache (.htaccess)
Create a `.htaccess` file in your `public_html`:
```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /
    
    # Handle Angular and React Router
    RewriteRule ^index\.html$ - [L]
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule . /index.html [L]
</IfModule>

# Enable compression
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/plain
    AddOutputFilterByType DEFLATE text/html
    AddOutputFilterByType DEFLATE text/xml
    AddOutputFilterByType DEFLATE text/css
    AddOutputFilterByType DEFLATE application/xml
    AddOutputFilterByType DEFLATE application/xhtml+xml
    AddOutputFilterByType DEFLATE application/rss+xml
    AddOutputFilterByType DEFLATE application/javascript
    AddOutputFilterByType DEFLATE application/x-javascript
</IfModule>
```

#### For Nginx
Add to your nginx configuration:
```nginx
location / {
    try_files $uri $uri/ /index.html;
}

location /api {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}
```

### 8. Verify Deployment
1. Visit your domain in a web browser
2. Test the analytics functionality
3. Check that all assets load correctly
4. Verify responsive design on mobile devices

## Troubleshooting

### Common Issues:
1. **404 Errors**: Check file paths and routing configuration
2. **API Not Working**: Verify Node.js server is running and ports are open
3. **Assets Not Loading**: Check file permissions and paths
4. **Performance Issues**: Enable compression and caching

### Hostinger-Specific Notes:
- Shared hosting may have limitations on Node.js apps
- Check if your plan supports Node.js applications
- Consider upgrading to VPS if you need full Node.js support

### Performance Optimization:
1. Enable browser caching
2. Compress images and assets
3. Use a CDN if available
4. Monitor resource usage

## Support
- Check Hostinger's documentation for Node.js hosting
- Contact Hostinger support for server-specific issues
- Verify your hosting plan supports the required features

## Files Structure After Deployment:
```
public_html/
├── dist/
│   ├── public/          # Static frontend files
│   └── index.js         # Server bundle
├── package.json
├── .htaccess           # Apache configuration
├── .env               # Environment variables
└── node_modules/      # Dependencies
```