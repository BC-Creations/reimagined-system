# reimagined-system
Custom T-shirts, Hats, and More
# create_storefront_local.ps1
# Creates storefront files locally (no git operations, no pushes).
# Usage (PowerShell in repo folder):
#   Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
#   .\create_storefront_local.ps1

function Write-File([string]$path, [string]$content) {
  $dir = Split-Path $path -Parent
  if (-not (Test-Path $dir) -and $dir -ne "") { New-Item -ItemType Directory -Path $dir -Force | Out-Null }
  $content | Out-File -FilePath $path -Encoding utf8 -Force
  Write-Host "Wrote $path"
}

# index.html
Write-File "index.html" @'
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>BC Creations – Shop Online</title>

  <!-- Google Fonts: Montserrat (headlines) + Inter (body) -->
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@600;700&family=Inter:wght@300;400;600&display=swap" rel="stylesheet">

  <link rel="stylesheet" href="/styles.css" />
</head>
<body>
  <header class="site-header">
    <div class="container header-inner">
      <div>
        <h1 class="logo">BC Creations</h1>
        <p class="tagline">Shirts • Hats • Letters — Made With Care</p>
      </div>

      <div class="header-controls">
        <button id="themeToggle" aria-label="Toggle theme">🌙</button>
        <button id="openCart" aria-label="Open cart">Cart (<span id="cartCount">0</span>)</button>
      </div>
    </div>
  </header>

  <aside class="cart" id="cart" aria-live="polite" aria-label="Shopping cart" hidden>
    <div class="cart-header">
      <h2>Your Cart</h2>
      <button id="closeCart" aria-label="Close cart">✕</button>
    </div>
    <div id="cartItems" class="cart-items"></div>
    <div class="cart-footer">
      <div class="cart-total">Total: <strong id="cartTotal">$0.00</strong></div>
      <div class="cart-actions">
        <button id="clearCart" class="muted">Clear</button>
        <button id="checkout">Checkout</button>
      </div>
    </div>
  </aside>

  <main class="container">
    <h2 class="section-title">Our Products</h2>
    <div id="loading" class="loading">Loading products…</div>
    <div class="grid" id="productGrid" aria-live="polite"></div>
  </main>

  <footer class="site-footer container">
    <small>© <span id="year"></span> BC Creations — Custom apparel & letters</small>
  </footer>

  <script src="/script.js" defer></script>
</body>
</html>
'@

