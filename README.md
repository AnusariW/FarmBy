# FrmBy – Expo Go + Supabase (React Native, JavaScript)

Four roles in one app: **Buyer · Farmer · Logistics partner · Admin**.

## 1. Supabase setup (5 min)
1. Create a project at https://supabase.com.
2. **SQL Editor → New query →** paste all of `supabase/schema.sql` → Run.
   (Creates tables, RLS policies, RPC functions, the `images` storage bucket and realtime.)
3. *Authentication → Providers → Email*: for quick testing turn **Confirm email OFF**.
4. *Project Settings → API*: copy the **Project URL** and **anon public key**.

## 2. Run the app
```bash
npm install
npx expo install --fix        # aligns versions with the SDK in your Expo Go
cp .env.example .env          # then paste your URL + anon key
npx expo start                # scan the QR with Expo Go
```
> Expo Go only runs the SDK it was built for. If it says "incompatible SDK", run
> `npx expo install expo@latest --fix` (or `npx expo install --fix` after editing the `expo` version in package.json).

## 3. Create an admin
Sign up normally in the app, then run in the SQL editor:
```sql
update profiles set role = 'admin' where id = (select id from auth.users where email = 'you@example.com');
```
Sign out and back in.

## Screens (by role)
| Role | Screens |
|---|---|
| Auth | Welcome, Role select, Login, Register |
| Buyer | Home, Product list (search/category/price/sort/stock filters), Product detail, Basket, Checkout (COD/wallet), Order success, My orders (status filter), Order tracking (live), Wallet, Messages, Chat, Notifications, Account, Farm profile |
| Farmer | Dashboard, My products (search/category/status filters, **add / edit / update / delete**, visibility toggle, image upload to Storage), Orders (accept/reject/ready), Buyer chats (realtime, read receipts, quick replies), **Farmer profile** (details + products + location), Edit farm details (GPS location) |
| Logistics | Available pickups (accept), My deliveries (status filter), Delivery detail (farm pickup pin → Maps, buyer address, picked up → in transit → delivered), Partner profile (vehicle, availability, location) |
| Admin | Dashboard stats, Users (search/role filter, activate/deactivate, promote, delete), Products moderation, All orders (status filter, assign logistics, change status, delete) |

## Order flow
buyer places order → farmer accepts → farmer marks *ready* → logistics partners are notified and one accepts →
picked up → in transit → delivered. Buyers see status change live.

## Notes
- Wallet top-up is a **demo** RPC; connect a real payment gateway for production.
- Stock is decremented atomically in `place_order`; a CHECK constraint prevents overselling.
- Order prices/totals are computed in the database, never trusted from the client.
