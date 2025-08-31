Startup Value Simulator
🚀 An interactive Scenario Builder that lets founders model cap tables across funding rounds and instantly see how much each founder would make at exit.

Submission for the Hackathon - August 2025

Live URL: https://startupvaluesimulator.netlify.app/

Demo Video: Watch the 3-minute walkthrough here

The Problem
When founding a startup, entrepreneurs often speculate on their potential returns at exit but lack a simple, interactive way to model ownership changes across funding rounds. Equity splits, ESOP allocations, and fundraising rounds impact founder ownership and eventual returns in complex ways. Most founders rely on spreadsheets, which are error-prone and lack intuitive scenario testing. This tool solves that.

Core Features
Initial Setup: Define founders, initial equity splits (by percentage or shares), and create an initial ESOP pool.

Dynamic Funding Rounds: Add and configure multiple funding rounds, including:

Priced Rounds: Standard equity rounds with pre-money valuations.

SAFEs: Simple Agreements for Future Equity that convert to equity, with support for Valuation Caps and Discounts.

ESOP Top-Ups: Increase the employee option pool, calculated either pre-money or post-money.

Founder Secondaries: Allow founders to sell a portion of their existing shares.

Instant Recalculation: All outputs update in real-time as you adjust inputs, providing immediate feedback.

Exit Simulation: Input a hypothetical exit valuation to see the final cash payout for every single stakeholder.

In-Depth Analysis & Visualization:

A round-by-round table view showing valuations, capital raised, and ownership changes.

Charts visualizing founder ownership dilution over time and the distribution of exit proceeds.

Calculation Audit: A transparent "Audit" view for each round that breaks down the exact formulas and numbers used for share price, SAFE conversions, and more.

Save & Share Scenarios: Generate a unique link to save and share your scenario with co-founders, advisors, or investors.

Tech Stack
Framework: Next.js 13 (App Router)

Language: TypeScript

Styling: Tailwind CSS

UI Components: shadcn/ui, Radix UI, Vaul (for drawers)

Visualizations: Chart.js (with react-chartjs-2) & Recharts

Backend & Database: Supabase (for saving/sharing scenarios)

Testing: Vitest & React Testing Library

Deployment: Vercel

Architecture
The application is designed with a clean separation of concerns, making it modular, testable, and performant.

Frontend (/app, /components): The UI is built with a component-based architecture. The main application state is managed in the root app/page.tsx component and passed down to modular, single-responsibility components like InitialSetupForm, FundingRoundsList, and ResultsDisplay.

Financial Engine (/lib/engine.ts): The core calculation logic is a pure, decoupled TypeScript function (calculateCapTable). It takes the complete scenario state as input and returns the calculated results, including any errors or warnings. This separation ensures that the complex financial math is isolated and can be unit-tested thoroughly without involving the UI.

Data Types (/lib/types.ts): We use centralized TypeScript interfaces to define the shape of our data (Founders, Rounds, SAFEs, etc.), ensuring type safety and clarity across the entire application.

Backend Service (/lib/supabase.ts): A simple service layer abstracts all interaction with the Supabase client, providing saveScenario and loadScenario functions for the "Share" feature.

Financial Assumptions
The calculation engine adheres to standard venture capital financing practices. The order of operations within a single funding round is as follows:

Pre-Money ESOP Top-Up: If enabled, the ESOP pool is increased first, diluting only the existing shareholders (founders, previous investors, and the existing ESOP).

SAFE Conversions: All SAFEs for the round convert into equity. The share price for conversion is the minimum of:

The Priced Round Share Price.

The price derived from the SAFE's Valuation Cap (Valuation Cap / Pre-Money Shares).

The price derived from the SAFE's Discount (Priced Round Share Price * (1 - Discount %)).

Priced Round Investment: New shares are issued to the new investors based on the capital raised and the pre-money valuation.

Founder Secondary Sale: If configured, a portion of a founder's existing shares are sold to an investor. This does not create new shares or bring capital into the company.

Post-Money ESOP Top-Up: If enabled, the ESOP pool is increased after all new shares from the priced round and SAFE conversions have been issued, diluting all shareholders.

Exit Payouts: Calculated as Final Ownership % * Exit Valuation. This version does not model advanced terms like liquidation preferences or participation rights.

Local Setup
To run the project locally, follow these steps:

Clone the repository:

git clone [https://github.com/sakthi0701/startup_simulatorv2.git](https://github.com/sakthi0701/startup_simulatorv2.git)
cd startup_simulatorv2

Install dependencies:

npm install

Set up Supabase Credentials:
This project uses Supabase to handle the "Share Scenario" feature. Create a free Supabase project and get your Project URL and Anon Key.

Open the lib/supabase.ts file and replace the placeholder values for supabaseUrl and supabaseAnonKey with your own credentials:

// In lib/supabase.ts
const supabaseUrl = 'YOUR_SUPABASE_PROJECT_URL'; // <-- EDIT THIS
const supabaseAnonKey = 'YOUR_SUPABASE_ANON_KEY'; // <-- EDIT THIS

You will also need to create a table in your Supabase project named scenarios with the following columns:

id (uuid, primary key)

created_at (timestamptz)

data (jsonb)

Run the development server:

npm run dev

Open http://localhost:3000 in your browser.

Running Tests
The core financial engine is thoroughly tested using Vitest. The tests cover all the acceptance criteria outlined in the hackathon prompt.

To run the tests, use the following command:

npm test

To run tests in watch mode:

npm run test:watch

The test file tests/engine.test.ts includes checks for:

Single priced round with no ESOP.

Priced round with pre-money ESOP top-up.

SAFE conversions (cap only, discount only, and mixed).

Rounds with a founder secondary sale.

Multi-round scenarios with both pre and post-money ESOP top-ups.

Integrity checks to ensure ownership always sums to 100%.

License & Attribution
The intellectual property for this project belongs to the development team. By submitting this project, we grant the hackathon organizers permission to feature and demo it with proper attribution.

This project uses several open-source libraries, and we are grateful to their authors. All dependencies are listed in the package.json file.
