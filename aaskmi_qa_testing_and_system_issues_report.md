# AAskMi System — QA Testing & System Issues Report

**Software Quality Assurance & System Verification**  
**System Under Test:** AAskMi Multi-Branch POS, Inventory & Analytics Platform  
**Source Basis:** QA Testing Checklist & Tester Observations

| In-Scope Test Cases | Passed (Clean) | Passed w/ Observations | Recorded Failed | Unrecorded / Unverified |
| :---: | :---: | :---: | :---: | :---: |
| **39** | **23** | **5** | **5** | **6** |

---

## I. Executive Summary

This Quality Assurance (QA) Testing and System Issues Report documents the current functional test results, usability observations, and system defects identified during evaluation of the **AAskMi** platform. Based on the in-scope sections of `AAskMi_QA_Testing_Checklist.xlsx` and supplementary testing observations, the system demonstrates functional stability across baseline login validation, Point of Sale (POS) menu display, cash and digital payment checkout, recipe-based ingredient stock deduction, and core inventory management operations. Out of 39 in-scope test cases evaluated across seven modules, 28 were recorded as **PASSED** in the checklist; however, a detailed audit of tester notes shows that 5 of those passing cases contain significant performance, usability, or functional issues, alongside 5 explicitly **FAILED** test cases and 6 unrecorded or data-blocked cases.

The highest-priority issues requiring immediate engineering remediation involve: (1) **session security and account isolation**, where using the browser Back and Forward buttons restores a previously opened user session and delayed top-right username synchronization during Sales page loading can expose a prior account when switching rapidly to POS; (2) **multi-branch data isolation**, where a price change made by a Taal Branch Manager altered the product price across all branches, and an unauthorized or inactive branch (Lipa) appears in both the Sales branch filter and User Management branch-assignment options; (3) **role-specific reporting failures**, where reports and branch data fail to load for the Owner role while loading normally for the Manager role; and (4) **POS cart interaction latency**, where adding items and incrementing or decrementing quantities each require approximately three seconds per action.

> **[Diagram — Figure 1: In-Scope Test Case Outcomes by Module (39 Total Test Cases)]**  
> *Horizontal stacked bar chart showing test case outcomes across the 7 in-scope modules. Legend: Clean Pass (23), Pass w/ Obs (5), Failed (5), Unv. (6).*
> - **1. Auth & Access (8):** 5 Clean, 2 Obs, 1 Fail
> - **2. Point of Sale (9):** 8 Clean, 1 Obs (3s Latency)
> - **4. Inventory Mgmt (7):** 7 Clean
> - **5. Sales Trans. (5):** 1 Clean, 1 Obs, 1 Fail, 2 Unrecorded
> - **7. Pred. Analytics (3):** 1 Obs (Defect noted), 1 Fail, 1 Data-Blocked
> - **8. Reports & Audit (3):** 1 Fail, 2 Unrecorded (1 Owner Defect Noted)
> - **9. Org & Settings (4):** 2 Clean, 1 Fail, 1 Unrecorded (Defects Noted)
>
> *Figure 1: Distribution of the 39 in-scope QA test cases across the seven evaluated AAskMi modules.*

---

## II. Scope of Testing

The testing effort evaluated seven core functional modules of the AAskMi system across three user roles (Owner, Manager, and Floater):

- **Included Modules (39 Test Cases):** Section 1: Authentication and Access Control (`AUTH-01` to `AUTH-08`), Section 2: Point of Sale (`POS-01` to `POS-09`), Section 4: Inventory Management (`INV-01` to `INV-07`), Section 5: Sales Transactions (`SAL-01` to `SAL-05`), Section 7: Predictive Analytics and Decision Support (`ANA-01` to `ANA-03`), Section 8: Reports and Audit (`REP-01` to `REP-03`), and Section 9: Organization, Users, and Settings (`SET-01` to `SET-04`).
- **Excluded Excel Sections:** Per project instructions, *Section 3: Floater Operations Portal* and *Section 6: Executive Dashboard* from the Excel checklist are completely excluded from this report. Unrelated cross-module observations recorded under in-scope sections (such as Manager navigation from POS to Floater in `AUTH-06` and Owner dashboard role display in `SET-01`) are retained.
- **Testing Limitations & Database Dependencies:** Features in Predictive Analytics (`ANA-01`, `ANA-02`, `ANA-03`) rely on historical consumption records and sufficient branch inventory items that were not fully populated in the test database. In addition, three test cases (`SAL-04`, `SAL-05`, and `REP-02`) had blank statuses and notes in the checklist. Features dependent on missing database records require additional testing with seeded data before their actual functionality or root cause can be confirmed.

> **Test Outcome Classifications Preserved in This Report**  
> **Failed:** Observed behavior does not meet expected results. **Passed with Observations (PASSED\*):** Expected core step succeeded, but tester notes identify remaining defects, performance delays, or usability concerns. **Blocked or Unverified:** Testing could not be completed or database records were insufficient to verify the feature. **Enhancement Request:** Current behavior works as designed, or requirement clarification is requested to improve workflow efficiency.

---

## III. Summary of Testing Results (Part 1: Authentication)

| Module | Test ID | Test Scenario | Recorded Status | Key Observation or Limitation |
| :--- | :--- | :--- | :--- | :--- |
| Auth & Access | **AUTH-01** | Log in with valid email and password | **PASSED** | Redirects to role-appropriate landing page as expected. |
| Auth & Access | **AUTH-02** | Log in with wrong password or non-existent email | **PASSED** | Clear error message displayed; user stays on login page. |
| Auth & Access | **AUTH-03** | Submit login form with empty fields | **PASSED** | Form validation prevents submission and highlights fields. |
| Auth & Access | **AUTH-04** | Register a new organization and owner account | **PASSED** | Organization and owner account created properly. |
| Auth & Access | **AUTH-05** | Log in as a Floater user (Role Routing) | **PASSED** | Redirected to `/floater`; restricted routes are blocked. |
| Auth & Access | **AUTH-06** | Log in as a Manager user (Role Routing) | **PASSED\*** | *Passed w/ Observations:* Too many steps to navigate from POS to Floater; two logout buttons exist (see ISS-02, ISS-03). |
| Auth & Access | **AUTH-07** | Log in as an Owner user (Role Routing) | **PASSED\*** | *Passed w/ Observations:* Verify whether Owner access to the POS module has been implemented as previously expected (see ISS-04). |
| Auth & Access | **AUTH-08** | Click Logout button from sidebar or header | **FAILED** | *Confirmed Failure:* Browser Back then Forward returns to a previously open account. Sales page load delays top-right username update; clicking POS immediately shows wrong/outdated account (see ISS-01). |

