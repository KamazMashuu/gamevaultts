# SQA Mock Exam — Situational Identification & Explanation
### Format: For each situation, (1) identify the specific QA activity, testing type, or practice that applies, then (2) explain in 2–4 complete sentences why it is appropriate and how it helps improve software quality.

*Covers Modules 1–3. Try answering each item yourself before checking the Answer Key at the end.*

---

**1.** Every time the development team modifies the payment module of an e-commerce system, they notice that unrelated features — like receipts and account balances — sometimes stop working correctly. Before releasing the update, what should the team do?

**2.** A requirements document states: "The system shall generate reports quickly." During the requirements phase, a QA analyst flags this statement and asks the business analyst to clarify an exact time limit and load condition.

**3.** During system design, a reviewer notices that the proposed banking application architecture stores user passwords in plain text in the database. The issue is raised and corrected before a single line of code is written.

**4.** A tester enters a valid username and an incorrect password into a login form, expecting the system to deny access. Instead, the system logs the user into the dashboard.

**5.** A project team proudly reports that their system passes 100% of its documented requirement checks. However, when real users try the system, they find it confusing and say it doesn't solve their actual problem, because the requirements were written based on incorrect assumptions about user needs.

**6.** Before any code is executed, a senior developer reads through the source code of a new module, checking it against the requirements document and architectural rules — without running the program.

**7.** A QA lead regularly pulls reports on code coverage percentages and the number of defects introduced per sprint over the last six months to identify whether quality is improving or declining.

**8.** After a new student enrollment system goes live, the QA team immediately checks: Can students log in? Can they view subjects? Can they enroll? Are payments reflected correctly? Are old student records still intact?

**9.** A software engineer, while coding a tax calculation feature, misreads the business rule and types `<` instead of `<=`. Weeks later, a taxpayer earning exactly $50,000 receives an incorrect tax computation when the system runs.

**10.** The QA team wants to confirm that the Student Information System (SIS) correctly sends and receives data from the separate Clearance System it was recently connected to.

**11.** The performance team simulates 10,000 concurrent users placing orders on an online shopping platform to measure response time and see if the system slows down or crashes.

**12.** A security specialist attempts to log in with SQL injection strings and checks whether unauthorized users can access restricted admin pages.

**13.** QA evaluates the entire enrollment system end-to-end — from student login, to subject selection, to payment, to printing the final enrollment document — to make sure all parts work together as a whole.

**14.** A test case is written to confirm that a registered student can successfully submit a clearance request using the exact process described in the requirements.

**15.** A tester deliberately leaves the username and password fields blank, then submits the login form, to see how the system responds to missing input.

**16.** An internal auditor visits a development team to confirm that they are following the company's mandated code-review checklist and its standard Git branching/versioning policy, regardless of what project they're working on.

**17.** A team writes: "Verify that registered users can log in" as a general statement of what needs to be tested, and separately prepares the exact steps (open login page → enter credentials → click Login → verify dashboard) along with the specific username and password values to use.

**18.** A team tests a simple calculator app using only the inputs `1+1`, `2+2`, `5×5`, and `100÷10` — all pass — and concludes the calculator has no defects.

**19.** After analyzing several months of defect reports, a QA manager notices that 70% of all reported bugs come from just two modules: the billing module and the file-upload module. The manager decides to allocate extra reviewers and more test cases to those two modules for the next release.

**20.** A vague requirement, "upload large files," was not clarified during the requirements phase. Developers assumed a 10MB cap. Months later, after most of the system was built, stakeholders revealed they needed 100MB uploads — forcing costly changes across the application, database, server validation, UI, storage, and test cases.

---
---

## ANSWER KEY

**1. Identification: Regression Testing**
Regression testing should be performed because it checks whether recent changes (to the payment module) have unintentionally affected existing, previously working functionality such as receipts or account balances. It is appropriate here because a defect's consequences can ripple into unrelated components that share dependencies with the modified code. Running regression tests before release helps catch these unintended side effects early, preventing the introduction of new failures into stable parts of the system.

