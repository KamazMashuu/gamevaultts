# Software Quality Assurance — Comprehensive Reviewer
### Covering Module 1 (Foundations of SQA), Module 2 (QA in the SDLC), and Module 3 (Software Testing Fundamentals)

---

## MODULE 1 — FOUNDATIONS OF SYSTEMS QUALITY ASSURANCE

### 1.1 What Do We Mean by "Quality"?
- **Quality** = the degree to which a product, service, or system satisfies specified requirements and meets user/stakeholder expectations.
- Functional correctness alone is **not sufficient**. A system can be mathematically correct yet functionally unusable due to:
  - Extreme latency
  - Vulnerability to unauthorized access
  - Unmaintainable source code
- Quality is **not a single yes/no property** — it's a *profile of characteristics* balanced against one another.

### The ISO/IEC 25010 Quality Model (8 Characteristics)
| Characteristic | Meaning |
|---|---|
| **Functional Suitability** | Provides functions that meet stated & implied needs |
| **Performance Efficiency** | Response time, processing speed, resource use |
| **Compatibility** | Exchanges data or shares an environment with other systems |
| **Usability** | Achieves user goals effectively, efficiently, with satisfaction |
| **Reliability** | Performs specified functions for a specified period |
| **Security** | Protects data with appropriate access authorization |
| **Maintainability** | Can be modified, corrected, or adapted efficiently |
| **Portability** | Transfers between hardware/software environments |

**Key Takeaway:** Software quality is multidimensional — evaluating it requires balancing functional expectations with technical, operational, and structural attributes.

### 1.2 What is Software Quality Assurance (SQA)?
- **SQA** = a planned and systematic pattern of actions that provides adequate confidence that a product conforms to established technical requirements and quality expectations.
- **Prevention over Detection**: SQA analyzes development *processes* to prevent flaws from being introduced — rather than only evaluating finished products to find flaws.

**Two Approaches to Quality:**
| Traditional (Reactive) | SQA (Preventive) |
|---|---|
| Development → Testing/Detection → Defect Fixing | Process & Audits → Defect Prevention → High-Quality Product |

*The earlier a flaw is caught, the cheaper it is to fix.*

**Primary SQA Activities (5):**
1. **Establishing Engineering Standards** — coding guidelines, architecture rules, versioning protocols, documentation templates
2. **Requirements and Design Reviews** — inspecting specs before implementation to catch ambiguities/gaps
3. **Defining Quality Standards & Procedures** — rules for unit testing, CI pipelines, code review approvals
4. **Monitoring Metrics** — analyzing code coverage and defect injection rates to spot trends
5. **Process Audits** — verifying teams comply with organizational methodologies and standards

### 1.3 Quality Assurance vs. Quality Control vs. Testing
| | Quality Assurance (QA) | Quality Control (QC) | Software Testing |
|---|---|---|---|
| **Focus** | Process-oriented | Product-oriented | Activity-oriented |
| **Goal** | Prevent defects from occurring | Identify & correct defects in deliverables | Execute systems to identify failures |
| **Timing** | Continuous, entire lifecycle | Applied to intermediate/final artifacts | During build & test phases |
| **Core Question** | "Are we using process controls to prevent flaws?" | "Does this build meet quality criteria?" | "Does the actual outcome match expected results?" |

**Relationship (nested):**
> QUALITY ASSURANCE (process prevention & standards) → contains → QUALITY CONTROL (product verification) → contains → TESTING (execution & evidence)

### 1.4 Verification and Validation (V&V)
| | **Verification** — "Building the Product Right" | **Validation** — "Building the Right Product" |
|---|---|---|
| Nature | Static evaluation (no execution) | Dynamic evaluation (running software) |
| Checks | Artifacts against conditions set at start of that phase | System against real user requirements/operational needs |
| Examples | Static code analysis, requirements review meetings, architectural conformance checks, schema reviews | System integration testing, UAT, usability lab trials, field stress testing |

