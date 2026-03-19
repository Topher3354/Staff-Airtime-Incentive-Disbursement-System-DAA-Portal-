# Use Case 08 — Staff Airtime & Incentive Disbursement System (DAA Portal)

**Category:** Finance & Payments
**Author:** Christopher Ayodeji Ojo — [Trublshut](https://whop.com/trublshut)
**Tools:** Microsoft Power Apps · Microsoft Power Automate · Microsoft Entra ID API · Vendor REST API · SharePoint

---

## 🔴 The Problem

Staff airtime and incentive disbursements are managed through spreadsheets and manual processes. Finance staff spend days each month collecting requests by email, verifying employee identity manually, chasing line manager approvals, entering payment details into vendor portals, and routing each disbursement to the correct mobile number or bank account.

This process is slow, error-prone, and unscalable. As headcount grows, the manual burden grows with it. A single data entry error sends payment to the wrong number. There is no audit trail. No real-time visibility for staff. And Finance is trapped doing repetitive work that adds no value.

---

## ✅ The Solution

A self-service DAA (Data and Airtime Allocation) Portal built on Microsoft Power Apps, integrated with Power Automate and Microsoft Entra ID — where staff submit their own requests, identity is verified automatically, approvals are routed to the correct manager, and payments are disbursed directly to the employee's registered mobile number or bank account via the vendor API.

Finance sets the policy. The system executes it.

> 💡 **This is a real production system** — built and deployed at Reliance Health, used daily across HR, Finance, and IT to manage staff airtime and incentive disbursements at scale.

---

## ⚙️ How It Works

```
Staff logs into DAA Portal (Power Apps — Entra ID SSO)
        ↓
Identity verified automatically via Microsoft Entra ID
        ↓
Staff selects request type: Airtime / Incentive / Data
Staff views their registered mobile number / account (pre-populated from Entra ID)
        ↓
Staff submits request (amount within allowed range)
        ↓
Power Automate: validate request eligibility
(check: last request date, monthly limit, outstanding requests)
        ↓
Route approval request to line manager (Teams Adaptive Card)
        ↓
Manager approves or rejects in Teams
        ↓
On approval: call vendor payment REST API
(payload: employee mobile / account, amount, type, reference)
        ↓
Vendor processes disbursement to employee mobile / account
        ↓
Portal updates in real time: status → "Disbursed"
        ↓
Confirmation notification sent to staff + manager
        ↓
Full transaction record written to Finance SharePoint tracker
```

---

## 🛠️ Tech Stack

| Tool | Role |
|------|------|
| Microsoft Power Apps | Self-service portal UI + Entra ID SSO |
| Microsoft Power Automate | Workflow orchestration + approval routing |
| Microsoft Entra ID API | Identity verification + employee data retrieval |
| Vendor REST API | Payment / airtime disbursement execution |
| SharePoint | Transaction log + Finance reporting |
| Microsoft Teams | Manager approval cards |

---

## 🔧 Key Logic

- **SSO authentication:** staff sign in with their Entra ID credentials — no separate login, no identity verification step required
- **Pre-population:** employee mobile number, bank account details, department, and manager are pulled from the Entra ID user profile automatically — staff cannot edit them, preventing payment redirection fraud
- **Eligibility rules:** Power Automate checks — last request within cooldown period? Monthly limit reached? Pending request already open? If any check fails, request is blocked with a clear reason
- **Approval routing:** manager is read directly from the Entra ID `manager` attribute — no manual routing configuration needed per employee
- **Vendor API payload:** structured JSON with employee reference, disbursement type, amount, and idempotency key (prevents duplicate disbursements on retry)
- **Real-time status:** portal status field connected to SharePoint — updates live as workflow progresses
- **Finance reporting:** SharePoint list captures every transaction — staff, manager, amount, type, date, vendor reference, approval timestamp — enabling monthly Finance reconciliation

---

## 📊 Outcomes

- ✅ Monthly manual disbursement process completely eliminated
- ✅ Staff have real-time visibility on every request from submission to disbursement
- ✅ Finance team freed from spreadsheet-based tracking and manual payment routing
- ✅ Every payment has a full identity-verified, manager-approved audit trail
- ✅ System scales to any number of staff with zero additional Finance workload
- ✅ Payment redirection fraud risk eliminated through Entra ID pre-population

---

## 📁 How to Use This

1. Build the Power Apps portal with Entra ID as the data source — use `Office365Users.MyProfile()` to pre-populate employee details
2. Create a SharePoint list as the request store — each submission creates a new list item
3. Power Automate trigger: on new SharePoint item creation
4. Add eligibility check conditions: query SharePoint for recent requests from same user
5. Create an Approval action routed to the manager (read from Entra ID `manager` field)
6. On approval: call your vendor's REST API with the disbursement payload (use Entra ID data — not user-entered data — for account details)
7. Update the SharePoint request item with disbursement status + vendor reference
8. Push a notification back to the Power Apps portal via a SharePoint update trigger
9. Send confirmation emails to staff + manager via Outlook connector

---

## 🔗 Related Use Cases

- [Use Case 07 — Vendor Payment Automation Pipeline](./UC-07-Vendor-Payment-Automation-Pipeline.md)
- [Use Case 10 — Expense Report Auto-Validation & Routing](./UC-10-Expense-Report-Auto-Validation.md)

---

*© 2026 Christopher Ayodeji Ojo · Trublshut AI Automation · christopherayodeji131@gmail.com*