---

## III. Summary of Testing Results (Part 2: POS & Inventory)

The table below continues the checklist summary for **Point of Sale (POS)** and **Inventory Management**. All statuses reflect the exact entries recorded in the uploaded QA testing checklist.

| Module | Test ID | Test Scenario | Recorded Status | Key Observation or Limitation |
| :--- | :--- | :--- | :--- | :--- |
| Point of Sale | **POS-01** | Open POS page and view available menu items | **PASSED** | All active menu items for the active branch are displayed. |
| Point of Sale | **POS-02** | Filter items by category tabs | **PASSED** | Item grid updates to match selected category. |
| Point of Sale | **POS-03** | Type item name in the POS search bar | **PASSED** | Item grid filters in real time to matching items. |
| Point of Sale | **POS-04** | Click item to add to cart; increment/decrement quantity; remove item | **PASSED\*** | *Passed w/ Observations (Performance):* Adding items takes ~3 seconds; incrementing or decrementing quantity also takes ~3 seconds per action (see ISS-05). |
| Point of Sale | **POS-05** | Complete a sale using Cash payment (tendered $\ge$ total) | **PASSED** | Change calculated accurately; receipt shown; cart clears. |
| Point of Sale | **POS-06** | Complete a sale using GCash / Maya / Card payment | **PASSED** | Sale recorded with selected digital payment method; receipt shown. |
| Point of Sale | **POS-07** | Complete sale of recipe-linked item; check Inventory | **PASSED** | Ingredient stock quantities automatically deducted per recipe. |
| Point of Sale | **POS-08** | Attempt to sell item whose ingredients have 0 stock | **PASSED** | System warns cashier or prevents checkout per stock rules. |
| Point of Sale | **POS-09** | Click Print Receipt after checkout | **PASSED** | Printable receipt view opens with branch, items, totals, and timestamp. |
| Inventory Mgmt | **INV-01** | View inventory items for active branch | **PASSED** | Displays current stock, unit, par/reorder point, and status badge. |
| Inventory Mgmt | **INV-02** | Create a new inventory item / ingredient | **PASSED** | New item saves and appears in the inventory table. |
| Inventory Mgmt | **INV-03** | Edit existing item name, unit, or reorder level | **PASSED** | Changes are saved and reflected immediately in the table. |
| Inventory Mgmt | **INV-04** | Add stock (restock) or manually adjust quantity with reason | **PASSED** | Stock quantity updates; adjustment log is recorded. |
| Inventory Mgmt | **INV-05** | Record spoilage or wastage for an inventory item | **PASSED** | Stock decreases by wasted amount; wastage entry is logged. |
| Inventory Mgmt | **INV-06** | Create or edit menu item recipe with ingredient quantities | **PASSED** | Recipe saves and links menu item to raw ingredients. |
| Inventory Mgmt | **INV-07** | Reduce item stock below reorder threshold | **PASSED** | Item is flagged as "Low Stock" in inventory and alerts. |

---

## III. Summary of Testing Results (Part 3: Sales, Analytics, Reports & Settings)

| Module | Test ID | Test Scenario | Recorded Status | Key Observation or Limitation |
| :--- | :--- | :--- | :--- | :--- |
| Sales Trans. | **SAL-01** | Open Sales page and view transaction list | **PASSED\*** | *Enhancement Request:* Make Sales KPI cards clickable to filter records, matching Inventory KPIs (see ISS-08). |
| Sales Trans. | **SAL-02** | Filter sales by Today, Yesterday, Last 7 Days, or Custom | **PASSED** | Table and summary cards update to match selected date range. |
| Sales Trans. | **SAL-03** | Switch between branches or "All Branches" on Sales page | **FAILED** | *Confirmed Failure:* Taal Manager price edit for Chicharong Bulaklak changed price across all branches; unauthorized/inactive Lipa branch appears in filter (see ISS-06, ISS-07). |
| Sales Trans. | **SAL-04** | Click a transaction row to view breakdown | **UNRECORDED** | *Unverified:* Status and notes left blank in checklist; requires QA execution. |
| Sales Trans. | **SAL-05** | Void or refund a transaction with reason | **UNRECORDED** | *Unverified:* Status and notes left blank in checklist; requires QA execution. |
| Pred. Analytics | **ANA-01** | Open Analytics page and view demand forecast | **PASSED\*** | *Passed w/ Observations (Functional Issue):* Estimated Consumption does not work; Historical Consumption is marked unavailable (see ISS-11). |
| Pred. Analytics | **ANA-02** | View AI/system restock suggestions based on consumption | **FAILED** | *Failed (Cause Unconfirmed):* Recommended Stock Optimizer failed; uncertain if due to missing inventory data or filtering bug (see ISS-12). |
| Pred. Analytics | **ANA-03** | View Menu Engineering profitability/popularity matrix | **UNRECORDED** | *Blocked / Unverified:* Could not be verified because the database lacks enough relevant items (see ISS-12). |
| Reports & Audit | **REP-01** | Generate sales summary report for specific period | **UNRECORDED\*** | *Unrecorded Status w/ Defect Note:* Reports and branch info sometimes fail to load for Owner, while loading for Manager (see ISS-13). |
| Reports & Audit | **REP-02** | Generate inventory usage and wastage report | **UNRECORDED** | *Unverified:* Status and notes left blank in checklist; requires QA verification. |
| Reports & Audit | **REP-03** | Click Export button on Reports or Sales page | **FAILED** | *Confirmed Failure:* Report exporting produces a legacy `.doc` file instead of `.docx` (see ISS-14). |
| Org & Settings | **SET-01** | Create new user (Manager/Floater) and assign to branch | **FAILED** | *Confirmed Failure:* Lipa appears in assigned-branch options despite no access; "Manager on Duty" shown on Owner dashboard (see ISS-09, ISS-10). |
| Org & Settings | **SET-02** | Deactivate or suspend an existing user account | **PASSED** | Deactivated user is prevented from logging in. |
| Org & Settings | **SET-03** | Add a new branch or edit existing branch information | **PASSED** | Branch saves and appears in branch selectors across the app. |
| Org & Settings | **SET-04** | Update user profile name or change password | **UNRECORDED\*** | *Unrecorded Status w/ Defect Notes:* Upload Photo control in Profile Settings is not clickable; import feature accepts only JSON files (see ISS-15, ISS-16). |

