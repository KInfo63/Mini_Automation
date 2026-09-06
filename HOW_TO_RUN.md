# Mini Automation — Data-Driven Edition
## How to Run (Extract → Run)

### Prerequisites
- Java 17+
- Maven 3.8+
- Node.js 18+
- MySQL 8 running on localhost:3306
- Chrome browser (Playwright downloads it automatically on first run)

---

## Step 1 — Configure Database

MySQL must be running. The app auto-creates the database.

Edit `backend/src/main/resources/application.properties` if your MySQL credentials differ:

```
spring.datasource.username=root
spring.datasource.password=Kartik@03
```

---

## Step 2 — Start Backend

```bash
cd backend
mvn spring-boot:run
```

First run takes ~2 minutes (Maven downloads dependencies including Playwright + Apache POI).
Backend starts on **http://localhost:8080**

---

## Step 3 — Start Frontend

Open a second terminal:

```bash
cd frontend_FIXED_v2/frontend
npm install
npm run dev
```

Frontend starts on **http://localhost:5173**

---

## Step 4 — Use the App

1. Open http://localhost:5173
2. Click **UI Automation** → **New Test**
3. Record your test
4. Open the test → click **Data-Driven** tab
5. Upload a CSV or XLSX file
6. Select Start Step and End Step
7. Validate mapping → Start Data-Driven Run
8. View live results on the report page

---

## Data-Driven CSV/XLSX Format

First row = column headers (must match recorded field names).
Each subsequent row = one iteration.

Example `customers.xlsx`:
| Customer Name | Email            | Mobile     | Address |
|---------------|------------------|------------|---------|
| Rahul         | rahul@test.com   | 9876543210 | Mumbai  |
| Amit          | amit@test.com    | 9876543211 | Pune    |

---

## New Features Added

- **Data-Driven Tab** in TestDetails page
- Upload CSV or XLSX (up to 50 MB)
- Auto field mapping (6-priority chain)
- Manual mapping override per field
- Pre-loop / Data-loop / Post-loop execution model
- Row-level SUCCESS/FAILED results
- Live-polling report page with expandable rows
- Two new DB tables auto-created: `data_driven_runs`, `data_driven_row_results`