**Key Risk:** A project can achieve **100% verification success** (conforms strictly to specifications) while **failing validation completely**, if the original specification misaligned with actual user needs.

### 1.5 Errors, Defects, and Failures
**Chain:** Human Error → *creates* → Defect/Fault → *triggers* → Failure

| Term | Definition | Example |
|---|---|---|
| **Error** (mistake) | Human action producing an incorrect result during design/coding/config | Engineer misinterprets tax rule, writes `<` instead of `<=` |
| **Defect** (fault/bug) | A flaw in a component/artifact resulting from an error; resides passively in requirements/design/code | Line 42: `if (income < 50000)` instead of `if (income <= 50000)` |
| **Failure** | An event where the system fails to perform its required function, triggered when execution activates a defect | A taxpayer earning exactly $50,000 gets an incorrect tax calculation |

---

## MODULE 2 — QUALITY ASSURANCE IN THE SDLC

### 2.0 Core Principle
> "Quality must be built into the software development process, not merely inspected into the finished product."

QA is **not confined to the Testing phase** — it runs across the **entire SDLC**.

### 2.1 The Seven Stages of the SDLC
**Planning → Requirements Analysis → System Design → Development → Testing → Deployment → Maintenance**
(Presented sequentially for learning, but real development involves iteration/feedback.)

#### 2.1.1 Planning
- Identifies: problem to solve, objectives/users, scope/resources, cost/schedule, technical requirements, risks/feasibility.
- **QA Questions:** Are objectives clear? Is scope realistic? Are quality objectives identified? Are risks recognized? Is there enough time/resources for testing?
- Poor planning creates downstream quality problems (e.g., insufficient testing time forces release with unresolved defects).

#### 2.1.2 Requirements Analysis
- **Functional Requirements** — what the system must *do* (e.g., "System shall allow students to log in").
- **Non-Functional Requirements** — quality attributes (e.g., "Respond within 3 seconds under normal load").
- **QA Activities:** requirement review, completeness checking, consistency checking, ambiguity checking, feasibility checking, testability analysis, traceability analysis, validation with stakeholders.
- **Vague requirement example:**
  - *Poor:* "The system shall generate reports quickly." (Quickly = 1 sec? 5 sec? 30 sec? — not testable.)
  - *Better:* "The system shall generate the monthly enrollment report within five seconds when processing up to 10,000 student records under normal operating conditions." (Measurable & testable.)

#### 2.1.3 System Design
- Decisions: architecture, database design, UI, components/interfaces, data flow, security/authentication, technology selection.
- **QA Activities:** architecture review, interface review, security review, design inspection, usability & compliance review.
- **Example:** A banking system design stores passwords in plain text — even if functionally correct, this is a serious security weakness. A **design review** should catch this *before implementation* (cheaper than fixing after coding/deployment).

#### 2.1.4 Development
- Developers create: programs/business logic, database structures, APIs/interfaces, configuration files.
- **QA Activities:** coding standards, code reviews, static analysis, unit testing, peer review, defect tracking, version control, secure coding.
- **Code Review** looks for: programming errors, security weaknesses, inefficient algorithms, maintainability problems, coding standard violations.
- Example: a reviewer catches a query that could expose sensitive data — fixed before formal system testing.

#### 2.1.5 Testing — Five Major Types
| Type | Purpose | Example |
|---|---|---|
| **Functional** | Does the software function correctly? | Can a student submit a clearance request? |
| **Integration** | Do components work together? | Does the SIS communicate with the Clearance System? |
| **System** | Evaluate the complete system end-to-end | Login → document generation |
| **Performance** | Response time, throughput, scalability, resource use | Load under many users |
| **Security** | Authentication, authorization, input validation, access control | Vulnerability checks |

#### 2.1.6 Deployment
- Activities: installation/configuration, database migration, user account setup/data conversion, user training, system verification & production monitoring.
- **QA Activities:** deployment verification, smoke testing, configuration verification, database verification, production acceptance testing, rollback verification.
- Example checklist (new enrollment system): Can students log in? Can subjects display? Can students enroll? Are payments reflected? Do reports work? Are old records preserved?