> **Analytical Note on Recorded Statuses vs. Tester Observations**  
> No recorded status from the Excel checklist has been altered in the tables above. However, QA analysis confirms that `ANA-01` (marked `PASSED`) and `REP-01` / `SET-04` (status left blank) contain explicit tester observations of non-working functionality that must be tracked as active system issues alongside the five test cases explicitly marked `FAILED`.

---

## IV. Detailed Findings and Issues

> **Severity Classification Scale**  
> **CRITICAL:** Severe security, data-integrity, or system-wide failure requiring immediate investigation. **HIGH:** Major functional, access-control, or data-isolation problem that significantly affects system reliability. **MEDIUM:** A functional issue or usability problem that affects some workflows but has a workaround. **LOW:** A minor UI issue, inconvenience, or nonessential improvement. Provisional ratings requiring further data or requirement verification are explicitly labeled.

| Severity Tier | Issue Count | Assigned Issue IDs | Primary Risk & Operational Summary |
| :--- | :--- | :--- | :--- |
| **CRITICAL** | **2 Issues** | `ISS-01`, `ISS-06` | Outdated session restoration / delayed account synchronization, and branch price changes propagating globally across all branches. |
| **HIGH** | **5 Issues** *(2 Prov.)* | `ISS-07`, `ISS-09`, `ISS-11*`, `ISS-12*`, `ISS-13` | Unauthorized Lipa branch exposure in filters and User Management, Owner report loading failure, and analytics/optimizer defects. |
| **MEDIUM** | **5 Issues** *(1 Prov.)* | `ISS-02`, `ISS-04*`, `ISS-05`, `ISS-14`, `ISS-15` | 3-second POS cart latency, excessive POS-to-Floater navigation steps, Owner POS access check, `.doc` export format, and unclickable photo upload. |
| **LOW** | **4 Issues** *(1 Prov.)* | `ISS-03`, `ISS-08`, `ISS-10`, `ISS-16*` | Dual logout buttons, clickable Sales KPI enhancement, Owner dashboard "Manager on Duty" display, and JSON-only import restriction. |

### Category 1: Authentication and Session Security

#### ISS-01: Outdated Session Restoration via Browser Back/Forward Navigation and Delayed Account Synchronization
**Affected Module:** Authentication & Access Control / Sales / POS | **Related Excel ID:** `AUTH-08` | **Severity:** CRITICAL | **Priority:** P1 (Immediate)

- **Observed Behavior:** Two closely related session-isolation issues were observed: (1) When the browser Back button is clicked and then the Forward button is clicked, the website returns to an account that was previously open. (2) During Sales page loading, the username displayed in the top-right corner updates only after the page finishes loading; if the POS module is clicked immediately before loading completes, the wrong or outdated account name remains visible or active.
- **Expected Behavior:** Logging out or switching accounts must completely invalidate the previous session so that browser Back/Forward navigation cannot restore an earlier account without re-authentication. Furthermore, account identity state must be synchronized before protected navigation links become interactive.
- **Actual Behavior:** Browser history navigation restores an outdated authenticated session or cached view, and premature navigation during Sales page loading carries over the previous account identity into POS.
- **Potential Impact:** Severe security and audit integrity risk on shared POS terminals. A lower-privilege user could access a previous Owner or Manager session, or POS sales could be logged under the wrong cashier account. Further investigation is required to determine whether the root cause is browser Back-Forward Cache (BFCache) retention, incomplete token cleanup, or asynchronous state hydration.
- **Recommended Corrective Action:** Destroy all client and server session tokens synchronously upon logout, enforce `Cache-Control: no-store, no-cache, must-revalidate` headers on authenticated routes, and block module navigation transitions until the authenticated user profile has fully resolved.
- **Verification Steps for Retesting:** (1) Log in as Account A, log out, and press browser Back then Forward; verify the app stays on `/login`. (2) Log in as Account B, open the Sales page, and immediately click POS while Sales is still loading; verify that Account B is displayed consistently in both the header and POS.

---

### Category 2: Navigation and Usability

#### ISS-02: Excessive Navigation Steps Required for a Manager to Switch from POS to Floater
**Affected Module:** Authentication / Navigation (POS & Floater) | **Related Excel ID:** `AUTH-06` | **Severity:** MEDIUM | **Priority:** P3

- **Observed & Actual Behavior:** A Manager must click through too many buttons or intermediate steps to navigate from the POS module to the Floater module.
- **Expected Behavior:** Managers who oversee both register checkout and floor operations should have a direct, one-click navigation control to switch between POS and Floater views.
- **Potential Impact & Recommended Action:** Unnecessary navigation steps slow down shift supervision during peak hours. Simplify the navigation bar or role switcher so a Manager can transition directly between POS and Floater in a single step.
- **Verification Steps for Retesting:** Log in as a Manager, open the POS page, and verify that switching to the Floater module requires no more than one or two direct clicks.