**2. Identification: Ambiguity Checking / Testability Analysis (a Requirements-phase QA activity)**
This is appropriate because the word "quickly" is subjective and has no measurable pass/fail criterion, making the requirement untestable as written. Requirements QA activities such as ambiguity and testability checking catch this early, before it is designed or coded around a wrong assumption. Fixing it at this stage — turning it into something like "within five seconds under normal load" — is far cheaper than discovering the ambiguity after the system is built.

**3. Identification: Design Review (Security Review)**
A design review is the correct activity because it is a verification step that inspects the architecture and design artifacts before any implementation occurs, catching structural and security flaws early. Storing passwords in plain text is a serious security weakness that could lead to data breaches if left undetected. Catching it during design — rather than after coding, testing, or deployment — is significantly less expensive and prevents the flaw from ever entering the codebase.

**4. Identification: Negative Testing (revealing a Failure / security Defect)**
This is negative testing because the tester deliberately supplied invalid input (a wrong password) to see how the system handles unacceptable conditions. The mismatch between the expected result (access denied) and the actual result (successful login) constitutes a test FAIL and points to a serious security defect. This demonstrates why negative testing is essential — real users (or attackers) don't always provide valid input, and the system must handle invalid input safely.

**5. Identification: Validation Failure (illustrates the Verification vs. Validation distinction)**
This situation illustrates the key risk that a system can achieve 100% verification (conforming strictly to the written specifications) while completely failing validation (not meeting actual user needs). Verification only confirms the product was built according to the spec — it says nothing about whether the spec itself was correct. This shows why both V&V activities are necessary: validation activities like UAT should have been performed earlier to confirm the requirements truly reflected real user needs.

**6. Identification: Verification (specifically Static Code Analysis / Code Review)**
This is verification because it is a static evaluation performed without executing the software, checking the code against the requirements and architectural rules set for that phase. It helps catch programming errors, standard violations, and design misalignment before the code is ever run or tested dynamically. Because it happens early in development, defects found this way are cheaper and easier to fix than those found later during testing or after deployment.

**7. Identification: Monitoring Metrics (an SQA Activity)**
Monitoring metrics is appropriate because SQA is a process-oriented, continuous activity that tracks indicators like code coverage and defect injection rates to detect trends in process health. This helps the QA lead identify whether current engineering practices are improving or degrading over time, rather than waiting to observe the effects only in finished products. It supports the prevention-focused nature of SQA by allowing the team to intervene in the process before problems compound.

**8. Identification: Deployment Verification / Smoke Testing (Production Acceptance Testing)**
These checks are deployment QA activities — specifically deployment verification and smoke testing — performed immediately after go-live to confirm the system's critical functions work correctly in the production environment. This is appropriate because deployment can introduce environment-specific issues (configuration, data migration, integration) that were not present in the test environment. Catching these issues right after launch prevents widespread user impact and allows for a quick rollback if needed.

**9. Identification: Error → Defect → Failure chain**
This situation traces the full lifecycle of a software flaw: the developer's misreading of the business rule is the **Error** (human mistake), the resulting incorrect code (`<` instead of `<=`) is the **Defect** sitting passively in the code, and the wrong tax computation experienced by the $50,000 earner is the **Failure** triggered when that defect was executed. Understanding this chain is useful because it shows quality problems originate from human actions, not just "buggy code," and reinforces why early reviews (which catch errors before they become defects) are valuable.

**10. Identification: Integration Testing**
Integration testing is appropriate because its purpose is to verify that separate components or systems — in this case, the SIS and the Clearance System — work together and exchange data correctly. Individual systems might function perfectly on their own (pass functional testing) yet still fail when connected due to data format mismatches or communication errors. Testing this interaction directly reduces the risk of integration failures being discovered only after both systems are live.

**11. Identification: Performance Testing**
Performance testing is the correct type here because it measures response time, throughput, scalability, and resource utilization under load. Simulating 10,000 concurrent users specifically evaluates whether the system can maintain acceptable speed and stability under realistic or peak traffic conditions. This is important because a system can be functionally correct yet still fail users if it becomes too slow or crashes under real-world usage volumes.

