# 📚 Evergreen City Library — Advanced SQL Analytics Project

![Library Project Banner](https://raw.githubusercontent.com/your-username/your-repo-name/main/assets/library_project_banner.jpg)

> 📍 A real-world SQL case study simulating a modern library's operations with thousands of rows of data, developed using PostgreSQL in **PgAdmin 4**.

---

## 🧾 Project Description

The **Evergreen City Library Project** is an end-to-end SQL portfolio project where we simulate the day-to-day operations of a public library system. 

**How the library operates:** They maintain a catalog of books by various authors and genres. Members borrow books, reserve books when they are unavailable, pay fees, and leave reviews. Staff members run daily operations, and financial transactions (fees/fines) are tracked.

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

  Set Up the Database in PostgreSQL


## Following questions are developed to solve business insights:

/* Q1. Find the top 10 most expensive books, showing: title, price, author_name, genre_name. */


SELECT b.title,
       b.price,
			 a.author_name,
			 g.genre_name
FROM books AS b
INNER JOIN authors AS a
ON b.author_id = a.author_id
INNER JOIN genres AS g
ON g.genre_id = b.genre_id
ORDER BY b.price DESC
LIMIT 10



/* Q2. Find the top 5 authors who have written the most books. Show: author_name, total_books. 
Sort the result by total_books in descending order. */


SELECT a.author_name,
			 COUNT(b.book_id) AS total_books
FROM authors AS a
INNER JOIN books AS b
ON b.author_id = a.author_id
GROUP BY a.author_name
ORDER BY total_books DESC
LIMIT 5;



/* Q3. Find all books that have a price higher than the average book price.
Show: title, price, author_name. */


SELECT b.title,
       a.author_name,
			 b.price
FROM books AS b
INNER JOIN authors AS a
ON a.author_id = b.author_id
WHERE b.price > 
			 (SELECT AVG(price)
				FROM books);



/* Q4. Find all genres that have more than 50 books. Show: genre_name, total_books. 
Sort the result by total_books in descending order. */


SELECT g.genre_name,
       COUNT(b.book_id) AS total_books
FROM 
books AS b
INNER JOIN genres AS g
ON g.genre_id = b.genre_id
GROUP BY g.genre_name
HAVING COUNT(b.book_id) > 50
ORDER BY 2 DESC;



/* Q5. Find the top 3 most expensive books in each genre. Show: genre_name, title, price.
rank_in_genre (rank based on price within each genre). */


WITH rn AS (
	SELECT b.title,
	       g.genre_name,
	       b.price,
				 RANK() OVER(PARTITION BY genre_name 
				   ORDER BY b.price DESC) AS rank_in_genre
	FROM books AS b
	INNER JOIN genres AS g
	ON g.genre_id = b.genre_id
)
SELECT title,
       genre_name,
			 price
FROM rn
WHERE rank_in_genre IN (1,2,3);



/* Q6. Find all book loans from the last 6 months and show whether they were returned late or on time.
Show the columns: loan_id, book_id, member_id, loan_date, due_date, return_date, status 
(display 'Late' if return_date > due_date, else 'On Time'. If not returned, show 'Not Returned'.) */


SELECT loan_id, 
       book_id, 
			 member_id, 
			 loan_date, 
			 due_date, 
			 return_date,
			 CASE
			   WHEN return_date > due_date THEN 'Late'
				 WHEN return_date IS NULL THEN 'Not Returned'
				 ELSE 'On Time'
			 END AS status				 
FROM book_loans
WHERE loan_date >= CURRENT_DATE - INTERVAL '6 Month';



/* Q7. Find the total revenue generated from book sales for each genre.
Show: genre_name, total_revenue (sum of price * stock)
Sort the result by total_revenue in descending order. */


SELECT g.genre_name,
       ROUND(SUM(b.price::decimal * b.stock), 2)
           AS total_revenue
FROM books AS b
INNER JOIN genres AS g
ON g.genre_id = b.genre_id
GROUP BY g.genre_id,
         g.genre_name
ORDER BY 2 DESC;



/* Q8. Find all members who currently have at least one active (Pending) reservation.
Show: member_id, first_name, last_name, total_pending_reservations
Sort by total_pending_reservations in descending order. */


SELECT m.member_id,
       m.first_name,
			 m.last_name,
			 COUNT(r.status)
			   AS total_pending_reservations
FROM members AS m
INNER JOIN reservations AS r
ON m.member_id = r.member_id
WHERE r.status = 'Pending'
GROUP BY m.member_id,
         m.first_name,
			   m.last_name
HAVING  COUNT(r.status) >= 1
ORDER BY 4 DESC;



/* Q9. Identify the Top 3 Most Loyal Members". Define loyalty as members who:
- Have borrowed the highest total number of books in the last 12 months.
- Have zero unpaid fines (fine = 0 or NULL in all book_loans).
Show: member_id, first_name, last_name, total_books_borrowed
Sort by total_books_borrowed in descending order and limit to 3. */


SELECT b.member_id,
       m.first_name,
			 m.last_name,
			 COUNT(b.loan_id)
			  AS total_books_borrowed
FROM book_loans AS b
INNER JOIN members AS m
ON m.member_id = b.member_id
WHERE b.loan_date >= CURRENT_DATE - INTERVAL '12 Month'
 AND b.member_id NOT IN 
     (SELECT member_id
		  FROM book_loans
			WHERE fine > 0
			 AND loan_date >= CURRENT_DATE - INTERVAL '12 Month'
			)
GROUP BY b.member_id,
	       m.first_name,
				 m.last_name
ORDER BY total_books_borrowed DESC
LIMIT 3;



-- Task: Goal: Analyze how book borrowing trends are changing over time.
/* Q10. Find the number of book loans per month for the last 12 months, along with the 
percentage growth compared to the previous month.
- Show: loan_month, total_loans, growth_percent.
- Sort the result in ascending order of loan_month. */


WITH loan_by_month AS (
	SELECT TO_CHAR(DATE_TRUNC('month', loan_date), 'mm-yyyy')
	          AS loan_month,
				 COUNT(loan_id) AS previous_month_loans,
				 LEAD(COUNT(loan_id)) OVER(
				    ORDER BY DATE_TRUNC('month', loan_date))
						  AS next_month_loans
	FROM book_loans
	WHERE loan_date >= CURRENT_DATE - INTERVAL '12 Month'
	GROUP BY DATE_TRUNC('month', loan_date)
	ORDER BY DATE_TRUNC('month', loan_date)
)
SELECT loan_month,
			 ROUND(((next_month_loans - previous_month_loans):: decimal
            / NULLIF(previous_month_loans, 0)) 
						   * 100, 2) || '%' AS growth_percent
FROM loan_by_month;



/* Goal: Identify the top 10 members who contributed the highest total revenue (payments + fines).
Q11. Calculate each member’s total_revenue as:
- total_revenue = SUM(payments.amount) + SUM(book_loans.fine)
- Rank members based on total_revenue (highest first).
- Show: member_id, first_name, last_name, total_revenue, rank_position
- Sort the final output by rank_position ascending and limit to Top 10 members. */


WITH amount AS (
	SELECT member_id,
	       SUM(amount) 
				    AS total_payment
	FROM payments
	GROUP BY member_id
),
fine AS (
	SELECT member_id,
	       SUM(fine)
	        AS total_fine
	FROM book_loans
	GROUP BY member_id
),
revenue AS (
	SELECT m.member_id,
	       m.first_name,
				 m.last_name,
				 SUM(p.total_payment) + SUM(b.total_fine)
				      AS total_revenue
	FROM members AS m
	LEFT JOIN amount AS p
	ON p.member_id = m.member_id
	LEFT JOIN fine AS b
	ON b.member_id= m.member_id
	GROUP BY m.member_id,
	         m.first_name,
				   m.last_name
)
SELECT *,
       RANK() OVER(ORDER BY total_revenue DESC)
			  AS ranking
FROM revenue
LIMIT 10;



/* Goal: Identify the most popular books based on how often they are borrowed and their average ratings.
Q12. For each book, find: book_id, title, total_borrows, avg_rating
- Only show books that have been borrowed at least 10 times.
- Sort the result by total_borrows (descending), and if there's a tie, sort by avg_rating (descending). */


SELECT b.book_id,
       b.title,
			 COUNT(bl.book_id)
			     AS total_borrows,
			 COALESCE(ROUND(AVG(r.rating::decimal), 2), 0) AS avg_rating
FROM books AS b
LEFT JOIN book_loans AS bl
ON b.book_id = bl.book_id
LEFT JOIN reviews AS r
ON r.book_id = b.book_id
GROUP BY b.book_id,
         b.title
HAVING COUNT(bl.book_id) >= 10
ORDER BY total_borrows DESC;



/* Goal: Find all members who have overdue books (books where return_date is after due_date, 
or not returned yet but due_date < current date).
Q13. Show: member_id, first_name, last_name, book_id, title, due_date, return_date, days_late.
- Sort by days_late in descending order (most overdue first). */


SELECT m.member_id,
       m.first_name,
			 m.last_name,
			 b.book_id,
			 b.title,
			 bl.due_date,
			 bl.return_date,
			 CASE
			   WHEN bl.return_date IS NOT NULL AND bl.return_date > bl.due_date
				   THEN (bl.return_date - bl.due_date)
				 WHEN bl.return_date IS NULL THEN (CURRENT_DATE - bl.due_date)
			   ELSE 0
			 END AS days_late
FROM members AS m
LEFT JOIN book_loans AS bl
ON m.member_id = bl.member_id
LEFT JOIN books AS b
ON b.book_id = bl.book_id
WHERE (bl.return_date > bl.due_date)
  OR (bl.return_date IS NULL AND bl.due_date < CURRENT_DATE)
ORDER BY days_late DESC;



/* Q14. Generate a report of monthly total revenue by combining:
- late return fine + payments revenue
- Results by Year-Month (e.g., 2025-07). Only include the last 12 months.
- Sort the results by month ascending.
- Show: month, total_payments, total_fines, total_revenue = total_payments + total_fines. */


WITH payment_revenue AS (
	SELECT SUM(amount) AS total_payments,
	       DATE_TRUNC('month', payment_date)
				     AS payment_month
	FROM payments
	WHERE payment_date >= CURRENT_DATE - INTERVAL '12 Month'
	GROUP BY payment_month
),
fine_revenue AS (
	SELECT SUM(fine) AS total_fine,
	       DATE_TRUNC('month', loan_date)
				     AS fine_month
	FROM book_loans
	WHERE loan_date >= CURRENT_DATE - INTERVAL '12 Month'
	GROUP BY fine_month
)
SELECT TO_CHAR(p.payment_month, 'YYYY-MM')
            AS revenue_month, 
       p.total_payments,
			 f.total_fine,
			 (p.total_payments + f.total_fine)
			     AS total_revenue
FROM payment_revenue AS p
INNER JOIN fine_revenue AS f
ON f.fine_month = p.payment_month
ORDER BY p.payment_month;



/* Q15. Find the top 3 most active members each month in the last 12 months based 
on the number of books borrowed.
- For each month (YYYY-MM), calculate the total number of books borrowed by each member.
- Rank Top 3 Members
- Show: month, member_id, first_name, last_name, total_books_borrowed, rank_in_month.
- Sort by month ASC, rank_in_month ASC. */


WITH monthly_rank AS (
	SELECT bl.member_id,
	       m.first_name,
				 m.last_name,
	       DATE_TRUNC('month', bl.loan_date) AS month,
	       COUNT(bl.loan_id) AS total_books_borrowed,
				 RANK() OVER(PARTITION BY DATE_TRUNC('month', bl.loan_date)
				    ORDER BY COUNT(bl.loan_id) DESC
				 ) AS rank_in_month
	FROM book_loans AS bl
	LEFT JOIN members AS m
	ON m.member_id = bl.member_id
	WHERE bl.loan_date >= CURRENT_DATE - INTERVAL '12 Month'
	GROUP BY bl.member_id,
	         m.first_name,
				   m.last_name,
	         month
)
SELECT member_id,
       first_name,
			 last_name,
			 total_books_borrowed,
			 TO_CHAR(month, 'YYYY-MM') AS month_year,
			 rank_in_month
FROM monthly_rank
WHERE rank_in_month <= 3;



/* Goal: Find members who have borrowed at least one book from every genre in the library.
Q16. For each member, check if they have borrowed at least one book from each genre.
- Show: member_id, first_name, last_name, total_genres_borrowed.
- Only include members where total_genres_borrowed = total_genres (in the genres table). */


SELECT bl.member_id,
       m.first_name,
			 m.last_name,
       COUNT(DISTINCT g.genre_id) 
			    AS total_genres_borrowed
FROM book_loans AS bl
INNER JOIN books AS b
ON b.book_id = bl.book_id
INNER JOIN genres AS g
ON b.genre_id = g.genre_id
INNER JOIN members AS m
ON m.member_id = bl.member_id
GROUP BY bl.member_id,
         m.first_name,
				 m.last_name
HAVING COUNT(DISTINCT g.genre_id) =
				(SELECT COUNT(*)
				 FROM genres
				 );



/* 	Q17. Find all members who have never borrowed any books.
- Show: member_id, first_name, last_name, email
- Sort the result by member_id. */


-- With Left Join
SELECT bl.member_id,
       m.first_name,
			 m.last_name,
			 m.email
FROM members AS m
LEFT JOIN book_loans AS bl
ON m.member_id = bl.member_id
WHERE bl.member_id IS NULL;



-- With 'No Exists' 
SELECT m.member_id,
       m.first_name,
       m.last_name,
       m.email
FROM members AS m
WHERE NOT EXISTS (
    SELECT 1
    FROM book_loans bl
    WHERE bl.member_id = m.member_id
);



/* Q18. Find the top 3 most borrowed books for each year based on the number of loans.
- For each year (from loan_date): Count how many times each book was borrowed.
- Show: year, book_id, title, total_borrows, rank_in_year.
- Sort by year ASC, rank_in_year ASC. */


WITH book_ranking AS (
	SELECT bl.book_id,
	       b.title,
	       EXTRACT(YEAR FROM bl.loan_date) AS year,
				 COUNT(bl.loan_id) AS total_borrows,
				 DENSE_RANK() OVER(PARTITION BY EXTRACT(YEAR FROM bl.loan_date) 
				   ORDER BY COUNT(bl.loan_id) DESC
				 ) AS rank_in_year
	FROM books AS b
	INNER JOIN book_loans AS bl
	ON b.book_id = bl.book_id
	GROUP BY EXTRACT(YEAR FROM bl.loan_date),
	         bl.book_id,
					 b.title
)
SELECT book_id,
       title,
			 year,
			 total_borrows,
			 rank_in_year
FROM book_ranking
WHERE rank_in_year <= 3
ORDER BY year, rank_in_year ASC;



/* Q19. Identify members who borrowed more books each month for 3 consecutive months within the last year.
- For each member, calculate the monthly count of borrowed books.
- Check if there is any sequence of 3 consecutive months where the count of borrowed books strictly 
increases (e.g., Jan < Feb < Mar).
- Show: member_id, first_name, last_name, month (YYYY-MM), books_borrowed
- Order results by member_id and month. */


WITH to_books_borrowed AS (
	SELECT member_id,
	       DATE_TRUNC('month', loan_date)
				    AS month,
				 COUNT(*) AS books_borrowed
	FROM book_loans
	WHERE loan_date >= CURRENT_DATE - INTERVAL '12 Month'
	GROUP BY member_id,
	         DATE_TRUNC('month', loan_date)
),
check_trend AS (
	SELECT member_id,
	       month,
				 books_borrowed,
				 LAG(books_borrowed, 1) OVER(PARTITION BY member_id 
				   ORDER BY month ASC
				 ) AS borrow_1,
				 LAG(books_borrowed, 2) OVER(PARTITION BY member_id 
				   ORDER BY month ASC
				 ) AS borrow_2,
				 LAG(month, 1) OVER (PARTITION BY member_id 
				   ORDER BY month
				 ) AS prev_month,
	        LAG(month, 2) OVER (PARTITION BY member_id 
					  ORDER BY month
				 ) AS prev_month_2
	FROM to_books_borrowed
)
SELECT c.member_id,
       m.first_name, 
			 m.last_name,
			 TO_CHAR(c.month, 'YYYY-MM') AS year_month,
       c.books_borrowed,
			 TO_CHAR(c.prev_month, 'YYYY-MM') AS prev_month,
			 TO_CHAR(c.prev_month_2, 'YYYY-MM') AS prev_month_2
FROM check_trend AS c
INNER JOIN members AS m
ON m.member_id = c.member_id
WHERE c.borrow_1 IS NOT NULL AND c.borrow_2 IS NOT NULL
  AND c.borrow_2 < c.borrow_1
  AND c.borrow_1 < c.books_borrowed
	AND c.month = c.prev_month + INTERVAL '1 month'
  AND c.prev_month = c.prev_month_2 + INTERVAL '1 month'
ORDER BY 1, year_month;



/* Q20. Find the top borrower(s) for each month — that is, the member(s) who borrowed 
the highest number of books in that month.
- Output Columns: month ('2024-01'), member_id, total_books_borrowed. */


WITH borrowers AS (
	SELECT member_id,
	       DATE_TRUNC('month', loan_date) AS year_month,
	       COUNT(loan_id) AS total_books_borrowed,
				 ROW_NUMBER() OVER(PARTITION BY DATE_TRUNC('month', loan_date)
				  ORDER BY COUNT(loan_id) DESC
				 ) AS rn
	FROM book_loans
	GROUP BY member_id,
	         DATE_TRUNC('month', loan_date)
	ORDER BY DATE_TRUNC('month', loan_date)
)
SELECT member_id,
       TO_CHAR(year_month, 'YYYY-MM') 
			      AS year_month,
			 total_books_borrowed
FROM borrowers
WHERE rn = 1;