#### ISS-03: Redundant Logout Buttons in Bottom-Right Area and Role-Switcher Dropdown
**Affected Module:** Navigation / User Interface | **Related Excel ID:** `AUTH-06` | **Severity:** LOW | **Priority:** P4

- **Observed & Actual Behavior:** Two separate logout buttons are present in the interface: one located in the bottom-right area and another inside the dropdown menu used to switch between Manager and Floater views.
- **Expected Behavior:** The interface should provide a single, consistent logout control in a standardized location so users do not accidentally log out while switching operational views.
- **Potential Impact & Recommended Action:** Redundant logout controls clutter the interface and increase the risk of accidental sign-outs when toggling between Manager and Floater modes. Review whether both buttons are necessary, remove the duplicate control, and standardize the layout.
- **Verification Steps for Retesting:** Log in as a Manager, inspect the main layout and the Manager/Floater dropdown menu, and confirm that only one consistent Logout button is displayed.

#### ISS-04: Unverified Implementation of POS Module Access for the Owner Role
**Affected Module:** Authentication & Role Routing / POS | **Related Excel ID:** `AUTH-07` | **Severity:** MEDIUM (Provisional — Pending Requirement Verification) | **Priority:** P3

- **Observed & Actual Behavior:** The system was previously expected to include POS access for the Owner role. While test cases `POS-01` through `POS-09` list "Manager / Owner" as authorized roles, the expected result column of `AUTH-07` omits `/pos` from the Owner's route list.
- **Expected Behavior:** If approved in the system requirements, the Owner role should have clear navigation and route access to the POS module for any active branch in their organization.
- **Potential Impact & Recommended Action:** Hands-on business owners may be unable to operate the register directly without logging into a separate Manager account. Verify the intended role matrix with project stakeholders and ensure routing and sidebar links for `/pos` are consistently enabled for the Owner role if required.
- **Verification Steps for Retesting:** Log in as an Owner, verify whether the POS navigation option is present per confirmed specifications, and test completing a sample POS transaction under a selected branch.

---

### Category 3: POS Performance and Transactions

#### ISS-05: Three-Second Latency on POS Cart Item Additions and Quantity Adjustments
**Affected Module:** Point of Sale (POS) | **Related Excel ID:** `POS-04` | **Severity:** MEDIUM | **Priority:** P2

- **Observed & Actual Behavior:** Clicking a menu item in POS takes approximately 3 seconds to add the item to the order cart, and each click on the increment (`+`) or decrement (`-`) quantity buttons also takes approximately 3 seconds to respond.
- **Expected Behavior:** Adding items and adjusting cart quantities should occur instantaneously in the user interface (under $200\text{ ms}$) with immediate subtotal and tax recalculation.
- **Potential Impact & Recommended Action:** A 3-second delay per tap severely slows cashier throughput during busy periods and can lead to repeated clicks and incorrect item quantities. Further investigation is required to determine whether cart actions trigger synchronous server/database round-trips on every tap. Refactor cart state updates to execute locally/optimistically in the client and validate stock upon checkout.
- **Verification Steps for Retesting:** Open POS, add 5 different items to the cart, and rapidly click the increment and decrement controls 10 times; verify that quantities and totals update immediately without multi-second lag.

---

### Category 4: Sales Filtering and Branch Data Isolation

| Isolation Area | Expected Branch Scoping Behavior | Observed Defect in Testing |
| :--- | :--- | :--- |
| **Product Pricing** | Price edits by a Branch Manager apply only to their assigned branch (or require Owner authority). | Taal Manager changed the price of *Chicharong Bulaklak*, and the price changed across **all branches** (`ISS-06`). |
| **Sales Branch Filter** | Displays only active branches belonging to the organization and authorized for the current user. | **Lipa** appears in the Sales filter even though the organization has no access or Lipa is inactive (`ISS-07`). |
| **User Management** | Displays only active branches within the Owner's organization when assigning staff. | **Lipa** appears in the assigned-branch options in User Management (`ISS-09`). |

#### ISS-06: Branch Manager Price Change Propagating Globally Across All Branches
**Affected Module:** Sales / Product Pricing Management | **Related Excel ID:** `SAL-03` | **Severity:** CRITICAL | **Priority:** P1 (Immediate)

- **Observed & Actual Behavior:** A Manager assigned to the **Taal** branch changed the price of *Chicharong Bulaklak*, and that price change took effect across all branches instead of remaining isolated to the Taal branch.
- **Expected Behavior:** Product prices managed at the branch level must be isolated to the specific branch, or global catalog pricing must be restricted strictly to the Owner role with branch-specific price overrides.
- **Potential Impact & Recommended Action:** Unintended global price changes corrupt POS billing, profit margins, and financial reporting across every branch in the organization. Investigate whether product prices are stored in a single global column without a branch-pricing mapping table or missing a `branch_id` filter on update queries, and enforce strict branch-level scoping.
- **Verification Steps for Retesting:** Log in as the Taal Branch Manager, modify the price of *Chicharong Bulaklak*, and verify that the updated price applies only to Taal while other branches retain their original price.

#### ISS-07: Unauthorized or Inactive "Lipa" Branch Appearing in Sales Branch Filter
**Affected Module:** Sales Transactions — Branch Filtering | **Related Excel ID:** `SAL-03` | **Severity:** HIGH | **Priority:** P1

- **Observed & Actual Behavior:** The Sales branch filter includes **Lipa** even though the tester reports that the organization has no access to that branch (or the branch is inactive).
- **Expected Behavior:** The Sales branch filter must only display active branches that belong to the user's organization and that the current user is authorized to access, and displayed sales records must correspond strictly to the selected branch.
- **Potential Impact & Recommended Action:** Exposes unauthorized or inactive branch records and risks skewing "All Branches" sales summaries. Investigate the branch-lookup query on the Sales page and filter by authenticated `organization_id`, user branch permissions, and active branch status.
- **Verification Steps for Retesting:** Log in as Owner and Manager, open the Sales branch filter, confirm Lipa and any inactive branches do not appear, and verify that selecting each authorized branch displays only its own sales records.