**12. Identification: Security Testing**
Security testing is appropriate because it specifically targets authentication, authorization, input validation, and vulnerabilities such as injection attacks. Attempting SQL injection and unauthorized access checks the system's ability to protect data and restrict access as required by the Security characteristic of ISO/IEC 25010. Identifying these weaknesses before release helps prevent data breaches, unauthorized access, and reputational or financial damage.

**13. Identification: System Testing**
This is system testing because it evaluates the complete, integrated system end-to-end rather than isolated components or module-to-module connections. It's appropriate because some defects only appear when the full workflow (login → enrollment → payment → document generation) runs together, even if each individual part passed its own tests. System testing gives stakeholders confidence that the product works as a cohesive whole, not just in isolated pieces.

**14. Identification: Functional Testing (Positive Testing)**
This is functional testing (executed here as a positive test) because it verifies that the system performs a required function — submitting a clearance request — correctly under normal, valid conditions. It directly checks that the implemented behavior matches the documented requirement. This type of testing is fundamental because it confirms the core features stakeholders paid for actually work as specified.

**15. Identification: Negative Testing**
This is negative testing because the tester is deliberately providing invalid/incomplete input (blank fields) to observe how the system handles unacceptable conditions, rather than testing the "happy path." It's appropriate because real users often make mistakes or submit incomplete data, and the system must respond gracefully (e.g., with a validation error) instead of crashing or behaving unpredictably. Good test coverage requires both positive and negative testing to be considered thorough.

**16. Identification: Process Audit**
This is a process audit because it verifies that teams are complying with organizational methodologies and standards, independent of any single product or project. It is appropriate because QA is process-oriented and continuous — it is not enough to check whether one product is good; the organization must confirm that its prescribed processes (checklists, versioning policies) are consistently followed across all teams. This helps maintain consistent quality standards at an organizational level rather than relying on individual team discipline.

**17. Identification: Test Scenario vs. Test Case vs. Test Data**
The general statement "Verify that registered users can log in" is a **Test Scenario** (what to test, high-level); the exact steps (open login page → enter credentials → click Login → verify dashboard) form the **Test Case** (how to test it); and the specific username/password values are the **Test Data** (what information to use). Distinguishing these is appropriate because each serves a different purpose — the scenario defines scope, the case defines procedure, and the data defines concrete input — and together they make testing repeatable, precise, and unambiguous for anyone executing the test later.

**18. Identification: Key Limitation of Testing (Testing cannot prove the absence of defects) / Exhaustive Testing is Impossible**
This situation illustrates that testing can only show the presence of defects under tested conditions, not prove their complete absence — the calculator has never been tested with negative numbers, decimals, division by zero, or special characters, so defects may still exist there. It's a misapplication of testing because the team is drawing an overly confident conclusion ("no defects") from a very limited, non-representative set of test cases. The correct approach is to test intelligently with representative and high-risk conditions rather than assume passing a few basic cases proves correctness.

**19. Identification: Defect Clustering (a Fundamental Testing Principle)**
This illustrates defect clustering, the principle that defects tend to concentrate in a small number of components rather than being evenly distributed across the system. Recognizing this pattern is appropriate because it allows the QA manager to allocate limited testing resources (extra reviewers, more test cases) where they will have the greatest impact on reducing risk. Focusing effort on historically defect-prone modules improves overall quality more efficiently than spreading testing evenly across all modules.

**20. Identification: Shift-Left Quality (failure to shift left) / Cost of Finding Defects Late**
This situation demonstrates the cost of finding defects late and the value of "shift-left" quality — because the ambiguous requirement wasn't clarified early through requirements review, the misunderstanding was only discovered after significant development had already occurred. Had QA activities (requirement clarification, testability analysis) been applied at the requirements phase, the correct file-size limit could have been confirmed at minimal cost. Instead, the fix now requires changes across the application, database, server, UI, storage, and test cases — proving that defects caught early are dramatically cheaper to resolve than those caught late.