# styles.css
Write-File "styles.css" @'
:root{
  --bg-gradient-1: #0f172a;
  --bg-gradient-2: #0b1220;
  --card: #ffffff;
  --muted: #94a3b8;
  --accent: #ef476f;
  --accent-2: #2ec4b6;
  --text-dark: #0f172a;
  --shadow-lg: 0 12px 30px rgba(2,6,23,0.45);
  --shadow-sm: 0 6px 18px rgba(2,6,23,0.12);
  --radius: 14px;
  --max-width:1100px;
  --card-bg: linear-gradient(180deg, rgba(255,255,255,0.98), rgba(251,251,251,0.98));
  --text-light: #ffffff;
}
:root[data-theme="light"]{
  --bg-gradient-1: #f6f8fb;
  --bg-gradient-2: #eef2f7;
  --muted: #6b7280;
  --text-dark: #061124;
  --card-bg: linear-gradient(180deg, #ffffff, #fbfbfd);
}
*{box-sizing:border-box}
html,body{height:100%}
body{
  margin:0;
  font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, Arial;
  color:var(--text-dark);
  background: linear-gradient(180deg, var(--bg-gradient-1) 0%, var(--bg-gradient-2) 100%);
  display:flex;
  flex-direction:column;
}
body::before{
  content:"";
  position:fixed;
  inset:0;
  background-image:
    radial-gradient(circle at 10% 20%, rgba(46,196,182,0.06) 0 8%, transparent 9%),
    radial-gradient(circle at 85% 80%, rgba(239,71,111,0.05) 0 10%, transparent 11%);
  pointer-events:none;
  z-index:0;
}
.container{
  width:90%;
  max-width:var(--max-width);
  margin:0 auto;
  padding:28px 0;
  position:relative;
  z-index:10;
}
.site-header{
  background:linear-gradient(90deg, rgba(255,255,255,0.03), rgba(255,255,255,0.01));
  color:var(--text-light);
  padding:20px 0;
  position:sticky;
  top:0;
  z-index:50;
}
.header-inner{display:flex;align-items:center;justify-content:space-between;gap:1rem}
.logo{margin:0;font-family:Montserrat, sans-serif;font-weight:700;font-size:1.4rem;color:var(--text-light)}
.tagline{margin:4px 0 0 0;color:rgba(255,255,255,0.85);font-size:0.95rem}
.header-controls{display:flex;align-items:center;gap:10px}
.header-controls button{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--text-light);padding:8px 10px;border-radius:10px;cursor:pointer}
.section-title{color:var(--text-light);font-family:Montserrat, sans-serif;font-weight:600;margin:6px 0 12px}
.grid{display:grid;grid-template-columns:repeat(auto-fit, minmax(240px, 1fr));gap:22px;margin-top:8px}
.card{background:var(--card-bg);border-radius:var(--radius);padding:14px;box-shadow:var(--shadow-sm);display:flex;flex-direction:column;gap:12px;transition:transform .22s,box-shadow .22s}
.card:hover{transform:translateY(-8px);box-shadow:var(--shadow-lg)}
.card .img-wrap{width:100%;height:200px;overflow:hidden;border-radius:10px}
.card img{width:100%;height:100%;object-fit:cover;transition:transform .42s}
.card:hover img{transform:scale(1.07) translateY(-4px)}
.card h3{margin:0;font-size:1.02rem;font-weight:600;color:var(--text-dark);font-family:Montserrat, sans-serif}
.card .price{color:var(--muted);font-weight:700}
.card .actions{margin-top:auto;display:flex;gap:10px;align-items:center}
button.cta{background:linear-gradient(135deg,var(--accent),var(--accent-2));color:white;padding:10px 14px;border-radius:10px;border:none;cursor:pointer;font-weight:700}
button.muted{background:transparent;color:rgba(11,18,32,0.8);border:1px solid rgba(11,18,32,0.06)}
.cart{position:fixed;right:22px;top:20px;width:360px;background:var(--card-bg);border-radius:16px;padding:14px;z-index:1200;display:flex;flex-direction:column;max-height:80vh;overflow:auto}
.cart-header{display:flex;align-items:center;justify-content:space-between}
.cart-items{margin-top:8px;display:flex;flex-direction:column;gap:12px}
.cart-item{display:flex;gap:10px;align-items:center;justify-content:space-between}
.cart-item .meta{flex:1}
.qty-controls{display:inline-flex;gap:6px;align-items:center}
.cart-footer{margin-top:12px;display:flex;flex-direction:column;gap:8px}
.loading{color:rgba(255,255,255,0.9);padding:18px 0}
.site-footer{color:rgba(255,255,255,0.8);padding:18px 0 48px 0;margin-top:28px;text-align:center}
@media(max-width:900px){.cart{right:12px;left:12px;top:auto;bottom:16px;width:auto}.card .img-wrap{height:180px}}
'@

# script.js
Write-File "script.js" @'
/* Frontend script: fetches /api/products, manages cart, calls server to create Stripe Checkout session,
   adds lazy-loading for images, image zoom on hover (CSS), and theme toggle persisted to localStorage. */

const API_BASE = ""; // same origin

let cart = loadCart();
let products = [];

const THEME_KEY = "bc_theme";
function applyTheme(theme){
  document.documentElement.setAttribute("data-theme", theme);
  const btn = document.getElementById("themeToggle");
  if (btn) btn.textContent = theme === "dark" ? "🌙" : "☀️";
}
function loadTheme(){
  const saved = localStorage.getItem(THEME_KEY);
  if (saved) return saved;
  const prefersLight = window.matchMedia && window.matchMedia("(prefers-color-scheme: light)").matches;
  return prefersLight ? "light" : "dark";
}
function toggleTheme(){
  const current = document.documentElement.getAttribute("data-theme") || loadTheme();
  const next = current === "dark" ? "light" : "dark";
  localStorage.setItem(THEME_KEY, next);
  applyTheme(next);
}
applyTheme(loadTheme());

function formatMoney(n){ return `$${n.toFixed(2)}`; }
function saveCart(){ localStorage.setItem("bc_cart_v1", JSON.stringify(cart)); }
function loadCart(){
  try {
    const raw = localStorage.getItem("bc_cart_v1");
    return raw ? JSON.parse(raw) : {};
  } catch(e){ return {}; }
}