#### ISS-08: Sales KPI Cards Are Not Clickable to Filter Corresponding Sales Records
**Affected Module:** Sales Transactions | **Related Excel ID:** `SAL-01` | **Severity:** LOW (Enhancement Request) | **Priority:** P4

- **Observed, Expected & Recommended Action:** Unlike the KPI cards in the Inventory module, the KPI summary cards on the Sales page are not clickable. Make the Sales KPI cards interactive so clicking a KPI filters or displays the corresponding sales records. *Retesting:* Click each Sales KPI card and confirm the transaction list updates accordingly.

> **Unverified Sales Test Cases (SAL-04 & SAL-05)**  
> `SAL-04` (Transaction Detail view) and `SAL-05` (Void/Refund Transaction and stock restoration) were left blank in the checklist and must be executed during the retesting cycle.

---

### Category 5: Role-Based Access and User Management

#### ISS-09: Unauthorized "Lipa" Branch Appearing in User Management Branch Assignment Options
**Affected Module:** Organization, Users & Settings — User Management | **Related Excel ID:** `SET-01` | **Severity:** HIGH | **Priority:** P1

- **Observed & Actual Behavior:** When creating or managing user accounts in User Management, **Lipa** appears under the assigned-branch options even though the organization reportedly has no access to that branch.
- **Expected Behavior:** The assigned-branch dropdown in User Management must only list active branches owned by the current organization and authorized for user assignment.
- **Potential Impact & Recommended Action:** Assigning staff to an unauthorized or inactive branch could cause cross-organization permission leaks or lock new users out of valid branch operations. Investigate how the User Management branch list is retrieved and filter it strictly by the organization's active branches.
- **Verification Steps for Retesting:** Log in as Owner, open User Management, inspect the branch assignment dropdown, verify Lipa and inactive branches are excluded, and create a test Manager assigned to an active branch.

#### ISS-10: "Manager on Duty" Section Displayed on Dashboard When Logged In as Owner
**Affected Module:** Organization, Users & Settings / Role Views | **Related Excel ID:** `SET-01` | **Severity:** LOW | **Priority:** P4

- **Observed & Actual Behavior:** The dashboard displays a "Manager on Duty" section even when the logged-in account has the Owner role.
- **Expected Behavior:** Dashboard components should reflect the logged-in user's role appropriately, showing organization-wide oversight for an Owner rather than a single-branch shift banner intended for branch-level views.
- **Potential Impact & Recommended Action:** Causes confusion regarding active role context. Review with the project team whether "Manager on Duty" should be hidden for the Owner role or redesigned to list the active manager per branch.
- **Verification Steps for Retesting:** Log in as an Owner and verify that the dashboard displays role-appropriate information matching the agreed design specification.

---

### Category 6: Inventory and Predictive Analytics

#### ISS-11: Estimated Consumption Feature Not Working and Historical Consumption Unavailable
**Affected Module:** Predictive Analytics & Decision Support | **Related Excel ID:** `ANA-01` | **Severity:** HIGH (Provisional — Pending Data & Code Investigation) | **Priority:** P2

- **Observed & Actual Behavior:** Although `ANA-01` was marked `PASSED` in the checklist, the tester notes state that the *Estimated Consumption* feature does not appear to work and *Historical Consumption* is marked unavailable.
- **Expected Behavior:** Both Historical Consumption and Estimated Consumption should calculate and display accurate ingredient usage trends and demand forecasts based on recorded POS sales and inventory adjustments.
- **Potential Impact & Recommended Action:** Managers and Owners cannot rely on demand forecasting for purchasing decisions. Investigate the calculation logic, data dependencies, and branch/date filtering for Estimated Consumption, and determine whether Historical Consumption is blocked by missing database records or incomplete implementation.
- **Verification Steps for Retesting:** Populate the test database with historical POS sales and recipe deductions, open the Analytics page, and verify that both Historical and Estimated Consumption render accurate outputs.

#### ISS-12: Recommended Stock Optimizer Failure and Menu Engineering Blocked by Insufficient Database Records
**Affected Module:** Predictive Analytics & Decision Support | **Related Excel ID:** `ANA-02`, `ANA-03` | **Severity:** HIGH (Provisional for ANA-02) / Blocked (ANA-03) | **Priority:** P2

- **Observed & Actual Behavior:** `ANA-02` (Recommended Stock Optimizer) is marked `FAILED`, with uncertainty about whether the issue is caused by missing inventory data or incorrect filtering. Additionally, `ANA-03` (Menu Engineering matrix) could not be verified because the database does not contain enough relevant items or records.
- **Expected Behavior:** Recommended Stock Optimizer should accurately filter inventory records and suggest reorder quantities; Menu Engineering should plot items by popularity and profitability once sufficient test data is present.
- **Potential Impact & Recommended Action:** Distinguishing confirmed code defects from missing test data requires seeding the database with representative inventory items, stock levels, recipes, and sales histories, then inspecting the Stock Optimizer filter queries.
- **Verification Steps for Retesting:** Seed the test database with multi-branch inventory and sales records, then re-execute `ANA-02` and `ANA-03` to confirm accurate restock suggestions and profitability/popularity classifications.

---

### Category 7: Reports and Export Functionality

#### ISS-13: Reports and Branch Information Fail to Load for Owner Role While Loading for Manager Role
**Affected Module:** Reports & Audit | **Related Excel ID:** `REP-01` (also affects `REP-02`) | **Severity:** HIGH | **Priority:** P1

