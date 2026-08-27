# Legacy Codebase Reusability Analysis
## FlexibPlus → New Platform (Phase 1)

> **Document Version:** 1.0
> **Evidence basis:** Direct source-code inspection of `c:\Users\10004463\Desktop\Flexib_x`
> **Constraint enforced:** No functionality is assumed. Every conclusion is backed by exact file/line references.

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [System Fingerprint](#2-system-fingerprint)
3. [Package & Module Inventory](#3-package--module-inventory)
4. [Database Schema Analysis](#4-database-schema-analysis)
5. [Security & Authentication Layer](#5-security--authentication-layer)
6. [User Management & Registration](#6-user-management--registration)
7. [Project / Application Registration](#7-project--application-registration)
8. [Test Repository Module](#8-test-repository-module)
9. [Test Execution Engine](#9-test-execution-engine)
10. [Automation Module](#10-automation-module)
11. [Dashboard & Reporting](#11-dashboard--reporting)
12. [Defect Management](#12-defect-management)
13. [Frontend / UI Layer](#13-frontend--ui-layer)
14. [External Integrations](#14-external-integrations)
15. [Reusability Scorecard](#15-reusability-scorecard)
16. [Phase 1 Requirement Mapping](#16-phase-1-requirement-mapping)
17. [Risk Register](#17-risk-register)
18. [Recommended Migration Strategy](#18-recommended-migration-strategy)

---

## 1. Executive Summary

FlexibPlus is a **Java 17 / Spring Boot 2.5.5 monolithic WAR** application that bundles a full-stack Quality Engineering platform. The backend is a single Spring context exposing REST APIs (`/api/flexibautomation/**`, `/api/auth/**`). The frontend is composed of **87+ static HTML pages** served from `src/main/resources/public`, communicating exclusively via jQuery AJAX.

**Key finding:** The backend business logic — security, entities, service operations — is **architecturally reusable** for the new platform, but requires significant refactoring to meet modern standards. The frontend is **not reusable** and must be fully rebuilt.

---

## 2. System Fingerprint

| Attribute | Value | Evidence |
|-----------|-------|----------|
| Runtime | Java 17 | `pom.xml` L23-24 |
| Framework | Spring Boot 2.5.5 | `pom.xml` L9 |
| Build output | WAR | `pom.xml` L12 |
| Server port | 5601 | `application.properties` L11 |
| Database | MySQL (`flexib_db`) | `application.properties` L1 |
| ORM | JPA/Hibernate (ddl-auto=update) | `application.properties` L9 |
| Security | Spring Security + JJWT 0.9.1 + Auth0 JWT 3.18.1 | `pom.xml` L126-184 |
| Auth token type | Bearer JWT (HTTP Header) + HTTP-Only Cookie | `AuthTokenFilter.java` L66-73 |
| Session policy | STATELESS | `WebSecurityConfig.java` L73 |
| Conn pool | HikariCP max=30 | `application.properties` L30 |
| Email | Gmail SMTP (spring-boot-starter-mail) | `application.properties` L40-47 |
| Git integration | JGit 4.8 | `pom.xml` L145-148 |
| Excel | Apache POI 4.1.2 | `pom.xml` L132-142 |
| PDF | PDFBox 2.0.24 + iText7 7.1.14 | `pom.xml` L235-251 |
| Charts | JFreeChart 1.5.3 | `pom.xml` L240-243 |

---

## 3. Package & Module Inventory

```
com.infotech.flexibautomation/
├── Models/               ← Auth-domain entities (User, Role, RefreshToken)
├── Repository/           ← Auth-domain Spring Data repos (UserRepository, RoleRepository)
├── entity/               ← 88 business-domain JPA entities (TestCase, Project, DefectMgt, ...)
├── repo/                 ← 77 Spring Data repository interfaces for business entities
├── service/              ← 84 service classes (business logic)
├── controller/           ← 8 REST controllers
│   ├── FlexibController.java   (5,524 lines — GOD CLASS, base: /api/flexibautomation)
│   ├── AuthController.java     (747 lines — auth endpoints: /api/auth)
│   ├── ChatBotController.java
│   ├── DownloadController.java
│   ├── ITechTicketController.java
│   └── RoleManagementController.java
├── security/
│   ├── WebSecurityConfig.java
│   ├── jwt/ (AuthTokenFilter, JwtUtils, AuthEntryPointJwt)
│   └── services/ (UserDetailsImpl, UserDetailsServiceImpl, RefreshTokenService, AuthUserService)
├── dto/                  ← Data Transfer Objects
├── model/                ← Additional model classes
├── payload/              ← Request/Response payload wrappers
└── constant/             ← Constants
```

> [!WARNING]
> `FlexibController.java` is **5,524 lines** with a single `@RequestMapping("/api/flexibautomation")`. All business operations — test cases, suites, execution, defects, automation, projects, users, dashboard, reports — are routed through this single controller. This must be broken apart in the new platform.

---

## 4. Database Schema Analysis

### 4.1 Confirmed Tables (via JPA entity `@Table` annotations)

| Table Name | Entity Class | Purpose |
|------------|-------------|---------|
| `AuthUsers` | `User.java` | Platform users |
| `AuthRoles` | `Role.java` | Role definitions |
| `user_roles` | (join table) | User - Role mapping |
| `Mapped_Project_To_User` | (collection table) | User - Project mapping |
| `Role_Activity_List` | (collection table) | Role - Activity mapping |
| `project` | `Project.java` | Project/App registry |
| `test_case` | `TestCase.java` | Test case repository |
| `test_suite` | `TestSuite.java` | Test suite groupings |
| `automation_result` | `AutomationResults.java` | Automation run results |
| `defect_management` | `DefectMgt.java` | Defect/Bug tracking |
| `user_stories` | `UserStoryDetails.java` | Agile user stories |
| `execution_plan` | `ExecutionPlan.java` | Test execution plans |

**Total confirmed entity tables:** 88 JPA entity classes → 88 mapped tables.

### 4.2 Current Database

- **Engine:** MySQL on port 6603 (non-standard port, dev environment)
- **Schema strategy:** `ddl-auto=update` — Hibernate auto-evolves schema. **No migration scripts (Flyway/Liquibase) exist.**
- **Naming:** `PhysicalNamingStrategyStandardImpl` (case-sensitive, column names are verbatim)

> [!IMPORTANT]
> **PostgreSQL Migration:** The new platform requires PostgreSQL. Migration is feasible but requires:
> 1. Replace `mysql-connector-java` with `postgresql` JDBC driver
> 2. Change dialect: `MySQL5Dialect` to `PostgreSQLDialect`
> 3. Audit all native queries for MySQL-specific syntax
> 4. Replace `ddl-auto=update` with proper Flyway migrations
> 5. Handle MySQL-specific column types (`TINYINT(1)` to `BOOLEAN`)

---

## 5. Security & Authentication Layer

### 5.1 Implementation Evidence

**File: [WebSecurityConfig.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/security/WebSecurityConfig.java)**

```java
// Line 72-77 — Security filter chain
http.cors().and().csrf().disable()
    .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
    .authorizeRequests()
    .antMatchers("/api/auth/**").permitAll()
    .antMatchers("/api/flexibautomation/**").permitAll()  // ALL BUSINESS APIS = NO AUTH
    .antMatchers("/*.html").permitAll();
// .anyRequest().authenticated(); ← THIS LINE IS COMMENTED OUT
```

> [!CAUTION]
> **Critical Security Flaw:** The entire `/api/flexibautomation/**` namespace — which contains ALL business endpoints including test case CRUD, project management, defect management, and execution — is `permitAll()` (no authentication required). The `anyRequest().authenticated()` line is **commented out**. The JWT filter exists but enforces nothing meaningful. The legacy system has **no API-level authorization**. The new platform MUST fix this.

**File: [JwtUtils.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/security/jwt/JwtUtils.java)**
- JWT generated via HS512 signing (L96-98)
- Secret stored in plaintext: `flexib.app.jwtSecret= bezKoderSecretKey` (`application.properties` L18)
- Expiry: 3 minutes (`jwtExpirationMs= 180000`) — very short
- Refresh token expiry: 4 minutes — also very short
- Cookie name: `flexibautomation`, path `/api`, `httpOnly=true`

**File: [AuthTokenFilter.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/security/jwt/AuthTokenFilter.java)**
- Reads JWT from `Authorization: Bearer <token>` header (L66-73)
- Validates → loads user → sets `SecurityContextHolder`
- Does NOT read from cookie (despite cookie generation logic in JwtUtils)

**File: [UserDetailsImpl.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/security/services/UserDetailsImpl.java)**
- `isEnabled()` checks `user.isactive == "ACTIVE"` (L122-127)
- Carries: `id, username, email, projectId, password, isactive, projectList, authorities`

### 5.2 Reusability Assessment

| Component | Status | Notes |
|-----------|--------|-------|
| `WebSecurityConfig.java` | ⚠️ Reusable with Major Fix | Fix the permitAll gap; upgrade to Spring Security 6.x syntax |
| `JwtUtils.java` | ⚠️ Reusable with Modification | Use strong secret from env variable; use newer JJWT API |
| `AuthTokenFilter.java` | ✅ Reusable with Minor Mod | Good pattern; minor cleanup needed |
| `UserDetailsImpl.java` | ✅ Reusable with Minor Mod | Add `organisationId`, `tenantId` if multi-tenant |
| `UserDetailsServiceImpl.java` | ✅ Directly Reusable | Standard Spring Security UserDetailsService |
| `RefreshTokenService.java` | ✅ Reusable with Modification | Logic is sound; expiry too short |

---

## 6. User Management & Registration

### 6.1 Evidence

**File: [AuthController.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/controller/AuthController.java)**

**Signup endpoint** (`POST /api/auth/signup`, L495-557):
- Validates username + email uniqueness
- Default password hardcoded: `final String pass = "flexib@123"` (L510) — **security issue**
- Auto-generates `userId` with pattern `UID_1`, `UID_2` (L529-548)
- Assigns roles from `AuthRoles` table
- Assigns `projectList` (Set of String)
- Creates default `UserSettings` record
- **No email verification flow**

**Signin endpoint** (`POST /api/auth/signin`, L105-193):
- Validates credentials via `AuthenticationManager`
- Validates project name ownership (L119-163)
- Returns JWT + refresh token + user profile
- Issues `isactive` check via `UserDetailsImpl.isEnabled()`

**Password Reset** (`POST /api/auth/password-reset-request`, L255-297):
- Validates username + email match
- Directly resets to new password — **no email OTP/token link verification**

**Change Password** (`POST /api/auth/change-password`, L299-344):
- Validates old password matches BCrypt hash
- Prevents reuse of same password

**User entity** (`User.java`, table `AuthUsers`):
```
id, userId, firstname, lastname, projectId, username, email, password (BCrypt),
status, isactive, projectName, projectList (Set<String>), roles (Set<Role>)
```

**Role entity** (`Role.java`, table `AuthRoles`):
```
id, rId, name, roleCreatedby, roleCreatedDate, projectID, activityList (Set<String>)
```

### 6.2 Reusability Assessment

| Feature | Status | Notes |
|---------|--------|-------|
| BCrypt password encoding | ✅ Directly Reusable | Standard `BCryptPasswordEncoder` |
| User registration API | ⚠️ Reusable with Modification | Remove hardcoded default password; add email verification |
| Login + JWT issuance | ✅ Reusable with Modification | Logic is sound; fix security config |
| Role-based authorization | ⚠️ Reusable with Modification | Roles are string-based, not enum-based; must harden |
| Password reset | ⚠️ Reusable with Modification | Add OTP/token-link flow; currently no verification email sent |
| User CRUD endpoints | ✅ Reusable with Modification | Clean up and add proper authorization guards |
| `User` entity | ⚠️ Reusable with Modification | Add `organisationId`, audit fields (`createdAt`, `updatedAt`) |

---

## 7. Project / Application Registration

### 7.1 Evidence

**Entity: [Project.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/entity/Project.java)** — Table: `project`
```
id, projectID, projectName, orgID, OrgName, projectType, description,
clientLocation, projStatus, startDate, endDate, msTeamConnector,
alertTimePicker, triageStatus, prefName, subOrgName, subOrgId, status_DB
```

**Repository: [ProjectRepo.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/repo/ProjectRepo.java)** (1,146 bytes)
- `findByProjectName(String name)`
- `existsByprojectName(String name)`

**Login integration:** The signin flow validates that the user is a member of the submitted project(s) before issuing a JWT. `projectList` on `User` = set of authorized project names.

### 7.2 Reusability Assessment

| Feature | Status | Notes |
|---------|--------|-------|
| `Project` entity schema | ✅ Reusable with Modification | Add `tenantId`, proper audit fields, FK to `Organisation` |
| Project-to-user mapping | ✅ Reusable with Modification | Currently via `Set<String> projectList`; use a proper join table |
| `ProjectRepo` | ✅ Directly Reusable | Standard Spring Data |
| Organisation hierarchy | ⚠️ Partially Implemented | `Organiz.java` (1,830 bytes), `SubOrganisation.java` exist; enforcement incomplete in services |
| App registration concept | ✅ Concept Reusable | "Project" IS the "Application" in the legacy model |

---

## 8. Test Repository Module

### 8.1 Evidence

**Entity: [TestCase.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/entity/TestCase.java)** — Table: `test_case`
```
id, tcId (unique, e.g. "TC_001"), projectName, userStory, testCaseName,
environment, testType, testMode, label, createdBy, testerName, priority,
browserType, estimatedTime, preconditions, createdDate, projectId,
usId, map_approval, mapId, comments, testCaseExeStatus, testCaseExeId,
-- @OneToMany: List<TestStep>
```

**Entity: `TestStep.java`** — Table: `test_step`
- Steps linked via `@ManyToOne` back to `TestCase`

**Entity: [TestSuite.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/entity/TestSuite.java)** — Table: `test_suite`
```
id, testSuiteID, testSuiteName, priority, createdBy, executedBy,
executedOn, projectId, createdDate, description, status, tType
```

**Entity: `TestSuiteTestCaseMap.java`** — Manages Suite to TestCase relationships

**Entity: `ExecutionPlan.java`** — Execution plan grouping

**Service: [TestCaseService.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/service/TestCaseService.java)** — **99,304 bytes / 2,483 lines**
Covers: CRUD for test cases, test steps, test suites, mapping, Excel import (Apache POI), PDF export (PDFBox + JFreeChart), linking to user stories.

**Repositories confirmed:**
- `TestCaseRepo.java`, `TestCaseRepository.java` (with native queries, 5,215 bytes)
- `TestStepRepo.java`, `TestStepRepository.java`
- `TestSuiteRepo.java`, `TestSuiteRepository.java`
- `TestSuitTestCaseMapRepo.java` (7,316 bytes — extensive query set)
- `TestExecutionRepo.java`

> [!WARNING]
> **Duplicate entity classes confirmed:** Both `entity/TestCase.java` (table `test_case`) and `entity/TestCases.java` exist. Similarly `TestStep.java` and `TestSteps.java` both exist. These are orphaned/legacy duplicates that MUST be consolidated before migration.

### 8.2 Reusability Assessment

| Feature | Status | Notes |
|---------|--------|-------|
| `TestCase` entity schema | ✅ Reusable with Modification | Clean fields; add `@CreatedDate`, `@LastModifiedDate`, proper FK to Project |
| `TestStep` entity | ✅ Reusable with Modification | Add `stepOrder` field (currently absent) |
| `TestSuite` entity | ✅ Reusable with Modification | Good base; add proper FK constraints |
| Test case CRUD service methods | ✅ Reusable with Modification | Buried inside 2,483-line service; must extract and refactor |
| Excel Import (Apache POI) | ✅ Reusable with Modification | Logic is functional; wrap in proper DTO and error handling |
| PDF Report generation | ✅ Reusable with Modification | PDFBox + JFreeChart code is functional |
| `TestCases.java` / `TestSteps.java` | ❌ Do Not Reuse | Duplicate/orphaned entities — must be consolidated |
| `TestSuiteTestCaseMap` | ✅ Reusable with Modification | Good many-to-many mapping concept |

---

## 9. Test Execution Engine

### 9.1 Evidence

**Manual Execution Flow:**
- Frontend: `Testcases_execution.html` (39,794 bytes)
- Backend: `TestExecutionService.java` (10,441 bytes)
- Entities: `TestExecution.java`, `TestStepResult.java`, `TestCaseResult.java`
- Flow: Step-by-step status updates (Pass/Fail/Skip) saved to `test_step_result` table; `test_case.TestCaseExeStatus` updated on completion

**Automated Execution Flow:**
- Frontend: `Automation.html` (205,779 bytes)
- Service: [ExecuteJarFileService.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/service/ExecuteJarFileService.java)

```java
// ExecuteJarFileService.java L20-21 — CONFIRMED IMPLEMENTATION
ProcessBuilder builder = new ProcessBuilder(
    "cmd.exe", "/c", "cd " + executeJarFileDto.getPath() +
    "&& java -jar " + executeJarFileDto.getJarFileName() + ".jar");
```

> [!IMPORTANT]
> There is **NO embedded Selenium, Playwright, or test runner** inside the backend source. Automation execution is entirely via `ProcessBuilder` invoking external pre-built JAR files. The backend acts only as an **orchestrator**. Automation results are stored in `automation_result` table via `AutomationResults.java`.

**Entity: [AutomationResults.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/entity/AutomationResults.java)** — Table: `automation_result`
```
id, projectName, testCaseId, testCaseName, testDescription, testScriptName,
environment, deviceName, operatingSystem, version, browser, status,
testCaseRemarks, executedAt, dateExecuted
```

Other automation result entities: `AvokaResults.java` (4,920 bytes), `JmeterResults.java` (2,917 bytes) — JMeter performance test result ingestion is implemented.

`ExecutionPlan.java` + `ExecutionPlanService.java` (49,063 bytes) — manages plan creation, suite assignment, and batch execution orchestration.

### 9.2 Reusability Assessment

| Feature | Status | Notes |
|---------|--------|-------|
| Manual execution flow (step-by-step) | ✅ Reusable with Modification | Entity/service pattern is sound; needs API cleanup |
| Execution Plan orchestration | ✅ Reusable with Modification | Large service (49K) but logic is relevant |
| `AutomationResults` entity | ✅ Reusable with Modification | Good schema; add FK to execution plan |
| `ExecuteJarFileService` (ProcessBuilder) | ⚠️ Reusable with Caution | Windows-only (`cmd.exe /c`); must be made OS-agnostic; add async execution |
| JMeter result ingestion | ✅ Reusable with Modification | `JmeterResults.java` and repo exist |
| `AvokaResults` entity | ❓ Assess Separately | Context-specific; determine if Avoka is still relevant |

---

## 10. Automation Module

### 10.1 Evidence

**Service: [WebAtomationService.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/service/WebAtomationService.java)** (10,235 bytes)
```java
// Line 28 — Hardcoded server path
private static final String SERVER_DIRECTORY = "C://webAutomation";
```
- `createFolder(String folderPath)` — creates directories under `C://webAutomation/`
- `updateFolder(oldName, newName)` — renames directories
- `deleteFolder(folderName)` — deletes directories

Other confirmed services:
- `GroovyFileService.java` (5,072 bytes) — Groovy script management
- `YMLFileService.java` (4,625 bytes) — YAML file management
- `AzureRepoCheckoutService.java` (3,466 bytes) — Azure DevOps checkout
- `GithubCGTCheckoutService.java` (3,338 bytes) — GitHub checkout
- `GitUploadService.java` (6,341 bytes) — Git push operations via JGit
- `JenkinsBuildWithParamService.java` (3,005 bytes) — Jenkins CI trigger
- `JenkinsDeleteBuildService.java` (2,942 bytes) — Jenkins build deletion
- `JenkinsJobCloneService.java` (2,905 bytes) — Jenkins job cloning
- `VisualAutoInitService.java`, `VisualAutoCreateRefService.java`, `VisualAutoApproveDeclineService.java`
- `CSVService.java` (6,261 bytes), `ExcelService.java` (6,444 bytes), `UnzipService.java` (6,838 bytes)

### 10.2 Reusability Assessment

| Feature | Status | Notes |
|---------|--------|-------|
| Folder/script management | ⚠️ Reusable with Modification | Remove hardcoded `C://webAutomation`; use configurable paths |
| JAR execution via ProcessBuilder | ⚠️ Reusable with Caution | Make async, cross-platform |
| Git integration (JGit) | ✅ Reusable with Modification | Upgrade JGit version; wrap in proper service |
| Jenkins CI integration | ✅ Reusable with Modification | HTTP-based Jenkins API calls; portable |
| CSV/Excel service | ✅ Reusable with Modification | Standard Apache POI; portable |
| Unzip service | ✅ Reusable with Modification | Standard Java NIO; portable |
| Azure/GitHub checkout services | ✅ Reusable with Modification | CI/CD pipeline integration |

---

## 11. Dashboard & Reporting

### 11.1 Evidence

**Backend services confirmed:**
- [DashBoardReportsService.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/service/DashBoardReportsService.java) (23,492 bytes) — aggregates task progress, burndown, resource utilization, schedule variance
- `DashboardUserStoryService.java` (5,990 bytes) — user story counts by status
- `DashboardTestCaseService.java` (6,557 bytes) — test case pass/fail counts
- `DashboardAutomationCountService.java` (1,862 bytes) — automation run counts
- `DashboardWebAndMobileStatusCountService.java` (2,159 bytes) — web/mobile test status
- `DashboardExecutionDetailsService.java` (1,927 bytes) — execution detail aggregation
- `KanbanDashboardService.java` (41,750 bytes) — Kanban board data
- `BurndownService.java` (1,577 bytes) — Sprint burndown calculations
- `PerformanceDashboardService.java` (1,673 bytes) — JMeter performance results display
- `PdfExporter.java` (5,510 bytes) — PDF generation
- `ExcelHelper.java` (36,411 bytes) — Excel export helper
- `TracibilityService.java` (914 bytes) — traceability matrix

### 11.2 Reusability Assessment

| Feature | Status | Notes |
|---------|--------|-------|
| Dashboard aggregation services | ✅ Reusable with Modification | Logic is functional; refactor into smaller focused services |
| Burndown calculations | ✅ Reusable with Modification | Portable math logic |
| Excel export (ExcelHelper) | ✅ Reusable with Modification | 36K of proven export logic |
| PDF export (PdfExporter) | ✅ Reusable with Modification | PDFBox implementation; portable |
| Kanban service (41K) | ⚠️ Reusable with Caution | Very large; audit what is needed for Phase 1 |
| Traceability matrix | ✅ Reusable with Modification | Simple service; portable |

---

## 12. Defect Management

### 12.1 Evidence

**Entity: [DefectMgt.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/entity/DefectMgt.java)** — Table: `defect_management`
```
id, defectID, title, category, reproductility, status, updatedDate,
reportedBy, assignedTo, featureList, severity, priority, description,
stepToReproduce, usID, tcID, projectID, file (byte[]), fileName, updatedBy
```

**Service:** `DefectMgtService.java` (**65,261 bytes** — second-largest service)
Covers: CRUD, file attachment (stored as byte[] in DB), history tracking, status workflow, email notifications on defect state changes.

**Entity: `DefectHistory.java`** — `defect_history` table
**Repository: `DefectMgtRepo.java`** (2,608 bytes) — custom queries by projectID, status, assignee

### 12.2 Reusability Assessment

| Feature | Status | Notes |
|---------|--------|-------|
| `DefectMgt` entity | ✅ Reusable with Modification | Move file storage from blob column to object storage |
| Defect CRUD service | ✅ Reusable with Modification | Core logic reusable; refactor 65K service into smaller units |
| Defect history tracking | ✅ Reusable with Modification | `DefectHistory` entity pattern is correct |
| File attachment (blob) | ❌ Do Not Reuse as-is | DB blob storage is an anti-pattern; use S3/MinIO |
| Email notifications | ✅ Reusable with Modification | `EmailNotificationService.java` (16,804 bytes) is usable |

---

## 13. Frontend / UI Layer

### 13.1 Evidence

```
src/main/resources/public/FlexibPlus_V1/  (87 HTML files)
  ProjectManagementDashboard.html   = 497,707 bytes
  Automation.html                   = 205,779 bytes
  userstories.html                  = 339,955 bytes
  Defect-tacker.html                = 104,831 bytes
  dashboard.html                    = 94,445 bytes
  css/, js/, images/
```

**Technology:** Vanilla HTML5 + jQuery AJAX + Bootstrap
**Communication:** All API calls via `$.ajax()` / `$.get()` / `$.post()` targeting REST endpoints
**Multiple backup/versioned copies exist** (`FlexibPlus_V1`, `FlexibPlus_V1 47`, `FlexibPlus_V1_30042006`) indicating manual version management without proper Git branching.

### 13.2 Reusability Assessment

| Aspect | Status | Notes |
|--------|--------|-------|
| HTML page structure | ❌ Not Reusable | Monolithic pages 100K–500K; rebuild as component-based SPA |
| jQuery AJAX patterns | ❌ Not Reusable | Use React/Vue/Angular with Axios |
| Business logic (embedded in HTML) | ❌ Not Reusable | Mix of DOM manipulation and business logic |
| CSS/Design system | ❌ Not Reusable | Build new design system |
| API endpoint contracts | ✅ Reference Value Only | Use as API contract documentation input for new backend |

---

## 14. External Integrations

| Integration | Service File | Status |
|-------------|-------------|--------|
| Jenkins CI/CD | `JenkinsBuildWithParamService.java`, `JenkinsDeleteBuildService.java`, `JenkinsJobCloneService.java` | ✅ Reusable with Modification |
| Azure DevOps Git | `AzureRepoCheckoutService.java` | ✅ Reusable with Modification |
| GitHub (JGit) | `GithubCGTCheckoutService.java`, `GitUploadService.java` | ✅ Reusable with Modification |
| Gmail SMTP | `Mailer.java`, `EmailNotificationService.java` | ✅ Reusable with Modification (externalize credentials) |
| Microsoft Teams | `msTeamConnector` field on `Project` entity | ⚠️ Partially implemented (field exists, no service confirmed) |
| JMeter result ingestion | `JmeterResults.java`, `JmeterResultsPerfCountRepo.java` | ✅ Reusable with Modification |
| ChatBot (internal) | `ChatBotService.java` (16,418 bytes), `ChatBotController.java` (17,977 bytes) | ✅ Assess Separately |
| Generative AI (Gemini/OpenAI) | `GenerativeAI.html`, `GenerativeAI - gemini.html` | Frontend only; no confirmed backend service |

---

## 15. Reusability Scorecard

| Category | Score | Classification |
|----------|-------|---------------|
| Spring Boot framework structure | 90% | ✅ Directly Reusable |
| Security (JwtUtils, AuthTokenFilter, UserDetailsImpl) | 70% | ⚠️ Reusable with Modification |
| Auth endpoints (login/signup/password) | 65% | ⚠️ Reusable with Modification |
| User entity & Role entity | 75% | ⚠️ Reusable with Modification |
| Project entity & repo | 80% | ✅ Reusable with Modification |
| TestCase entity + TestStep entity | 80% | ✅ Reusable with Modification |
| TestSuite entity | 75% | ✅ Reusable with Modification |
| Test execution logic | 70% | ⚠️ Reusable with Modification |
| Automation JAR execution | 40% | ⚠️ Reusable with Caution (OS-specific) |
| Dashboard/report services | 65% | ⚠️ Reusable with Modification |
| Defect management | 60% | ⚠️ Reusable with Modification |
| External integrations (Jenkins/Git) | 70% | ✅ Reusable with Modification |
| Frontend HTML/JS/CSS | 0% | ❌ Do Not Reuse |
| Database (MySQL schema) | 60% | ⚠️ Requires PostgreSQL Migration |
| WebSecurityConfig authorization | 10% | ❌ Must Rebuild (critical security flaw) |
| FlexibController (God class) | 10% | ❌ Must Be Decomposed |

---

## 16. Phase 1 Requirement Mapping

### REQ-01: Login & Authentication

| Requirement | Legacy Coverage | Code Reference | Gap |
|-------------|----------------|---------------|-----|
| Username/password login | ✅ Exists | `AuthController.java` L105 `/api/auth/signin` | Project-list validation tightly coupled to login; must decouple |
| JWT token issuance | ✅ Exists | `JwtUtils.java` L60-98 | Secret in plaintext; short expiry; fix required |
| Refresh token | ✅ Exists | `AuthController.java` L346, `RefreshTokenService.java` | Very short 4-min expiry; must increase |
| Logout (token invalidation) | ✅ Exists | `AuthController.java` L592 `/api/auth/signout` | Only clears cookie; no server-side token blacklist |
| Account status check | ✅ Exists | `UserDetailsImpl.isEnabled()` L122 | `isactive == "ACTIVE"` check works |
| Password reset | ⚠️ Partial | `AuthController.java` L255 | No email OTP flow; direct reset without verification |
| MFA / 2FA | ❌ Not Present | — | Must implement from scratch |

### REQ-02: User Registration

| Requirement | Legacy Coverage | Code Reference | Gap |
|-------------|----------------|---------------|-----|
| Register new user | ✅ Exists | `AuthController.java` L495 `/api/auth/signup` | Hardcoded default password `flexib@123`; no email verification |
| Role assignment | ✅ Exists | `AuthController.java` L515-527 | Roles must pre-exist in DB |
| Unique username/email enforcement | ✅ Exists | `AuthController.java` L497-503 | Works correctly |
| User profile update | ✅ Exists | `AuthController.java` L431 `/api/auth/UpdateSignUpUser` | Partial fields only |
| User activation/deactivation | ✅ Exists | `AuthController.java` L413 `/api/auth/UserStatus` | Toggle Active/InActive |
| Email notification on registration | ⚠️ Partial | `Mailer.java` (commented out at L553) | Email send code exists but is commented out |

### REQ-03: Application / Project Registration

| Requirement | Legacy Coverage | Code Reference | Gap |
|-------------|----------------|---------------|-----|
| Create application/project | ✅ Exists | Endpoints in `FlexibController.java`; `Project` entity | Must extract from god controller |
| Assign users to project | ✅ Exists | `User.projectList` (Set of String), signup flow | Many-to-many should be explicit join table |
| Organisation hierarchy | ⚠️ Partial | `Organiz.java`, `SubOrganisation.java` entities exist | Enforcement is incomplete in services |
| Project status management | ✅ Exists | `Project.projStatus`, `Project.status_DB` fields | Two status fields — inconsistent; must consolidate |

### REQ-04: PostgreSQL Migration

| Requirement | Legacy Coverage | Gap |
|-------------|----------------|-----|
| PostgreSQL driver | ❌ Not Present | Add `postgresql` JDBC dependency |
| PostgreSQL dialect | ❌ Not Present | Replace `MySQL5Dialect` |
| Schema migration tooling | ❌ Not Present | Add Flyway; write migration scripts from `ddl-auto=update` |
| Native query audit | ❌ Needed | Scan all `@Query(nativeQuery=true)` for MySQL-specific syntax |

### REQ-05: Application Discovery / AKB

| Requirement | Legacy Coverage | Gap |
|-------------|----------------|-----|
| Application catalog | ✅ Partial | `Project` entity covers application metadata | Add `applicationUrl`, `techStack`, `apiDocUrl` fields |
| Module/feature mapping | ⚠️ Partial | `ModuleMaster.java`, `FeatureDetails.java` entities exist | Link them properly to projects |
| Knowledge base articles | ❌ Not Present | No knowledge base entity exists | Must implement from scratch |

### REQ-06: Security Hardening

| Requirement | Legacy Coverage | Gap |
|-------------|----------------|-----|
| API authorization enforcement | ❌ Critical Gap | All `/api/flexibautomation/**` is `permitAll()` | MUST fix `WebSecurityConfig.java` |
| Secret management | ❌ Critical Gap | `jwtSecret = bezKoderSecretKey` in properties file | Use environment variable / Vault |
| CORS policy | ⚠️ Partial | CORS enabled but no explicit origins configured | Define allowed origins |
| Input validation | ⚠️ Partial | `@Valid` on some DTOs | Audit all endpoints |
| HTTPS enforcement | ❌ Not Present | No SSL config in application.properties | Must configure TLS |

### REQ-07: Test Repository

| Requirement | Legacy Coverage | Code Reference | Gap |
|-------------|----------------|---------------|-----|
| Create test case | ✅ Exists | `TestCaseService.java` | Extract from 2,483-line service |
| Test steps management | ✅ Exists | `TestStep` entity, service methods | Add step ordering field |
| Test suite management | ✅ Exists | `TestSuite` entity, `TestSuiteService.java` (12,865 bytes) | Clean FK constraints |
| Excel import | ✅ Exists | Apache POI in `TestCaseService.java` | Wrap in clean API |
| Test case search/filter | ✅ Exists | `TestCaseRepository.java` with Specification pattern | Reusable |
| Link to user stories | ✅ Exists | `tcId` to `usId` on `TestCase` | Works; clean up |
| Version control for test cases | ❌ Not Present | No history/versioning entity for test cases | Implement |

### REQ-08: Dashboard

| Requirement | Legacy Coverage | Code Reference | Gap |
|-------------|----------------|---------------|-----|
| Test execution summary | ✅ Exists | `DashboardTestCaseService.java`, `DashboardExecutionDetailsService.java` | Port to new data model |
| Automation status | ✅ Exists | `DashboardAutomationCountService.java` | Reusable |
| Defect summary | ✅ Exists | Aggregation in `DashBoardReportsService.java` | Reusable |
| Task/project progress | ✅ Exists | `KanbanDashboardService.java`, `BurndownService.java` | Reusable |
| Charts/graphs | ✅ Exists | JFreeChart in backend; rendered as server-side images | Consider moving to frontend charting library |

### REQ-09: Automation Module

| Requirement | Legacy Coverage | Code Reference | Gap |
|-------------|----------------|---------------|-----|
| Script/JAR management | ✅ Exists | `WebAtomationService.java` | Remove hardcoded `C://webAutomation` path |
| Trigger automated test | ✅ Exists | `ExecuteJarFileService.java` | Make async; cross-platform |
| Result capture | ✅ Exists | `AutomationResults` entity + repo | Reusable |
| Test data (Excel) upload | ✅ Exists | `ExcelService.java` | Reusable |
| Jenkins CI integration | ✅ Exists | Jenkins services | Reusable |

### REQ-10: Reports

| Requirement | Legacy Coverage | Code Reference | Gap |
|-------------|----------------|---------------|-----|
| PDF report | ✅ Exists | `PdfExporter.java` (PDFBox + JFreeChart) | Upgrade to newer PDF library if needed |
| Excel report | ✅ Exists | `ExcelHelper.java` (36,411 bytes) | Reusable |
| Traceability matrix | ✅ Exists | `TracibilityService.java` | Expand scope |
| Automation reports | ✅ Exists | `Reports_Automation.html` + backend data | Rebuild frontend; reuse backend data |

---

## 17. Risk Register

| Risk | Severity | Evidence | Mitigation |
|------|----------|----------|------------|
| All business APIs are unauthenticated | 🔴 Critical | `WebSecurityConfig.java` L77 `permitAll()` on all `/api/flexibautomation/**` | Fix security config as Day 1 task |
| JWT secret in plaintext config | 🔴 Critical | `application.properties` L18 `bezKoderSecretKey` | Move to env variable / secrets manager |
| God controller (5,524 lines) | 🔴 High | `FlexibController.java` — single class for all business routes | Decompose into feature controllers |
| No schema migration tooling | 🟠 High | `ddl-auto=update`, no Flyway/Liquibase | Add Flyway before PostgreSQL migration |
| Hardcoded file paths (Windows) | 🟠 High | `WebAtomationService.java` L28 `C://webAutomation` | Use configurable path properties |
| Default password hardcoded | 🟠 High | `AuthController.java` L510 `"flexib@123"` | Generate random password or require user-set |
| Duplicate entity classes | 🟡 Medium | `TestCase.java` vs `TestCases.java`, `TestStep.java` vs `TestSteps.java` | Consolidate before migration |
| No unit/integration tests | 🟡 Medium | Test coverage not confirmed in source | Add test coverage before refactoring |
| Short JWT expiry (3 min) | 🟡 Medium | `jwtExpirationMs= 180000` | Increase to 15-60 min; use refresh token |
| MySQL-specific native queries | 🟡 Medium | Various `@Query(nativeQuery=true)` annotations | Audit before PostgreSQL migration |

---

## 18. Recommended Migration Strategy

### Phase 1A — Foundation (Week 1-2)
1. **Fork the codebase** and create a new Spring Boot 3.x project (Java 17/21)
2. **Fix the security critical flaw** — uncomment `anyRequest().authenticated()`, add proper role guards per endpoint
3. **Migrate JWT secret** to environment variable
4. **Decompose `FlexibController.java`** into feature-specific controllers:
   - `ProjectController`, `TestCaseController`, `TestSuiteController`, `ExecutionController`, `DefectController`, `DashboardController`, `AutomationController`
5. **Add Flyway** for schema migrations; generate initial baseline migration from existing `ddl-auto=update` state

### Phase 1B — Database Migration (Week 2-3)
1. Replace MySQL driver with PostgreSQL
2. Update Hibernate dialect
3. Run Flyway baseline on PostgreSQL
4. Audit and fix native queries for PostgreSQL compatibility

### Phase 1C — Service Refactoring (Week 3-6)
1. Split `TestCaseService.java` (99K) into: `TestCaseService`, `TestStepService`, `TestSuiteService`, `TestImportService`, `TestReportService`
2. Split `DefectMgtService.java` (65K) into: `DefectService`, `DefectHistoryService`, `DefectAttachmentService`
3. Consolidate duplicate entities (`TestCase`/`TestCases`, `TestStep`/`TestSteps`)
4. Add `@CreatedDate`, `@LastModifiedDate` audit fields to all entities
5. Remove hardcoded file paths; externalize to `application.properties`
6. Move file storage from DB blobs to S3/MinIO

### Phase 1D — Frontend Rebuild
1. Build new React/Vue/Next.js SPA from scratch
2. Use backend REST APIs as contracts (reference legacy HTML pages for UX flows only)
3. Implement proper state management and JWT token handling in frontend

### Phase 1E — Testing & Hardening
1. Add unit tests for all refactored services
2. Add integration tests for all REST endpoints
3. Configure HTTPS/TLS
4. Define and enforce CORS policy
5. Complete input validation audit across all endpoints

---

## Appendix A — File Reference Index

| File | Path | Size | Role |
|------|------|------|------|
| [FlexibController.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/controller/FlexibController.java) | `controller/` | 231,206 B | God controller — ALL business routes |
| [AuthController.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/controller/AuthController.java) | `controller/` | 30,870 B | Auth endpoints |
| [TestCaseService.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/service/TestCaseService.java) | `service/` | 99,304 B | Test repository business logic |
| [KanbanService.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/service/KanbanService.java) | `service/` | 124,399 B | Kanban board logic |
| [DefectMgtService.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/service/DefectMgtService.java) | `service/` | 65,261 B | Defect management logic |
| [ExecutionPlanService.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/service/ExecutionPlanService.java) | `service/` | 49,063 B | Execution plan logic |
| [ExcelHelper.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/service/ExcelHelper.java) | `service/` | 36,411 B | Excel export |
| [WebSecurityConfig.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/security/WebSecurityConfig.java) | `security/` | 3,389 B | Spring Security config |
| [JwtUtils.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/security/jwt/JwtUtils.java) | `security/jwt/` | 5,095 B | JWT generation/validation |
| [AuthTokenFilter.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/security/jwt/AuthTokenFilter.java) | `security/jwt/` | 2,934 B | JWT request filter |
| [User.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/Models/User.java) | `Models/` | 4,138 B | User entity |
| [Role.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/Models/Role.java) | `Models/` | 2,122 B | Role entity |
| [TestCase.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/entity/TestCase.java) | `entity/` | 8,506 B | Test case entity |
| [Project.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/entity/Project.java) | `entity/` | 6,138 B | Project entity |
| [DefectMgt.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/entity/DefectMgt.java) | `entity/` | 7,025 B | Defect entity |
| [AutomationResults.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/entity/AutomationResults.java) | `entity/` | 5,175 B | Automation results entity |
| [ExecuteJarFileService.java](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/java/com/infotech/flexibautomation/service/ExecuteJarFileService.java) | `service/` | 1,910 B | External JAR runner |
| [application.properties](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/resources/application.properties) | `resources/` | 1,664 B | App configuration |
| [pom.xml](file:///c:/Users/10004463/Desktop/Flexib_x/pom.xml) | root | 7,375 B | Maven build descriptor |

---

*End of Report*