async function fetchProducts(){
  const loading = document.getElementById("loading");
  try {
    const res = await fetch(`${API_BASE}/api/products`);
    if (!res.ok) throw new Error("Failed to fetch products");
    products = await res.json();
    renderProducts();
  } catch (err) {
    document.getElementById("productGrid").innerHTML = `<p class="error">Could not load products: ${err.message}</p>`;
  } finally {
    if (loading) loading.remove();
  }
}

function renderProducts(){
  const grid = document.getElementById("productGrid");
  if (!products || products.length === 0){
    grid.innerHTML = "<p>No products available.</p>";
    return;
  }
  grid.innerHTML = products.map(p => `
    <article class="card" aria-labelledby="p${p.id}-title">
      <a class="img-wrap" href="#" onclick="return false;">
        <img data-src="${escapeHtml(p.img)}" alt="${escapeHtml(p.name)}" class="lazy" loading="lazy">
      </a>
      <h3 id="p${p.id}-title">${escapeHtml(p.name)}</h3>
      <div class="price">${formatMoney(p.price)}</div>
      <div class="actions">
        <button data-id="${p.id}" class="add">Add to Cart</button>
      </div>
    </article>
  `).join("");

  grid.querySelectorAll("button.add").forEach(btn => {
    btn.addEventListener("click", () => addToCart(Number(btn.dataset.id)));
  });

  setupLazyLoading();
}

function setupLazyLoading(){
  const lazyImages = [].slice.call(document.querySelectorAll("img.lazy"));
  if ("IntersectionObserver" in window) {
    const imgObserver = new IntersectionObserver((entries, observer) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          const img = entry.target;
          img.src = img.dataset.src;
          img.classList.remove("lazy");
          observer.unobserve(img);
        }
      });
    }, { rootMargin: "100px 0px", threshold: 0.01 });
    lazyImages.forEach(img => imgObserver.observe(img));
  } else {
    lazyImages.forEach(img => { img.src = img.dataset.src; img.classList.remove("lazy"); });
  }
}

function addToCart(id, qty = 1){
  const product = products.find(p => p.id === id);
  if (!product) return;
  if (!cart[id]) cart[id] = { product, quantity: 0 };
  cart[id].quantity += qty;
  saveCart();
  renderCart();
}

function removeFromCart(id){
  delete cart[id];
  saveCart();
  renderCart();
}

function changeQty(id, qty){
  if (!cart[id]) return;
  cart[id].quantity = Math.max(0, qty);
  if (cart[id].quantity === 0) removeFromCart(id);
  else saveCart();
  renderCart();
}

function clearCart(){
  cart = {};
  saveCart();
  renderCart();
}

function cartTotals(){
  const items = Object.values(cart);
  const count = items.reduce((s,i)=>s+i.quantity,0);
  const total = items.reduce((s,i)=>s + i.quantity * i.product.price,0);
  return { count, total };
}

function renderCart(){
  const cartEl = document.getElementById("cart");
  const itemsEl = document.getElementById("cartItems");
  const { count, total } = cartTotals();
  document.getElementById("cartCount").textContent = count;

  if (count === 0){
    itemsEl.innerHTML = `<p><strong>Cart is empty</strong></p>`;
    document.getElementById("cartTotal").textContent = formatMoney(0);
    return;
  }

  itemsEl.innerHTML = Object.values(cart).map(item => `
    <div class="cart-item" data-id="${item.product.id}">
      <div class="meta">
        <strong>${escapeHtml(item.product.name)}</strong>
        <small>${formatMoney(item.product.price)} each</small>
        <div class="qty-controls" aria-label="Quantity controls for ${escapeHtml(item.product.name)}">
          <button class="qty-decrease" aria-label="Decrease quantity">−</button>
          <span class="qty" aria-live="polite">${item.quantity}</span>
          <button class="qty-increase" aria-label="Increase quantity">+</button>
          <button class="remove muted" aria-label="Remove item">Remove</button>
        </div>
      </div>
      <div class="line-price">${formatMoney(item.quantity * item.product.price)}</div>
    </div>
  `).join("");

  itemsEl.querySelectorAll(".cart-item").forEach(node => {
    const id = Number(node.dataset.id);
    node.querySelector(".qty-increase").addEventListener("click", () => changeQty(id, cart[id].quantity + 1));
    node.querySelector(".qty-decrease").addEventListener("click", () => changeQty(id, cart[id].quantity - 1));
    node.querySelector(".remove").addEventListener("click", () => removeFromCart(id));
  });

  document.getElementById("cartTotal").textContent = formatMoney(total);
}

