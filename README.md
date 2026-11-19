Project Summary 
# 🍛 Zaika — Complete Restaurant Management System

**Zaika** is an end-to-end, full-stack smart restaurant management platform built with **Python, FastAPI, Pydantic, SQLAlchemy, MySQL, SQLite, JWT Auth, and Streamlit**. It digitizes the entire dining lifecycle—from contactless QR code ordering and live Kitchen Display Systems (KDS) to waiter SLA alerts, split billing, guest gamification, and real-time owner analytics.

---

## 🏗️ System Architecture

Zaika uses a **3-Tier Full-Stack Architecture** backed by a **Hybrid Dual-Database** setup:

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                        STREAMLIT FRONTEND (Port 8501)                   │
│  [Customer QR Menu]  [Staff Floor Map]  [3-Station KDS]  [Owner Admin]  │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │ HTTP / JSON (Requests + JWT)
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                       FASTAPI REST BACKEND (Port 8000)                  │
│     [Pydantic Validation]   [JWT & RBAC Guards]   [SQLAlchemy ORM]      │
└──────────────────┬───────────────────────────────────┬──────────────────┘
                   │                                   │
                   ▼                                   ▼
┌──────────────────────────────────┐ ┌────────────────────────────────────┐
│      MYSQL 8.0+ (Primary DB)     │ │     SQLITE (Audit Log Cache)       │
│  10 Normalized Relational Tables │ │  Fast Local Event & Action Trail   │
└──────────────────────────────────┘ └────────────────────────────────────┘
```

### Why Dual Databases?
* **MySQL (`zaika_db`):** Stores structured, transactional restaurant data across 10 normalized relational tables with strict foreign-key integrity.
* **SQLite (`audit_logs.db`):** Acts as a lightweight, zero-latency local audit log capturing staff logins, order transitions, and payment events without adding write overhead to MySQL.

---

## ✨ Key Features (10 End-to-End Modules)

1. **Live Table & Floor Management:** Interactive floor plan for Tables `T1`–`T6` with real-time statuses (`Empty`, `Ordering`, `Eating`, `Bill Requested`, `Needs Help`), active table counter, and unique QR token security.
2. **Contactless Customer QR Menu (No Login):** Scanning a table's QR code opens the mobile menu immediately with Veg/Non-Veg filters, allergen tags (`Dairy`, `Nuts`, `Gluten`), spice levels (`Mild`, `Medium`, `Hot`), `Half`/`Full` portions, and `Deal of the Day` badges.
3. **Two-Step Order Flow & Live Countdown:** Customers place orders from their phones (`Placed`), staff verify and dispatch them (`Send to Kitchen`), and guests track real-time cooking timers (*"Your food is cooking — 11:30 min left"*).
4. **3-Station Kitchen Display System (KDS):** Automatically routes order items to **Tandoor**, **Curry**, and **Drinks** columns with color-coded urgency timers (`Green` = New, `Yellow` = Cooking Long, `Red` = Late).
5. **Guest Engagement Hub:** Unlocks Free WiFi credentials after ordering, tracks loyalty rewards (`200 points = Free Mango Lassi`), hosts an interactive **Spice Quiz**, and displays a live **Table vs. Table Challenge Leaderboard**.
6. **Call Waiter Alert System:** One-tap requests (`Water`, `Napkins`, `Bill`, `Other Help`) flash the table red on the staff map and measure staff response speed in seconds (*"Waiter Reached in 8 sec"*).
7. **Billing, Split Calculator & Checkout:** Auto-generates itemized bills with 5% GST, tip options (`5%`, `10%`, `15%`, custom), equal (`2/3/4-way`) or item-wise bill splitting, and 5 payment methods (`Cash`, `Card`, `Bank Transfer`, `Google Pay`, `Apple Pay`).
8. **Post-Payment Feedback:** Prompts guests for a `1.0`–`5.0` star rating and review immediately after payment settlement.
9. **Owner Analytics Dashboard:** Live KPI metrics, Plotly hourly sales and top-selling dish charts, waiter SLA performance tracking, menu CRUD controls, and staff role management.
10. **Role-Based Access Control (RBAC):** Token-free QR access for Customers; JWT + bcrypt protected portals for **Staff**, **Kitchen**, and **Owner** roles.

---

## 🛠️ Tech Stack

| Layer | Technologies |
| :--- | :--- |
| **Frontend** | Streamlit, Plotly, Requests, QRCode |
| **Backend** | FastAPI, Uvicorn, Pydantic |
| **Database & ORM** | MySQL 8.0+ (`pymysql`), SQLite, SQLAlchemy ORM |
| **Security** | JWT (`python-jose`), Password Hashing (`passlib[bcrypt]`), RBAC |
| **Testing & DevOps** | Pytest, FastAPI `TestClient`, Docker, Docker Compose |

---

## 📂 Project Structure

```text
zaika-restaurant-system/
├── docker-compose.yml
├── .env.example
├── README.md
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── main.py                  # FastAPI entry point, CORS & router registration
│   ├── database.py              # Dual MySQL & SQLite engines + session generators
│   ├── models.py                # 10 MySQL + 1 SQLite SQLAlchemy ORM models
│   ├── schemas.py               # Pydantic request/response validation schemas
│   ├── auth.py                  # bcrypt hashing, JWT creation & RBAC dependencies
│   ├── seed_data.py             # Seeds T1–T6 tables, sample menu & default Owner
│   ├── routers/
│   │   ├── auth_router.py       # Login & staff user registration
│   │   ├── tables_router.py     # Live floor map & QR verification
│   │   ├── menu_router.py       # Menu filtering & Owner CRUD
│   │   ├── orders_router.py     # Order flow & 3-station KDS endpoints
│   │   ├── alerts_router.py     # Call Waiter alerts & SLA tracking
│   │   ├── billing_router.py    # Bill calculation, split payments & reviews
│   │   └── analytics_router.py  # Owner KPIs, sales charts & guest engagement
│   └── tests/
│       └── test_api.py          # Pytest automated validation & RBAC tests
└── frontend/
    ├── Dockerfile
    ├── requirements.txt
    ├── app.py                   # Entry router (QR Customer vs. Staff Login)
    ├── utils/
    │   └── api_client.py        # Centralized HTTP wrapper with JWT headers
    └── pages/
        ├── 1_Customer_QR_Menu.py
        ├── 2_Staff_Floor_Map.py
        ├── 3_Kitchen_Display.py
        ├── 4_Billing_Checkout.py
        └── 5_Owner_Dashboard.py
