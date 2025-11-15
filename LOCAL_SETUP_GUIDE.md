# Baaje Electronics - Local Setup Guide

## 🚀 Project Overview

**Baaje Electronics** is a modern e-commerce platform built with React 19, featuring:
- Product catalog with categories
- Shopping cart functionality
- User authentication (login/signup)
- Favorites/Wishlist system
- Admin panel
- Responsive design with Tailwind CSS
- Mobile-friendly interface

---

## 📋 Prerequisites

Before running the project locally, ensure you have:

- **Node.js** (v16 or higher) - [Download](https://nodejs.org/)
- **Yarn** (v1.22+) - [Download](https://yarnpkg.com/)
- **Git** - [Download](https://git-scm.com/)
- **A modern browser** (Chrome, Firefox, Safari, or Edge)

To check installed versions:
```bash
node --version
yarn --version
```

---

## 📂 Project Structure

```
frontend/
├── public/
│   └── index.html                 # Main HTML file
├── src/
│   ├── App.js                    # Root component with routing
│   ├── index.js                  # Entry point
│   ├── components/
│   │   ├── Navbar.js             # Navigation bar component
│   │   ├── Footer.js             # Footer component
│   │   ├── AuthModal.js          # Login/Signup modal
│   │   └── ui/                   # Shadcn UI components (pre-built)
│   ├── pages/
│   │   ├── HomePage.js           # Home/Products listing
│   │   ├── CategoryPage.js       # Category filtering
│   │   ├── ProductDetailPage.js  # Product details
│   │   ├── CartPage.js           # Shopping cart
│   │   ├── CheckoutPage.js       # Checkout process
│   │   ├── FavoritesPage.js      # Wishlist
│   │   ├── ProfilePage.js        # User profile
│   │   ├── AdminPanel.js         # Admin dashboard
│   │   └── AboutPage.js          # About page
│   ├── hooks/
│   │   └── use-toast.js          # Toast notifications hook
│   └── lib/
│       └── utils.js              # Utility functions
├── tailwind.config.js            # Tailwind CSS configuration
├── craco.config.js               # Create React App override config
├── package.json                  # Dependencies and scripts
└── README.md                      # Original CRA documentation
```

---

## ⚙️ Installation Steps

### Step 1: Clone/Navigate to Project
```bash
cd /home/lubugg/Desktop/finalig-main/frontend
```

### Step 2: Install Dependencies
Using Yarn (recommended):
```bash
yarn install
```

Or using npm:
```bash
npm install
```

**Note:** The project uses Yarn as the package manager (specified in `package.json`). A `node_modules/` folder will be created with all 1000+ dependencies.

### Step 3: Start Development Server
```bash
yarn start
```

Or with npm:
```bash
npm start
```

**What happens next:**
- The Craco build tool will compile your React app
- A development server will start on `http://localhost:3000`
- Your browser should open automatically
- The page reloads when you make code changes (Hot Module Replacement)

---

## 🌐 Accessing the Application

Once the server is running:

**URL:** `http://localhost:3000`

**Key Features Available:**

1. **Home Page** (`/`)
   - Browse products
   - Filter by category
   - Add items to cart

2. **Product Details** (`/product/:productId`)
   - View full product information
   - Add to cart
   - Add to favorites

3. **Shopping Cart** (`/cart`)
   - View cart items
   - Update quantities
   - Remove items
   - Proceed to checkout

4. **Checkout** (`/checkout`)
   - Review order
   - Enter shipping details
   - Complete purchase

5. **Favorites** (`/favorites`)
   - Manage wishlist items
   - Add to cart from favorites

6. **User Profile** (`/profile`)
   - View account information
   - Logout

7. **Admin Panel** (Access: `Ctrl + Alt + A`)
   - Dashboard with admin controls
   - Manage products and orders

---

## 🔧 Key Technologies & Dependencies

### Core Framework
- **React 19.0.0** - Modern UI library
- **React Router 7.5.1** - Client-side routing
- **React Hooks** - State management (useState, useEffect)

### UI & Styling
- **Tailwind CSS 3.4.17** - Utility-first CSS framework
- **Shadcn UI Components** - Pre-built accessible components:
  - Buttons, Cards, Modals, Forms, Tabs, etc.
- **Lucide React** - Icon library
- **Sonner** - Toast notifications

### Form Handling
- **React Hook Form 7.56.2** - Efficient form state management
- **Zod 3.24.4** - TypeScript-first schema validation
- **HookForm Resolvers** - Integration between form and validation

### UI Components (Radix UI)
- Accordion, Alert Dialog, Avatar, Badge
- Checkbox, Collapsible, Dialog, Drawer
- Dropdown Menu, Hover Card, Menubar
- Navigation Menu, Popover, Progress
- Radio Group, Scroll Area, Select
- Separator, Sheet, Skeleton, Slider
- Switch, Tabs, Toggle, Tooltip

### Utilities
- **Axios 1.8.4** - HTTP client for API calls
- **Date-fns 4.1.0** - Date manipulation
- **Embla Carousel** - Carousel/Slider component
- **Class Variance Authority** - Type-safe component variants
- **CLSX** - Conditional CSS class names

### Development Tools
- **Craco 7.1.0** - Create React App configuration override
- **PostCSS** - CSS processing
- **Autoprefixer** - Vendor prefix automation
- **ESLint & Prettier** - Code quality tools

---

## 🚀 Available Scripts

### Development
```bash
yarn start        # Start dev server (port 3000)
yarn test         # Run tests in watch mode
```

### Production
```bash
yarn build        # Create optimized build for production
yarn deploy       # Deploy to GitHub Pages
```

### Building
The build process:
1. Compiles React components
2. Minifies JavaScript and CSS
3. Optimizes images and assets
4. Creates a `build/` folder with production-ready files

**Build output location:** `/build` folder
**Note:** The `homepage` in package.json is set to GitHub Pages URL

---

## 💾 Local Storage Features

The app uses browser localStorage to persist:
- **User authentication data:** `token`, `user` (JSON)
- **Shopping cart:** `cart` (JSON array of products)
- **Favorites:** Managed through component state

Clear localStorage in browser DevTools Console:
```javascript
localStorage.clear();
```

---

## 🔐 Authentication System

### Implementation
- **Modal-based Auth** - `AuthModal.js` component
- **Login/Signup** - Toggle between modes
- **Token Storage** - JWT stored in localStorage
- **Protected Routes** - Favorites and Profile pages require login

### How it Works
1. User clicks "Login" in Navbar
2. AuthModal opens with login/signup forms
3. Form data validated with Zod schema
4. On success: Token and user data saved to localStorage
5. App state updated with user information
6. Protected pages become accessible

---

## 🛒 Shopping Cart Logic

### Features
- Add items with quantity selection
- Update quantities
- Remove individual items
- Clear entire cart
- Persistent storage across sessions

### Implementation (`App.js`)
```javascript
// Add to cart function
const addToCart = (product, quantity = 1) => {
  // Check if product already in cart
  // Update quantity or add as new item
};

// Update quantity
const updateCartQuantity = (productId, quantity) => {
  // Remove if quantity <= 0
  // Otherwise update quantity
};

// Remove from cart
const removeFromCart = (productId) => {
  // Filter out the product
};

// Clear cart
const clearCart = () => {
  setCart([]);
};
```

---

## 🎨 Customization Guide

### Change App Name/Title
Edit `public/index.html`:
```html
<title>Your Store Name</title>
<meta name="description" content="Your description">
```

### Modify Colors & Theme
Edit `tailwind.config.js`:
```javascript
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: '#your-color',
        // Add more colors
      },
    },
  },
}
```

### Add Navigation Links
Edit `src/components/Navbar.js` - Add routes in the navigation list

### Connect Real Backend API
In components/pages, replace mock data calls with:
```javascript
import axios from 'axios';

const fetchProducts = async () => {
  try {
    const response = await axios.get('https://your-api.com/products');
    setProducts(response.data);
  } catch (error) {
    console.error('Error:', error);
  }
};
```

---

## 🐛 Troubleshooting

### Issue: Port 3000 already in use
**Solution:**
```bash
# Option 1: Use a different port
PORT=3001 yarn start

# Option 2: Kill the process using port 3000
lsof -ti:3000 | xargs kill -9
```

### Issue: Dependencies installation fails
**Solution:**
```bash
# Clear cache and reinstall
rm -rf node_modules yarn.lock
yarn install
```

### Issue: Module not found errors
**Solution:**
```bash
# Clear cache
yarn cache clean

# Reinstall dependencies
yarn install
```

### Issue: Hot reload not working
**Solution:**
1. Check if file changes are detected
2. Restart dev server: `Ctrl + C` then `yarn start`
3. Clear browser cache (F12 → Settings → Clear cache)

### Issue: Build fails
**Solution:**
```bash
# Check for TypeScript/ESLint errors
yarn build

# Fix common issues
rm -rf build
yarn install
yarn build
```

---

## 📱 Mobile Testing

### Test on Mobile Devices
1. Find your machine's local IP:
```bash
# On Linux/Mac
ifconfig | grep "inet "

# On Windows
ipconfig
```

2. Open on mobile:
```
http://YOUR_IP:3000
```

### Mobile Viewport Testing
- Open DevTools (F12)
- Toggle Device Toolbar (Ctrl + Shift + M)
- Select different device sizes

---

## 🔄 Git Workflow

### Check status
```bash
git status
```

### Commit changes
```bash
git add .
git commit -m "Description of changes"
```

### Push to repository
```bash
git push origin main
```

---

## 📦 Build & Deployment

### Create Production Build
```bash
yarn build
```

This creates a `build/` folder ready for deployment.

### Deploy to GitHub Pages
```bash
yarn deploy
```

### Deployment Platforms
- **GitHub Pages** - `yarn deploy`
- **Vercel** - Connect GitHub repo to Vercel
- **Netlify** - Connect GitHub repo to Netlify
- **Traditional Server** - Upload `build/` folder

---

## 🆘 Getting Help

1. **Check Console** - Press F12 and check the Console tab for errors
2. **Network Tab** - Check API calls if backend connected
3. **React DevTools** - Install React DevTools extension for debugging
4. **Repo Issues** - Check GitHub repository issues
5. **Documentation** - Review official docs:
   - [React](https://react.dev)
   - [React Router](https://reactrouter.com)
   - [Tailwind CSS](https://tailwindcss.com)
   - [Shadcn UI](https://ui.shadcn.com)

---

## ✅ Verification Checklist

After setup, verify:
- ✓ Dev server running at `http://localhost:3000`
- ✓ Page loads without console errors
- ✓ Navigation links work
- ✓ Add to cart functionality works
- ✓ Local storage persists data
- ✓ Mobile responsive design works
- ✓ All UI components render correctly

---

## 📝 Additional Notes

- **Hot Reload:** Changes to files automatically refresh the page
- **Console Logging:** Use `console.log()` for debugging
- **React StrictMode:** App wrapped in StrictMode for development warnings
- **Source Maps:** Available for debugging minified code
- **PWA Ready:** App configured for Progressive Web App features

---

**Last Updated:** November 12, 2025

For more information, visit the project repository or documentation links above.
