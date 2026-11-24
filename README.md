# Royal Dine RMS - Complete Restaurant & Hotel Management System (MERN Stack)

A full-scale, production-ready enterprise Restaurant Management System (RMS) engineered using **React.js, Node.js, Express.js (MVC Architecture), MongoDB, and Socket.IO**.

The platform is a complete business management solution covering five interconnected interfaces with real-time socket events, cryptographic QR code ordering, and a role-based security system.

---

## 🏛️ System Architecture

### 1. Five Major Workstations / Interfaces

```
                                    ┌────────────────────────────────────────────────────────┐
                                    │               Node.js + Express.js API                 │
                                    │       (MVC Architecture with Socket.IO Real-time)      │
                                    └───────────────────────────┬────────────────────────────┘
                                                                │
             ┌──────────────────────┬───────────────────────────┼───────────────────────────┬──────────────────────┐
             ▼                      ▼                           ▼                           ▼                      ▼
┌─────────────────────────┐  ┌─────────────┐             ┌─────────────┐             ┌─────────────┐        ┌─────────────┐
│ Customer QR Mobile Menu │  │ Waiter POS  │             │ Kitchen KDS │             │ Cashier Desk│        │  Admin Hub  │
│  (/menu/t/:token)       │  │ (/waiter)   │             │ (/kitchen)  │             │ (/cashier)  │        │  (/admin)   │
└─────────────────────────┘  └─────────────┘             └─────────────┘             └─────────────┘        └─────────────┘
```

1. **Super Admin / Restaurant Admin (`/admin`)**:
   - Master Control Center: Today's revenue, order counts, table occupancy, average order value.
   - Products CRUD: Portion sizes/variants (Half, Full, Regular, Large), add-ons groups (extra cheese, dips), instant "86 / Sold Out" stock toggle.
   - Categories: Grouping dishes and routing to responsible kitchen prep stations.
   - Table Grid: Multi-floor support (Ground Floor, Rooftop Terrace), sections (Indoor, Outdoor, Bar), capacity, and live status.
   - Table QR Generator: Cryptographically signed table tokens, QR preview, printable physical standee cards with Wi-Fi info.
   - Orders Hub: Real-time orders stream, status progression, cancellation reasons, and pricing audit trail.
   - Raw Materials Inventory: Stock monitoring, minimum alert thresholds, quick restock adjustments (+/-), unit costs.
   - Reports & Analytics: Horizon filtering (Today, Yesterday, Week, Month), settlement channels (Cash, UPI, Card), top sellers CSV export.
   - Business & Tax Settings: CGST, SGST, service charges, inclusive/exclusive tax calculation, guest Wi-Fi credentials.

2. **Customer QR Ordering Website (`/menu/t/:token`)**:
   - Mobile-first web app accessed by scanning physical table QR standees.
   - Secure token validation (backend validates cryptographic token; no raw table number tampering).
   - Category filtering, Veg/Non-Veg dietary toggles, search.
   - Portions and add-ons modal with special cooking requests.
   - Live Order Tracking (`OrderTracker`): 4-stage stepper (Order Placed ➔ In Kitchen ➔ Food Ready ➔ Served).
   - Waiter Assistance & Bill Request buttons with live Socket.IO dispatching.

3. **Waiter / Staff POS Panel (`/waiter`)**:
   - Real-time floor plan with status tiles (`Available`, `Occupied`, `Order Placed`, `Preparing`, `Food Ready`, `Billing`, `Cleaning`).
   - Manual Table Order POS for walk-ins or guests who do not want to use QR phones.
   - Multi-order appending: add extra dishes or drinks to an ongoing table session.
   - Immediate audio-visual alerts when a table taps "Call Waiter".

4. **Kitchen Display System - KDS (`/kitchen`)**:
   - Dedicated high-contrast, paperless digital kitchen display.
   - Station routing filters: `All`, `Main Kitchen`, `Tandoor`, `Chinese Kitchen`, `Beverage`, `Dessert`.
   - Preparation timers with warning (10+ min) and urgent (20+ min) color alerts.
   - Dish-by-dish checkoff (`Start Preparing` ➔ `Mark Ready` ➔ `Served`).
   - Audible kitchen chime alert when new orders arrive.

5. **Billing & Cashier Panel (`/cashier`)**:
   - Pending table checkout list with unpaid items.
   - Automatic bill generation with itemized breakdown, CGST, SGST, and service charges.
   - Manager courtesy discounts and promo code applications.
   - Payment channels: Cash (with change calculator), UPI digital QR, Card, or Split payments.
   - One-click thermal receipt printer format for customer invoices.
   - Table reset to `Cleaning` status upon payment completion.

---

## 📂 Project Structure

