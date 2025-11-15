# 🔧 Solutions & Troubleshooting Guide

## Common Issues and Solutions

### 1. Port 3000 Already in Use

**Problem:** Error message: "Address already in use :::3000"

**Solution 1: Use Different Port**
```bash
PORT=3001 yarn start
# OR
PORT=3002 yarn start
```

**Solution 2: Kill Process Using Port 3000**
```bash
# On Linux/Mac
lsof -ti:3000 | xargs kill -9

# On Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

**Solution 3: Check What's Using the Port**
```bash
lsof -i :3000
# Shows: COMMAND PID USER
```

---

### 2. Dependencies Installation Fails

**Problem:** `yarn install` fails with error messages

**Solution: Clear Cache and Reinstall**
```bash
# Remove node_modules and lock files
rm -rf node_modules yarn.lock package-lock.json

# Clear yarn cache
yarn cache clean

# Reinstall
yarn install
```

**Alternative: Use npm instead**
```bash
npm install
npm start
```

**Check Node/Yarn Versions**
```bash
node --version  # Should be v16+
yarn --version  # Should be v1.22+
npm --version   # Should be v8+
```

---

### 3. Module Not Found Errors

**Problem:** "Module not found" or "Cannot find module"

**Example Error:**
```
ERROR in ./src/pages/HomePage.js
Module not found: Error: Can't resolve '@/pages/HomePage'
```

**Solution 1: Check Path Alias Configuration**
Verify `jsconfig.json`:
```json
{
  "compilerOptions": {
    "baseUrl": "src",
    "paths": {
      "@/*": ["*"]
    }
  }
}
```

**Solution 2: Fix Import Paths**
```javascript
// Wrong
import HomePage from './pages/HomePage';

// Correct
import HomePage from '@/pages/HomePage';
```

**Solution 3: Check File Exists**
```bash
# List all files
ls -la src/pages/

# Check specific file
test -f src/pages/HomePage.js && echo "File exists"
```

**Solution 4: Restart Dev Server**
```bash
# Press Ctrl + C to stop
# Then restart
yarn start
```

---

### 4. Hot Reload Not Working

**Problem:** Changes don't appear when editing files

**Quick Fixes:**
1. **Hard Refresh Browser:**
   - Windows: `Ctrl + Shift + R`
   - Mac: `Cmd + Shift + R`
   - Firefox: `Ctrl + F5`

2. **Restart Dev Server:**
   ```bash
   # Press Ctrl + C
   yarn start
   ```

3. **Clear Browser Cache:**
   - F12 → Application → Clear Storage → Clear All

4. **Check for Syntax Errors:**
   - Check Console tab in DevTools
   - Look for red error messages

---

### 5. Blank Page Loads

**Problem:** Browser shows blank page or white screen

**Diagnose:**
1. **Check Console (F12 → Console tab)** for errors
2. **Check Network tab** for failed requests
3. **Check React DevTools** for component tree

**Solutions:**

**If error: "Cannot read property 'map' of undefined"**
```javascript
// Wrong - assumes products always exists
products.map(p => ...)

// Correct - check first
products?.map(p => ...) || <p>Loading...</p>
```

**If error: "localStorage is not defined"**
```javascript
// Wrong - doesn't check if in browser
const data = JSON.parse(localStorage.getItem('key'));

// Correct
const data = typeof localStorage !== 'undefined' 
  ? JSON.parse(localStorage.getItem('key')) 
  : null;
```

**If no errors but blank page:**
```javascript
// Add console log to see if component renders
function HomePage() {
  console.log('HomePage component rendered');
  return <div>Home Page</div>;
}
```

---

### 6. Styling Issues (Tailwind CSS Not Working)

**Problem:** Classes like `bg-blue-500` not styling

**Solution 1: Check tailwind.config.js**
```javascript
module.exports = {
  content: [
    "./index.html",
    "./src/**/*.{js,jsx}",  // Make sure this includes all files
  ],
  // ... rest of config
}
```

**Solution 2: Rebuild CSS**
```bash
# Stop server (Ctrl + C)
rm -rf node_modules/.cache
yarn start
```

**Solution 3: Check Class Names**
```javascript
// Tailwind classes must be complete
<div className="bg-blue-500">  // Works
<div className="bg-blue-">     // Doesn't work - incomplete
<div className={`bg-${color}`}> // Doesn't work - dynamic classes
```

**Solution 4: Use clsx for Dynamic Classes**
```javascript
import clsx from 'clsx';

