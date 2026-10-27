# QA Testing and System Issues Report for AAskMi

## I. Executive Summary
This document provides a comprehensive Quality Assurance (QA) and System Issues Report for the AAskMi software system. Based on the `AAskMi_QA_Testing_Checklist.xlsx` and supplementary QA observations, testing indicates that while the system's core modules are operational, several critical and high-severity defects require immediate remediation. 

The most pressing concerns involve session security (improper state restoration via browser navigation), data isolation failures (branch-specific pricing applying globally), and role-based access control discrepancies (Owner accounts failing to load reports, and unauthorized branches appearing in user filters). Additionally, predictive analytics features remain unverified or are failing, pending database population and further investigation. Addressing these foundational data-integrity and security issues is essential before proceeding to user acceptance testing or deployment.

## II. Scope of Testing
The testing scope encompasses the following modules within the AAskMi system:
*   Authentication and Access Control
*   Point of Sale (POS)
*   Inventory Management
*   Sales Transactions
*   Predictive Analytics and Decision Support
*   Reports and Audit
*   Organization, Users, and Settings

**Exclusions:**
As per testing guidelines, Section 3 (Floater Operations Portal) and Section 6 (Executive Dashboard) from the Excel checklist have been completely excluded from this review. However, general usability concerns regarding navigation between the POS and Floater modules have been retained.

**Limitations:**
Testing of the "Predictive Analytics and Decision Support" and certain "Inventory Management" features was severely limited due to a lack of sufficient historical data and relevant items in the test database. Consequently, features like Estimated Consumption, Historical Consumption, and the Recommended Stock Optimizer require additional testing with populated data before their functional correctness can be fully confirmed.

## III. Summary of Testing Results

The following table summarizes the status of key test scenarios derived from the `AAskMi_QA_Testing_Checklist.xlsx` and supplementary QA notes. 

| Module | Test ID / Ref | Test Scenario | Recorded Status | Key Observation / Limitation |
| :--- | :--- | :--- | :--- | :--- |
| Authentication | AUTH-01 | Prevent unauthorized access via browser navigation | **FAILED** | Back/Forward buttons restore previous accounts without re-authentication. |
| Authentication | AUTH-02 | Synchronize account identity during page loads | **FAILED** | POS page loads with outdated top-right username initially. |
| POS | POS-01 | Add items to cart and modify quantities | **PASSED W/ OBS.** | Significant performance delay (~3 seconds) per action. |
| Sales | SLS-01 | Branch-specific pricing isolation | **FAILED** | Price changes in one branch (Taal) apply to all branches globally. |
| Sales | SLS-02 | Branch filtering reflects authorized access | **FAILED** | Unauthorized/inactive branches (Lipa) are visible in filters. |
| Analytics | ANL-01 | Calculate Estimated Consumption | **FAILED** | Output calculation is incorrect or missing; requires data investigation. |
| Analytics | ANL-02 | Display Historical Consumption | **UNVERIFIED** | Marked as unavailable; blocked by missing data or incomplete implementation. |
| Analytics | ANL-03 | Recommended Stock Optimizer execution | **FAILED** | Uncertainty if failure is due to logic defect or missing DB records. |
| Reports | REP-01 | Load reports for Owner role | **FAILED** | Reports fail to load for Owner, but succeed for Manager. |
| Settings | SET-01 | Upload User Profile Photo | **FAILED** | Upload photo control is unresponsive/not clickable. |

*(Note: Test IDs are assigned categorically based on the provided testing checklist observations.)*

## IV. Detailed Findings and Issues

### Authentication and Session Security

**1. Issue ID:** AUTH-ISS-01
**2. Issue title:** Unauthorized Session Restoration via Browser Navigation
**3. Description of observed behavior:** When the browser's Back button is used and then the Forward button is clicked, the website returns to an account that was previously open.
**4. Expected behavior:** A previously authenticated account should not become accessible without proper, fresh authentication once a user navigates away or logs out.
**5. Actual behavior:** The application restores an outdated authenticated session or caches the previous state, bypassing security checks.
**6. Affected module:** Authentication and Access Control
**7. Related Excel test ID:** AUTH-01
**8. Potential impact:** Severe security vulnerability allowing unauthorized users to access a previous user's account on shared devices.
**9. Recommended corrective action:** Implement strict session invalidation upon logout. Ensure client-side route guards verify the validity of the authentication token on every page load. Configure cache-control headers (`no-store`, `no-cache`) for authenticated routes.
**10. Severity and priority:** **Critical** / High Priority
**11. Verification steps for retesting:** Log in as User A. Log out. Click the browser Back button, then Forward. Verify the system redirects to the login screen and blocks access to the dashboard.

