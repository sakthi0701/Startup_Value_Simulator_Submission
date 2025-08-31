# 🚀 Startup Value Simulator

An **interactive Scenario Builder** that lets founders model cap tables across funding rounds and instantly see how much each founder would make at exit.  

**Submission for the Hackathon - August 2025**  

- **Live URL:** [startupvaluesimulator.netlify.app](https://startupvaluesimulator.netlify.app/)  
- **Demo Video:** *Watch the 3-minute walkthrough here*  

---

## 📌 The Problem
When founding a startup, entrepreneurs often speculate on their potential returns at exit but lack a simple, interactive way to model ownership changes across funding rounds.  

- Equity splits, ESOP allocations, and fundraising rounds impact founder ownership and eventual returns in complex ways.  
- Most founders rely on spreadsheets, which are **error-prone** and lack intuitive scenario testing.  

👉 This tool solves that.

---

## ✨ Core Features
- **Initial Setup**: Define founders, initial equity splits (by percentage or shares), and create an initial ESOP pool.  
- **Dynamic Funding Rounds**: Add and configure multiple funding rounds, including:  
  - **Priced Rounds**: Standard equity rounds with pre-money valuations.  
  - **SAFEs**: Convert with Valuation Caps and Discounts.  
  - **ESOP Top-Ups**: Pre- or post-money.  
  - **Founder Secondaries**: Founders can sell existing shares.  
- **Instant Recalculation**: All outputs update in real-time as you adjust inputs.  
- **Exit Simulation**: Input a hypothetical exit valuation to see final payouts.  
- **In-Depth Analysis & Visualization**:  
  - Round-by-round tables showing valuations, capital raised, and ownership changes.  
  - Charts for dilution over time & exit distributions.  
  - Transparent "Audit" view for formulas and SAFE conversions.  
- **Save & Share Scenarios**: Generate unique links to share scenarios with co-founders, advisors, or investors.  

---

## 🛠 Tech Stack
- **Framework**: Next.js 13 (App Router)  
- **Language**: TypeScript  
- **Styling**: Tailwind CSS  
- **UI Components**: shadcn/ui, Radix UI, Vaul (drawers)  
- **Visualizations**: Chart.js (with react-chartjs-2), Recharts  
- **Backend & Database**: Supabase (for saving/sharing scenarios)  
- **Testing**: Vitest & React Testing Library  
- **Deployment**: Vercel  

---

## 🏗 Architecture
- **Frontend (`/app`, `/components`)**: Component-based architecture, with main state managed in `app/page.tsx`.  
- **Financial Engine (`/lib/engine.ts`)**: Pure TypeScript function `calculateCapTable`, fully decoupled and testable.  
- **Data Types (`/lib/types.ts`)**: Centralized TypeScript interfaces for clarity and type safety.  
- **Backend Service (`/lib/supabase.ts`)**: Abstracts Supabase interactions for saving/loading scenarios.  

---

## 📊 Financial Assumptions
The engine follows **standard venture capital financing practices**.  

Order of operations in each funding round:
1. **Pre-Money ESOP Top-Up** – ESOP pool expanded first (diluting existing holders).  
2. **SAFE Conversions** – Convert at the lowest price among:  
   - Priced Round Share Price  
   - SAFE Valuation Cap Price  
   - SAFE Discount Price  
3. **Priced Round Investment** – New shares issued based on capital raised & pre-money valuation.  
4. **Founder Secondary Sale** – Founders can sell a portion of their shares (no new issuance).  
5. **Post-Money ESOP Top-Up** – Additional ESOP dilution after new shares are issued.  
6. **Exit Payouts** – Calculated as `Final Ownership % * Exit Valuation`.  

⚠️ *Advanced terms like liquidation preferences are not modeled in this version.*  

---

## ⚡ Local Setup
1. **Clone the repository**  
   ```bash
   git clone https://github.com/sakthi0701/startup_simulatorv2.git
   cd startup_simulatorv2

### 1. Install dependencies

```bash
npm install
```

### 2. Set up Supabase Credentials

This project uses Supabase to handle the **"Share Scenario"** feature.

* Create a free Supabase project and get your **Project URL** and **Anon Key**.
* Open the `lib/supabase.ts` file and replace the placeholder values:

```ts
// In lib/supabase.ts
const supabaseUrl = 'YOUR_SUPABASE_PROJECT_URL'; // <-- EDIT THIS
const supabaseAnonKey = 'YOUR_SUPABASE_ANON_KEY'; // <-- EDIT THIS
```

* Create a table in your Supabase project named **`scenarios`** with the following columns:

  * `id (uuid, primary key)`
  * `created_at (timestamptz)`
  * `data (jsonb)`

---

### 3. Run the development server

```bash
npm run dev
```

Then open [http://localhost:3000](http://localhost:3000) in your browser.

---

## ✅ Running Tests

The core financial engine is thoroughly tested using **Vitest**. The tests cover all the acceptance criteria outlined in the hackathon prompt.

Run all tests:

```bash
npm test
```

Run in watch mode:

```bash
npm run test:watch
```

The test file `tests/engine.test.ts` includes checks for:

* Single priced round with no ESOP
* Priced round with pre-money ESOP top-up
* SAFE conversions (cap only, discount only, and mixed)
* Rounds with a founder secondary sale
* Multi-round scenarios with both pre and post-money ESOP top-ups
* Integrity checks to ensure ownership always sums to **100%**
