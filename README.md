# Config JSON Documentation

This folder explains how each config JSON under assets/configs/json is structured, how it is executed by the app, and how to safely update it.

## Quick Reference Matrix

| Config | Initial Page | Flows | Primary Type | Typical Submit Pipeline | Core Entities |
|---|---|---:|---|---|---|
| ATTENDANCE.json | manageAttendance | 3 | Template-heavy | CUSTOM_DATA -> CREATE_EVENT -> NAVIGATION | AttendanceRegisterModel, AttendeeModel, AttendanceLogModel |
| CLOSEHOUSEHOLD.json | CLOSEHOUSEHOLD | 2 | Form + success template | FETCH_TRANSFORMER_CONFIG -> CREATE_EVENT -> NAVIGATION | HOUSEHOLD, INDIVIDUAL, MEMBER, PROJECTBENEFICIARY |
| COMPLAINTS.json | complaintInbox | 4 | Template + form | FETCH_TRANSFORMER_CONFIG -> CREATE_EVENT -> NAVIGATION | PgrServiceModel |
| HFREFERRAL.json | referralInbox | 4 | Template + form | FETCH_TRANSFORMER_CONFIG -> CREATE_EVENT/UPDATE_EVENT -> NAVIGATION | HFReferralModel |
| INVENTORY.json | manageStock | 10 | Mixed templates/forms | FETCH_TRANSFORMER_CONFIG -> CREATE_EVENT -> UPDATE_STOCK_BALANCE -> NAVIGATION | ProjectFacilityModel, StockModel, ProductVariantModel |
| PERMISSION_HANDLER.json | permissionHandler | 1 | Template | REQUEST_PERMISSION | PermissionModel |
| REGISTRATION.json | searchBeneficiary | 15 | Complex mixed workflow | FETCH_TRANSFORMER_CONFIG -> CREATE_EVENT/UPDATE_EVENT -> UPDATE_STOCK_BALANCE -> NAVIGATION | IndividualModel, HouseholdModel, ProjectBeneficiaryModel, TaskModel |
| STOCKRECONCILIATION.json | stockReconciliationDetails | 2 | Form + success template | FETCH_TRANSFORMER_CONFIG -> CREATE_EVENT -> NAVIGATION | StockReconciliationModel |
| STOCKREPORTS.json | viewReports | 3 | Template reporting | SEARCH_EVENT-driven filters | StockModel, StockReconciliation |

## Document Index

- ATTENDANCE.json: ATTENDANCE.md
- CLOSEHOUSEHOLD.json: CLOSEHOUSEHOLD.md
- COMPLAINTS.json: COMPLAINTS.md
- HFREFERRAL.json: HFREFERRAL.md
- INVENTORY.json: INVENTORY.md
- permission_handler.json: PERMISSION_HANDLER.md
- REGISTRATION.json: REGISTRATION.md
- STOCKRECONCILIATION.json: STOCKRECONCILIATION.md
- STOCKREPORTS.json: STOCKREPORTS.md

## How These JSON Files Work

Each file is a flow-definition document consumed by the Digit flow engine. At runtime, the engine:

1. Loads the file by module name.
2. Opens initialPage.
3. Resolves each flow by name and screenType.
4. Runs initActions before rendering the page.
5. Executes wrapperConfig searches and joins data into contextData.
6. Renders header/body/footer templates and form pages.
7. Executes onAction pipelines for submit/navigation.

## Common Structure

- name: Module identifier.
- initialPage: First page/flow opened for this module.
- flows: List of screens or forms.
- screenType: TEMPLATE or FORM.
- initActions: Startup behavior (search, clear state, scanner, etc.).
- wrapperConfig: Data source binding (root entity, relations, searchConfig).
- body/header/footer: UI templates with actions.
- onAction: Submit pipeline (transform/create/update/navigate).

## Expression and Data Binding

- {{navigation.*}}: Data passed from previous page.
- {{singleton.*}}: Global context, selected project, user, tenant.
- {{contextData.*}}: Data loaded by wrapper/search.
- {{widgetData.*}}: Local widget state.
- {{fn:*}}: Utility functions from the flow engine.

## Safe Change Workflow

1. Duplicate the target JSON.
2. Change one flow at a time.
3. Verify action names and entity names are valid.
4. Confirm visibility conditions and data paths exist.
5. Run end-to-end in app before promoting to main config.

## Frequent Failure Points

- Wrong flow name in NAVIGATION properties.
- Mismatch between wrapperName/dataSource/contextData keys.
- Missing search filters causing empty list screens.
- Invalid expression paths in visibility or value templates.
- submit onAction missing FETCH_TRANSFORMER_CONFIG before CREATE_EVENT.
