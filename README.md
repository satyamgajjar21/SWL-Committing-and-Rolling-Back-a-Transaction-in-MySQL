# Hands-on Lab: Committing and Rolling Back a Transaction in MySQL

This lab introduces Transaction Control Language (TCL) commands in MySQL — focusing on how to commit and roll back transactions to ensure data integrity. You will create stored procedures that demonstrate atomic transactions using the COMMIT and ROLLBACK commands.

---

## Table of Contents

- [Overview](#overview)  
- [Objectives](#objectives)  
- [Software and Database](#software-and-database)  
- [Setup Instructions](#setup-instructions)  
- [Sample Exercise: Transaction with COMMIT and ROLLBACK](#sample-exercise-transaction-with-commit-and-rollback)  
- [Practice Exercise](#practice-exercise)  
- [Conclusion](#conclusion)  
- [Authors](#authors)

---

## Overview

A **transaction** is a sequence of one or more SQL operations treated as a single logical unit of work. The transaction must satisfy ACID properties:

- **Atomicity**: All or nothing  
- **Consistency**: Maintain database integrity  
- **Isolation**: Transactions do not interfere  
- **Durability**: Once committed, changes persist  

The `COMMIT` command permanently saves changes made in a transaction, while `ROLLBACK` undoes changes in the current transaction unit if an error occurs.

---

## Objectives

By completing this lab, you will be able to:

- Write stored procedures that perform transactions  
- Use `COMMIT` to permanently save changes  
- Use `ROLLBACK` to undo incomplete transactions  

---

## Software and Database

- **Software:** MySQL via phpMyAdmin interface  
- **Databases:**  
  - `Mysql_learners` database  
  - Tables used: `BankAccounts`, `ShoeShop`  

---

## Setup Instructions

1. Download and execute the following SQL scripts in phpMyAdmin to create and populate tables:  
   - [`BankAccounts-CREATE.sql`](#)  
   - [`ShoeShop-CREATE.sql`](#)  

---

## Sample Exercise: Transaction with COMMIT and ROLLBACK

Scenario: Rose is purchasing boots and trainers from ShoeShop. The stored procedure `TRANSACTION_ROSE` updates balances and stock within a single transaction, rolling back all changes if any part fails.

### Stored Procedure Creation

```sql
DELIMITER //
CREATE PROCEDURE TRANSACTION_ROSE()
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
        RESIGNAL;
    END;

    START TRANSACTION;

    UPDATE BankAccounts
    SET Balance = Balance - 200
    WHERE AccountName = 'Rose';

    UPDATE BankAccounts
    SET Balance = Balance + 200
    WHERE AccountName = 'Shoe Shop';

    UPDATE ShoeShop
    SET Stock = Stock - 1
    WHERE Product = 'Boots';

    UPDATE BankAccounts
    SET Balance = Balance - 300
    WHERE AccountName = 'Rose';

    COMMIT;
END //
DELIMITER ;
