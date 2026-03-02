Identity Reconciliation

A production-ready Node.js microservice designed to handle identity reconciliation for e-commerce stores. This API links fragmented customer data (emails and phone numbers) across multiple orders, seamlessly consolidating them into a single unified customer profile.

## 🚀 Live Deployment
* **Base URL:** `https://identityreconciliationapi.onrender.com`
* **Endpoint:** `POST /identify`
* **Health Check:** `GET /`

## 🛠️ Tech Stack
* **Runtime & Framework:** Node.js, Express
* **Language:** TypeScript (Strict mode enabled for type safety)
* **ORM:** Prisma
* **Database:** PostgreSQL (Hosted on Neon)
* **Deployment:** Render

## 🧠 Core Logic Flow & Architecture
The system treats customer identities as a "Tree" data structure. The oldest contact is the **Root Primary**, and any subsequent linked contacts are **Secondary** nodes pointing to that root. 

When a `POST /identify` request is received, the system dynamically queries the database for any matching emails or phone numbers and handles the following four scenarios:

1. **New Customer (Creation):** If no matches are found in the database, a new `Contact` row is created with `linkPrecedence` set to `"primary"`.
   
2. **New Information (Extension):** If the incoming data matches an existing tree, but introduces a *new* piece of information (e.g., a known phone number but a brand new email), a new `Contact` row is created with `linkPrecedence` set to `"secondary"` and linked to the oldest primary contact.

3. **Status Quo (Retrieval):** If the incoming email and phone number already exist exactly as provided within the same tree, no database mutations occur. The existing consolidated profile is simply returned.

4. **Account Consolidation (The Merge):** If an incoming request bridges two previously isolated primary accounts (e.g., matching the email of Account A and the phone of Account B), the system merges them:
   * It identifies the oldest primary contact across all matched trees.
   * It demotes any newer primary contacts to `"secondary"` status.
   * It actively updates all existing secondary contacts of the demoted primaries to point directly to the oldest root primary, ensuring a flattened, highly efficient query structure.

## 💻 Local Setup & Installation

1. **Clone the repository:**
  
   git clone [https://github.com/Manav-Bhatt/YOUR-REPO-NAME.git](https://github.com/Manav-Bhatt/YOUR-REPO-NAME.git)
   cd YOUR-REPO-NAME

2. **Install dependencies:**

   npm install

3. **Environment Configuration:**

   Create a .env file in the root directory and add your PostgreSQL connection string:
   DATABASE_URL="postgresql://user:password@host:port/dbname?sslmode=require"


4. **Database Sync & Build:**


   npx prisma db push
   npm run build

5. **Run the server:**

    npm start
