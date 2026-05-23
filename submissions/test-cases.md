# Test Cases — Test Case Table

| Info | |
|---|---|
| **Group** | Group 09 |
| **Date Created** | 18/05/2026 |
| **System** | https://stqa.rbc.vn |
| **Reference** | SRS v1.0 |

---

## Step 1: Input Domain Modeling (IDM)

### IDM — Login (REQ-01)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Are credentials valid? | Valid email + valid password | `librarian@library.com` / `admin123` | Login successful, redirected to dashboard |
| | Valid email + wrong password | `librarian@library.com` / `wrongpass` | Specific error: "Incorrect password" |
| | Non-existent email | `nobody@x.com` / (any password) | Error: account not found |
| Are fields empty? | Both filled | (any valid value) | Form processed normally |
| | Both fields empty | `""` / `""` | Both fields highlighted with validation error |

### IDM — Book Search (REQ-03)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Does keyword match a record? | Yes | `"Flutter"` | Matching books displayed |
| | No | `"XYZ999"` | "No results found" message displayed |
| Can user trigger search by pressing Enter? | Yes — Enter key works | Type keyword, press Enter | Search executes immediately |
| | No — must click button manually | Type keyword, press Enter | Nothing happens (Bug: requires manual button click) |

### IDM — Borrow Book (REQ-04)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Member status? | Active | MEM002 | Borrowing allowed |
| | Suspended | MEM004 | Rejected: "Member is suspended" |
| | Expired | MEM005 | Rejected: message distinct from "suspended" |
| Number of books currently borrowed? (BVA) | 2 (one below SRS limit of 3) | MEM with 2 books | Allowed; member now has 3 |
| | 3 (at SRS limit) | MEM with 3 books | Rejected: borrow limit reached |
| | 4 (actual system limit — Bug) | MEM with 4 books | Should reject; system currently allows |
| Book availability? | Available | BOOK001 | Borrow allowed |
| | Already borrowed | BOOK003 | Rejected |

### IDM — Return Book (REQ-05)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Is return on time? | On time | Return on or before due date | Accepted, no warning |
| | Overdue | Return after due date | Accepted WITH visible overdue warning |
| Does the book belong to the logged-in member? | Yes | Own borrowed book | Return accepted |
| | No — another member's book | Different member's book ID | Rejected: "This book does not belong to your account" |

### IDM — Member Data Access (REQ-06)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Whose records does the member view? | Own records | Logged-in member's ID | Records displayed |
| | Another member's records | Different member's ID | Access denied |

### IDM — Member Registration (REQ-07)

| Characteristic | Block | Representative Value | Expected Result |
|---|---|---|---|
| Email format valid? | Valid | `user@example.com` | Accepted |
| | Missing dot in domain | `user@examplecom` | Rejected: "Invalid email format" |
| | Missing @ symbol | `userexample.com` | Rejected: "Invalid email format" |

---