```

---

## 🚀 Getting Started

### Option 1: Run with Docker Compose (Recommended)
1. **Clone the repository:**
   ```bash
   git clone https://github.com
   cd zaika-restaurant-system
   ```
2. **Launch MySQL, FastAPI, and Streamlit:**
   ```bash
   docker-compose up --build -d
   ```
3. **Access the applications:**
   * **Customer QR Menu (Table T1 Demo):** `http://localhost:8501/?table=T1&token=tok_t1_99a1`
   * **Staff / Kitchen / Owner Portal:** `http://localhost:8501`
   * **Interactive API Swagger Docs:** `http://localhost:8000/docs`

### Option 2: Run Locally with Virtual Environment
1. **Create and activate a virtual environment:**
   ```bash
   python -m venv venv
   source venv/bin/activate        # Windows: venv\Scripts\activate
   ```
2. **Install dependencies:**
   ```bash
   pip install -r backend/requirements.txt -r frontend/requirements.txt
   ```
3. **Configure environment variables:**
   Create a `.env` file in the root directory:
   ```env
   MYSQL_URL=mysql+pymysql://root:your_password@localhost:3306/zaika_db
   SQLITE_URL=sqlite:///./audit_logs.db
   SECRET_KEY=zaika_super_secret_jwt_key_2026
   API_BASE_URL=http://localhost:8000
   ```
4. **Seed initial tables, menu items & Owner account:**
   ```bash
   python -m backend.seed_data
   ```
5. **Start Backend & Frontend in separate terminals:**
   ```bash
   # Terminal 1 (Backend)
   uvicorn backend.main:app --reload --port 8000

   # Terminal 2 (Frontend)
   streamlit run frontend/app.py
   ```

---

## 🧪 Running Automated Tests

Run the `pytest` suite to verify API health, Pydantic input validation (`422` errors), and RBAC security (`401`/`403` guards):

```bash
pytest -v
```

---

## 🔑 Default Demo Credentials

| Role | Email | Password | Access Scope |
| :--- | :--- | :--- | :--- |
| **Owner** | `owner@zaika.com` | `owner123` | Full Dashboard, Menu CRUD, Staff Management |
| **Staff** | `staff@zaika.com` | `staff123` | Floor Map, Order Confirmation, Call Alerts, Billing |
| **Kitchen** | `kitchen@zaika.com` | `kitchen123` | 3-Station Kitchen Display System (KDS) |
| **Customer** | *No Login Required* | *Scan Table QR* | `?table=T1&token=tok_t1_99a1` |

---

## 📄 License
This project is built for educational and portfolio demonstration purposes.
