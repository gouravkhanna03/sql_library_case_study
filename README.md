# 📚 Evergreen City Library — Advanced SQL Analytics Project

![Library Project Banner](https://raw.githubusercontent.com/your-username/your-repo-name/main/assets/library_project_banner.jpg)

> 📍 A real-world SQL case study simulating a modern library's operations with thousands of rows of data, developed using PostgreSQL in **PgAdmin 4**.

---

## 🧾 Project Description

The **Evergreen City Library Project** is an end-to-end SQL portfolio project where we simulate the day-to-day operations of a public library system.  
We perform **data analysis** using complex SQL queries to extract actionable insights related to:

- Book borrowing trends
- Revenue from fines and payments
- Genre popularity
- Customer loyalty and engagement
- Monthly performance metrics

This project is built for **resume showcase** and **interview demonstration**, reflecting strong skills in SQL logic, RDBMS understanding, and business-oriented analytics.

---

## 📊 Dataset Overview (Tables Used)

| Table Name       | Description                                  |
|------------------|----------------------------------------------|
| `members`        | Information about library members            |
| `books`          | Library book inventory and stock             |
| `authors`        | Authors linked to books                      |
| `genres`         | Book categories/genres                       |
| `book_loans`     | Lending transactions (loan, due, return, fine)|
| `payments`       | Payments made by members                     |
| `reservations`   | Book reservations with status                |
| `reviews`        | Member reviews and book ratings              |

---

## 📌 Key Insights Generated

- Top genres contributing to revenue
- Members who never paid fines
- Members who borrowed books in **3 consecutive months**
- Growth trend in book loans and revenue
- Top-rated and most-borrowed books
- Monthly top borrowers and loyal members
- Revenue split by payments vs. fines

---

## 🛠️ Tech Stack & SQL Concepts Used

- **RDBMS**: PostgreSQL with **PgAdmin 4**
- **SQL Concepts**:
  - `JOIN`, `LEFT JOIN`, `INNER JOIN`
  - `GROUP BY`, `HAVING`, `ORDER BY`
  - Aggregate Functions: `SUM`, `COUNT`, `AVG`
  - Subqueries and Nested Queries
  - `CASE` Statements
  - `CTEs` (Common Table Expressions)
  - Window Functions: `RANK()`, `DENSE_RANK()`, `LAG()`, `ROW_NUMBER()`
  - Date Functions: `DATE_TRUNC`, `EXTRACT`, `INTERVAL`, `CURRENT_DATE`
  - `NULLIF`, `COALESCE`

---

## 🧑‍💻 How to Use This Project

- Clone the repository:
  ```bash
  git clone https://github.com/your-username/evergreen-city-library-sql.git