**1. Issue ID:** AUTH-ISS-02
**2. Issue title:** Asynchronous Account Identity Synchronization on Sales Page
**3. Description of observed behavior:** During Sales page loading, the username displayed in the top-right corner changes only after the page completes loading. Clicking the POS module immediately during this window can carry over the wrong or outdated account name.
**4. Expected behavior:** The UI should immediately and consistently reflect the currently authenticated user across all components, or block interaction until the current user context is fully resolved.
**5. Actual behavior:** The interface momentarily displays an outdated account identity, leading to potential data entry under the wrong user context if navigation occurs too quickly.
**6. Affected module:** Authentication / Navigation
**7. Related Excel test ID:** AUTH-02
**8. Potential impact:** Data integrity issues and audit trail corruption if transactions are logged under the wrong user session.
**9. Recommended corrective action:** Investigate the component lifecycle and state management. Ensure the user context is globally resolved and UI elements are blocked with a loading state until the correct session data is fetched.
**10. Severity and priority:** **High** / High Priority
**11. Verification steps for retesting:** Switch accounts. Quickly navigate to the Sales page and immediately click POS before the page fully loads. Verify the correct username is maintained and transactions log correctly.

### Sales Filtering and Branch Data Isolation

**1. Issue ID:** SLS-ISS-01
**2. Issue title:** Global Propagation of Branch-Specific Price Changes
**3. Description of observed behavior:** A Manager assigned to the Taal branch changed the price of "Chicharong Bulaklak", but the price change was applied across all branches globally.
**4. Expected behavior:** Product pricing adjustments made by a branch manager should be isolated and apply strictly to their authorized branch.
**5. Actual behavior:** Product prices are being shared globally, overwriting prices for other branches.
**6. Affected module:** Sales Transactions / Inventory Management
**7. Related Excel test ID:** SLS-01
**8. Potential impact:** Major data-integrity failure leading to incorrect pricing, financial losses, and disrupted operations across all unassociated branches.
**9. Recommended corrective action:** Correct the database schema and API endpoints to ensure product pricing is related to a `branch_id`. Ensure the update query restricts modifications to the specific branch tied to the manager's session.
**10. Severity and priority:** **High** / High Priority
**11. Verification steps for retesting:** Log in as Taal Manager. Update a product price. Log in as a different branch Manager. Verify the product price remains unchanged in the second branch.

**1. Issue ID:** SLS-ISS-02
**2. Issue title:** Visibility of Unauthorized and Inactive Branches in Filters
**3. Description of observed behavior:** The Sales filters (and User Management assignment options) include "Lipa", even though the current organization/user has no access to this branch, and the branch may be inactive.
**4. Expected behavior:** Branch filters should dynamically populate only with active branches that the current user is explicitly authorized to access.
**5. Actual behavior:** The system populates dropdowns with inactive or unauthorized branch records.
**6. Affected module:** Sales Transactions / Organization, Users, and Settings
**7. Related Excel test ID:** SLS-02
**8. Potential impact:** Confusion for users, potential data leakage regarding organization structures, and errors if an unauthorized branch is selected for data operations.
**9. Recommended corrective action:** Update the backend API providing the branch list to strictly filter by `is_active = true` (unless explicitly requested otherwise) and join against the user's `authorized_branches` access control list.
**10. Severity and priority:** **Medium** / Medium Priority
**11. Verification steps for retesting:** Log in as a user without access to Lipa. Open the Sales filter and User Management branch dropdowns. Verify Lipa is not present.

### Role-Based Access and User Management

**1. Issue ID:** RBA-ISS-01
**2. Issue title:** Reports Fail to Load for Owner Role
**3. Description of observed behavior:** Reports and branch information fail to load when logged in as the Owner, but load successfully for users with the Manager role.
**4. Expected behavior:** The Owner role should have supreme or identical read permissions to the Manager role, successfully loading all reports and branch data.
**5. Actual behavior:** The system fails to retrieve data or triggers an error specifically for the Owner role.
**6. Affected module:** Reports and Audit
**7. Related Excel test ID:** REP-01
**8. Potential impact:** Major functional failure preventing business owners from accessing critical audit and financial data.
**9. Recommended corrective action:** Review the backend permissions middleware and database queries. Ensure the Owner role is explicitly granted read access to the report generation endpoints, or fix null-reference errors if the Owner role isn't tied to a specific branch in the same way a Manager is.
**10. Severity and priority:** **High** / High Priority
**11. Verification steps for retesting:** Log in as Owner. Navigate to Reports. Verify all tables and branch information populate correctly without console or network errors.