<div className={clsx(
  'bg-blue-500',
  isActive && 'opacity-100',
  !isActive && 'opacity-50'
)}>
```

---

### 7. API/Backend Connection Issues

**Problem:** Cannot connect to backend API

**Solution 1: Verify API is Running**
```bash
# Test if backend server is running
curl http://localhost:5000/api/health

# If not running, start your backend server
# Backend might be on different port
```

**Solution 2: Check CORS Issues**
```javascript
// Error in Console: "Access to XMLHttpRequest blocked by CORS policy"

// Backend needs to allow CORS from localhost:3000
// Add to backend (Express example):
const cors = require('cors');
app.use(cors({
  origin: 'http://localhost:3000',
  credentials: true
}));
```

**Solution 3: Fix API URLs**
```javascript
// Check .env file
REACT_APP_API_URL=http://localhost:5000

// Use in code
const apiUrl = process.env.REACT_APP_API_URL;
const response = await fetch(`${apiUrl}/api/products`);
```

**Solution 4: Add Error Handling**
```javascript
useEffect(() => {
  fetch(`${apiUrl}/products`)
    .then(res => {
      if (!res.ok) throw new Error(`HTTP error! status: ${res.status}`);
      return res.json();
    })
    .then(data => setProducts(data))
    .catch(error => {
      console.error('Fetch error:', error);
      setError(error.message);
    });
}, []);
```

---

### 8. Authentication/Login Not Working

**Problem:** Login form submits but doesn't work

**Solution 1: Check localStorage**
```javascript
// In browser console (F12)
localStorage.getItem('token')
localStorage.getItem('user')