## Step 2: Test Cases

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|---|---|---|---|---|---|---|---|
| TC-01 | Login with valid credentials | Login page open at https://stqa.rbc.vn | 1. Open the login page. <br> 2. Enter valid email and password. <br> 3. Click Login. | Email: `librarian@library.com` / Password: `admin123` | Dashboard is displayed; login successful. | REQ-01 | EP |
| TC-02 | Login with incorrect password shows correct error message | Login page open | 1. Enter a valid registered email. <br> 2. Enter an incorrect password. <br> 3. Click Login. | Email: `librarian@library.com` / Password: `wrongpass` | A specific error message is displayed indicating the **password** is wrong (e.g. "Incorrect password. Please try again."). The message must not reference member suspension or any unrelated issue. | REQ-01 | EP |
| TC-03 | Login with both fields empty — both fields (or one empty field) should be highlighted | Login page open, no data entered | 1. Leave both email and password fields blank. <br> 2. Click Login. | Email: `""` / Password: `""` | Both the email field AND the password field are highlighted with a validation error. The form is not submitted. | REQ-01 | EP |
| TC-04 | Login with non-existent account | Login page open | 1. Enter an email not registered in the system. <br> 2. Enter any password. <br> 3. Click Login. | Email: `nobody@test.com` / Password: `admin123` | Error message displayed (e.g. "Account not found or invalid credentials"). | REQ-01 | EP |
| TC-05 | Logout from the system | Logged in as librarian. | 1. Click the Logout button. | — | Session ends. User is redirected to the login page. Accessing a restricted page afterwards requires re-login. | REQ-01 | EP |
| TC-06 | Book list shows status in real-time after borrow | Logged in as librarian. Book list open. An available book exists. | 1. Navigate to Borrow / Return book. <br> 2. Return any books for a member. <br> 3. Navigate back to the book list. | Book: BOOK001 / Member: MEM002 | The status of that book immediately updates from Borrowed to Available on the page in real-time without requiring a page refresh. | REQ-02 | EP |
| TC-07 | Search book's name on main page | Logged in. Main page open. | 1. Click the search input field. <br> 2. Type a keyword. <br> | Keyword: `"Flutter"` | Search executes automatically and results are displayed. No manual button click required. | REQ-03 | EP |
| TC-08 | Records searching is not case-sensitive | Logged in. 'Book / Return' page open. | 1. Click the search records input field. <br> 2. Type a Member ID. <br> | Keyword: `"mem002"` | Results are displayed. | REQ-03 | EP |
| TC-09 | Search records by clicking the Search button | Logged in. Main page open. | 1. Type a keyword in the search field. <br> 2. Click the Search button. | Keyword: `"Flutter"` | Matching records are displayed. | REQ-03 | EP |
| TC-10 | Search with no-match keyword shows "No results" message | Logged in. | 1. Type a keyword with no match in the database. 2. Click Search. | Keyword: `"XYZ999"` | A clear "No results found" message is displayed. The results area is not left blank. | REQ-03 | EP |
| TC-11 | Search by author name (not just book title) | Logged in. Main page open. | 1. Click the search input field. <br> 2. Type an author's name. <br> 3. Click Search or wait for auto-search. | Keyword: `"Nguyễn Minh Đức"` | Books authored by "Nguyễn Minh Đức" are displayed in the results. | REQ-03 | EP |
| TC-12 | Filter by genre returns only matching books | Logged in. Main page open. | 1. Click the genre filter dropdown. <br> 2. Select a specific genre. | Genre: `"Kinh tế"` | Only books belonging to the "Kinh tế" category are displayed in the book list. | REQ-03 | EP |
| TC-13 | Borrow book as an active member (normal case) | Logged in as MEM003. MEM003 is active, 0 books currently borrowed. BOOK001 is available. | 1. Navigate to Borrow / Return book. <br> 2. Select BOOK001. <br> 3. Click to borrow. <br> 4. Confirm. | Book: BOOK001 / Member: MEM003 | Borrow is recorded successfully. MEM003 now has 1 book borrowed. | REQ-04 | EP |
| TC-14 | Borrow rejected for suspended member | Logged in as librarian. MEM004 has status "Suspended". An available book exists. | 1. Navigate to Borrow Book. <br> 2. Assign an available book to MEM004. <br> 3. Confirm. | Member: MEM004 (Suspended) | System rejects the borrow. Error message references **suspension** specifically (e.g. "Member is suspended and cannot borrow books."). | REQ-04 | EP |
| TC-15 | Borrow rejected for expired member — message distinct from suspended | Logged in as librarian. MEM005 has status "Expired". An available book exists. | 1. Navigate to Borrow Book. <br> 2. Assign an available book to MEM005. <br> 3. Confirm. | Member: MEM005 (Expired) | System rejects the borrow. Error message references **expiry** specifically (e.g. "Member subscription has expired.") — text must be different from the suspended message. | REQ-04 | EP |
| TC-16 | BVA: borrow allowed when member has 1 book | Logged in. A member has exactly 1 book currently borrowed. An available book exists. | 1. Navigate to Borrow Book. <br> 2. Assign another available book to this member. <br> 3. Confirm. | Member: 1 book borrowed | Borrow is allowed. Member now holds 2 books. | REQ-04 | BVA |
| TC-17 | Borrow allowed when member has 2 books (BVA: one below limit) | Logged in. A member has exactly 2 books currently borrowed. An available book exists. | 1. Navigate to Borrow Book. <br> 2. Assign another available book to this member. <br> 3. Confirm. | Member: 2 books borrowed | Borrow is allowed. Member now holds 3 books — at the maximum limit. | REQ-04 | BVA |
| TC-18 | Borrow rejected when member has 3 books (BVA: at SRS limit) | Logged in. A member has exactly 3 books currently borrowed. An available book exists. | 1. Navigate to Borrow Book. <br> 2. Assign another available book to this member. <br> 3. Confirm. | Member: 3 books borrowed | System rejects the borrow. Message displayed: "Borrow limit reached (maximum 3 books)." | REQ-04 | BVA |
| TC-19 | Borrow rejected when member has 4 books (BVA: above SRS limit) | Logged in. A member has exactly 4 books currently borrowed. An available book exists. | 1. Navigate to Borrow Book. <br> 2. Assign another available book to this member. <br> 3. Confirm. | Member: 4 books borrowed | System rejects the borrow. The SRS limit is 3 books; allowing 4 is a bug. | REQ-04 | BVA |
| TC-20 | Borrow rejected for a book already borrowed (unavailable) | Logged in as a member (e.g.: MEM003). BOOK003 is already borrowed by MEM002 in seed data. | 1. Navigate to home page. <br> 2. Search for BOOK003. <br> 3. No plus symbol is shown. | Book: BOOK003 / Member: MEM002 | System hide the borrow button, indicating the book is unavailable (e.g. already borrowed). | REQ-04 | EP |
| TC-21 | Return book on time — no overdue warning shown | Logged in. MEM002 has borrowed BOOK001. Due date has not yet passed. | 1. Navigate to Return Book. <br> 2. Select BOOK001 for MEM002. <br> 3. Confirm return. | Return date: on or before due date | Book returned successfully. No overdue warning is displayed. | REQ-05 | EP |
| TC-22 | Return book late — overdue warning must be displayed | Logged in. MEM002 has borrowed BOOK001. The due date has already passed. | 1. Navigate to Return Book. <br> 2. Select BOOK001 for MEM002. <br> 3. Confirm return with a date past the due date. | Return date: after due date | Book is returned AND an overdue warning is visibly displayed (e.g. "This book is X day(s) overdue."). | REQ-05 | EP |
| TC-23 | Member cannot return a book borrowed by another member | Logged in as MEM002. BOOK005 is currently borrowed by MEM003 (not MEM002). | 1. Navigate to Return Book. <br> 2. Enter or select BOOK005. <br> 3. Confirm return. | Book: BOOK005 (borrowed by MEM003) / Logged-in user: MEM002 | System rejects the return. Message: "This book does not belong to your account." | REQ-05 | EP |
| TC-24 | Member cannot view another member's borrow records | Logged in as MEM002. MEM003 has existing borrow records. | 1. Navigate to member borrow history. <br> 2. Attempt to access the records of MEM003. | Logged-in user: MEM002 / Target: MEM003 | Access is denied. System shows "Access denied" or redirects to MEM002's own records only. MEM003's records are not visible. | REQ-06 | EP |
| TC-25 | Librarian can trigger overdue check and records are marked | Logged in as librarian. Borrow records page open. | 1. Navigate to borrow records. <br> 2. Click "Kiểm tra quá hạn" (Check overdue). | Record: BR001 (due 15/09/2024) | Overdue check is triggered, and record BR001 status is marked as "Quá hạn". | REQ-06 | EP |
| TC-26 | Register new member with valid email | Logged in as librarian. Add New Member page open. | 1. Fill in all required fields with valid data. <br> 2. Enter a properly formatted email. <br> 3. Click Submit. | Email: `newmember@example.com` | Member is created successfully. A confirmation message is shown. | REQ-07 | EP |
| TC-27 | Register new member — email missing dot in domain is rejected | Logged in as librarian. Add New Member page open. | 1. Fill in all required fields. <br> 2. Enter an email without a dot in the domain part. <br> 3. Click Submit. | Email: `newmember@examplecom` | System rejects the form. Validation error shown: "Invalid email format." No member record is created. | REQ-07 | EP |
| TC-28 | Register new member — email missing @ symbol is rejected | Logged in as librarian. Add New Member page open. | 1. Fill in all required fields. 2. Enter an email without the @ symbol. 3. Click Submit. | Email: `newmemberexample.com` | System rejects the form. Validation error shown: "Invalid email format." | REQ-07 | EP |
| TC-29 | Duplicate email rejected during member registration | Logged in as librarian. Add New Member page open. | 1. Fill in all required fields. <br> 2. Enter an email that already exists. <br> 3. Click Submit. | Email: `ba.nguyen@email.com` | System rejects with a duplicate email error. | REQ-07 | EP |
| TC-30 | Librarian can view borrow records of any member | Logged in as librarian. "Mượn / Trả" page open. | 1. Navigate to "Mượn / Trả". <br> 2. Search by MEM002's ID. <br> 3. Verify member's borrow records. | Member ID: `"MEM002"` | MEM002's borrow records (including BR001, BR004) are displayed. | REQ-08 | EP |
| TC-31 | Verify book information displayed correctly | Logged in as librarian. Main page open. | 1. Verify book information. | Book ID: `"BOOK001"` | Book information (title, author, etc.) is displayed correctly. | REQ-02 | EP |


## Summary

| Feature Group | # of TCs | REQ Coverage | IDM Technique Applied |
|---|---|---|---|
| Login / Authentication | 5 (TC-01 to TC-05) | REQ-01 | EP |
| Book List / Real-time Status | 1 (TC-06) | REQ-02 | EP |
| Searching & Filtering | 6 (TC-07 to TC-12) | REQ-03 | EP |
| Borrow Book | 8 (TC-13 to TC-20) | REQ-04 | EP, BVA |
| Return Book | 3 (TC-21 to TC-23) | REQ-05 | EP |
| Overdue Check & Access | 2 (TC-24, TC-25) | REQ-06 | EP |
| Member Registration | 4 (TC-26 to TC-29) | REQ-07 | EP |
| Access Control | 1 (TC-30) | REQ-08 | EP |
| Book Information | 1 (TC-31) | REQ-02 | EP |
| **Total**: **31** | REQ-01, REQ-02, REQ-03, REQ-04, REQ-05, REQ-06, REQ-07, REQ-08, REQ-09 | EP + BVA |
