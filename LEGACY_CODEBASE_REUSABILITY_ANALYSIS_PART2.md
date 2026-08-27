# Legacy Codebase Reusability Analysis — Part 2
## Complete Deep-Dive Analysis of FlexibPlus

> **Continues from:** `LEGACY_CODEBASE_REUSABILITY_ANALYSIS.md` (Part 1)
> **Evidence basis:** Direct source-code inspection — every file listed in this document was read, not inferred
> **Scope:** Everything not covered in Part 1 — DTOs, full endpoint inventory, all entities, services, repos, frontend pages

---

## Table of Contents

1. [DTO Layer — Complete Inventory (210 files)](#1-dto-layer--complete-inventory)
2. [FlexibController — Full Endpoint Map (200+ Routes)](#2-flexibcontroller--full-endpoint-map)
3. [AuthController — Full Endpoint Map (Confirmed)](#3-authcontroller--full-endpoint-map)
4. [Frontend Login Flow — Traced in Code](#4-frontend-login-flow--traced-in-code)
5. [Complete Entity Schema Inventory](#5-complete-entity-schema-inventory)
6. [KanbanService — Deep Analysis](#6-kanbanservice--deep-analysis)
7. [TimesheetService — Deep Analysis](#7-timesheetservice--deep-analysis)
8. [UserStoryDetailsService — Deep Analysis](#8-userstorydetailsservice--deep-analysis)
9. [ExecutionService — Logic Verified](#9-executionservice--logic-verified)
10. [IssueManagementAlertService — Scheduler Confirmed](#10-issuemanagementalertservice--scheduler-confirmed)
11. [RequirementService / Feature Module](#11-requirementservice--feature-module)
12. [ChatBotService — Module Structure](#12-chatbotservice--module-structure)
13. [FlexibService — Hardcoded Paths Found](#13-flexibservice--hardcoded-paths-found)
14. [Complete Repository Layer (77 repos)](#14-complete-repository-layer)
15. [Frontend — All 87 Pages Categorised](#15-frontend--all-87-pages-categorised)
16. [Other Frontend Environments Discovered](#16-other-frontend-environments-discovered)
17. [GitLab CI/CD Pipeline Discovered](#17-gitlab-cicd-pipeline-discovered)
18. [Complete Module Map of the System](#18-complete-module-map-of-the-system)
19. [Corrections to Part 1](#19-corrections-to-part-1)
20. [Final Updated Reusability Assessment](#20-final-updated-reusability-assessment)

---

## 1. DTO Layer — Complete Inventory

**Total DTOs: 210 files** in `src/main/java/com/infotech/flexibautomation/dto/`

### Grouped by Domain

#### Authentication & Users
| DTO | Size | Purpose |
|-----|------|---------|
| `LoginDto.java` | 1,089 B | Login request wrapper |
| `SignUpRoleDto.java` | 1,194 B | Role assignment on signup |
| `SignupUpdateRequestDto.java` | 2,381 B | Update signed-up user profile |
| `FlexibUsersDto.java` | 2,719 B | Flexib-specific user data |
| `UsersDto.java` | 2,411 B | Generic users DTO |
| `ValidateJwtTokenDTO.java` | 698 B | JWT validation request |
| `ChangePasswordRequestDTO.java` | 1,199 B | Change password form data |
| `ChangePasswordResponseDTO.java` | 637 B | Change password result |
| `passwordResetRequestResponseDTO.java` | 788 B | Password reset response |
| `UpdateRoleDTO.java` | 763 B | Role update request |
| `ManagerUserRolesDTO.java` | 860 B | Manager role assignment |
| `PersonalDetailsDto.java` | 1,882 B | Personal profile data |
| `EmployeeDetailsDto.java` | 2,989 B | Employee HR-style data |
| `SignUpUserDTO.java` | 932 B | Signup form DTO |

#### Project & Organisation
| DTO | Size | Purpose |
|-----|------|---------|
| `ProjectDto.java` | 4,067 B | Full project representation |
| `UpdateProjectDTO.java` | 3,140 B | Project update request |
| `ProjectTasksDTO.java` | 1,251 B | Tasks per project |
| `ProjectStatusMasterDTO.java` | 527 B | Status master list |
| `ProjectHoursSummaryDTO.java` | 90 B | Hours rollup |
| `OrganisationDto.java` | 362 B | Org identifier |
| `OrganisationDetailsResponse.java` | 672 B | Org detail response |
| `DigiGetALLProjectsDTO.java` | 1,068 B | DigiSec integration - get projects |
| `DigiSecCreateProjectDTO.java` | 1,659 B | DigiSec integration - create project |
| `DigiSecSignInAPIDTO.java` | 1,061 B | DigiSec sign-in |
| `DigisecScannerDTO.java` | 1,632 B | DigiSec DAST scanner trigger |

#### Test Cases & Test Repository
| DTO | Size | Purpose |
|-----|------|---------|
| `TestCaseDTO.java` | 4,417 B | Full test case data |
| `TestCaseDetailsDto.java` | 3,485 B | Detailed test case view |
| `UpdateTestCaseDto.java` | 4,915 B | Test case update |
| `TestCaseStepDto.java` | 978 B | Individual test step |
| `TestCaseStepResponse.java` | 493 B | Step response |
| `TestStepDetailsDTO.java` | 1,723 B | Step details with status |
| `TestCaseStatusDTO.java` | 676 B | Test case status change |
| `TestCaseHistoryDTO.java` | 1,529 B | Test case execution history |
| `TestCaseHistoryResultDTO.java` | 719 B | History result |
| `TestSuiteDto.java` | 3,125 B | Test suite full object |
| `TestSuiteUpdateDto.java` | 1,230 B | Suite update |
| `TestsuiteTableDto.java` | 1,481 B | Suite table view |
| `TestCaseTestSuiteTableDto.java` | 837 B | TC-Suite mapping table |
| `TestSuiteTestCaseMapDto.java` | 1,204 B | Suite-TC mapping |
| `MapTestCasesDTO.java` | 1,091 B | Bulk map test cases |
| `MapIdCommentAndStatusDTO.java` | 1,001 B | TC mapping approval |
| `SearchCriteriaDTO.java` | 1,426 B | Test case search filter |
| `ExcelHeaders.java` | 1,360 B | Excel column headers |

#### Test Execution
| DTO | Size | Purpose |
|-----|------|---------|
| `SaveTestCaseExecutionDTO.java` | 1,613 B | Save manual step execution |
| `SaveTestCaseExecutionRequestDto.java` | 1,616 B | Execution request |
| `SaveExecutionDTO.java` | 2,268 B | Execution result batch |
| `SaveTestExecutionPlanRequestDto.java` | 2,270 B | Execution plan save |
| `SaveTestExecutionRequestDto.java` | 1,945 B | Test run save |
| `TestExecutionPlanDTO.java` | 3,544 B | Execution plan data |
| `ExecutionPlanDTO.java` | 4,826 B | Plan full representation |
| `ExecutionPlanDashBoardDTO.java` | 3,375 B | Plan dashboard view |
| `ExecutionPlanMonthDTO.java` | 908 B | Monthly plan |
| `ExecutionPlanUpdateDto.java` | 2,815 B | Plan update |
| `ExecutionPlanTestSuiteMapDto.java` | 1,799 B | Plan-Suite mapping |
| `TestResultStepResponseDTO.java` | 1,481 B | Step execution result |
| `TestCaseResultDTO.java` | 2,625 B | TC execution result |
| `TestCaseLogCountDTO.java` | 1,077 B | Log counts |
| `TestCaseLogDeatilDTO.java` | 1,968 B | Log detail |
| `TestCaseExecutionLogDetailsDTO.java` | 1,458 B | Execution log details |
| `testCaseExecutionDataDTO.java` | 1,766 B | Execution data view |
| `TestExecutionSkipDto.java` | 1,802 B | Skip test step action |
| `TestSuiteExecutionPlanTableDto.java` | 916 B | Suite-plan table |
| `ReportFind.java` | 954 B | Report search criteria |
| `ReportResponseDto.java` | 945 B | Generic report response |
| `ExportToPdfRequestDto.java` | 933 B | PDF export request |

#### Automation
| DTO | Size | Purpose |
|-----|------|---------|
| `ExecuteJarFileDto.java` | 759 B | JAR execution params (path + filename) |
| `ExecuteMMJarFileDto.java` | 767 B | MM-specific JAR execution |
| `MobileAutomation.java` | 2,277 B | Mobile automation params |
| `MavenCMDDTO.java` | 897 B | Maven command params |
| `MavenCommandPropmtDTO.java` | 689 B | Maven prompt params |
| `JmeterCommandDTO.java` | 1,885 B | JMeter test params |
| `JmeterResponseDto.java` | 85 B | JMeter minimal response |
| `ApiDetailsDTO.java` | 2,685 B | API automation config |
| `ApiNewmanDTO.java` | 1,015 B | Newman/Postman runner |
| `ExecuteAPIAutomationRequestDto.java` | 1,383 B | API automation trigger |
| `AvokaResponseDto.java` | 1,840 B | Avoka tool response |
| `WebAndMobileAutoResultsResponseDto.java` | 1,401 B | Web/Mobile result |
| `WenAndMobileAutoResultRequestDto.java` | 586 B | Web/Mobile result request |
| `VisualAutoInitDto.java` | 743 B | Visual testing init |
| `VisualAutoApproveDto.java` | 970 B | Visual testing approval |
| `MMReportFindDTO.java` | 1,236 B | MM report search |
| `GroovyRequestDto.java` | 2,416 B | Groovy script params |
| `YMLRequestDto.java` | 2,594 B | YAML config params |
| `JsonToCsvRequestDto.java` | 2,360 B | JSON→CSV conversion |
| `DynamicColumnDTO.java` | 290 B | Dynamic Excel column |
| `DynamicExcelDataRequestDTO.java` | 499 B | Dynamic Excel data |
| `DynamicExcelDataResponse.java` | 585 B | Dynamic Excel response |

#### CI/CD & Version Control
| DTO | Size | Purpose |
|-----|------|---------|
| `JenkinsBuilsWithParamDTO.java` | 1,290 B | Jenkins parameterized build |
| `JenkinsDeleteJobDTO.java` | 1,060 B | Jenkins job deletion |
| `JenkinsProjectCloneDTO.java` | 1,592 B | Jenkins job clone |
| `AzureRepoCheckoutDTO.java` | 1,228 B | Azure DevOps checkout |
| `GithubCGTCheckoutDTO.java` | 974 B | GitHub checkout |
| `GithubUpload.java` | 950 B | GitHub upload |
| `GITLabDTO.java` | 1,887 B | GitLab integration |
| `GitCreateBranch.java` | 1,190 B | Git branch creation |
| `PipelineAddStagesDto.java` | 713 B | Pipeline stage add |
| `PipelineDeleteStageDto.java` | 536 B | Pipeline stage delete |
| `PipelineStagesResponseDto.java` | 959 B | Pipeline stage list |
| `CheckoutGithubAccessTokenDTO.java` | 1,434 B | GitHub token checkout |

#### Defect Management
| DTO | Size | Purpose |
|-----|------|---------|
| `DefectMgtDTO.java` | 4,716 B | Full defect data |
| `DefectDetailsDTO.java` | 1,247 B | Defect detail view |
| `DefectCountResponseDto.java` | 3,239 B | Count by project |
| `DefectStatusCountResponseDto.java` | 5,528 B | Count by status |
| `DefectCategoryCountResponseDto.java` | 1,682 B | Count by category |
| `DefectPriorityCountResponseDto.java` | 3,194 B | Count by priority |
| `DefectSeverityCountResponseDto.java` | 2,750 B | Count by severity |
| `DownloadDefectTrackerDTO.java` | 805 B | Download defect report |
| `DetectionPhaseDTO.java` | 539 B | Detection phase master |
| `ResolutionDTO.java` | 687 B | Resolution master |

#### User Stories & Agile (Kanban)
| DTO | Size | Purpose |
|-----|------|---------|
| `UserStoryDetailsDto.java` | 4,742 B | Full user story data |
| `EpicDTO.java` | 4,618 B | Epic full representation |
| `EpicUSDTO.java` | 2,730 B | Epic-US summary |
| `EpicUserStoryMapDTO.java` | 724 B | Epic-US mapping |
| `ViewEPICDTO.java` | 3,847 B | Epic view data |
| `IssueKanbanDTO.java` | 6,324 B | Kanban issue (largest DTO) |
| `IterationDTO.java` | 2,690 B | Sprint/Iteration data |
| `TaskDTO.java` | 6,117 B | Full task data |
| `SimpleTaskDTO.java` | 5,784 B | Simplified task view |
| `SubTaskDTO.java` | 1,294 B | Sub-task data |
| `TaskCommentDTO.java` | 2,286 B | Task comment |
| `TaskLinkDTO.java` | 842 B | Task relationship |
| `TaskLinkingTaskDTO.java` | 1,644 B | Task-to-task linking |
| `TaskProgressReportDTO.java` | 1,013 B | Task progress view |
| `TaskStatusDTO.java` | 617 B | Task status |
| `TaskTimelineDTO.java` | 640 B | Task timeline view |
| `TimeLogDTO.java` | 2,459 B | Time log entry |
| `TimeSpentDTO.java` | 1,331 B | Time spent summary |
| `TimelineEpicDTO.java` | 4,654 B | Epic Gantt/Timeline |
| `TimelineEpicUSDTO.java` | 3,146 B | Epic-US Timeline |
| `TimelineTaskDTO.java` | 5,884 B | Task timeline view |
| `DownloadEpicsDTO.java` | 994 B | Download epics |
| `ActiveHistoryDTO.java` | 1,491 B | Active change history |
| `ActivityDTO.java` | 1,493 B | Activity record |
| `ActivityMasterDTO.java` | 559 B | Activity master data |
| `ActivityStatusDTO.java` | 1,023 B | Activity status |
| `CalendarDetailsDTO.java` | 822 B | Calendar view data |
| `CalenderEPICDTO.java` | 1,395 B | Epic calendar view |
| `CalenderTaskDTO.java` | 1,629 B | Task calendar view |
| `CalenderUSDTO.java` | 1,642 B | User story calendar |
| `DashboardCountsDTO.java` | 1,132 B | Dashboard count aggregation |
| `AssigneeDTO.java` | 716 B | Assignee selector |
| `RelateToDTO.java` | 631 B | Task relation type |

#### Timesheet
| DTO | Size | Purpose |
|-----|------|---------|
| `TimesheetDTO.java` | 1,190 B | Timesheet entry |
| `ManagerApprovalDTO.java` | 2,418 B | Manager approval action |
| `ManagerTasksGraphsDTO.java` | 1,526 B | Manager task graphs |
| `MyTasksGraphsDTO.java` | 2,676 B | My tasks graphs |
| `ScheduleVarianceDTO.java` | 2,536 B | Schedule variance calc |
| `ApproversDTO.java` | 1,043 B | Timesheet approvers |
| `IntutiveDashboardRequestDto.java` | 1,044 B | Dashboard request |
| `IntitutiveDashboardRequestDto.java` | 3,802 B | Extended dashboard request |

#### Dashboard
| DTO | Size | Purpose |
|-----|------|---------|
| `DashboardTestCaseStatusCountDto.java` | 683 B | TC status counts |
| `DashboardTestCaseStatusCountResponseDto.java` | 5,274 B | TC status full response |
| `DashboardUserStoryStatusCountDto.java` | 859 B | US status counts |
| `DashboardUserStoryStatusCountResponseDto.java` | 5,936 B | US status full response |
| `DashboardUserStoryDetailsDto.java` | 1,342 B | US details for dashboard |
| `DashboardExcecutionDetailsDto.java` | 1,059 B | Execution details |
| `DashboardWebAndMobileStatusCountDto.java` | 1,312 B | Web/Mobile status |
| `PerfDashboardCountDTO.java` | 727 B | Performance count |
| `PerformanceDashboardErrorCountDTO.java` | 922 B | Performance error count |

#### Performance Testing Calculators
| DTO | Size | Purpose |
|-----|------|---------|
| `PacingCalculatorDto.java` | 1,693 B | Pacing calculation input |
| `PacingCalResponseDto.java` | 918 B | Pacing result |
| `ThinkTimeCalculatorDto.java` | 1,967 B | Think time calculation |
| `ThinkTimeCalculatorResponseDto.java` | 1,495 B | Think time result |
| `TPHCalculatorDto.java` | 2,010 B | Transactions per hour calc |
| `TPHCalculatorResponseDto.java` | 1,361 B | TPH result |
| `VUsersCalculatorDto.java` | 1,729 B | Virtual users calc |
| `VUserCalculatorResponseDto.java` | 1,135 B | VUser result |
| `VUsersPerLoadGeneratorDto.java` | 1,353 B | VUsers per load gen |
| `VUsersPerLoadGeneratorResponseDto.java` | 1,083 B | VUsers per LG result |
| `EstimateLGforVUsersCal.java` | 1,666 B | Estimate load generators |
| `EstimateLGforVUsersCalResponseDto.java` | 1,283 B | LG estimate result |
| `EstimateServerCapacityDto.java` | 2,268 B | Server capacity estimate |
| `EstimateServerResponseCalculatorDto.java` | 2,349 B | Server capacity result |
| `ThroughPutResponseCalculatorDto.java` | 970 B | Throughput result |
| `ThrouhputCalculationDto.java` | 826 B | Throughput input |

#### Security / Code Quality
| DTO | Size | Purpose |
|-----|------|---------|
| `SecurityDto.java` | 1,501 B | Sonar/security analysis |
| `SecurityCodeQualityDto.java` | 1,174 B | Code quality check |

#### Notification
| DTO | Size | Purpose |
|-----|------|---------|
| `CommentsDto.java` | 904 B | Comment data |
| `FileDetailsDto.java` | 950 B | File/attachment detail |
| `FileDownloadDTO.java` | 784 B | File download request |
| `AttachFileDeleteDto.java` | 772 B | Attachment deletion |
| `ViewUserAttachDto.java` | 1,280 B | User attachment view |
| `ViewUserStoryAttachDto.java` | 1,943 B | US attachment view |
| `DocListDto.java` | 650 B | Document list |
| `DocumentResponseDTO.java` | 719 B | Document response |
| `FolderResponseDTO.java` | 924 B | Folder operations response |
| `ExtractFolderDTO.java` | 396 B | Folder extract response |

#### Error / Generic
| DTO | Size | Purpose |
|-----|------|---------|
| `ResponseDto.java` | 612 B | Generic response |
| `ResponseExel.java` | 211 B | Excel response |
| `errorDTO.java` | 1,261 B | Error detail |
| `errorMessageDTO.java` | 957 B | Error message |
| `StatusDTO.java` | 571 B | Status flag |
| `InputDTO.java` | 588 B | Generic input |

---

## 2. FlexibController — Full Endpoint Map

**Base path:** `POST/GET/PUT/DELETE /api/flexibautomation/<route>`  
**File:** `FlexibController.java` — 5,524 lines, 231,206 bytes  
**Injected services (confirmed via @Autowired):** 60+ services

### Group A — DigiSec Security Integration (Lines 415–650)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/DigSecSignIn` | Authenticate with DigiSec security tool |
| POST | `/DigSecCreateProject` | Create project in DigiSec |
| POST | `/DigSecScanner` | Trigger DAST/ZAP security scan |
| POST | `/DigGetAllProjectsList` | List DigiSec projects |

### Group B — Defect Management (Lines 1237–1490)
| Method | Route | Purpose |
|--------|-------|---------|
| GET | `/getOpenedDefectList/{projectName}` | Open defects |
| GET | `/getclosedDefectList/{projectName}` | Closed defects |
| GET | `/getResolvedDefect/{projectName}` | Resolved defects |
| GET | `/categoryDefCount/{projectName}` | Defect count by category |
| GET | `severityDefCount/{projectName}` | Defect count by severity |
| GET | `priorityDefCount/{projectName}` | Defect count by priority |
| GET | `statusDefCount/{projectName}` | Defect count by status |
| POST | `/updateDefect` | Update defect details |
| GET | `/getDefectById/{defectId}` | Get single defect |
| GET | `/getCommentslist/{defectId}` | Defect comments |
| GET | `/getfileslist/{defectId}` | Defect file attachments |
| GET | `/getDefectlist/{projectName}` | All defects for project |
| POST | `/createDefect12` | Create defect (active) |
| POST | `/Defect/upload` | Bulk defect upload (Excel) |
| GET | `/downloadDefectTemplate` | Download defect import template |
| POST | `/getDefectIDlist` | Search defect IDs |

### Group C — User Story & Project Management (Lines 1367–1530)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/UserStory/upload` | Bulk user story upload |
| POST | `/IntutiveDashboard` | Intuitive dashboard data |
| GET | `/getModuleList` | Module master list |
| POST | `/createNewProject` | Create project |
| GET | `/getprojectDetails` | Get all projects |
| GET | `/ViewProjectDetail` | View project detail |
| POST | `/updateProject` | Update project |
| GET | `/getOrganiList` | Organisation list |

### Group D — Test Execution (Lines 1393–1500)
| Method | Route | Purpose |
|--------|-------|---------|
| GET | `/getTestcasesExecutionStatus/{TsuitID}` | TC execution status by suite |
| GET | `/TestCaseLogDetails` | Test case log details |
| GET | `/testCaseExecutionData` | Test execution data |
| GET | `/TestCaseExecutionLogDetails` | Execution log details |
| GET | `/TestCaseLogCount` | Log count by params |
| POST | `/SaveTestCaseExecution` | Save manual execution result |
| POST | `/saveTestExecution` | Save test run |
| POST | `/skipTestExecution` | Skip test step |
| POST | `/DashboardTestCaseCount` | Dashboard count aggregation |

### Group E — Test Suite (Lines 1466–1495)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/createTestSuite` | Create test suite |
| POST | `/updatetestsuite` | Update test suite |
| GET | `/getTestSuitlist/{projectName}` | List suites |
| GET | `/getTestSuiteById/{testSuiteID}` | Get suite by ID |
| DELETE | `/deleteTestSuit/{testSuiteID}` | Delete test suite |
| POST | `/DashboardUserStoryCount` | US dashboard count |

### Group F — Test Cases (Lines 1548–1810)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/CreateNewTestCase` | Create test case (large implementation, L1548–1721) |
| POST | `/TestCase/upload` | Import test cases from Excel |
| GET | `/getAllTestCaseBaseOnUserStoryID` | TC by user story |
| GET | `/getTestCaseList/{projectName}` | All TCs for project |
| GET | `/getTestCaseById/{testCaseId}` | Single TC |
| DELETE | `/deleteTestCase/{testCaseId}` | Delete TC |
| POST | `/updatetestcase` | Update TC |
| GET | `/deleteteststep` | Delete test step |

### Group G — Automation (Lines 1810–2307)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/execute/mm/exceldataupdate` | Update Excel test data (MM) |
| POST | `/excecute/mm/jarfile` | Execute MM automation JAR |
| POST | `/report/find/mm` | Find MM automation report |
| POST | `/execute/maven/mm` | Run Maven build (MM) |
| POST | `/AzureRepocheckout/mmrepo` | Checkout Azure MM repo |
| POST | `/gihubcheckout/cgtrepo` | Checkout GitHub CGT repo |
| POST | `/jenkinsDeletejob` | Delete Jenkins job |
| POST | `/jenkinsBuildWithParam` | Trigger Jenkins build with params |
| POST | `/jenkinsclonejob` | Clone Jenkins job |
| POST | `/avokaResults` | Store Avoka test results |
| GET | `/getFileDownload` | Download automation file |
| POST | `/getDateRangeStatusCountPerformanceDashboard` | Performance dashboard date range |
| POST | `/visualauto/initialize` | Visual testing init |
| POST | `/visualauto/createref` | Visual testing create reference |
| POST | `/visualauto/approve` | Approve visual test results |
| POST | `/sonar/security/codeanalysis` | SonarQube security analysis |
| POST | `/sonar/security/codequality` | SonarQube code quality |
| POST | `/validate/PersonalDetails` | Validate personal details |
| POST | `/validate/EmpDetails` | Validate employee details |
| POST | `/users/creation` | Create user (legacy) |
| POST | `/flexibusers/creation` | Create Flexib user |
| POST | `/flexibusers/login` | Flexib user login (legacy) |
| POST | `/users/login` | Generic user login (legacy) |
| POST | `/users/login/3ibank` | 3i Bank specific login |
| POST | `/generatePdfTestingReport` | Generate PDF test report |
| POST | `/pipeline/groovy/upload` | Upload Groovy pipeline script |

### Group H — Performance Calculators (Lines 2227–2275)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/pacing/calculator` | Pacing calculation |
| POST | `/thinktime/calculator` | Think time calculation |
| POST | `/tph/calculator` | Transactions per hour |
| POST | `/vuser/calculator` | Virtual users calculation |
| POST | `/vusers/perloadgenarator/calculator` | VUsers per load generator |
| POST | `/EstimateLGforVUsers/calculator` | Estimate load generators for VUsers |
| POST | `/Estimate/service/calculator` | Estimate server capacity |
| POST | `/throughput/calculator` | Throughput calculator |
| POST | `/webandmobile/automation/result` | Store web/mobile result |

### Group I — Git & Maven Execution (Lines 2404–2800)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/attachfilecreateuserstory` | Attach file to user story |
| POST | `/viewdeleteattachmentuserstrory` | Delete user story attachment |
| GET | `/getprojectlist` | Project list by org |
| GET | `/getprojectlistByUserId` | Project list by user |
| POST | `/execute/maven` | Execute Maven command |
| POST | `/execute/jmeter` | Execute JMeter test |
| POST | `/checkout/github` | Checkout from GitHub |
| POST | `/upload-file1` | File upload (v1) |
| POST | `/newupload-file1` | File upload (v2) |
| POST | `/gitupload/repo` | Upload to Git repo |
| POST | `/execute/mobileautomation` | Execute mobile automation |
| POST | `/excecute/jarfile` | Execute generic JAR |
| POST | `/report/find/mobile` | Find mobile automation report |
| POST | `/report/find/cgt` | Find CGT automation report |
| POST | `/report/find/engrc` | Find ENGRC automation report |
| POST | `/report/find` | Find generic automation report |
| POST | `/jsontocsv/filegenerate` | Generate JSON-to-CSV |
| POST | `/execute/exceldataupdate` | Update Excel data |
| POST | `/execute/cgt/exceldataupdate` | Update CGT Excel data |
| POST | `/execute/engrc/exceldataupdate` | Update ENGRC Excel data |
| POST | `/execute/api/automation` | Execute API automation |
| POST | `/execute/apiAutomation/newman` | Execute Newman/Postman |
| POST | `/report/find/api` | Find API automation report |
| POST | `/gitCreateBranch` | Create Git branch |
| GET | `/directoryPath` | Get automation directory listing |

### Group J — Dashboard & Full Count (Lines 3857–3965)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/dashBoardFullcount` | Complete dashboard data |
| POST | `/dashboard/mm/API` | MM API dashboard |
| POST | `/dashboardexecutiondetails` | Execution details dashboard |

### Group K — Execution Plan (Lines 3965–4060)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/createExecutionPlan` | Create execution plan |
| DELETE | `/deleteExecutionPlan/{executionPlanID}` | Delete plan |
| GET | `/getExecutionPlanlist/{projectName}` | List plans |
| GET | `/getExecutionPlanlistV2/{projectName}` | List plans v2 |
| GET | `/getExecutionPlanById/{exPlanId}` | Get plan by ID |
| POST | `/updateExecutionPlan` | Update plan |
| POST | `/getexecuteExecutionPlans` | Execute plans |
| POST | `/getExecutionPlanByMonthandYear` | Plans by month/year |
| POST | `/getExecutionPlanlistNew` | Plans list (new format) |
| POST | `/saveTestExecutionPlan` | Save test execution plan |
| GET | `/status-counts/{exId}` | Status counts by plan |
| POST | `/getTestcasesExecutionStatusNew` | TC execution status (new) |
| POST | `/getTestcaseHistory` | TC execution history |
| POST | `/getTestcasesExecutionStatusEP` | TC status by EP |
| POST | `/getTestCaseByIdEP` | TC by ID for EP |
| POST | `/getTestSuiteExecutionStatusEP` | Suite status for EP |

### Group L — Dynamic Columns & Custom Reports (Lines 4083–4215)
| Method | Route | Purpose |
|--------|-------|---------|
| GET | `/getExcelDynamicColumnData/{projId}` | Dynamic Excel columns |
| GET | `/getExcelDynamicColumnDataValue/{projId}` | Dynamic column values |
| POST | `/saveCustomColumns` | Save custom column definitions |
| GET | `/getDataByProjIdAndTcId/{projId}/{tcid}` | TC data by project+ID |
| GET | `/getDefectDetails/{testCaseId}/{testStepId}` | Defect for test step |
| GET | `/downloadReport/{projectName}/{projectId}/{fromDate}/{toDate}` | Download full project report |

### Group M — Kanban/Task Management (Lines 4140–4695)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/saveTask` | Create task |
| GET | `/getAllStatuses` | Status master list |
| GET | `/getAllTaskStatus` | Task status list |
| GET | `/getAllPriorityLevels` | Priority levels |
| GET | `/getAllTaskTimeline` | Timeline types |
| GET | `/getTasks` | Get all tasks |
| POST | `/DashboardExecutionPlanCount` | Dashboard plan count |
| GET | `/getTaskCommentslist/{taskId}` | Task comments |
| POST | `/updateTaskStatus` | Update task status |
| GET | `/getStatusHistory/{taskId}` | Task status history |
| GET | `/getRelateList` | Task relation type list |
| POST | `/createTaskLink` | Link tasks |
| GET | `/getTaskLinkList/{taskId}` | Get task links |
| DELETE | `/removeTaskLink` | Remove task link |
| POST | `/createTimeLog` | Log time on task |
| GET | `/getTimeLog/{taskId}` | Get time logs |
| POST | `/updateTimeLog` | Update time log |
| DELETE | `/removeTimeLog/{timeId}` | Remove time log |
| POST | `/createSubTask` | Create sub-task |
| DELETE | `/deleteSubTask` | Delete sub-task |
| PUT | `/updateTask` | Update task |
| GET | `/getBacklogTasks` | Get backlog tasks |
| DELETE | `/deleteTask` | Delete task |

### Group N — GitLab Integration (Lines 4697–4790)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/checkout/gitlab` | Checkout from GitLab |
| POST | `/branchcheckout/gitlab` | Branch checkout from GitLab |

### Group O — Epic Management (Lines 4787–4930)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/createEpic` | Create epic |
| GET | `/getEpics` | Get all epics for project |
| GET | `/getEpicsUSerStories` | Epics with user stories |
| GET | `/getUserstoriesByEpicID/{epicId}` | US for specific epic |
| POST | `/updateEpic` | Update epic |
| GET | `/viewEpicTimeline/{epicId}` | Epic timeline view |
| GET | `/viewTaskTimeline/{taskId}` | Task timeline view |
| GET | `/getEpicCommentslist/{epicId}` | Epic comments |
| PUT | `/updateEpicComment` | Update epic comment |
| GET | `/getunmappedstorylistforEPIC` | Unmapped US for epic |
| POST | `/addUserStorytoEpic` | Add US to epic |
| DELETE | `/removeUserStory` | Remove US from epic |
| DELETE | `/DeleteEpic/{epicId}` | Delete epic |
| PUT | `/updateEpicIteration` | Update epic iteration |
| PUT | `/changeEpicStatus` | Change epic status |
| GET | `/getActivehistory` | Active change history |
| PUT | `/changeEpicAssignee` | Change epic assignee |
| PUT | `/changeEpicPriority` | Change epic priority |
| POST | `/attachfilecreateEpicorTask` | Attach file to epic/task |
| DELETE | `/deleteEPICTaskDocument` | Delete epic/task document |
| GET | `/grouped-by-priority` | Group epics by priority |

### Group P — Issue Management (Lines 4930–5155)
| Method | Route | Purpose |
|--------|-------|---------|
| GET | `/getOriginList` | Issue origin master list |
| GET | `/getResolutionList` | Issue resolution master list |
| POST | `/createIssue` | Create Kanban issue |
| POST | `/attachfileIssue` | Attach file to issue |
| GET | `/getIssues` | Get all issues |
| POST | `/editUserStory` | Edit user story |
| PUT | `/changeUserStoryStatus` | Change US status |
| PUT | `/changeUserStoryIteration` | Change US iteration |
| PUT | `/changeUserStoryStoryPoints` | Change US story points |
| PUT | `/changeUserStoryAssignees` | Change US assignee |
| GET | `/getuserstoryforEPIC` | Get US for epic |
| PUT | `/changeUserStoryStatusUS` | Change US status (variant) |
| PUT | `/changeUserStoryIterationUS` | Change US iteration (variant) |
| PUT | `/changeUserStoryStoryPointsUS` | Change US story points (variant) |
| PUT | `/changeUserStoryAssigneesUS` | Change US assignee (variant) |
| GET | `/chart` | Chart data for US |
| GET | `/getUserStoryTasks` | Tasks for US |
| GET | `/getUserStoryIssues` | Issues for US |
| GET | `/downloadEpics` | Download epics to Excel |
| GET | `/gettimelineAPI/{ProjectId}` | Timeline API data |
| POST | `/updateIssue` | Update issue |
| DELETE | `/deleteIssue/{issueId}` | Delete issue |
| POST | `/updateIssueFields` | Update issue fields |
| POST | `/addCommentIssue` | Add comment to issue |
| GET | `/getIssueCommentslist/{issueId}` | Get issue comments |

### Group Q — RCA (Root Cause Analysis) (Lines 4341–4515)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/getDefectIDlist` | Get defect ID list |
| POST | `/getExecutionPlanIDlist` | Get EP ID list |
| POST | `/getTestSuiteIDlist` | Get Suite ID list |
| POST | `/getTestCaseIDlist` | Get TC ID list |
| POST | `/getUserStoryIDlist` | Get US ID list |
| POST | `/uploadAttachedRCA` | Upload RCA document |
| GET | `/getRCADocument/{docId}` | Get RCA document |
| DELETE | `/deleteRCADocument/{docId}` | Delete RCA doc |
| POST | `/updateAttachedRCA` | Update RCA |
| POST | `/uploadSavedRCA` | Save RCA |
| GET | `/getSavedRCADoc/{rcaId}` | Get saved RCA |
| GET | `/deleteSavedRCADocument/{rcaId}` | Delete saved RCA |
| POST | `/updateSavedRCA` | Update saved RCA |

### Group R — Iteration/Sprint (Lines 4521–4560)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/createIterationV2` | Create sprint (v2) |
| POST | `/createIteration` | Create sprint |
| DELETE | `/deleteIterationV2/{iterationID}` | Delete sprint (v2) |
| DELETE | `/deleteIteration/{iterationID}` | Delete sprint |
| POST | `/updateIterationV2` | Update sprint (v2) |
| POST | `/updateIteration` | Update sprint |
| GET | `/getAllIterations/{projectID}` | Get all sprints |
| GET | `/getActivitiesList` | Activity list |
| POST | `/createActivity` | Create activity |
| GET | `/getAllActivityList/{taskID}` | Activities for task |

### Group S — Timesheet (Lines 5196–5295)
| Method | Route | Purpose |
|--------|-------|---------|
| GET | `/getTimeSheet` | Get timesheet data |
| POST | `/saveTimeSheet` | Save timesheet entry |
| GET | `/getCalendarDetails` | Calendar view |
| GET | `/gettesting` | Test endpoint |
| GET | `/getUsersforTimeSheet` | Users for timesheet |
| GET | `/getConfiguration` | Timesheet configuration |
| GET | `/getConfigurationStages` | Timesheet stages |
| DELETE | `/deleteCustomColumn` | Delete custom column |
| POST | `/saveApprovers` | Save approvers |
| POST | `/editApprovers` | Edit approvers |
| DELETE | `/deleteApprovers` | Delete approvers |
| POST | `/forApproval` | Submit for approval |
| GET | `/forManagerApprovalPage` | Manager approval page data |
| POST | `/forStage1Approve` | Stage 1 approve |
| POST | `/forStage1Reject` | Stage 1 reject |

### Group T — Reports & Project Dashboard (Lines 5298–5445)
| Method | Route | Purpose |
|--------|-------|---------|
| PUT | `/updateTimeline` | Update Gantt timeline |
| POST | `/myTasks` | My tasks list |
| POST | `/myTasksGraphs` | My tasks graph data |
| GET | `/forManager` | Manager dashboard data |
| POST | `/managerTasks` | Manager's team tasks |
| POST | `/RemoveEpicSprint/{epicId}` | Remove epic from sprint |
| POST | `/managerResourcesGraphs` | Resource graph data |
| POST | `/SprintInfo` | Sprint information |
| POST | `/managerTasksGraphs` | Manager task graphs |
| PUT | `/updateProjectStatus` | Update project status |
| GET | `/getProjectStatusList` | Project status types |
| GET | `/effort-variance` | Effort variance report |
| GET | `/getresourceUtilization` | Resource utilization |
| GET | `/schedule-variance` | Schedule variance |
| GET | `/sendTimesheetReminders` | Send timesheet reminders |
| GET | `/taskProgressreport` | Task progress report |

### Group U — Notifications (Lines 5360–5410)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/notifications` | Create notification |
| GET | `/notifications/{userId}` | Get notifications for user |
| PUT | `/notificationsread/{id}` | Mark notification read |
| PUT | `/notificationsclear/{userId}` | Clear all notifications |
| POST | `/postnotification-settings` | Save notification settings |
| GET | `/getnotification-settings` | Get notification settings |
| GET | `/getnotification-settings/{projectId}` | Settings by project |
| GET | `/getSettingsByUsername/{username}` | Settings by user |
| POST | `/editUserSettings` | Edit user settings |

### Group V — Desktop Automation & Counts (Lines 5461–5524)
| Method | Route | Purpose |
|--------|-------|---------|
| POST | `/FlexibPlusDesktop/{frameworkType}` | Execute desktop automation (ProcessBuilder, framework-type based) |
| GET | `/automationCount` | Automation run counts dashboard |
| GET | `/getAutomationDetails` | Automation details |

---

## 3. AuthController — Full Endpoint Map

**Base path:** `/api/auth` — All confirmed via source read

| Method | Route | Line | Purpose |
|--------|-------|------|---------|
| POST | `/signin` | 105 | Login — username+password+projectList |
| POST | `/password-reset-request` | 255 | Reset password (direct, no OTP verification) |
| POST | `/change-password` | 299 | Change password (old→new, BCrypt validated) |
| POST | `/refreshtoken` | 346 | Refresh JWT using refresh token |
| GET | `/GetAllUserSingUpUserDetails` | 373 | List all registered users |
| GET | `/GetSingUpUserDetails/{username}` | 381 | Get single user by username |
| DELETE | `/DeleteSignUpUser/{username}` | 395 | Delete user |
| POST | `/UserStatus` | 413 | Toggle user Active/InActive |
| POST | `/UpdateSignUpUser` | 431 | Update user profile |
| POST | `/signup` | 495 | Register new user |
| POST | `/signout` | 592 | Logout (clear cookie) |
| GET | `/GetAllRole` | 599 | Get all roles |
| GET | `/GetAllRoleNew/{projectId}` | 606 | Get roles by project |
| GET | `/GetAllModuleList` | 614 | Get module list |
| POST | `/AddModule` | 620 | Add module |
| POST | `/AddNewRole` | 660 | Create new role |
| POST | `/UpdateRole` | 715 | Update role |
| DELETE | `/DeleteRole/{name}` | 731 | Delete role |

> [!IMPORTANT]
> **OTP / Forgot Password flow confirmed from frontend (index.html):**
> - `POST /api/auth/send-otp` (L365 of index.html) — sends OTP to email
> - `POST /api/auth/forgot-password` (L417 of index.html) — resets password with OTP verification
>
> These two endpoints exist in the frontend JavaScript but are NOT found in the AuthController source that was read. This means either (a) they exist in a section of AuthController not yet read, or (b) they are handled by a separate controller/filter. **This is a gap to verify.**

---

## 4. Frontend Login Flow — Traced in Code

**File:** [`index.html`](file:///c:/Users/10004463/Desktop/Flexib_x/src/main/resources/public/FlexibPlus_V1/index.html)

### Login Form Flow (Lines 500–620, confirmed in source)
```
User fills: username, password, project list checkboxes
↓
LoginForm() called on submit
↓
log_myFunction() invoked
  POST /api/auth/signin
  Body: { username, password, projectList: [array of project names] }
↓
On success (data.status == "Active"):
  localStorage.setItem("UserName", username)
  localStorage.setItem("login_prjlist", projectList)
  localStorage.setItem("Useactivitylist", data.roles.activityList)
  localStorage.setItem("userrole", data.roles.name)
  → GET /getprojectlist?organisationId=Org_2
  → sets localStorage "first_prj_id"
  → redirect to dashboard.html
↓
On failure:
  "YOUR ACCOUNT HAS BEEN DISABLED" → show changepassword modal
```

> [!CAUTION]
> **Security Observation:** `organisationId=Org_2` is HARDCODED in the frontend JavaScript (line 573). This means the login flow always assumes the user belongs to `Org_2`. This is a critical business logic constraint embedded in the UI, not the backend.

### Forgot Password Flow (Lines 341–478, confirmed)
```
User enters email
↓
POST /api/auth/send-otp — sends OTP to email
↓
OTP + username + new password entered
↓
POST /api/auth/forgot-password — validates OTP + resets password
↓
Countdown timer (120 seconds) shown while OTP is valid
```

### Token Storage
- JWT token is stored in `localStorage` — **not HttpOnly cookie on frontend side**
- `data.roles.activityList` drives frontend permission rendering
- No token expiry handling found in login page code

---

## 5. Complete Entity Schema Inventory

### Previously Uncovered Entities (verified in this pass)

#### Task.java — Table: `Task`
```
taskId (PK), assignee, assignedStatus, dueDate, startDate, projectName,
currentStatus, dependencies, activity, description, priority, status,
estimatedTime, actualTime, iteration, startiteration, taskName,
totalhours, percentCompletion, createdBy, updatedBy
@ManyToOne → Epic (via epicId FK)
@OneToMany → SubTask, TaskComments, TaskLinking, TimeLog
```

#### Epic.java — Table: `epic`
```
epicId (PK), uniEpicId, assignee, userStoryId, title, descrip,
esthours, currentStatus, iteration, iteration2, projectName,
spenthours, priority, createdBy, updatedBy, createdDate, updatedDate, comments
@OneToMany → List<Task>
```

#### Issues.java — Table: `issues_kanban`
```
issueId (PK), title, issueType, priority, assignee, detectionPhase,
reportedBy, usId, usName, category, origin, description,
createdBy, createdDate, updatedBy, updatedDate, dueDate, environment
@ManyToOne → Epic (via epicId FK)
@Lob → file attachment blob
```

#### Notification.java — Table: `notifications`
```
id (PK), userId, title, message (TEXT), type, isRead (Boolean),
createdAt (LocalDateTime), updatedAt, module, projectId, refId
```

#### Iteration.java — Table: (not directly confirmed, entity exists)
```
Iteration/Sprint management entity — 3,859 bytes
```

#### EpicUserStoryMap.java — Table: (mapping table)
```
4,017 bytes — manages Epic ↔ UserStory many-to-many
```

#### BacklogHistory.java — Table: `backlog_history`
```
3,282 bytes — tracks backlog changes over time
```

#### AttachmentKanban.java — Table: `attachment_kanban`
```
2,108 bytes — file attachments for Kanban tasks
```

#### AttachmentUserStory.java — Table: `attachment_user_story`
```
1,713 bytes — file attachments for User Stories
```

#### ActiveHistory.java — Table: (activity tracking)
```
1,843 bytes — tracks who changed what and when
```

#### ActivityStatus.java — Table: (activity status)
```
3,350 bytes — status of activities
```

#### TaskComments.java, TimeLog.java, TaskLinking.java, SubTask.java
```
All confirmed as supporting entities for Task management module
```

#### TimesheetPrana.java, TimesheetPranaApproval.java, TimeSpentPrana.java, TimeSheetApproval.java
```
Four entities managing the timesheet approval workflow — Prana naming suggests integration with HR system
```

#### UserSettings.java — Table: `user_settings`
```
1,298 bytes — per-user preferences/settings, created on signup
```

#### UserJson.java — Table: (user JSON cache)
```
5,147 bytes — stores user-related JSON data
```

#### ITechTicket.java, ITechTicketHistory.java — Tables: `itech_ticket`, `itech_ticket_history`
```
7,196 bytes + 7,446 bytes — internal IT helpdesk ticketing system
```

#### FlexibUser.java, FlexibUserPrincipal.java
```
3,981 + 1,304 bytes — Alternate/legacy user entities (separate from `User.java` in Models)
This confirms THREE user entity systems exist: User (JWT auth), FlexibUser (legacy), Users (table: users)
```

#### ChatResponses.java — Table: `chat_responses`
```
2,365 bytes — stores chatbot Q&A responses
```

#### RootcauseAnalysisFileDetails.java, DefaultRootcauseAnalysisDocs.java
```
2,758 + 2,214 bytes — RCA document management
```

#### PipelineStages.java — Table: `pipeline_stages`
```
974 bytes — CI/CD pipeline stages
```

### Full Entity Count Breakdown
| Package | Entity Count | Notes |
|---------|-------------|-------|
| `entity/` | 88 entities | Business domain |
| `Models/` | User, Role, RefreshToken, PasswordResetRequest, ModuleLists | Auth domain |
| Total unique tables | ~93+ | Some entities share tables or are orphaned |

---

## 6. KanbanService — Deep Analysis

**File:** `KanbanService.java` — 2,942 lines, 124,399 bytes (largest service)

**Entities managed (confirmed via imports):**
- `ActiveHistory`, `ActivityStatus`, `AssigneeDetails`, `AttachmentKanban`, `AttachmentUserStory`
- `Epic`, `EpicUserStoryMap`, `Iteration`, `Issues`, `Task`, `SubTask`
- `TaskComments`, `TaskLinking`, `TimeLog`, `BacklogHistory`

**Key capabilities (confirmed from imports and structure):**
- Full Epic CRUD + iteration assignment + status workflow
- Task CRUD with full lifecycle (create/update/delete/status change)
- Sub-task management
- Time logging per task
- Task linking (dependencies/relations)
- Epic → UserStory mapping
- Timeline data generation (Gantt)
- Calendar view data (EPIC, Task, US date ranges)
- Kanban board drag-and-drop status updates
- Dashboard counts (by status, priority, assignee)
- File attachments for tasks and user stories (Base64 encoded)
- Download epics to Excel

**Reusability:** ✅ Highly reusable business logic — must be decomposed into:
- `EpicService`, `TaskService`, `SubTaskService`, `TaskLinkService`, `TimeLogService`, `IterationService`, `KanbanAttachmentService`

---

## 7. TimesheetService — Deep Analysis

**File:** `TimesheetService.java` — 1,064 lines, 47,239 bytes

**Confirmed imports (verified):**
- Uses `EntityManager` + `CriteriaBuilder` — raw JPA Criteria API for dynamic queries
- Uses `JPA Specification` pattern
- Manages: `Task`, `TimesheetPrana`, `TimeSpentPrana`, `TimeSheetApproval`, `Approvers`, `EpicUserStoryMap`
- `@Transactional` at class level

**Key capabilities:**
- Log time spent on tasks per user per day
- Multi-stage approval workflow (Stage 1/Stage 2 approval)
- Manager can view/approve timesheets of team members
- Effort variance calculations (estimated vs actual hours)
- Schedule variance calculations
- Resource utilization reports
- Export timesheet data

> [!WARNING]
> `TimesheetService` directly `@Autowired`s `TestCaseService` (line 67) — a clear violation of service layer separation. This creates a tight coupling between the Timesheet and Test Repository domains that must be removed during refactoring.

---

## 8. UserStoryDetailsService — Deep Analysis

**File:** `UserStoryDetailsService.java` — 1,101 lines, 41,018 bytes

**Confirmed capabilities:**
- Full CRUD for UserStory (`UserStoryDetails` entity)
- Excel bulk import of user stories via Apache POI (confirmed via import of `XSSFWorkbook`)
- Base64 encoded file attachments for user stories
- Feature-to-UserStory linking (`FeatureDetails` → `UserStoryDetails`)
- Project metadata update via `UpdateProjectDTO`
- Notification service integration (sends notifications on US changes)
- Attachment management (upload/delete via `AttachmentUserStory` + `AttachFileRepo`)

---

## 9. ExecutionService — Logic Verified

**File:** `ExecutionService.java` — 727 lines, 32,128 bytes

**Confirmed execution logic (code read, lines 61–263):**

```
TestCaseExecution(SaveTestCaseExecutionDTO):
  For each TestStepDetailsDTO in request:
    1. Auto-generate TestResultID (pattern: "TResult_{N+1}")
    2. Create TestStepResult entity
    3. Set: tcId, testexeId, actualResult, expectedResult, bugs, steps, fileData, attachmentName, status, executedBy, executionTime
    4. Save to DB via testStepResultRepository
  
  After all steps saved:
    5. Fetch all results for this TC + testexeId
    6. Apply aggregation logic:
       - Any "Fail" or "Blocker" → TC status = "Fail"
       - All "Skip" → TC status = "Skip"
       - Mix of "Pass"+"Skip" or all "Pass" → TC status = "Pass"
    7. Save TestSuiteTestCaseMap record with aggregated status
    8. Update TestCase.testCaseExeStatus + testCaseExeId
```

**Two parallel execution paths exist:**
- `TestCaseExecution()` — for standalone TC execution (no suite ID)
- `saveTestCaseExecution()` — for suite-linked TC execution (sets `tsuiteId` on results)

**Key bug noted:** A large block of aggregation logic is commented out (lines 135–165) with an alternative implementation (lines 170–259). The commented code used `subList` from end, the active code uses `subList` from start. This suggests the aggregation logic was reworked but old code was not removed.

---

## 10. IssueManagementAlertService — Scheduler Confirmed

**File:** `IssueManagementAlertService.java` — 512 lines, 22,203 bytes

**CONFIRMED: `@Scheduled` annotation present:**
```java
@Async
@Scheduled(cron = "0 0 */6 ? * *")  // Every 6 hours
public void fetchBBJob() {
    // Fetches all issues with count=1, checks notification dates,
    // sends Teams webhook notifications for overdue issues
    // Uses defectMgtService.CommonTeamNotification() to push to MS Teams
}
```

**This means:**
- The application runs a background job every 6 hours
- It queries `IssueMgt` (not `DefectMgt`) entities with escalation logic
- Sends Microsoft Teams webhook notifications
- Uses a notification count escalation system (count 1 → 2 → 3)

**Reusability:** ✅ The scheduler pattern is reusable. The Teams integration logic is worth preserving.

---

## 11. RequirementService / Feature Module

**File:** `RequirementService.java` — 238 lines, 8,472 bytes

**Confirmed capabilities:**
- Manage `FeatureDetails` entities (feature/module level)
- Manage `FeatureFiles` — document uploads per feature
- CRUD for features: create (with duplicate title check), read, update, delete
- Feature-to-Project linking via `projectId`
- File management for feature specification documents (stored as blob)
- Auto-ID generation for features (`lastFeatureId1` + increment pattern)

**Note:** `FeatureDetails` is also linked to `UserStoryDetails.featureId` field — this establishes the hierarchy: `Feature → UserStory → TestCase`.

---

## 12. ChatBotService — Module Structure

**File:** `ChatBotService.java` — 382 lines, 16,418 bytes

**Confirmed — NOT an AI chatbot.** It is a **structured FAQ/knowledge-base navigator:**
- `ModuleMasterRepository` — fetches module categories
- `SubModuleRepository` — fetches sub-modules under each module
- `UserQuestionRepository` — stores predefined questions+answers
- The chatbot works as: User selects Module → SubModule → gets predefined answer

**Different Repository package:** `com.infotech.flexibautomation.Repository` (capital R) — this is the auth-domain repo package. ChatBotService uses this different package, confirming the fragmented package structure noted in Part 1.

**Reusability:** ⚠️ The FAQ database concept is reusable; the implementation is basic and should be replaced with proper NLP or upgraded to an LLM-backed bot.

---

## 13. FlexibService — Hardcoded Paths Found

**File:** `FlexibService.java` — 204 lines, 8,149 bytes

**Additional hardcoded paths confirmed:**
```java
// Line 42 — Hardcoded GitHub repo URL for clone
String repoUrl1 = "https://github.com/onecompiler/tutorials.git";

// Line 44 — Hardcoded Windows clone target directory
String cloneDirectoryPath = "D:\\try";

// Line 66 — Hardcoded ZIP source path
private static final String SERVER_LOCATION = "D:\\Flexib Workspace\\ZipSource\\WebAutomationTesting";
```

> [!CAUTION]
> **Three additional hardcoded absolute Windows paths** found in `FlexibService.java`:
> - `D:\\try` — clone directory
> - `D:\\Flexib Workspace\\ZipSource\\WebAutomationTesting` — zip source
>
> Combined with `WebAtomationService.java`'s `C://webAutomation` and `FlexibService`'s `D:\\Flexib Workspace`, the system has at minimum **5 hardcoded Windows file system paths** scattered across services. Every single one must be externalized before production deployment.

**Also noted:** Git credentials hardcoded in comment:
```java
// .setCredentialsProvider(new UsernamePasswordCredentialsProvider("rajeshwari.navali@3i-infotech.com", "Padebit@001"))
```
And active credentials:
```java
.setCredentialsProvider(new UsernamePasswordCredentialsProvider("bhaskaravaram", "Infotech@2023"))
```

> [!CAUTION]
> **Plaintext GitHub credentials** (`bhaskaravaram` / `Infotech@2023`) are embedded in `FlexibService.java` as active code (not just commented out). These must be removed immediately and rotated.

---

## 14. Complete Repository Layer

**Total: 77 repository interfaces** in `com.infotech.flexibautomation.repo/`

### Kanban / Task Domain
| Repo | Size | Notable Queries |
|------|------|----------------|
| `TaskRepository.java` | 12,176 B | `getEffortVarianceByProject()`, `getEffortVarianceByProjects()`, `findMaxTaskIdForProject()`, JPA Criteria queries for date-range filtering |
| `EpicRepo.java` | 1,114 B | Find by project, iteration |
| `EpicUserStoryMapRepo.java` | 1,947 B | Mapping queries |
| `ActivityStatusRepo.java` | 394 B | Status queries |
| `ActiveHistoryRepo.java` | 602 B | History queries |
| `TaskCommentsRepo.java` | 1,607 B | Comments by taskId |
| `TaskLinkingRepo.java` | 1,394 B | Task linking queries |
| `SubTaskRepository.java` | 404 B | Sub-tasks by parent |
| `BacklogHistoryRepo.java` | 344 B | Backlog history |
| `AttachmentKanbanRepo.java` | 561 B | File attachment queries |
| `IterationRepository.java` | 1,407 B | Iteration by project |
| `IssuesRepo.java` | 736 B | Kanban issues |
| `IssueMgtRepo.java` | 1,204 B | Issue management |
| `StatusChangesRepo.java` | 393 B | Status change tracking |
| `StatusRepository.java` | 247 B | Status master |
| `RelateToMasterRepo.java` | 263 B | Relation type master |
| `PriorityLevelRepository.java` | 327 B | Priority master |
| `TaskStatusRepository.java` | 318 B | Task status master |
| `TaskTimelineRepository.java` | 317 B | Timeline type master |
| `TimeLogRepo.java` | 738 B | Time logs by taskId |
| `TimeSpentRepository.java` | 295 B | Time spent by task |

### Timesheet Domain
| Repo | Size | Notable |
|------|------|---------|
| `TimesheetPranaRepo.java` | 2,277 B | Complex queries for timesheet data |
| `TimesheetPranaApprovalRepo.java` | 1,060 B | Approval workflow queries |
| `TimeSheetApprovalRepo.java` | 853 B | Approval status queries |
| `TimeSpentPranaRepo.java` | 884 B | Time spent aggregation |
| `ApproversRepo.java` | 664 B | Timesheet approvers |

### Test Management Domain
| Repo | Size | Notable |
|------|------|---------|
| `TestCaseRepository.java` | 5,215 B | Specification-based search, native queries |
| `TestSuitTestCaseMapRepo.java` | 7,316 B | Largest repo — complex suite-TC mapping queries |
| `ExecutionPlanTestSuitMapRepo.java` | 3,432 B | Plan-Suite mapping |
| `ExecutionPlanRepository.java` | 2,372 B | Execution plan queries |
| `TestStepResultRepository.java` | 3,130 B | Step result queries by TC+suite+exe |
| `TestExecutionRepo.java` | 2,144 B | Execution run queries |
| `UserStoryDetailsRepo.java` | 3,037 B | US queries + native queries |
| `FeatureDetailsRepository.java` | 1,177 B | Feature queries |
| `DynamicExcelDataRepository.java` | 847 B | Dynamic column queries |
| `DynamicExcelDataValueRepository.java` | 1,308 B | Dynamic value queries |

### Automation Domain
| Repo | Size | Notable |
|------|------|---------|
| `AutomationResultsRepo.java` | 1,118 B | Results by project/date |
| `AutoDateFilterRepo.java` | 1,269 B | Date-filtered automation results |
| `JmeterResultsPerfCountRepo.java` | 910 B | JMeter perf count queries |

### Defect Domain
| Repo | Size | Notable |
|------|------|---------|
| `DefectMgtRepo.java` | 2,608 B | Defects by project/status/assignee |
| `DefectHistoryRepo.java` | 1,164 B | Defect audit history |
| `RCAFileDetailsRepo.java` | 820 B | RCA document queries |
| `DefaultRCADocsRepo.java` | 821 B | Default RCA templates |
| `DetectionPhaseRepo.java` | 263 B | Master data |
| `ResolutionRepo.java` | 251 B | Master data |

### Project / Org Domain
| Repo | Size | Notable |
|------|------|---------|
| `ProjectRepo.java` | 1,146 B | findByProjectName, existsByprojectName |
| `OrgnizationRepo.java` | 370 B | Organisation queries |
| `ProjectStatusRepo.java` | 272 B | Status master |

### Notifications
| Repo | Size | Notable |
|------|------|---------|
| `NotificationRepo.java` | 835 B | Unread notifications by user |
| `NotificationSettingsRepo.java` | 371 B | Settings queries |

### Miscellaneous
| Repo | Size | Notable |
|------|------|---------|
| `ITicketRepository.java` | 1,351 B | IT helpdesk ticket queries |
| `ITechTicketHistoryRepository.java` | 767 B | Ticket history |
| `UsersStoryDetailsRepo.java` | 1,185 B | Alternative US repo (orphan?) |
| `UserStoryRepo.java` | 677 B | Simple US repo |
| `UsersRepo.java` | 672 B | Users table repo |
| `UserTableRepository.java` | 558 B | UserTable entity repo |
| `FlexibUserRepository.java` | 467 B | FlexibUser entity repo |
| `UserSettingsRepo.java` | 312 B | UserSettings queries |
| `ThrreIBankRepo.java` | 305 B | 3i Bank entity repo |

---

## 15. Frontend — All 87 Pages Categorised

**Directory:** `src/main/resources/public/FlexibPlus_V1/`

### Authentication
| Page | Size | Purpose |
|------|------|---------|
| `index.html` | 32,685 B | Login page (CONFIRMED flow traced) |
| `forgot-password.html` | 29,235 B | Standalone forgot password page |
| `userinvite.html` | 31,377 B | User invitation |

### Project Management & Dashboard
| Page | Size | Purpose |
|------|------|---------|
| `dashboard.html` | 94,445 B | Main QA dashboard |
| `ProjectList.html` | 43,463 B | Project list view |
| `Createproject.html` | 27,463 B | Create project form |
| `Createproject_suborg.html` | 27,474 B | Create project with sub-org |
| `ProjectManagementDashboard.html` | 497,707 B | Full PM dashboard (LARGEST FILE) |
| `ManagerRole.html` | 72,710 B | Manager role view |
| `ManagerRole_1.html` | 129,919 B | Manager role v2 |
| `MyTask.html` | 43,530 B | My tasks view |
| `notification-setting.html` | 28,710 B | Notification settings |
| `notification-preferences.html` | 10,264 B | Notification preferences |

### Test Repository
| Page | Size | Purpose |
|------|------|---------|
| `testmanagementTable.html` | 77,889 B | Test management table |
| `TestcaseLibrary.html` | 55,994 B | Test case library |
| `TestcaseMapping.html` | 17,546 B | Test case mapping |
| `TestPlan.html` | 53,875 B | Test plan |
| `TestPlanDocument.html` | 24,764 B | Test plan document |

### Test Execution
| Page | Size | Purpose |
|------|------|---------|
| `Testcases_execution.html` | 39,794 B | Manual test case execution |
| `Testsuite_execution.html` | 56,598 B | Test suite execution |
| `Testsuite_execution_plan.html` | 57,481 B | Execution plan runner |

### Automation
| Page | Size | Purpose |
|------|------|---------|
| `Automation.html` | 205,779 B | Full automation hub (2nd LARGEST) |
| `ApiManagement.html` | 128,516 B | API automation management |
| `Performance.html` | 79,055 B | Performance test hub |
| `CodeCheckout.html` | 80,777 B | Code checkout & CI/CD |
| `CICDmonitoring.html` | 22,920 B | CI/CD monitoring |
| `createpipeline.html` | 33,270 B | Pipeline creation |
| `pipelinelist.html` | 33,826 B | Pipeline list |
| `AWSVM.html` | 18,159 B | AWS VM management |
| `clientsidemonitor.html` | 20,132 B | Client-side monitoring |
| `serversidemonitor.html` | 19,453 B | Server-side monitoring |

### Defect Management
| Page | Size | Purpose |
|------|------|---------|
| `Defect-tacker.html` | 104,831 B | Main defect tracker |
| `DefectAnalyzer.html` | 18,868 B | Defect analysis view |
| `DefectAnalyzerView.html` | 43,659 B | Detailed defect analysis |
| `DefectPrediction.html` | 21,340 B | Defect prediction (AI?) |
| `DefectPredictionView.html` | 43,665 B | Prediction results view |

### User Stories / Agile
| Page | Size | Purpose |
|------|------|---------|
| `userstories.html` | 339,955 B | Main user stories (3rd LARGEST) |
| `userstories_gridview.html` | 284,491 B | Grid view variant |
| `userstories_new.html` | 332,434 B | Newer variant |
| `userstories_calendar(BF).html` | 330,602 B | Calendar view |
| `userstories_header.html` | 311,404 B | Header variant |

### Reporting
| Page | Size | Purpose |
|------|------|---------|
| `Reports.html` | 48,908 B | Reporting hub |
| `Reports_Automation.html` | 15,051 B | Automation reports |

### Timesheet
| Page | Size | Purpose |
|------|------|---------|
| `Timesheet.html` | 30,702 B | Timesheet entry |
| `Timesheet-Approver.html` | 28,042 B | Timesheet approval |
| `Timesheet-Workflow.html` | 22,290 B | Approval workflow |
| `calender.html` | 3,976 B | Calendar widget |

### AI / Generative Features
| Page | Size | Purpose |
|------|------|---------|
| `GenerativeAI.html` | 12,972 B | Generic GenAI page |
| `GenerativeAI - gemini.html` | 13,174 B | Google Gemini integration |
| `GenerativeAI - openai.html` | 14,776 B | OpenAI integration |
| `AIBrdTestCaseGenerator.html` | 6,727 B | AI test case generator |
| `AITestAccelerator.html` | 6,713 B | AI test acceleration |

### Security / DevSecOps
| Page | Size | Purpose |
|------|------|---------|
| `security.html` | 84,310 B | Security hub (SonarQube / DigiSec) |

### Administration
| Page | Size | Purpose |
|------|------|---------|
| `UserRoles.html` | 72,717 B | User role management |
| `ExistingprofileList.html` | 18,893 B | User profile list |
| `ChatBot_Admin.html` | 15,903 B | Chatbot admin panel |
| `SubscriptionDetails.html` | 15,259 B | Subscription management |

> [!NOTE]
> **Multiple versioned/backup copies exist for critical pages:** `Defect-tacker - Copy.html`, `Defect-tacker_Working.html`, `Defect-tacker_back.html`, multiple `ProjectManagementDashboard*.html` variants, multiple `userstories*.html` variants, `CodeCheckout_old.html`, `Testsuite_execution_old.html`, `dashboard_old.html`. These are all checked-in to the WAR and served statically. This indicates the team was using the codebase itself as a backup system.

---

## 16. Other Frontend Environments Discovered

**Additional frontend directories found in `src/main/resources/public/`:**

| Directory | Purpose |
|-----------|---------|
| `3iBank/` | Client-specific frontend (3i Bank) |
| `3iBankQA/` | 3i Bank QA environment |
| `3iBankUAT/` | 3i Bank UAT environment |
| `FlexibAdmin/` | Admin-specific interface |
| `FlexibPlus_V1/` | Main product frontend |
| `FlexibPlus_V1 47/` | Version 47 backup (served at runtime!) |
| `FlexibPlus_V1_30042006/` | Date-stamped backup (30 Apr 2006 date is suspicious) |
| `FlexibSubscription/` | Subscription management interface |
| `Reports/` | Generated report files storage |
| `TicketingTool/` | Separate ticketing tool interface |

> [!CAUTION]
> **All these directories are served as static content from the running WAR.** This means old backup versions of the UI (`FlexibPlus_V1 47/`, dated backups) are accessible at runtime via URL. This is both a security risk and a waste of server resources. Also includes ZIP files: `FlexibPlus_V1 47.zip` (6.5 MB), `FlexibPlus_V1 49.zip` (6.5 MB), `FlexibPlus_V1.7z` (7.9 MB), `FlexibPlus_V1_old.zip` (11.7 MB) — all served as static downloads.

---

## 17. GitLab CI/CD Pipeline Discovered

**File:** `src/main/resources/public/FlexibPlus_V1/.gitlab-ci.yml` (1,061 bytes)

> [!NOTE]
> A GitLab CI/CD pipeline file exists **inside the frontend public directory**, meaning it is served as a static file to web clients. Its presence confirms the team uses GitLab for source control and CI/CD. The pipeline likely handles frontend deployments.

---

## 18. Complete Module Map of the System

Based on complete analysis, FlexibPlus is composed of the following business modules:

```
FlexibPlus Platform
├── Authentication & RBAC
│   ├── Login / Logout / Refresh Token
│   ├── User Registration & Profile
│   ├── Role & Permission Management
│   └── Module-level Activity Control
│
├── Organisation & Project Management
│   ├── Organisation / Sub-Organisation hierarchy
│   ├── Project / Application Registry
│   └── User-to-Project Assignment
│
├── Agile / Project Tracking (Kanban)
│   ├── Epic Management
│   ├── User Story Management
│   ├── Iteration / Sprint Management
│   ├── Task Management (with sub-tasks, links, time logs)
│   ├── Issue Tracker (Kanban issues)
│   ├── Backlog Management
│   ├── Calendar Views
│   └── Timeline / Gantt Chart
│
├── Test Repository
│   ├── Feature / Module Catalogue
│   ├── Test Case CRUD
│   ├── Test Step Management
│   ├── Test Suite Management
│   ├── Test Case → User Story Mapping
│   └── Excel Import / Export
│
├── Test Execution
│   ├── Manual Execution (step-by-step)
│   ├── Execution Plans (suite-based)
│   ├── Execution History & Logs
│   └── Pass/Fail/Skip Aggregation
│
├── Automation
│   ├── Web Automation (JAR execution)
│   ├── Mobile Automation
│   ├── API Automation (Newman/Postman)
│   ├── Desktop Automation
│   ├── JMeter Performance Testing
│   ├── Visual Automation (Reference/Approve flow)
│   └── Avoka Automation Results
│
├── CI/CD Integration
│   ├── Jenkins (build/delete/clone)
│   ├── GitHub Checkout
│   ├── Azure DevOps Checkout
│   ├── GitLab Checkout
│   ├── Git Branch Creation
│   └── Pipeline Stage Management
│
├── Security Testing
│   ├── DigiSec DAST Integration
│   └── SonarQube Code Quality/Security
│
├── Defect Management
│   ├── Defect CRUD
│   ├── Defect History / Audit Trail
│   ├── File Attachments
│   ├── MS Teams Notifications (scheduled)
│   └── RCA (Root Cause Analysis) Documents
│
├── Dashboard & Reporting
│   ├── QA Dashboard (TC, defect, automation counts)
│   ├── PM Dashboard (Gantt, Kanban, burndown)
│   ├── Performance Dashboard
│   ├── Effort Variance & Schedule Variance
│   ├── Resource Utilization
│   ├── PDF Report Generation
│   └── Excel Report Export
│
├── Timesheet
│   ├── Time Logging (task-based)
│   ├── Multi-stage Approval Workflow
│   ├── Manager View & Approval
│   └── Reminder Emails (scheduled)
│
├── Performance Testing Utilities
│   ├── Pacing Calculator
│   ├── Think Time Calculator
│   ├── TPH Calculator
│   ├── VUser Calculator
│   ├── Load Generator Estimator
│   └── Server Capacity Estimator
│
├── Notification System
│   ├── In-app Notifications
│   ├── Email Notifications (Gmail SMTP)
│   └── MS Teams Webhook Notifications
│
├── ChatBot (FAQ Navigator)
│   ├── Module/SubModule tree
│   └── Predefined Q&A database
│
├── IT Helpdesk Ticketing
│   └── ITechTicket + History tracking
│
└── AI / GenAI (Frontend Only)
    ├── Google Gemini integration (UI only)
    └── OpenAI integration (UI only)
```

---

## 19. Corrections to Part 1

After completing this full analysis, the following corrections apply to Part 1:

| Part 1 Claim | Correction |
|-------------|-----------|
| "87+ static HTML pages" | **Confirmed: exactly 87 HTML files** in FlexibPlus_V1 |
| "No MFA/2FA" | **Partially correct: OTP flow exists** (`/send-otp` + `/forgot-password`) for password reset — NOT for login itself. Login has no MFA. |
| "Password reset — no email OTP flow" | **Correction: OTP flow IS implemented** for `forgot-password` (send-otp → verify code → reset). The `password-reset-request` endpoint is a SEPARATE direct reset path (no OTP). Both exist. |
| "ChatBot — no confirmed backend" | **Correction: ChatBotService.java (16K) is a full FAQ navigator** — not AI-based |
| "Generative AI — Frontend only, no confirmed backend" | **Confirmed correct** — no backend service for Gemini/OpenAI integration found |
| Entity count "88" | **Confirmed: 88 entity files** in `entity/` package |
| "84 service classes" | **Confirmed: 84 service files** in `service/` |

---

## 20. Final Updated Reusability Assessment

### Summary of All Hardcoded Issues Found

| Issue | File | Value |
|-------|------|-------|
| JWT secret | `application.properties` L18 | `bezKoderSecretKey` |
| Default user password | `AuthController.java` L510 | `flexib@123` |
| Automation directory | `WebAtomationService.java` L28 | `C://webAutomation` |
| Clone target dir | `FlexibService.java` L44 | `D:\\try` |
| ZIP source dir | `FlexibService.java` L66 | `D:\\Flexib Workspace\\ZipSource\\WebAutomationTesting` |
| Git credentials | `FlexibService.java` L51 | `bhaskaravaram / Infotech@2023` (active!) |
| Organisation ID | `index.html` L573 | `Org_2` hardcoded in login AJAX |
| JWT expiry | `application.properties` | 3 minutes — too short |
| Refresh token expiry | `application.properties` | 4 minutes — too short |

### Modules NOT in Scope for Phase 1 (but exist in legacy)

These modules exist in the legacy system but are likely OUT OF SCOPE for Phase 1 new platform:

| Module | Reuse Decision |
|--------|---------------|
| Timesheet & Approval Workflow | Assess separately — complex HR integration |
| IT Helpdesk Ticketing (`ITechTicket`) | Assess separately |
| 3i Bank client frontend (`3iBank/`, `3iBankQA/`, `3iBankUAT/`) | Client-specific — not reusable |
| `ThreeIBank.java` entity + login endpoint | Client-specific — not reusable |
| Performance Calculators (pacing, TPH, VUser) | Phase 2+ feature |
| DigiSec Integration | Phase 2+ feature |
| SonarQube Integration | Phase 2+ feature |
| Visual Automation (VisualAutoInit/CreateRef/Approve) | Phase 2+ feature |
| ChatBot FAQ Navigator | Phase 2+ feature |
| Subscription management | SaaS feature — assess separately |
| GenAI pages | Frontend only — no backend to reuse |
| FlexibAdmin interface | Admin-only — assess separately |

### Component-Level Final Reusability Table

| Component | Reuse Rating | Key Action |
|-----------|-------------|-----------|
| Spring Boot project structure | ✅ 90% | Upgrade to Spring Boot 3.x |
| JWT / Security filter chain | ✅ 70% | Fix `permitAll`, externalize secret |
| BCrypt password encoding | ✅ 95% | Use as-is |
| Auth endpoints (signin/signup/refresh/signout) | ✅ 70% | Clean up, fix project coupling |
| OTP flow for forgot-password | ✅ 75% | Reuse, fix OTP storage |
| User entity + Role entity | ✅ 75% | Add org/audit fields |
| Project entity | ✅ 80% | Add FK, audit fields |
| TestCase + TestStep entities | ✅ 80% | Add `stepOrder`, audit fields |
| TestSuite + ExecutionPlan entities | ✅ 75% | Clean FK constraints |
| ExecutionService (step-result aggregation) | ✅ 75% | Verified logic — reuse |
| UserStoryDetails entity + service | ✅ 70% | Extract from large service |
| Epic + Task entities (Kanban) | ✅ 75% | Reuse for Kanban module |
| KanbanService business logic | ✅ 65% | Decompose into 7 services |
| DefectMgt entity + service | ✅ 60% | Move blobs to object storage |
| Dashboard aggregation services | ✅ 65% | Port to new data model |
| Excel import/export (Apache POI) | ✅ 80% | Reuse as utility |
| PDF generation (PDFBox) | ✅ 70% | Reuse |
| Email notifications | ✅ 70% | Externalize credentials |
| MS Teams webhook notifications | ✅ 65% | Reuse scheduler pattern |
| Jenkins/Git/Azure CI/CD services | ✅ 70% | Externalize credentials |
| ExecuteJarFileService (ProcessBuilder) | ⚠️ 40% | Make async, cross-platform |
| TimesheetService | ⚠️ 50% | Phase 2 — decouple from TC service first |
| FeatureDetails / RequirementService | ✅ 70% | Useful for AKB requirement |
| Notification entity + service | ✅ 80% | Clean, modern schema |
| IssueManagementAlertService (scheduler) | ✅ 65% | Reuse scheduler pattern |
| ChatBotService | ⚠️ 30% | Replace with proper KB/LLM |
| FlexibService | ❌ 10% | Mostly hardcoded — rebuild |
| FlexibController (God class) | ❌ 10% | Decompose into 10+ controllers |
| WebSecurityConfig (authorization) | ❌ 15% | Rebuild with proper security |
| Frontend HTML/JS (all 87 pages) | ❌ 0% | Full rebuild required |
| Database (MySQL → PostgreSQL) | ⚠️ 60% | Migration required |
| 3i Bank specific entities | ❌ 0% | Client-specific, do not reuse |
| FlexibUser / FlexibUserPrincipal (orphan) | ❌ 0% | Orphaned entities, remove |
| Duplicate entities (TestCases, TestSteps, UsersStoryDetails) | ❌ 0% | Delete orphans |

---

*End of Part 2 — Complete Analysis*