**1. Issue ID:** RBA-ISS-02
**2. Issue title:** Inappropriate "Manager on Duty" Display for Owners
**3. Description of observed behavior:** The dashboard displays a "Manager on Duty" section even when the logged-in account has the Owner role.
**4. Expected behavior:** Dashboard views should be tailored to the user's role. Owners should either see an aggregate view or a modified label.
**5. Actual behavior:** The UI rigidly displays Manager-specific information to the Owner.
**6. Affected module:** Dashboard / Organization, Users, and Settings
**7. Related Excel test ID:** N/A (Supplementary Note)
**8. Potential impact:** Minor UI confusion and poor user experience.
**9. Recommended corrective action:** Implement conditional UI rendering based on the user's role. Verify with stakeholders what information the Owner should see in place of the Manager on Duty card.
**10. Severity and priority:** **Low** / Low Priority
**11. Verification steps for retesting:** Log in as Owner. Verify the "Manager on Duty" widget is hidden or appropriately repurposed based on updated requirements.

### Inventory and Predictive Analytics

**1. Issue ID:** ANL-ISS-01
**2. Issue title:** Predictive Analytics Features Non-Functional or Unverified
**3. Description of observed behavior:** The "Estimated Consumption" feature does not work. "Recommended Stock Optimizer" is marked as failed, and "Historical Consumption" is unavailable. 
**4. Expected behavior:** Analytics modules should calculate and display metrics based on historical inventory and sales data.
**5. Actual behavior:** Features are failing, outputting errors, or returning blank states. It is unclear if this is due to calculation defects, filtering issues, or simply a lack of database test records.
**6. Affected module:** Predictive Analytics and Decision Support
**7. Related Excel test ID:** ANL-01, ANL-02, ANL-03
**8. Potential impact:** Loss of core decision-support functionality.
**9. Recommended corrective action:** The QA team and Developers must seed the test database with at least 30-90 days of synthetic historical sales and inventory data. Once populated, QA must retest to determine if the logic itself is defective or if the features were merely failing gracefully due to empty datasets. 
**10. Severity and priority:** **Medium (Provisional - Requires Investigation)** / Medium Priority
**11. Verification steps for retesting:** Seed database with sufficient historical data. Navigate to Predictive Analytics. Verify Estimated Consumption, Historical Consumption, and Stock Optimizer yield mathematically accurate outputs.

### Reports, Exporting, and Profile Settings

**1. Issue ID:** EXP-ISS-01
**2. Issue title:** Incorrect Report Export Format
**3. Description of observed behavior:** Exporting a report produces a `.doc` file format.
**4. Expected behavior:** System documentation implies exports should be in a modern format (e.g., `.docx`), subject to business requirement verification.
**5. Actual behavior:** The system generates a legacy `.doc` file.
**6. Affected module:** Reports and Audit
**7. Related Excel test ID:** N/A (Supplementary Note)
**8. Potential impact:** Minor inconvenience; compatibility issues with modern office software relying on `.docx`.
**9. Recommended corrective action:** Verify the exact required export format with stakeholders. If `.docx` is required, update the export library/script to generate the correct MIME type and file extension.
**10. Severity and priority:** **Low** / Low Priority
**11. Verification steps for retesting:** Generate a report export. Verify the downloaded file extension is `.docx` and opens cleanly in Microsoft Word.

**1. Issue ID:** SET-ISS-01
**2. Issue title:** Profile Photo Upload Control Unresponsive
**3. Description of observed behavior:** The "Upload Photo" control in Profile Settings cannot be clicked.
**4. Expected behavior:** Clicking the control should open the operating system's native file selection dialog.
**5. Actual behavior:** The control is static and unresponsive.
**6. Affected module:** Organization, Users, and Settings
**7. Related Excel test ID:** SET-01
**8. Potential impact:** Users cannot customize their profiles. 
**9. Recommended corrective action:** Inspect the HTML/CSS of the upload button. Ensure the `<input type="file">` is properly linked to the button, not obscured by `z-index` layers, and not disabled by JavaScript state errors.
**10. Severity and priority:** **Low** / Low Priority
**11. Verification steps for retesting:** Navigate to Profile Settings. Click Upload Photo. Verify the file explorer opens and a photo can be successfully uploaded and saved.

## V. Performance and Usability Observations

While not strictly functional failures, the following observations from the Excel checklist significantly degrade the user experience and warrant attention:

*   **POS Transaction Delays:** Adding items to the POS takes approximately **three seconds per action**. Similarly, incrementing or decrementing items in the cart takes approximately **three seconds per click**. 
    *   *Potential Effect:* This creates a severe bottleneck for cashiers during peak hours, leading to long customer lines and user frustration. This suggests unoptimized database queries, synchronous external API calls, or excessive re-rendering on the frontend.