- **Observed & Actual Behavior:** Reports and branch information sometimes fail to load when logged in as the **Owner**, whereas the exact same reports load successfully when logged in as a **Manager**.
- **Expected Behavior:** The Owner role must be able to load all sales, inventory usage, and branch reports reliably across both individual branches and all branches combined.
- **Potential Impact:** Prevents the business owner from reviewing financial performance and inventory audit trails. Further investigation is required to determine whether the report endpoint expects a single assigned `branch_id` (which is present on a Manager account but null or multi-branch on an Owner account) or has a role-permission or error-handling defect.
- **Recommended Corrective Action:** Audit the report API endpoints, role-permission middleware, and frontend branch-selector initialization for the Owner role to ensure both organization-wide and single-branch queries succeed.
- **Verification Steps for Retesting:** Log in as an Owner, navigate to the Reports page, test loading Sales Summary (`REP-01`) and Inventory Usage/Wastage (`REP-02`) reports across each branch and "All Branches," and verify that all data loads without error.

#### ISS-14: Report Exporting Produces a Legacy .doc File Instead of .docx (or CSV/PDF)
**Affected Module:** Reports & Audit — Exporting | **Related Excel ID:** `REP-03` | **Severity:** MEDIUM | **Priority:** P3

- **Observed & Actual Behavior:** Clicking the Export button on the Reports or Sales page currently produces a `.doc` file instead of a `.docx` file (while the checklist also references CSV/PDF export).
- **Expected Behavior:** Exported report files must use the intended modern file format (`.docx` if Word output is required, alongside any required CSV or PDF options) and contain accurate rows and totals matching the on-screen report.
- **Potential Impact & Recommended Action:** Legacy `.doc` files can trigger compatibility or security warnings in modern office suites. Verify the required export formats with the project specifications and update the export generator to output valid `.docx` (and/or CSV/PDF) files.
- **Verification Steps for Retesting:** Click Export on both the Reports and Sales pages, verify that the downloaded file uses the required `.docx` (or CSV/PDF) extension, and confirm that the file opens cleanly with accurate totals.

---

### Category 8: Profile Settings and Data Importing

#### ISS-15: "Upload Photo" Control in Profile Settings Is Not Clickable
**Affected Module:** Organization, Users & Settings — Profile Settings | **Related Excel ID:** `SET-04` | **Severity:** MEDIUM | **Priority:** P3

- **Observed & Actual Behavior:** The Upload Photo control in Profile Settings is not clickable and does not open a file-selection dialog when clicked.
- **Expected Behavior:** Clicking the Upload Photo control should open the operating system's file picker, allow the user to select a valid image file, and update the user's profile photo upon saving.
- **Potential Impact & Recommended Action:** Users cannot upload or change their profile avatar. Inspect the Profile Settings component to ensure the button is wired to an active `<input type="file">` element and is not blocked by a disabled attribute or overlapping CSS layer.
- **Verification Steps for Retesting:** Open Profile Settings, click Upload Photo, select an image file, save changes, and verify the new photo persists (also complete verification of profile name and password updates under `SET-04`).

#### ISS-16: Data Import Feature Accepts Only JSON Files
**Affected Module:** Organization, Users & Settings — Data Import | **Related Excel ID:** `SET-04` | **Severity:** LOW (Provisional — Pending Requirement Verification) | **Priority:** P4

- **Observed & Actual Behavior:** The import feature in Settings accepts only `.json` files.
- **Expected Behavior:** The import control should support the file formats defined by the system requirements and clearly communicate accepted file types to the user.
- **Potential Impact & Recommended Action:** If the import tool is intended for end-user data onboarding rather than developer database snapshots, a JSON-only restriction may be difficult for non-technical staff to use. Investigate whether JSON-only support is intentional or if additional formats are required by the project scope.
- **Verification Steps for Retesting:** Confirm the intended import file specifications with the development team and test importing a valid file to verify proper parsing and error handling.

---

## V. Performance and Usability Observations

In addition to functional failures, testing identified several performance bottlenecks and usability issues that affect daily operational efficiency across the POS, Sales, and Settings interfaces. Because backend profiling has not yet been performed, the underlying technical causes of these delays remain unconfirmed and require developer investigation.

> **[Diagram — Figure 2: Comparison of Target vs Observed POS Cart Interaction Time (POS-04)]**  
> *Horizontal comparison chart titled: "Cumulative Cashier Wait Time for a 5-Item Order with 5 Quantity Adjustments (POS-04)"*
> - **Expected UI Response ($<0.2\text{ s}$ / tap):** $\sim 2.0\text{ seconds}$ total UI response across 10 actions
> - **Observed POS-04 ($\sim 3.0\text{ s}$ / tap):** $5\text{ Item Adds} \times 3\text{ s} = 15\text{ s}$ | $5\text{ Qty Taps} \times 3\text{ s} = 15\text{ s}$ ($30\text{ s}$ Total Wait)
> - *Note: Root cause is unconfirmed; developers should profile whether each tap triggers synchronous database queries or state re-renders.*
>
> *Figure 2: Cumulative cashier wait-time impact caused by the 3-second delay per cart action observed in POS-04.*

- **POS Item Addition and Cart Quantity Delay (`POS-04` / `ISS-05`):** Adding a menu item to the POS cart takes approximately three seconds, and each tap on the cart increment (`+`) or decrement (`-`) button also takes approximately three seconds. For a standard order involving five items and five quantity adjustments, the cashier experiences roughly 30 seconds of cumulative interface lag. This delay can cause cashiers to click repeatedly—resulting in unintended item quantities—and creates checkout queues during peak service hours.
- **Delayed Header Username Update During Sales Loading (`AUTH-08` / `ISS-01`):** When loading the Sales page, the account name in the top-right header updates only after the page finishes loading. If a user clicks into the POS module before loading completes, an outdated account name may remain visible or active, creating both usability confusion and a critical account-isolation hazard.
- **Unnecessary Navigation Steps and Duplicate Logout Controls (`AUTH-06` / `ISS-02`, `ISS-03`):** Managers switching from POS to Floater must perform too many clicks, increasing friction during active floor supervision. Additionally, the presence of two logout buttons (one in the bottom-right corner and one inside the Manager/Floater switcher dropdown) creates visual clutter and increases the likelihood of an accidental sign-out.
- **Inconsistent Control Interactivity Across Modules (`SAL-01` / `ISS-08`, `SET-04` / `ISS-15`):** Users accustomed to clicking KPI cards in the Inventory module to filter items expect the same behavior on the Sales page, where KPI cards are currently static. Similarly, the unclickable Upload Photo button in Profile Settings prevents users from completing profile customization.

