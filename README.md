# WEB-API-v2
Test hexagonal framework based WEB API for test application

1. Purpose
This page documents the technical design & development plan for integrating the SRS Results API with the StudentGrades scoring platform.
The output of this page satisfies the sprint acceptance criteria requiring Technical Design Specification for StudentGrades integration.
________________________________________
2. Background
•	LP wants to automate student scoring decisions for STT colleges.
•	The Results API orchestrates data from SIF, UniHub, Graphana, KML, and StudentGrades.
•	Integration must use Hexagonal Architecture with ordered execution steps.
________________________________________
3. High-Level Flow
1.	A client (rollno UI / internal service) calls Results API with:
o	APID
o	College Portfolio
o	Model Name (AG/OM)
2.	Results API:
o	Converts APID → SIF
o	Validates Model + Portfolio
o	Builds StudentGrades request
o	Calls StudentGrades via OAuth2
3.	StudentGrades returns scoring fields (as per sheet C2).
4.	Results API:
o	Maps scoring results
o	Validates mandatory fields
o	Returns response or error depending on scenario
________________________________________
4. Architecture (Hexagonal)
Components
•	Domain:
o	ResultsContext
o	ResultsStep
o	StudentGradesPort
o	Request/Response DTOs
•	Application:
o	ResultsFlowService
o	Ordered Steps (validate→build→invoke→map→validate-response)
•	Adapters:
o	REST Controller
o	WebClient Adapter for StudentGrades
o	OAuth Client
o	TLS/JKS Config (student_class.jks)
________________________________________
5. Request & Response Contracts
Request (from C1 sheet)
•	Model Name → modelName
•	SIF → studentIdentificationNum
•	College Portfolio → studentGroupId (hardcoded for v1)
Response (from C2 sheet)
•	StudentGrades fields mapped dynamically to structured DTOs
________________________________________
6. Scenarios to Implement
Scenario 1 – Valid Model, Portfolio, SIF
→ Success, return scoring results
Scenario 2 – Invalid College Portfolio
→ Validation error OR StudentGrades error mapping
Scenario 3 – Invalid Model Name
→ Same as Scenario 2
________________________________________
7. Security
•	OAuth2 client-credentials
•	HTTPS + TLS
•	Truststore: student_class.jks
•	No secrets in source code
________________________________________
8. Development Tasks
Create the following subtasks in STTSTROL JIRA:
T1 – Domain Models
•	ResultsContext
•	DTOs
•	StudentGradesPort
T2 – Implement Steps
•	Validate request
•	Build request
•	Invoke StudentGrades
•	Map SL response
•	Validate response
T3 – Outbound Adapter
•	WebClient
•	OAuth client
•	JKS configuration
T4 – Controller
•	New endpoint
•	Flow invocation
T5 – Error Mapping
•	Map SL errors to Results API errors
T6 – Testing
•	Unit tests (steps)
•	Integration tests (mock StudentGrades)
T7 – Documentation
•	Update this Confluence page
•	Attach mapping files (R1 + R2)
________________________________________
9. Testing Plan
Unit Tests
•	Happy path
•	Invalid model
•	Invalid portfolio
•	Missing SIF
Integration Tests
•	Mock SL responses
•	Validate mapping & errors
________________________________________
10. Risks / Open Questions
•	Final model list for DG/DM?
•	Full SL error contract?
•	Should results be persisted?
