# ⚡ Quick Start Guide - Baaje Electronics

## 🎯 Get Running in 2 Minutes

### Step 1: Install Dependencies (3 min)
```bash
cd /home/lubugg/Desktop/finalig-main/frontend
yarn install
```

### Step 2: Start Development Server (1 min)
```bash
yarn start
```

### Step 3: Open Browser
Navigate to: **`http://localhost:3000`**

---

## 🎮 What You Can Do Now

### Browse Products
- View products on home page
- Click on products to see details
- Filter by categories

### Try Shopping Features
- Click "Add to Cart"
- Go to cart (icon in navbar)
- Increase/decrease quantities
- Proceed to checkout

### Test Authentication
- Click "Login" button in navbar
- Enter test credentials:
  - **Email:** test@example.com
  - **Password:** test123
- Create new account to signup
- Access "Favorites" and "Profile" (requires login)

### Admin Panel
- Press: **`Ctrl + Alt + A`**
- Access admin dashboard (if logged in as admin)

---

## 🛠 Development Tips

### Make Code Changes
Edit files in `src/` folder - page will auto-refresh

### View Errors
Press **F12** to open Developer Tools → Console tab

### Stop Server
Press **Ctrl + C** in terminal

### Restart Server
```bash
yarn start
```

### Build for Production
```bash
yarn build
```
Output: `build/` folder

---

## 🗂️ Key Files to Know

| File | Purpose |
|------|---------|
| `src/App.js` | Main app logic & routing |
| `src/pages/HomePage.js` | Home page with products |
| `src/components/Navbar.js` | Top navigation |
| `src/components/AuthModal.js` | Login/Signup |
| `tailwind.config.js` | Styling configuration |
| `package.json` | Dependencies & scripts |

---

## 🆘 Common Issues

**Port 3000 in use?**
```bash
PORT=3001 yarn start
```

**Module errors?**
```bash
rm -rf node_modules yarn.lock
yarn install
yarn start
```

**Page not loading?**
1. Check console (F12)
2. Hard refresh (Ctrl+Shift+R)
3. Restart server

---

## 📚 Full Documentation

See **`LOCAL_SETUP_GUIDE.md`** for complete setup details, troubleshooting, and customization guide.

---

**Happy Coding! 🚀**
