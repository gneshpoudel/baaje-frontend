# 📖 Baaje Electronics - Base Code Documentation

## Table of Contents
1. [App.js - Root Component](#appjs---root-component)
2. [Routing System](#routing-system)
3. [State Management](#state-management)
4. [Component Architecture](#component-architecture)
5. [API Integration Pattern](#api-integration-pattern)
6. [Common Code Examples](#common-code-examples)

---

## App.js - Root Component

### Overview
`src/App.js` is the root component that manages:
- User authentication state
- Shopping cart state
- Route definitions
- Global event handlers

### Full Code Breakdown

```javascript
import { useState, useEffect } from 'react';
import { HashRouter, Routes, Route, Navigate } from 'react-router-dom';
import '@/App.css';

// Import all pages and components
import HomePage from '@/pages/HomePage';
import CategoryPage from '@/pages/CategoryPage';
import ProductDetailPage from '@/pages/ProductDetailPage';
import FavoritesPage from '@/pages/FavoritesPage';
import CartPage from '@/pages/CartPage';
import CheckoutPage from '@/pages/CheckoutPage';
import AboutPage from '@/pages/AboutPage';
import ProfilePage from '@/pages/ProfilePage';
import AdminPanel from '@/pages/AdminPanel';
import Navbar from '@/components/Navbar';
import Footer from '@/components/Footer';
import AuthModal from '@/components/AuthModal';
import { Toaster } from '@/components/ui/sonner';

function App() {
  // STATE MANAGEMENT
  const [user, setUser] = useState(null);                    // Logged-in user
  const [showAuthModal, setShowAuthModal] = useState(false); // Auth modal visibility
  const [authMode, setAuthMode] = useState('login');         // login or signup
  const [cart, setCart] = useState([]);                      // Shopping cart items
  const [showAdmin, setShowAdmin] = useState(false);         // Admin panel visibility

  // INITIALIZATION EFFECT
  useEffect(() => {
    // 1. Load user from localStorage if exists
    const token = localStorage.getItem('token');
    const savedUser = localStorage.getItem('user');
    if (token && savedUser) {
      setUser(JSON.parse(savedUser));
    }

    // 2. Load cart from localStorage if exists
    const savedCart = localStorage.getItem('cart');
    if (savedCart) {
      setCart(JSON.parse(savedCart));
    }

    // 3. Admin panel shortcut: Ctrl + Alt + A
    const handleKeyPress = (e) => {
      if (e.ctrlKey && e.altKey && e.key === 'a') {
        e.preventDefault();
        setShowAdmin(true);
      }
    };

    window.addEventListener('keydown', handleKeyPress);
    return () => window.removeEventListener('keydown', handleKeyPress);
  }, []);

  // PERSIST CART TO LOCALSTORAGE
  useEffect(() => {
    localStorage.setItem('cart', JSON.stringify(cart));
  }, [cart]);

  // LOGIN HANDLER
  const handleLogin = (userData, token) => {
    setUser(userData);
    localStorage.setItem('token', token);
    localStorage.setItem('user', JSON.stringify(userData));
    setShowAuthModal(false);
  };

  // LOGOUT HANDLER
  const handleLogout = () => {
    setUser(null);
    localStorage.removeItem('token');
    localStorage.removeItem('user');
  };

  // OPEN AUTH MODAL
  const openAuth = (mode = 'login') => {
    setAuthMode(mode);
    setShowAuthModal(true);
  };

  // ADD TO CART - Combines quantities if product already exists
  const addToCart = (product, quantity = 1) => {
    setCart(prevCart => {
      const existingItem = prevCart.find(item => item.id === product.id);
      if (existingItem) {
        return prevCart.map(item =>
          item.id === product.id
            ? { ...item, quantity: item.quantity + quantity }
            : item
        );
      }
      return [...prevCart, { ...product, quantity }];
    });
  };

  // UPDATE CART QUANTITY
  const updateCartQuantity = (productId, quantity) => {
    if (quantity <= 0) {
      removeFromCart(productId);
    } else {
      setCart(prevCart =>
        prevCart.map(item =>
          item.id === productId ? { ...item, quantity } : item
        )
      );
    }
  };

  // REMOVE FROM CART
  const removeFromCart = (productId) => {
    setCart(prevCart => prevCart.filter(item => item.id !== productId));
  };

  // CLEAR CART
  const clearCart = () => {
    setCart([]);
  };

  // RETURN JSX
  return (
    <HashRouter>
      <div className="app-container">
        <Navbar
          user={user}
          onLogout={handleLogout}
          onOpenAuth={openAuth}
          cartCount={cart.reduce((sum, item) => sum + item.quantity, 0)}
        />

        <main className="main-content">
          <Routes>
            {/* Public Routes */}
            <Route path="/" element={<HomePage addToCart={addToCart} user={user} onOpenAuth={openAuth} />} />
            <Route path="/category/:categoryId" element={<CategoryPage addToCart={addToCart} user={user} onOpenAuth={openAuth} />} />
            <Route path="/product/:productId" element={<ProductDetailPage addToCart={addToCart} user={user} onOpenAuth={openAuth} />} />
            <Route path="/cart" element={<CartPage cart={cart} updateQuantity={updateCartQuantity} removeFromCart={removeFromCart} />} />
            <Route path="/checkout" element={<CheckoutPage cart={cart} clearCart={clearCart} user={user} />} />
            <Route path="/about" element={<AboutPage />} />

            {/* Protected Routes - Redirect to home if not logged in */}
            <Route path="/favorites" element={user ? <FavoritesPage addToCart={addToCart} user={user} /> : <Navigate to="/" />} />
            <Route path="/profile" element={user ? <ProfilePage user={user} onLogout={handleLogout} /> : <Navigate to="/" />} />
          </Routes>
        </main>

        <Footer />

        {/* Auth Modal */}
        {showAuthModal && (
          <AuthModal
            mode={authMode}
            onClose={() => setShowAuthModal(false)}
            onLogin={handleLogin}
            onSwitchMode={(mode) => setAuthMode(mode)}
          />
        )}

        {/* Admin Panel */}
        {showAdmin && (
          <AdminPanel onClose={() => setShowAdmin(false)} />
        )}

        {/* Toast Notifications */}
        <Toaster position="top-right" />
      </div>
    </HashRouter>
  );
}

export default App;
```

---

## Routing System

### Route Structure

```javascript
<Routes>
  {/* PUBLIC ROUTES - Anyone can access */}
  <Route path="/" element={<HomePage />} />
  <Route path="/category/:categoryId" element={<CategoryPage />} />
  <Route path="/product/:productId" element={<ProductDetailPage />} />
  <Route path="/cart" element={<CartPage />} />
  <Route path="/checkout" element={<CheckoutPage />} />
  <Route path="/about" element={<AboutPage />} />

  {/* PROTECTED ROUTES - Only logged-in users */}
  <Route path="/favorites" element={user ? <FavoritesPage /> : <Navigate to="/" />} />
  <Route path="/profile" element={user ? <ProfilePage /> : <Navigate to="/" />} />
</Routes>
```

### Navigation Methods

```javascript
// From component:
import { useNavigate } from 'react-router-dom';

function MyComponent() {
  const navigate = useNavigate();
  
  const goToProduct = (productId) => {
    navigate(`/product/${productId}`);
  };
  
  const goBack = () => {
    navigate(-1);
  };
}
```

### Get URL Parameters

```javascript
import { useParams } from 'react-router-dom';

function ProductDetailPage() {
  const { productId } = useParams();
  // productId now contains the ID from URL
}
```

---

## State Management

### Global State Flow

```
┌─────────────────────────────────────────┐
│            App.js (Global State)        │
├─────────────────────────────────────────┤
│  • user                (auth state)     │
│  • cart                (shopping)       │
│  • showAuthModal       (UI)             │
│  • showAdmin           (UI)             │
└────────────┬──────────────────┬─────────┘
             │                  │
    ┌────────▼──────┐   ┌──────▼─────────┐
    │   Navbar      │   │   HomePage    │
    │   Footer      │   │   CartPage    │
    │   AuthModal   │   │   CheckoutPage│
    └───────────────┘   └───────────────┘
```

### LocalStorage Keys

```javascript
// User data
localStorage.setItem('token', 'jwt_token_here');
localStorage.setItem('user', JSON.stringify({ id, name, email }));

// Cart data
localStorage.setItem('cart', JSON.stringify([
  { id: 1, name: 'Product', price: 100, quantity: 2 },
  { id: 2, name: 'Product2', price: 50, quantity: 1 }
]));

// Retrieve data
const user = JSON.parse(localStorage.getItem('user'));
const cart = JSON.parse(localStorage.getItem('cart'));
const token = localStorage.getItem('token');

// Clear all
localStorage.clear();
```

### State Update Patterns

```javascript
// 1. SIMPLE STATE UPDATE
const [user, setUser] = useState(null);
setUser(userData);

// 2. CONDITIONAL UPDATE (depends on current state)
setCart(prevCart => {
  // prevCart is the current state
  return [...prevCart, newItem];
});

// 3. ASYNC STATE UPDATE
useEffect(() => {
  fetchData().then(data => {
    setUser(data);
  });
}, []); // Empty dependency = run once on mount

// 4. CLEANUP ON UNMOUNT
useEffect(() => {
  const handler = (e) => console.log(e);
  window.addEventListener('click', handler);
  
  return () => {
    // This cleanup function runs when component unmounts
    window.removeEventListener('click', handler);
  };
}, []);
```

---

## Component Architecture

### Example: HomePage Component

```javascript
import { useState, useEffect } from 'react';
import ProductCard from '@/components/ProductCard';

function HomePage({ addToCart, user, onOpenAuth }) {
  // LOCAL STATE
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // FETCH DATA ON MOUNT
  useEffect(() => {
    const fetchProducts = async () => {
      try {
        setLoading(true);
        // TODO: Replace with real API call
        const response = await fetch('/api/products');
        const data = await response.json();
        setProducts(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchProducts();
  }, []);

  // HANDLE ADD TO CART
  const handleAddToCart = (product) => {
    if (!user) {
      onOpenAuth('login'); // Require login
      return;
    }
    addToCart(product, 1);
  };

  // RENDER
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div className="home-page">
      <h1>Products</h1>
      <div className="grid grid-cols-3 gap-4">
        {products.map(product => (
          <ProductCard
            key={product.id}
            product={product}
            onAddToCart={handleAddToCart}
          />
        ))}
      </div>
    </div>
  );
}

export default HomePage;
```

---

## API Integration Pattern

### Setup Axios

```javascript
// src/api/client.js
import axios from 'axios';

const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:5000';

const apiClient = axios.create({
  baseURL: API_BASE_URL,
  timeout: 10000,
});

// Add auth token to requests
apiClient.interceptors.request.use(config => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

export default apiClient;
```

### Using API Client

```javascript
// In component
import apiClient from '@/api/client';

useEffect(() => {
  // GET request
  apiClient.get('/products')
    .then(response => setProducts(response.data))
    .catch(error => console.error(error));
}, []);

// POST request
const handleCreateProduct = async (productData) => {
  try {
    const response = await apiClient.post('/products', productData);
    console.log('Created:', response.data);
  } catch (error) {
    console.error('Error:', error.response.data);
  }
};

// PUT request
const handleUpdateProduct = async (productId, updatedData) => {
  try {
    const response = await apiClient.put(`/products/${productId}`, updatedData);
    console.log('Updated:', response.data);
  } catch (error) {
    console.error('Error:', error);
  }
};

// DELETE request
const handleDeleteProduct = async (productId) => {
  try {
    await apiClient.delete(`/products/${productId}`);
    console.log('Deleted');
  } catch (error) {
    console.error('Error:', error);
  }
};
```

---

## Common Code Examples

### Example 1: Form Submission with Validation

```javascript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

// Define validation schema
const loginSchema = z.object({
  email: z.string().email('Invalid email'),
  password: z.string().min(6, 'Password too short'),
});

function LoginForm({ onLogin }) {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: zodResolver(loginSchema),
  });

  const onSubmit = async (data) => {
    try {
      // Send to API
      const response = await apiClient.post('/auth/login', data);
      onLogin(response.data.user, response.data.token);
    } catch (error) {
      console.error('Login failed:', error);
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register('email')}
        placeholder="Email"
        type="email"
      />
      {errors.email && <p>{errors.email.message}</p>}

      <input
        {...register('password')}
        placeholder="Password"
        type="password"
      />
      {errors.password && <p>{errors.password.message}</p>}

      <button type="submit">Login</button>
    </form>
  );
}
```

### Example 2: Conditional Rendering

```javascript
function ProductCard({ product, user, onOpenAuth }) {
  // Show different content based on conditions
  return (
    <div className="card">
      <h3>{product.name}</h3>
      <p>${product.price}</p>

      {/* If logged in: show all buttons */}
      {user ? (
        <>
          <button>Add to Cart</button>
          <button>Add to Favorites</button>
        </>
      ) : (
        /* If not logged in: show login prompt */
        <button onClick={() => onOpenAuth('login')}>
          Login to Shop
        </button>
      )}

      {/* Show stock status */}
      {product.stock > 0 ? (
        <p className="text-green-500">In Stock</p>
      ) : (
        <p className="text-red-500">Out of Stock</p>
      )}
    </div>
  );
}
```

### Example 3: Filter and Sort

```javascript
function ProductList({ products }) {
  const [filters, setFilters] = useState({
    category: 'all',
    minPrice: 0,
    maxPrice: 1000,
    sortBy: 'name',
  });

  // Filter products
  const filtered = products
    .filter(p => 
      (filters.category === 'all' || p.category === filters.category) &&
      p.price >= filters.minPrice &&
      p.price <= filters.maxPrice
    )
    .sort((a, b) => {
      if (filters.sortBy === 'price') return a.price - b.price;
      if (filters.sortBy === 'name') return a.name.localeCompare(b.name);
      return 0;
    });

  return (
    <>
      <select value={filters.category} onChange={(e) => 
        setFilters({...filters, category: e.target.value})
      }>
        <option value="all">All Categories</option>
        <option value="electronics">Electronics</option>
        <option value="accessories">Accessories</option>
      </select>

      <input
        type="range"
        min="0"
        max="1000"
        value={filters.maxPrice}
        onChange={(e) => 
          setFilters({...filters, maxPrice: Number(e.target.value)})
        }
      />

      <div>
        {filtered.map(product => (
          <div key={product.id}>{product.name} - ${product.price}</div>
        ))}
      </div>
    </>
  );
}
```

### Example 4: Toast Notifications

```javascript
import { toast } from 'sonner';

// In component
const handleAddToCart = (product) => {
  try {
    addToCart(product);
    // Show success toast
    toast.success(`${product.name} added to cart!`, {
      duration: 2000,
    });
  } catch (error) {
    // Show error toast
    toast.error('Failed to add to cart', {
      duration: 2000,
    });
  }
};

// Different toast types
toast.success('Success message');
toast.error('Error message');
toast.info('Info message');
toast.warning('Warning message');
toast.loading('Loading...');
toast.promise(
  promise,
  {
    loading: 'Loading...',
    success: 'Done!',
    error: 'Error'
  }
);
```

### Example 5: Custom Hook

```javascript
// src/hooks/useCartSummary.js
import { useMemo } from 'react';

export function useCartSummary(cart) {
  return useMemo(() => {
    const itemCount = cart.reduce((sum, item) => sum + item.quantity, 0);
    const subtotal = cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);
    const tax = subtotal * 0.1; // 10% tax
    const total = subtotal + tax;

    return { itemCount, subtotal, tax, total };
  }, [cart]);
}

// Usage in component
import { useCartSummary } from '@/hooks/useCartSummary';

function CartPage({ cart }) {
  const { itemCount, subtotal, tax, total } = useCartSummary(cart);

  return (
    <div>
      <p>Items: {itemCount}</p>
      <p>Subtotal: ${subtotal.toFixed(2)}</p>
      <p>Tax: ${tax.toFixed(2)}</p>
      <p>Total: ${total.toFixed(2)}</p>
    </div>
  );
}
```

---

## Useful Tailwind CSS Classes

```html
<!-- Flexbox -->
<div className="flex justify-center items-center gap-4">

<!-- Grid -->
<div className="grid grid-cols-3 gap-4">

<!-- Colors & Typography -->
<h1 className="text-2xl font-bold text-blue-600">

<!-- Spacing -->
<div className="p-4 m-8 mb-4">

<!-- Responsive -->
<div className="w-full md:w-1/2 lg:w-1/3">

<!-- Shadows & Borders -->
<div className="shadow-lg border border-gray-200 rounded-lg">

<!-- Hover States -->
<button className="bg-blue-500 hover:bg-blue-600 transition">

<!-- Conditional Classes (with clsx) -->
import clsx from 'clsx';
<div className={clsx('p-4', isActive && 'bg-blue-500')}>
```

---

## Environment Variables

Create `.env` file in project root:

```env
REACT_APP_API_URL=http://localhost:5000
REACT_APP_ENABLE_VISUAL_EDITS=false
```

Access in code:
```javascript
const apiUrl = process.env.REACT_APP_API_URL;
```

---

**End of Base Code Documentation**

For questions or updates, refer to official documentation:
- [React Docs](https://react.dev)
- [React Router](https://reactrouter.com)
- [Tailwind CSS](https://tailwindcss.com)
- [React Hook Form](https://react-hook-form.com)
- [Zod Validation](https://zod.dev)
- [Shadcn UI](https://ui.shadcn.com)
