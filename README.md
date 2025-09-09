## Project Summary

A desktop Java Swing application for managing electricity billing workflows. It supports admin and customer roles, including customer onboarding, meter information management, bill calculation and generation, bill payment tracking, and general utilities (notepad, calculator) within the UI.

## Tech Stack

- Language/UI: Java, Swing (desktop GUI)
- Database: MySQL (JDBC)
- Build: NetBeans (Ant project), build.xml, nbproject/

## Environment & Configuration

- MySQL database expected at jdbc:mysql:///ebs with username root and password 1234 (see Conn.java).
- Tables referenced in code: login, customer, meter_info, tax, bill.
- Run from NetBeans or via java after compiling sources.

## System Architecture

- UI Layer (Swing JFrames):
  - Login: username/password with role selection (Admin/Customer) reads login table; on success opens Project shell.
  - Project: main menu window controlling navigation based on role (Admin gets master ops; Customer gets info/user/report menus).
  - Admin functions: NewCustomer, CustomerDetails, DepositDetails, CalculateBill.
  - Customer functions: ViewInformation, UpdateInformation, BillDetails, PayBill, GenerateBill.
- Data Access:
  - Simple JDBC via Conn class exposing Connection and Statement created per instance, used across screens.
- Assets:
  - Icons and background images under src/icon.

## Key Workflows

- Authentication (Login)

  - User enters credentials and selects role. Query: select * from login where username=? and password=? and user=?.
  - On success, retrieves meter_no and opens Project with role and meter number.

- Main Navigation (Project)

  - Role-based menu composition:
    - Admin: Master (New Customer, Customer Details, Deposit Details, Calculate Bill), Utilities, Exit.
    - Customer: Information (Update/View), User (Pay Bill, Bill Details), Report (Generate Bill), Utilities, Exit.

- Bill Calculation (Admin)

  - CalculateBill loads meter numbers from customer, resolves selected customer details, accepts units and month.
  - Computes total using tax table: cost per unit + meter rent + service charges + service tax + swachh bharat cess + fixed tax.
  - Inserts bill record: insert into bill (meter_no, month, units, totalbill, status='Not Paid').

- Bill Payment (Customer)

  - PayBill loads current bill for a selected month and updates status to Paid on payment, then opens Paytm screen.

- Bill Generation (Customer)
  - GenerateBill composes a detailed bill preview in a text area from customer, meter_info, tax, and bill tables for a selected month.

## Database Entities (inferred)

- login(username, password, user, meter_no)
- customer(meter_no, name, address, city, state, email, phone, ...)
- meter_info(meter_no, meter_location, meter_type, phase_code, bill_type, days, ...)
- tax(cost_per_unit, meter_rent, service_charge, service_tax, swacch_bharat_cess, fixed_tax)
- bill(meter_no, month, units, totalbill, status)

## Notable Classes

- Conn: central JDBC helper connecting to MySQL (configure credentials accordingly).
- Login: credential + role check against DB; routes to Project.
- Project: main menu and role-based navigation.
- CalculateBill: compute and persist monthly bills.
- PayBill: display and mark bills as paid.
- GenerateBill: assemble printable bill summary for the month.
- Other screens: NewCustomer, CustomerDetails, DepositDetails, ViewInformation, UpdateInformation, BillDetails, MeterInfo, Signup, Splash, Paytm.

## Security & Considerations

- SQL queries are string-concatenated; use PreparedStatements to prevent SQL injection.
- Database credentials are hardcoded; externalize to config or environment.
- No password hashing; store hashed passwords if possible.
- Connection handling uses a single Statement; consider pooled connections and proper resource closing.

## Setup & Run

1. Create MySQL schema ebs and required tables/columns (based on code above).
2. Update Conn.java with your DB credentials.
3. Open in NetBeans (Ant project) or compile via javac; ensure classpath includes MySQL JDBC driver.
4. Run Login to start the application.