#### 2.1.7 Maintenance
- Activities: bug fixes/security patches, performance improvements, new features, DB/config updates, compatibility updates.
- **QA Activities:** regression testing, change impact analysis, monitoring, incident management, defect analysis, release verification, user feedback analysis.
- **Regression Testing** — checks whether changes unintentionally broke existing functionality. Example: modifying the payment module could accidentally affect registration, receipts, reports, or account balances.

### 2.2 QA Activities Across the SDLC (Summary Table)
| Phase | Major QA Activities |
|---|---|
| Planning | Quality planning, risk identification, feasibility review |
| Requirements | Requirement review, completeness, consistency, testability, traceability |
| Design | Architecture review, security review, interface review |
| Development | Coding standards, code review, static analysis, unit testing |
| Testing | Functional, integration, system, performance, security testing |
| Deployment | Deployment verification, smoke testing, configuration verification |
| Maintenance | Regression testing, monitoring, incident management, change impact analysis |

> QA shifts from simply *finding* defects to also *preventing* them — across every phase.

### 2.3 Shift-Left Quality
- **Traditional sequence:** Requirements → Design → Coding → Testing (testing gets attention only after development).
- **Shift-Left sequence:** Requirements+QA → Design+QA → Coding+QA → Testing (QA embedded at every step).
- The earlier a defect is detected, the easier (and cheaper) it is to correct.
- Example: if "upload large files" isn't clarified early, developers may cap uploads at 10MB; discovering later that 100MB is needed forces costly changes across app, database, server, validation, UI, storage, and test cases.

### 2.4 Cost of Finding Defects
- Cost to fix a defect **increases** the later it is found: Requirements < Design < Development < Testing < Production.
- A misunderstanding found during requirements may need only clarification; found after deployment, it may require code changes, DB changes, retesting, redeployment, user communication, data correction, and business disruption.
- **Early quality activities = investments in defect prevention.**

### 2.5 Five Principles of Software QA
1. QA is not only testing — it begins before testing and continues after deployment.
2. Quality should be built in early — starting at planning/requirements.
3. Requirements determine what's tested — unclear/unmeasurable requirements are hard to test.
4. Earlier detection is generally better/cheaper.
5. Quality is everyone's responsibility — devs, testers, analysts, PMs, users, stakeholders.

---

## MODULE 3 — SOFTWARE TESTING FUNDAMENTALS

### 3.0–3.1 Introduction
Software can: produce incorrect results, reject valid input, accept invalid input, crash unexpectedly, respond slowly, expose sensitive info, behave inconsistently across devices, or fail under load.

> Software testing = a systematic process of designing tests, executing them, observing results, comparing actual vs. expected behavior, recording evidence, and communicating findings.

### 3.2 The Fundamental Testing Process (4-Step Cycle)
**Input → Execution → Observation → Comparison**
1. **Input** — tester provides data or performs an action
2. **Execution** — software processes the input
3. **Observation** — tester observes actual behavior
4. **Comparison** — actual result vs. expected result

**Worked Example — Login Test:**
- **PASS:** Input `student01/Password123` → clicks Login → dashboard displayed → Expected = Actual → **PASS**
- **FAIL:** Same valid credentials → system shows "Invalid username or password" → Expected ≠ Actual → **FAIL**, documented with detail for developers.

### 3.3 Common Misconceptions About Testing
1. "Testing finds every bug" — false; impossible to prove all defects found.
2. "Testing starts after programming" — false; quality activities start earlier in SDLC.
3. "If it runs, it's correct" — false; a program can execute fine yet produce wrong results.
4. "Only testers own quality" — false; shared across devs, analysts, PMs, users.
5. "A failed test = tester's fault" — false; failure may point to a defect, bad requirement, or bad environment.