> **Distinguishing Confirmed Defects from Data-Starved Features**  
> While the performance delays and branch-isolation bugs above were directly observed during active workflows, features in **Predictive Analytics** (`ANA-01`, `ANA-02`, `ANA-03`) could not be fully evaluated because the test database lacked sufficient inventory items and historical consumption records. Populating a realistic test dataset is essential before concluding whether those analytics features have underlying calculation defects.

---

## VI. Recommendations and Action Plan

The table below outlines a prioritized action plan organized into six corrective categories, identifying the affected module, recommended engineering action, priority level, and acceptance criteria for verification.

| Action Category | Module & ID | Recommended Engineering Action | Prio. | Acceptance Criteria |
| :--- | :--- | :--- | :---: | :--- |
| **1. Immediate Security & Data Integrity** | Auth / Session (`ISS-01`) | Invalidate session tokens on logout, set `no-store` cache headers, and synchronize account identity before allowing module navigation. | **P1** | Browser Back/Forward after logout never restores a prior account; clicking POS during Sales load shows the current user only. |
| **1. Immediate Security & Data Integrity** | Sales / Pricing (`ISS-06`) | Scope product price updates to the specific `branch_id` (or restrict global pricing to Owner with branch-level overrides). | **P1** | A price change by a Taal Manager updates only the Taal branch and does not alter prices in other branches. |
| **2. Major Functional Defects** | Reports & Audit (`ISS-13`) | Fix role-specific report loading and branch-selector queries so the Owner role can load all reports without error. | **P1** | Owner can load Sales and Inventory reports across individual branches and All Branches matching Manager figures. |
| **2. Major Functional Defects** | Profile Settings (`ISS-15`) | Repair the Upload Photo button event binding so clicking it opens the file-selection dialog and saves the uploaded avatar. | **P3** | Upload Photo control is clickable, accepts valid images, and updates the user profile photo. |
| **2. Major Functional Defects** | Reports Export (`ISS-14`) | Verify required export formats and update the report exporter to generate valid `.docx` (and/or CSV/PDF) files instead of `.doc`. | **P3** | Exported report files use the required modern extension and match on-screen report data. |
| **3. Branch Filtering & Access Control** | Sales Filter (`ISS-07`) | Filter the Sales branch dropdown by authenticated organization, user branch permissions, and active branch status. | **P1** | Lipa and any inactive or unauthorized branches are excluded; displayed sales match the selected branch. |
| **3. Branch Filtering & Access Control** | User Mgmt (`ISS-09`) | Restrict the assigned-branch options in User Management to active branches belonging to the Owner's organization. | **P1** | Lipa no longer appears in User Management branch options; staff can only be assigned to valid active branches. |
| **3. Branch Filtering & Access Control** | Role Routing (`ISS-04`, `ISS-10`) | Confirm Owner POS access requirement and implement if required; review "Manager on Duty" visibility on the Owner dashboard. | **P3** | Owner access to POS and dashboard widgets align with confirmed role specifications. |
| **4. Performance & Usability** | POS Cart (`ISS-05`) | Optimize POS cart state management so item additions and quantity increments/decrements update locally without 3-second delays. | **P2** | Adding items and clicking `+` or `-` updates the cart in under $200\text{ ms}$ with accurate subtotals. |
| **4. Performance & Usability** | Navigation (`ISS-02`, `ISS-03`) | Streamline POS-to-Floater navigation for Managers and consolidate the two logout buttons into one consistent location. | **P3** | Managers switch between POS and Floater in 1–2 clicks; only one Logout button appears in the UI. |
| **5. Additional Data & Testing Needed** | Analytics & Unverified Cases (`ISS-11`, `ISS-12`) | Seed the test database with inventory items and historical sales; investigate Estimated/Historical Consumption, Stock Optimizer, and unrecorded cases (`SAL-04/05`, `REP-02`). | **P2** | All analytics features and previously unrecorded test cases are fully verified with seeded test data. |
| **6. Optional Enhancements** | Sales KPIs & Import (`ISS-08`, `ISS-16`) | Make Sales KPI cards clickable to filter records; verify whether JSON-only import is intentional or if additional formats are needed. | **P4** | Clicking Sales KPI cards filters transactions; import formats match confirmed system requirements. |

---

## VII. Retesting Checklist

The QA tester can use the practical checklist below to verify fixes after the development team completes remediation. Items are ordered by priority, beginning with account isolation, session security, branch-specific pricing, branch filtering, and role permissions.

