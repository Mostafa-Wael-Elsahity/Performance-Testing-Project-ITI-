# ITI Performance Testing Project

**Overview**  
This repository contains a comprehensive Apache JMeter test-suite for functional and light performance validation of two systems under test:

- **JPetStore** — a traditional web e-commerce application (enabled scenario)
- **restful-booker** — a RESTful JSON API (disabled by default)

Artifacts include JMeter test plans (`.jmx`), parameterized CSV test data, and timestamped screenshots used as execution evidence.

---

## Table of Contents
- [Purpose and Scope](#purpose-and-scope)  
- [Repository Contents](#repository-contents)  
- [Test Plan Architecture Overview](#test-plan-architecture-overview)  
- [Systems Under Test](#systems-under-test)  
- [Data-Driven Testing Architecture](#data-driven-testing-architecture)  
- [Test Execution Configuration](#test-execution-configuration)  
- [How to Run (CLI)](#how-to-run-cli)  
- [Test Evidence and Results](#test-evidence-and-results)  
- [Notes & Implementation Details](#notes--implementation-details)  
- [Contributing & Contact](#contributing--contact)  
- [License](#license)

---

## Purpose and Scope
This document provides a high-level overview of the test automation suite for validating functionality and basic performance of the two target applications. It highlights the design choices, repository contents, test execution configuration, and evidence included in the repo.

For deeper details see the in-repo files / pages:
- **JMeter Test Plan Architecture**
- **JPetStore E-commerce Test Scenario**
- **restful-booker API Test Scenario**
- **Test Data Configuration**

---

## Repository Contents

| File | Type | Purpose | Lines / Size |
|---|---:|---|---:|
| `JMeter_Assignment.jmx` | JMeter Test Plan XML | Main test execution configuration with 3 thread groups (modular, selectable) | **1,297 lines** |
| `registeration_data.csv` | Test Data | User registration parameters (17 fields, 2 data rows) | 5 lines |
| `payment_info.csv` | Test Data | Payment card info (3 fields, 1 data row) | 3 lines |
| `2025-10-28_12-29.png` | Evidence | Test setup / JMeter GUI snapshot | image file |
| `2025-10-28_12-30.png` | Evidence | Test run / result snapshot | image file |

> Sources: file listing at repository root

---

## Test Plan Architecture Overview
The `JMeter_Assignment.jmx` implements a modular test plan with selective execution of scenarios via individual Thread Groups. Key characteristics:

- Modular topology: separate Thread Groups for JPetStore, restful-booker, and an "Ultimate" / advanced group
- Selective execution: enable/disable Thread Groups to control which scenario runs
- Shared configuration: HTTP Request Defaults and common Config Elements for protocol/domain reuse
- CSV Data-driven: CSV Data Set Config(s) load test data with `shareMode=all` for cross-thread sharing
- Token extraction: Regex Extractor components capture CSRF tokens from HTML responses for use in form submissions

(See `JMeter_Assignment.jmx` for exact XML structure and component mapping.)

---

## Systems Under Test

### JPetStore (E-commerce)
- **Target:** `https://petstore.octoperf.com`  
- **Type:** Java web app (Struts)  
- **Scenario (enabled):**
  - User registration & account creation
  - Product browsing & catalog navigation
  - Cart operations
  - Checkout & order submission
  - CSRF token handling (`__fp` and `_sourcePage` extraction)
- **Thread Group Status:** Enabled (active)

### restful-booker (API)
- **Target:** `https://restful-booker.herokuapp.com`  
- **Type:** RESTful JSON API  
- **Scenario (disabled):**
  - Token-based authentication
  - Booking creation (JSON)
  - Retrieval / listing of bookings
  - Conditional updates via ForEach loops
  - Deletion of bookings
- **Thread Group Status:** Disabled by default (can be enabled to run)

---

## Data-Driven Testing Architecture
- CSV files drive parameterization for registration and payment.
- `CSV Data Set Config` is configured with `shareMode=all` so data can be consumed by multiple threads/samplers.
- **Dynamic username pattern**: `${userId}_${__time}` to ensure uniqueness per execution.
- CSRF protection implemented by extracting tokens from HTML responses (RegexExtractor) and passing them in POST payloads.
- Combined POST payloads use: 17 registration variables + 3 payment variables + 2 extracted tokens = **22 parameters**.

### CSV files (format notes)
> Actual column names are in the CSV files. The CSVs present in the repo are:
- `registeration_data.csv` — (17 fields, 2 rows). Example format (placeholder):