// Clear if corrupted
localStorage.clear()
```

**Solution 2: Check Form Validation**
```javascript
// Add console logs
const onSubmit = async (data) => {
  console.log('Form data:', data); // Check what's being sent
  
  try {
    const response = await apiClient.post('/auth/login', data);
    console.log('Response:', response.data); // Check response
  } catch (error) {
    console.error('Error:', error.response?.data); // Check error
  }
};
```

**Solution 3: Verify Token Storage**
```javascript
// Make sure token is being saved
handleLogin = (userData, token) => {
  console.log('Storing token:', token);
  localStorage.setItem('token', token);
  localStorage.setItem('user', JSON.stringify(userData));
  setUser(userData);
};
```

**Solution 4: Check Auth Header in Requests**
```javascript
// Add to axios interceptor
apiClient.interceptors.request.use(config => {
  const token = localStorage.getItem('token');
  console.log('Token in header:', token); // Debug
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

---

### 9. Build Fails

**Problem:** `yarn build` fails with errors

**Solution 1: Check for Console Errors**
```bash
yarn build 2>&1 | tee build.log
# Check build.log for errors
```

**Solution 2: Fix JavaScript Errors**
```javascript
// Common errors:
// 1. Missing closing brackets
// 2. Undefined variables
// 3. Incorrect imports
```

**Solution 3: Check Environment Variables**
```bash
# Create .env file if missing
REACT_APP_API_URL=http://localhost:5000
REACT_APP_ENABLE_VISUAL_EDITS=false

# Make sure all env vars used in code are defined
echo $REACT_APP_API_URL  # Should print the URL
```

**Solution 4: Clear Build Cache**
```bash
rm -rf build/ node_modules/.cache
yarn build
```

---

### 10. Memory/Performance Issues

**Problem:** Dev server very slow or crashes

**Solution 1: Check System Resources**
```bash
# Check memory usage
free -h

# Check disk space
df -h
```

**Solution 2: Restart Everything**
```bash
# Kill all node processes
pkill -f node

# Clear caches
rm -rf node_modules/.cache

# Restart
yarn start
```

**Solution 3: Disable Unnecessary Plugins**
Check `craco.config.js` and disable unused plugins

**Solution 4: Use Production Mode for Testing**
```bash
yarn build
# Serve build directory locally
npx serve -s build -l 3000
```

---

### 11. React DevTools Not Working

**Problem:** React DevTools extension not showing components

**Solution 1: Install Extension**
- Chrome: [React DevTools](https://chrome.google.com/webstore/detail/react-developer-tools)
- Firefox: [React DevTools](https://addons.mozilla.org/firefox/addon/react-devtools/)

**Solution 2: Enable for Local Development**
```bash
# Make sure app is in development mode
yarn start  # Not production build

# Extension should work on localhost:3000
```

**Solution 3: Check Console for React Version**
```javascript
// In browser console:
React.version  // Should show 19.0.0 or similar
```

---

### 12. localStorage Errors

**Problem:** localStorage access denied or quota exceeded

**Solution 1: Clear Storage**
```javascript
// In browser console:
localStorage.clear()
sessionStorage.clear()

// Refresh page
location.reload()
```

**Solution 2: Check Private/Incognito Mode**
- localStorage may be disabled in private browsing
- Test in normal mode

**Solution 3: Check Storage Quota**
```javascript
// Get storage info
navigator.storage.estimate().then(estimate => {
  console.log('Used:', estimate.usage, 'bytes');
  console.log('Available:', estimate.quota, 'bytes');
});
```

**Solution 4: Handle Errors Gracefully**
```javascript
try {
  localStorage.setItem('key', 'value');
} catch (e) {
  if (e.name === 'QuotaExceededError') {
    console.log('Storage quota exceeded');
  }
}
```

---

### 13. Mobile Responsive Issues

**Problem:** App doesn't look right on mobile

**Solution 1: Test in DevTools**
```
F12 → Toggle Device Toolbar (Ctrl+Shift+M)
Select different device sizes
```

**Solution 2: Add Viewport Meta Tag**
Already in `public/index.html`:
```html
<meta name="viewport" content="width=device-width, initial-scale=1" />
```

**Solution 3: Use Responsive Classes**
```javascript
<div className="w-full md:w-1/2 lg:w-1/3 xl:w-1/4">
  // w-full = 100% on mobile
  // md:w-1/2 = 50% on medium screens
  // lg:w-1/3 = 33% on large screens
</div>
```

**Solution 4: Test on Real Device**
```bash
# Get local IP
ifconfig | grep "inet "

# Access on mobile
http://YOUR_IP:3000
```

---

### 14. Deployment Issues

**Problem:** App works locally but fails on production

**Common Causes:**
1. API URLs hardcoded to localhost
2. Missing environment variables
3. Case-sensitive file paths on Linux servers
4. Asset paths using relative URLs

**Solution 1: Use Environment Variables**
```javascript
// src/config.js
export const API_URL = process.env.REACT_APP_API_URL || 'http://localhost:5000';

// In components
import { API_URL } from '@/config';
const response = await fetch(`${API_URL}/products`);
```

**Solution 2: Check Build Output**
```bash
yarn build
ls -la build/

# Verify static/js and static/css folders exist
```

**Solution 3: Set Correct Homepage**
In `package.json`:
```json
{
  "homepage": "https://yourdomain.com/path"  // or "./" for root
}
```

---

## Quick Reference Troubleshooting

| Issue | Command to Try |
|-------|---|
| Port in use | `PORT=3001 yarn start` |
| Module errors | `rm -rf node_modules && yarn install` |
| Blank page | Press F12 and check Console |
| Styles not working | `yarn start` (restart dev server) |
| Can't connect API | Check backend is running, check CORS |
| Login not working | Check `localStorage` in DevTools |
| Build fails | Check for JavaScript syntax errors |
| Slow performance | Close other apps, restart dev server |
| Mobile test | Open DevTools → Toggle Device Toolbar |

---

## Getting Help

### Where to Check
1. **Browser Console** - F12 → Console tab
2. **Network Tab** - F12 → Network tab (for API calls)
3. **React DevTools** - Install extension from Chrome/Firefox store
4. **Terminal Output** - Check yarn/npm start output
5. **Build Logs** - `yarn build 2>&1 | tee build.log`

### Debug Tools
```javascript
// Add temporary console logs
console.log('Debug:', variable);
console.warn('Warning:', data);
console.error('Error:', error);
console.table(arrayData);

// Use debugger
debugger;  // Execution pauses here when DevTools open
```

### Resources
- React Docs: https://react.dev
- React Router: https://reactrouter.com
- Tailwind CSS: https://tailwindcss.com
- MDN Docs: https://developer.mozilla.org

---

**Last Updated:** November 12, 2025

For more solutions or if stuck, review the documentation or create an issue in the repository.