### 3.4 Why Do We Test? (6 Reasons)
1. **Find defects** — expose incorrect behavior
2. **Verify requirements** — confirm system satisfies specification
3. **Validate user expectations** — system can meet requirements yet disappoint real users
4. **Reduce risk** — consequences vary by system (game < online shopping < banking < medical)
5. **Support decision-making** — gives stakeholders evidence for release decisions
6. **Improve confidence** — successful testing raises confidence without guaranteeing perfection

### 3.5 A Key Limitation
> Testing can demonstrate the **presence** of defects but generally **cannot prove their absence** — untested conditions or undiscovered defects may always remain.

Example: a calculator tested only with `1+1, 2+2, 5×5, 100÷10` (all pass) still may hide defects with negative numbers, decimals, very large numbers, zero, division by zero, or special characters. This is why testers select **representative and high-risk conditions** rather than trying to test everything.

### 3.6 Five Fundamental Testing Principles
1. **Testing shows the presence of defects** — a pass proves it worked under *that* condition, not that it's defect-free.
2. **Exhaustive testing is usually impossible** — too many possible inputs; select intelligently.
3. **Early testing** — quality activities should start with requirements/design review, not just code.
4. **Defect clustering** — defects often concentrate in a small number of components; prioritize accordingly.
5. **Testing is context-dependent** — a banking app, a game, and a hospital system carry very different risks.

### 3.7–3.8 Core Vocabulary
| Term | Question it answers | Example |
|---|---|---|
| **Test Scenario** | What are we testing? (high-level, broad) | "Verify that registered users can log in." |
| **Test Case** | How will we test it? (specific steps/inputs/expected results) | Open login page → enter credentials → click Login → verify dashboard |
| **Test Data** | What information will we use? (actual values) | Username: student01, Password: Password123 |

**Worked Example (Test Case Documentation):**
| Field | Value |
|---|---|
| Requirement | Registered students can log in using valid credentials |
| Test Case ID | LOGIN-001 |
| Precondition | Student account exists |
| Test Data | Username: student01, Password: Password123 |
| Test Steps | Open login page → enter username → enter password → click Login |
| Expected Result | Student is redirected to the dashboard |
| Actual Result | Student is redirected to the dashboard |
| Status | PASS |

### 3.9 Expected vs. Actual Results
- **Expected Result** — what the system *should* do per the requirement.
- **Actual Result** — what the system *actually* did.
- **PASS example:** reject wrong password → error shown, access denied → matches expected → PASS.
- **FAIL example:** reject wrong password → system instead logs the user in → mismatch → **potentially serious security defect.**

### 3.12–3.13 Positive Testing vs. Negative Testing
| | Positive Testing | Negative Testing |
|---|---|---|
| Purpose | Verifies system works correctly with **valid** inputs/expected conditions | Checks system behavior with **invalid/unexpected/unacceptable** input |
| Example | Valid username/password → expect successful login | Blank fields, wrong formats, unexpected characters |

> Positive and negative testing complement each other — good coverage needs both.

### Key Takeaways (Module 3)
1. Testing is systematic — planned and documented, not random.
2. It finds defects, not perfection — a pass only covers the tested condition.
3. Exhaustive testing is impossible — prioritize and select cases intelligently.
4. Start testing early — review requirements and design first.
5. Defects often cluster — focus extra effort where risk concentrates.
6. Testing depends on context — different systems carry different risks.
7. Scenario ≠ Case ≠ Data — what to test → how → with what information.
8. Compare expected vs. actual — that comparison decides pass or fail.

---

## QUICK-REFERENCE GLOSSARY
- **QA** — process-oriented, prevents defects, continuous across lifecycle
- **QC** — product-oriented, corrects defects in deliverables
- **Testing** — activity-oriented, executes system to find failures
- **Verification** — building the product right (static, no execution)
- **Validation** — building the right product (dynamic, execution-based)
- **Error → Defect → Failure** — mistake → flaw → observable malfunction
- **Shift-Left** — moving QA activities earlier in the SDLC
- **Regression Testing** — re-testing to ensure changes didn't break existing features
- **Defect Clustering** — defects concentrate in a small number of components
- **Test Scenario / Case / Data** — what / how / with-what-values