function escapeHtml(str){
  return String(str).replace(/[&<>"']/g, (m) => ({ "&":"&amp;","<":"&lt;",">":"&gt;","\"":"&quot;","'":"&#39;"}[m]));
}

function setupCartToggle(){
  const cartEl = document.getElementById("cart");
  document.getElementById("openCart").addEventListener("click", () => { cartEl.hidden = false; });
  document.getElementById("closeCart").addEventListener("click", () => { cartEl.hidden = true; });
  document.getElementById("clearCart").addEventListener("click", () => {
    if (confirm("Clear the cart?")) clearCart();
  });
  document.getElementById("checkout").addEventListener("click", async () => {
    const { total } = cartTotals();
    if (total === 0) return alert("Cart is empty.");
    const items = Object.values(cart).map(({ product, quantity }) => ({ id: product.id, quantity }));
    try {
      const res = await fetch(`${API_BASE}/api/create-checkout-session`, {
        method: "POST",
        headers: {"Content-Type":"application/json"},
        body: JSON.stringify({ items })
      });
      const data = await res.json();
      if (data.url) {
        window.location = data.url;
      } else {
        alert("Could not start checkout: " + (data.error || "unknown"));
      }
    } catch (err) {
      alert("Checkout error: " + err.message);
    }
  });
}

document.addEventListener("DOMContentLoaded", () => {
  const themeBtn = document.getElementById("themeToggle");
  if (themeBtn) themeBtn.addEventListener("click", toggleTheme);
});

document.getElementById("year").textContent = new Date().getFullYear();
renderCart();
setupCartToggle();
fetchProducts();
'@

# products.json
Write-File "products.json" @'
[
  { "id": 1, "name": "BC Shirt", "price": 20.00, "img": "https://via.placeholder.com/800x600?text=BC+Shirt" },
  { "id": 2, "name": "BC Hat", "price": 15.00, "img": "https://via.placeholder.com/800x600?text=BC+Hat" },
  { "id": 3, "name": "Custom Letters", "price": 10.00, "img": "https://via.placeholder.com/800x600?text=Letters" }
]
'@

# .env.example
Write-File ".env.example" @'
# Copy to .env and fill with your keys
PORT=3000
# Stripe keys from https://dashboard.stripe.com/apikeys
STRIPE_SECRET_KEY=sk_test_your_secret_key_here
STRIPE_WEBHOOK_SECRET=whsec_your_webhook_secret_here
# URL where your server will be reachable (used to build success/cancel URLs)
PUBLIC_URL=http://localhost:3000
# (Optional) Admin basic auth password to view orders
ADMIN_PASSWORD=changeme
'@

# package.json
Write-File "package.json" @'
{
  "name": "bc-creations-shop",
  "version": "1.0.0",
  "description": "BC Creations - static storefront with Node/Express + Stripe + SQLite",
  "main": "server/index.js",
  "scripts": {
    "start": "node server/index.js",
    "dev": "nodemon server/index.js",
    "migrate": "node server/migrate.js"
  },
  "engines": {
    "node": ">=18"
  },
  "dependencies": {
    "better-sqlite3": "^8.0.1",
    "cors": "^2.8.5",
    "dotenv": "^16.0.0",
    "express": "^4.18.2",
    "stripe": "^12.0.0",
    "body-parser": "^1.20.2",
    "basic-auth": "^2.0.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.22"
  }
}
'@

# server/migrate.js
Write-File "server/migrate.js" @'
const Database = require("better-sqlite3");
const db = new Database("./server/db/orders.db");
db.pragma("journal_mode = WAL");

db.exec(`
CREATE TABLE IF NOT EXISTS orders (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  stripe_session_id TEXT,
  created_at TEXT NOT NULL DEFAULT (datetime("now")),
  items_json TEXT NOT NULL,
  total REAL NOT NULL,
  paid INTEGER NOT NULL DEFAULT 0,
  metadata_json TEXT
);
`);