```
d:\retaurant management\
├── restaurant-backend/              # MVC Backend
│   ├── src/
│   │   ├── config/
│   │   │   └── db.js                # MongoDB connection handler
│   │   ├── controllers/             # Business Logic & Handlers
│   │   │   ├── authController.js
│   │   │   ├── productController.js
│   │   │   ├── categoryController.js
│   │   │   ├── tableController.js
│   │   │   ├── qrController.js
│   │   │   ├── orderController.js
│   │   │   ├── kdsController.js
│   │   │   ├── billingController.js
│   │   │   ├── inventoryController.js
│   │   │   ├── reportController.js
│   │   │   ├── publicMenuController.js
│   │   │   └── settingsController.js
│   │   ├── models/                  # Mongoose Schemas (with restaurantId)
│   │   │   ├── Restaurant.js
│   │   │   ├── User.js
│   │   │   ├── Category.js
│   │   │   ├── Product.js
│   │   │   ├── Floor.js
│   │   │   ├── Table.js
│   │   │   ├── QRCode.js
│   │   │   ├── Order.js
│   │   │   ├── Inventory.js
│   │   │   └── Invoice.js
│   │   ├── routes/                  # Express REST Routes
│   │   ├── middleware/              # JWT Protect & Role Authorize
│   │   ├── sockets/
│   │   │   └── socketHandler.js     # Real-time event broadcasting
│   │   ├── utils/
│   │   │   └── seedData.js          # Full database seeder
│   │   ├── app.js
│   │   └── server.js
│   ├── .env
│   └── package.json
│
├── restaurant-frontend/             # Component-wise React + Tailwind CSS
│   ├── src/
│   │   ├── api/
│   │   │   ├── apiClient.js         # Axios instance with auth interceptor
│   │   │   └── socket.js            # Socket.IO client singleton
│   │   ├── context/
│   │   │   ├── AuthContext.jsx      # Role & User state
│   │   │   └── CartContext.jsx      # Mobile ordering cart
│   │   ├── components/
│   │   │   ├── common/              # Navbar, Modal, StatCard, Badges
│   │   │   ├── admin/               # Dashboard, Products, Tables, QR, Orders, Inventory, Reports, Settings
│   │   │   ├── customer/            # CustomerMenu, ProductModal, CartDrawer, OrderTracker
│   │   │   ├── waiter/              # WaiterDashboard, NewOrderModal
│   │   │   ├── kitchen/             # KitchenDisplaySystem (KDS)
│   │   │   └── cashier/             # CashierBilling & Thermal Receipts
│   │   ├── pages/                   # Route Pages (HomePage, LoginPage, AdminPage, WaiterPage, KitchenPage, CashierPage, CustomerMenuPage)
│   │   ├── App.jsx
│   │   └── index.css
│   ├── vite.config.js
│   └── package.json
```

---

## ⚡ Default Demo Credentials

Pre-seeded staff accounts with 1-click login on `/login`:

| Workstation / Role | Email | Password | Access Rights |
| :--- | :--- | :--- | :--- |
| **Super Admin** | `admin@royaldine.com` | `admin123` | Complete system access |
| **Waiter Staff** | `waiter@royaldine.com` | `waiter123` | Table grid, manual orders, guest calls |
| **Kitchen Chef** | `chef@royaldine.com` | `chef123` | KDS screen, dish checkoff, station filter |
| **Cashier Desk** | `cashier@royaldine.com` | `cashier123` | Invoices, payments, discounts, settlements |

---

## 🚀 How to Run the System

### 1. Prerequisites
- **Node.js**: v18+ or v20+
- **MongoDB**: Running locally on `mongodb://127.0.0.1:27017` or MongoDB Atlas URI in `restaurant-backend/.env`.

### 2. Start Backend Server
```bash
cd "restaurant-backend"
npm install
npm run seed       # Seeds tables, menu, inventory & staff accounts
npm start          # Runs on http://localhost:5001
```

### 3. Start Frontend Client
```bash
cd "restaurant-frontend"
npm install
npm run dev        # Runs on http://localhost:5173
```

---

## 🔒 Security & Architecture Highlights
- **Cryptographic QR Tokens**: Table QR links use unguessable random hashes (`/menu/t/:token`). Client browsers cannot change the URL to place orders on other tables.
- **Price & Variant Snapshots**: Every placed order freezes dish name, portion, add-ons, and pricing at the exact moment of order placement. Subsequent menu price adjustments never alter historic receipts.
- **Real-Time Synchronized State**: Waiters, chefs, cashiers, and customers receive immediate UI updates via Socket.IO without manual page refreshing.
- **Auditable Role Boundaries**: Restricted backend APIs guard each workstation, verifying JWT tokens and roles.