| Retest ID | Focus Area & Issue ID | Specific Verification Steps | Expected Result |
| :--- | :--- | :--- | :--- |
| **RT-01** | Session Security (`ISS-01` / `AUTH-08`) | Log in to an account, click Logout, then press the browser Back button followed by the Forward button. | The previous account remains inaccessible and the user is required to authenticate on the login page. |
| **RT-02** | Account Isolation (`ISS-01` / `AUTH-08`) | Log out of Account A, log in as Account B, open the Sales page, and immediately click POS before Sales finishes loading. | The top-right username and POS session consistently display Account B without reverting to Account A. |
| **RT-03** | Branch-Specific Pricing (`ISS-06` / `SAL-03`) | Log in as a Taal Branch Manager, change the price of *Chicharong Bulaklak*, and inspect the price in another active branch. | The price change applies only to the Taal branch; prices in all other branches remain unchanged. |
| **RT-04** | Sales Branch Filtering (`ISS-07` / `SAL-03`) | Open the Sales page as Owner and Manager, inspect the branch filter options, and filter transactions by each branch. | Lipa and any inactive/unauthorized branches are hidden; displayed sales records match the selected branch. |
| **RT-05** | User Branch Assignment (`ISS-09` / `SET-01`) | Log in as Owner, open User Management, start creating a new user, and check the assigned-branch dropdown list. | Only active branches authorized for the organization are listed; Lipa does not appear. |
| **RT-06** | Owner Report Loading (`ISS-13` / `REP-01/02`) | Log in as Owner, open the Reports module, and generate Sales Summary and Inventory Usage/Wastage reports across branches. | All reports and branch details load reliably for the Owner role and match Manager report data. |
| **RT-07** | Role Permissions & View (`ISS-04`, `ISS-10`) | Log in as Owner, verify POS access per confirmed requirements, and check whether "Manager on Duty" appears on the dashboard. | Owner POS access and dashboard sections conform to the finalized role-permission specification. |
| **RT-08** | POS Cart Latency (`ISS-05` / `POS-04`) | Open POS, add 5 items to the cart, and rapidly click the cart increment (`+`) and decrement (`-`) buttons. | Items add and quantities update almost immediately (under $200\text{ ms}$) without a 3-second delay per action. |
| **RT-09** | Navigation & Logout UI (`ISS-02`, `ISS-03`) | Log in as Manager, navigate from POS to Floater, and inspect the bottom-right area and role-switcher dropdown. | Switching from POS to Floater takes 1–2 clicks, and only one consistent Logout button is present. |
| **RT-10** | Analytics & Optimizer (`ISS-11`, `ISS-12`) | With seeded inventory and historical sales data, test Estimated/Historical Consumption, Stock Optimizer, and Menu Engineering. | `ANA-01`, `ANA-02`, and `ANA-03` calculate and display accurate outputs without errors. |
| **RT-11** | Report Export Format (`ISS-14` / `REP-03`) | Click Export on the Reports and Sales pages and inspect the downloaded file extension and contents. | The exported file uses the required `.docx` (or CSV/PDF) format rather than `.doc` and opens cleanly. |
| **RT-12** | Settings & Unrecorded Sales (`ISS-08/15/16`, `SAL-04/05`) | Test Upload Photo in Profile Settings, data import, clickable Sales KPIs, Transaction Detail view, and Void/Refund workflow. | Upload Photo opens the file picker; `SAL-04` and `SAL-05` pass verification; KPIs and imports match requirements. |

---

## VIII. Conclusion

The current QA evaluation of the **AAskMi** system shows that core day-to-day workflows—including user login validation, POS product browsing and filtering, cash and digital payment processing, automatic recipe-based stock deduction, and branch inventory adjustments—are functioning as expected across 23 cleanly passing test cases. However, the presence of critical session-handling flaws, cross-branch data isolation failures, role-based report loading errors, and noticeable POS interaction delays indicates that the system requires targeted corrective development before it can be considered ready for final capstone evaluation or production deployment.

In particular, the restoration of a previously authenticated account via browser Back/Forward navigation and delayed header username synchronization during Sales loading (`ISS-01`), the global propagation of a Taal Branch Manager's price change across all branches (`ISS-06`), the appearance of the unauthorized or inactive Lipa branch in Sales filters and User Management (`ISS-07`, `ISS-09`), and the failure of reports to load under the Owner role (`ISS-13`) directly affect security, financial accuracy, and multi-branch governance. Furthermore, because several Predictive Analytics and Sales/Report test cases were blocked by insufficient test database records or left unrecorded in the initial checklist, a second testing cycle using a properly seeded multi-branch database is essential to confirm full functional readiness.

> **Required Next Steps Before Final Evaluation or Deployment**  
> 1. **Remediate P1 Security & Isolation Defects:** Fix session invalidation and identity synchronization (`ISS-01`), isolate branch-level product pricing (`ISS-06`), remove unauthorized/inactive branches from filters and User Management (`ISS-07`, `ISS-09`), and resolve Owner report loading failures (`ISS-13`).  
> 2. **Optimize POS Responsiveness & UI Controls:** Eliminate the 3-second cart latency in `POS-04` (`ISS-05`), streamline POS-to-Floater navigation (`ISS-02`), remove the redundant logout button (`ISS-03`), fix the Upload Photo control (`ISS-15`), and update report exporting to `.docx` / required formats (`ISS-14`).  
> 3. **Seed Test Database & Complete Retesting:** Populate representative multi-branch inventory and sales history records to verify Predictive Analytics (`ANA-01` to `ANA-03`) and execute all remaining unverified test cases (`SAL-04`, `SAL-05`, `REP-02`) using the Section VII Retesting Checklist.

| Readiness Gate Criterion | Current QA Status | Exit Requirement for Final Sign-Off |
| :--- | :--- | :--- |
| **Session & Account Isolation** | **Action Required** | Zero session restoration via browser Back/Forward and immediate header identity sync (`RT-01`, `RT-02`). |
| **Multi-Branch Data Isolation** | **Action Required** | Branch-isolated pricing and strict organization/active-branch filtering in Sales and User Management (`RT-03` to `RT-05`). |
| **Role Permissions & Reporting** | **Action Required** | Owner reports load reliably across all branches; Owner POS access and dashboard widgets verified (`RT-06`, `RT-07`, `RT-11`). |
| **POS Performance & Usability** | **Optimization Needed** | Cart additions and quantity adjustments respond in $<200\text{ ms}$; navigation and Profile Photo upload work cleanly (`RT-08`, `RT-09`, `RT-12`). |
| **Analytics & Full Test Coverage** | **Pending Seeded Data** | All 6 unrecorded/blocked cases (`ANA-01/02/03`, `SAL-04/05`, `REP-02`) verified against seeded test data (`RT-10`, `RT-12`). |