console.log("Migration complete. DB: ./server/db/orders.db");
db.close();
'@

# server/index.js
Write-File "server/index.js" @'
/* server/index.js
   Node + Express server that serves products.json, creates Stripe Checkout sessions,
   stores orders in SQLite, and exposes a webhook for Stripe to mark paid orders.
*/

require("dotenv").config();
const express = require("express");
const path = require("path");
const fs = require("fs");
const bodyParser = require("body-parser");
const cors = require("cors");
const Database = require("better-sqlite3");

const STRIPE_SECRET_KEY = process.env.STRIPE_SECRET_KEY;
const STRIPE_WEBHOOK_SECRET = process.env.STRIPE_WEBHOOK_SECRET;
const PUBLIC_URL = process.env.PUBLIC_URL || "http://localhost:3000";
const PORT = process.env.PORT || 3000;
const ADMIN_PASSWORD = process.env.ADMIN_PASSWORD || "changeme";

if (!STRIPE_SECRET_KEY) {
  console.error("Missing STRIPE_SECRET_KEY in .env");
  process.exit(1);
}

const stripe = require("stripe")(STRIPE_SECRET_KEY);

const app = express();

app.use((req, res, next) => {
  if (req.originalUrl === "/api/webhook") {
    next();
  } else {
    bodyParser.json()(req, res, next);
  }
});
app.use(cors());

app.use(express.static(path.join(__dirname, "..")));

const PRODUCTS_PATH = path.join(__dirname, "..", "products.json");
function loadProducts() {
  const raw = fs.readFileSync(PRODUCTS_PATH);
  return JSON.parse(raw);
}

const dbDir = path.join(__dirname, "db");
if (!fs.existsSync(dbDir)) { fs.mkdirSync(dbDir, { recursive: true }); }
const dbPath = path.join(dbDir, "orders.db");
const db = new Database(dbPath);
db.pragma("journal_mode = WAL");
db.exec(\`
CREATE TABLE IF NOT EXISTS orders (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  stripe_session_id TEXT,
  created_at TEXT NOT NULL DEFAULT (datetime("now")),
  items_json TEXT NOT NULL,
  total REAL NOT NULL,
  paid INTEGER NOT NULL DEFAULT 0,
  metadata_json TEXT
);
\`);

const insertOrder = db.prepare(\`INSERT INTO orders (stripe_session_id, items_json, total, paid, metadata_json) VALUES (@stripe_session_id, @items_json, @total, @paid, @metadata_json)\`);
const getOrderBySession = db.prepare(\`SELECT * FROM orders WHERE stripe_session_id = ? LIMIT 1\`);
const markPaid = db.prepare(\`UPDATE orders SET paid = 1 WHERE stripe_session_id = ?\`);
const listOrders = db.prepare(\`SELECT * FROM orders ORDER BY created_at DESC LIMIT 200\`);

app.get("/api/products", (req, res) => {
  try {
    const products = loadProducts();
    res.json(products);
  } catch (err) {
    res.status(500).json({ error: "Failed to load products" });
  }
});

app.post("/api/create-checkout-session", async (req, res) => {
  try {
    const { items } = req.body;
    if (!items || !Array.isArray(items) || items.length === 0) {
      return res.status(400).json({ error: "No items provided" });
    }
    const products = loadProducts();
    const line_items = items.map(i => {
      const prod = products.find(p => p.id === i.id);
      if (!prod) throw new Error("Invalid product id: " + i.id);
      return {
        price_data: {
          currency: "usd",
          product_data: { name: prod.name },
          unit_amount: Math.round(prod.price * 100)
        },
        quantity: i.quantity
      };
    });

    const session = await stripe.checkout.sessions.create({
      payment_method_types: ["card"],
      mode: "payment",
      line_items,
      success_url: `${PUBLIC_URL}/?checkout=success&session_id={CHECKOUT_SESSION_ID}`,
      cancel_url: `${PUBLIC_URL}/?checkout=cancel`,
    });

    const total = line_items.reduce((s, li) => s + (li.price_data.unit_amount/100) * li.quantity, 0);
    insertOrder.run({
      stripe_session_id: session.id,
      items_json: JSON.stringify(items),
      total,
      paid: 0,
      metadata_json: null
 