*   **Navigation Friction:** The workflow for a Manager to navigate from the POS module to the Floater module requires too many steps and buttons. 
    *   *Potential Effect:* Reduces operational efficiency. The navigation path should be simplified (e.g., a direct toggle or quick-link).
*   **Redundant UI Elements:** There are two logout buttons (one bottom-right, one in the Manager/Floater dropdown). 
    *   *Potential Effect:* Clutters the interface. The team should review UI/UX guidelines to standardize a single, predictable location for session termination.
*   **Unclickable Sales KPIs:** Sales KPI cards are static, whereas Inventory KPI cards are clickable and act as filters. 
    *   *Potential Effect:* Inconsistent user interface patterns. Making Sales KPIs clickable would improve usability and data exploration.
*   **Data Import Restrictions:** The import feature currently only accepts JSON files. 
    *   *Potential Effect:* Users typically expect CSV or Excel formats for bulk business data. The team should verify if restricting to JSON is intentional or if parsing libraries for CSV/XLSX should be integrated.

## VI. Recommendations and Action Plan

The following action plan is prioritized to guide the development team:

**Priority 1: Immediate Security and Data-Integrity Corrections**
*   **Action:** Fix session state restoration (AUTH-ISS-01). Ensure strict client-side guarding and server-side token invalidation.
*   **Action:** Fix branch-specific pricing leakage (SLS-ISS-01). Ensure `UPDATE` queries for pricing are bound strictly to the `branch_id`.
*   **Action:** Resolve asynchronous account loading (AUTH-ISS-02) to prevent cross-session transaction logging.

**Priority 2: Major Functional Defects & Access Control**
*   **Action:** Debug and resolve the failure of Reports loading for the Owner role (RBA-ISS-01). 
*   **Action:** Implement strict data filtering on the backend so unauthorized and inactive branches (e.g., Lipa) do not appear in any user dropdowns or sales filters (SLS-ISS-02). 
*   **Action:** Verify the requirement for Owner POS access and implement if required.

**Priority 3: Performance and Usability Improvements**
*   **Action:** Profile the POS cart functionality. Optimize the add/increment/decrement actions to respond in under 500ms (down from 3 seconds). Investigate optimistic UI updates or bulk API processing.
*   **Action:** Fix the unclickable Profile Photo upload button (SET-ISS-01).
*   **Action:** Remove redundant logout buttons and streamline POS-to-Floater navigation.

**Priority 4: Features Requiring Additional Data/Testing**
*   **Action:** Seed the database with comprehensive test data (sales, inventory history). 
*   **Action:** Re-run testing on Estimated Consumption, Historical Consumption, and Recommended Stock Optimizer to confirm if mathematical models are functional (ANL-ISS-01).

**Priority 5: Optional Enhancements**
*   **Action:** Convert `.doc` exports to `.docx` if confirmed by stakeholders.
*   **Action:** Make Sales KPI cards clickable to match Inventory UX.
*   **Action:** Evaluate adding CSV support to the data import tool.

## VII. Retesting Checklist

The QA team should utilize this checklist once the development team marks the above issues as resolved.

| ID | Module | Verification Step | Expected Result | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Auth | Log in, log out, click browser Back button. | User remains on login screen; no dashboard access. | [ ] |
| 2 | Auth | Rapidly click POS during Sales page load. | Username matches actual logged-in user consistently. | [ ] |
| 3 | Sales | Change price of item in Branch A. Check Branch B. | Price in Branch B is entirely unaffected. | [ ] |
| 4 | Sales | Open branch filters with restricted account. | Unauthorized/inactive branches (Lipa) are completely hidden. | [ ] |
| 5 | Reports| Log in as Owner and open Reports module. | All reports and branch info load successfully without errors. | [ ] |
| 6 | POS | Click 'Add to Cart' and increment quantities. | Action registers almost instantly (under 0.5s), no 3s delay. | [ ] |
| 7 | UI/UX | Navigate from POS to Floater module. | Navigation is achieved in 1-2 intuitive clicks. | [ ] |
| 8 | Analytics| Ensure DB has 30 days data, view Stock Optimizer. | Module displays logical recommendations without failing. | [ ] |

## VIII. Conclusion

The AAskMi system demonstrates a functional baseline for point-of-sale and organizational management; however, current testing reveals significant vulnerabilities in session management and branch data isolation. The global propagation of branch-specific pricing and the ability to restore secure sessions via browser navigation are critical blockers for deployment. 

The immediate next steps require the development team to focus entirely on Priorities 1 and 2 (Security, Data Isolation, and Access Control). Concurrently, the test environment must be populated with rich historical data so QA can properly validate the Predictive Analytics modules. Once these core issues are remediated and the POS performance delays are optimized, the system will be ready for a final round of regression testing.