# Crumble & Cream

Responsive bakery storefront with a shared Supabase PostgreSQL database, customer ordering, and live behind-the-counter Admin Order Desk.

---

## Example Accounts & Credentials

The database and UI come pre-configured with example accounts for both Admin and Customer:

### 1. Administrator Account (Behind-the-Counter Desk)
* **Email:** `admin@crumbleandcream.com`
* **Password:** `Admin@12345`
* **Role:** `admin`
* **Capabilities:**
  * Access the live **Order Desk** (`#order-desk`).
  * View incoming customer orders in realtime.
  * Update order status (`New`, `Confirmed`, `Baking`, `Ready`, `Completed`, `Cancelled`).
  * Realtime notifications and order counts.

### 2. Customer Account (Storefront Ordering)
* **Email:** `customer@example.com`
* **Password:** `Customer@12345`
* **Role:** `customer`
* **Capabilities:**
  * Browse the bakery menu and story.
  * Add fresh bakes to bag and checkout.
  * Place pickup/delivery orders that sync directly to the database.
  * View order history.

> **Tip:** In both Preview Mode and Live Mode, clicking **Account** in the header lets you click a single button to auto-fill the Admin or Customer credentials!

---

## Database Setup (Supabase)

The project uses a Supabase PostgreSQL database with Row Level Security (RLS) and Supabase Realtime.

### Step 1: Create a Supabase Project
1. Go to [supabase.com](https://supabase.com) and create a free project.
2. In the Supabase Dashboard, navigate to **Project Settings** -> **API**.
3. Copy your **Project URL** and **anon public key**.

### Step 2: Configure Environment Variables
Open [.env.local](file:///.env.local) and paste your credentials:
```env
VITE_SUPABASE_URL=https://your-project-id.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key-here
```

### Step 3: Run Database Migrations
1. In the Supabase Dashboard, click **SQL Editor**.
2. Open and run [`supabase/schema.sql`](file:///supabase/schema.sql):
   * Creates the `products`, `profiles`, `orders`, and `order_items` tables.
   * Sets up Row Level Security (RLS) policies and security triggers.
   * Configures Supabase Realtime publication on `orders`.
3. Open and run [`supabase/seed.sql`](file:///supabase/seed.sql):
   * Seeds all 10 bakery products (Cakes, Pastries, Breads, Brownies, Cupcakes).
   * Automatically creates the example Admin (`admin@crumbleandcream.com`) and Customer (`customer@example.com`) accounts in `auth.users` with confirmed emails and encrypted passwords.
   * Promotes the admin in `public.profiles`.
   * Seeds an initial sample order for instant Order Desk preview.

*(Optional)* If you only want to seed or re-seed the example users at any time, run [`supabase/seed_users.sql`](file:///supabase/seed_users.sql).

### Step 4: Promote Any Custom User to Admin
If you register a different email via the UI and want to make it an admin, run:
```sql
update public.profiles
set role = 'admin'
where id = (select id from auth.users where email = 'your-email@example.com');
```

---

## Run Locally

```bash
# 1. Install dependencies
npm install

# 2. Start Vite dev server
npm run dev

# 3. Build for production
npm run build
```

* **Live Database Mode:** Active when `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` are populated in `.env.local`. Orders sync across devices in realtime.
* **Preview Demo Mode:** Active when Supabase is not configured yet. Everything runs smoothly in memory with sample data and instant role switching between Admin and Customer.
