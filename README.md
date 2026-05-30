# CRM & Stripe Integration: Requirements Specification

## 1. Business Context & User Stories

### Project Goal
An online education platform (Online School) aims to automate the course purchasing process. When a student pays for a course on the website, their status must update automatically in the CRM, and access to the educational materials should be granted immediately without manual intervention.

### User Stories
* **As a Student**, I want to pay for a course quickly and securely using my credit card on the website so that I can get instant access to the student dashboard.
* **As a School Administrator**, I want the student's status in the CRM to automatically change to "Active" upon successful payment so that I don't have to manage access manually.

---

## 2. System Architecture & UML Sequence Diagram

The diagram below illustrates the interaction between the Student, the School Frontend, the CRM Backend, and the Stripe API during the checkout process:

```mermaid
sequenceDiagram
    autonumber
    actor Student as Student
    participant Frontend as School Website (Frontend)
    participant Backend as CRM Server (Backend)
    participant Stripe as Stripe API (Payment Gateway)

    Student->>Frontend: Clicks "Buy Course" & enters card details
    Frontend->>Backend: Sends order creation request (Order ID, Amount)
    Backend->>Stripe: Sends POST /v1/charges (Card Data, Amount)
    
    alt Successful Payment
        Stripe-->>Backend: Returns 200 OK (Payment Intent ID, Success)
        Backend->>Backend: Updates student status to "Paid" in Database
        Backend-->>Frontend: Returns "Success" response
        Frontend-->>Student: Displays "Payment Successful!" screen
    else Error (e.g., Insufficient Funds)
        Stripe-->>Backend: Returns 402 Payment Required (Error: Card Declined)
        Backend-->>Frontend: Relays payment error details
        Frontend-->>Student: Displays "Insufficient funds. Please try another card."
    end
