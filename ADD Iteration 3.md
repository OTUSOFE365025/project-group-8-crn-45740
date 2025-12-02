# **1.4 ADD Iteration 3: Addressing Quality Attribute Scenario Driver**
This iteration focuses on the QA-4 quality attribute scenario defined earlier in the architecture design process. This scenario addresses Privacy and Security by ensuring that when a user attempts to access unauthorized academic information, the system denies access and logs the attempt to comply with institutional privacy policies. 

*The inputs relevant to this iteration include:*
- The selected Quality Attribute Scenario 4 (Privacy & Security).
- Previously defined architectural constraints.
- Existing components identified in Iterations 1 and 2 (AUTH SSO Provider, API Gateway, User/Profile Service, Monitoring Component).
- Functional requirements related to access control and identity validation.

# 1.4.1 Step 2: Establish Iteration Goal by Selecting Drivers
In this iteration, the architect chooses to focus on Quality Attribute Scenario 4: When a user tries to access unauthorized academic information, the system denies access and logs the attempt, enforcing institutional privacy policies.

# 1.4.2 Step 3: Choose One or More Elements of the System to Refine
For this scenario, the elements to refine are the ones related to authentication and security:
- AUTH SSO Provider
- API Gateway (Ingress)
- User/Profile Service
- Monitoring Component

# 1.4.3 Step 4: Choose One or More Design Concepts That Satisfy the Selected Drivers 
The design concepts used in this iteration are the following:
| **Design Decisions & Location** | **Rationale & Assumptions** |
|--------|--------------------------------|
| Introduce authorization/access control to ensure that only permitted users can access sensitive information | Prevents users with unauthorized roles from accessing higher-level information, such as grades and exam details. In addition, ensures that certain data can only be accessed by the owner and those they choose to share it with, such as the lecturer and a student’s grades. |
| Introduce audit logging to monitor all activity, including unauthorized attempts | By actively logging all actions committed by each user to the monitoring component, attempts to access prohibited information can be quickly traced back to the perpetrator. |

# 1.4.4 Step 5: Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces 
The instantiation design decisions are summarized in the following table: 
| **Design Decisions & Location** | **Rationale & Assumptions** |
|--------|--------------------------------|
| Apply role-based authorization and access control | Use the AUTH SSO provider to authenticate users and assign identity tokens. The API gateway must validate these tokens to allow permitted users to access privatized information. This ensures that users without the correct identity tokens are rejected, while recording the attempt. |
| Define clear-cut roles with varying levels of access | Provide a profile service that stores user attributes, roles and permissions. The layered architecture system can take advantage of this and maintain security between different types of accounts. |
|Use the API Gateway to monitor all requests and track them in an audit log | Allow the gateway to keep track of all activities that pass through, writing them to a log and pushing them to an external monitoring component, such as CloudWatch. This allows administrators to easily keep track of malicious attempts to access prohibited information. |

The results of these instantiation decisions are recorded in the next step. 

# 1.4.5 Step 6: Sketch Views and Record Design Decisions 
The following figure displays a refined deployment diagram that includes the incorporation of the security features. It explicitly includes monitoring components that ensure authorization is enforced and audit trails are included. The diagram illustrates how the SSO authenticates users, the API gateway validation access tokens and how requests, especially unauthorized requests are logged within the system. 
<img width="1228" height="465" alt="Screenshot 2025-12-01 194535" src="https://github.com/user-attachments/assets/fb8ecc57-2161-4bc5-8a12-aab7c8ce3634" />
*Figure 1 - Refined Deployment diagram*

The following table describes responsibilities for elements that have not been previously listed (in past iterations):
| **Element** | **Responsibility** |
|--------|--------------------------------|
| AUTH (SSO Provider) | Checks users' institutional credentials to generate (identity) tokens. The tokens have the user's account ability encoded within, providing permissions to the account. |
| API Gateway | Acts as the central entry point for all client requests into the system, handling all security and routing. The gateway validates users tokens from the SSO and enforces the permissions applicable to the account. |
| Analytics & Monitoring Service | Provides monitoring services and audits/ logs all events that occur into the database |
| User Profile Service | User account management & profile related information (i.e. personal information, transcript, etc). |


The UML sequence diagram shown in figure 2 illustrates how the API gateway enforces permission abilities to support UC - 2 (Maintaining student privacy) which is associated with QA - 4 (Privacy & Security). It shows how the system checks users credentials, and generates a token that is passed between different system components to verify the users ability to access specific features and information.
<img width="1160" height="545" alt="Screenshot 2025-12-01 204216" src="https://github.com/user-attachments/assets/a8685d40-4702-4d9d-9cf4-895d9866f1d1" />
*Figure 2 Sequence Diagram of UC - 2*

From the interactions presented and identified in the sequence diagram above, initial methods for interfaces of all the interacting elements present can be identified: 
| Method Name                         | Description                                     |
|------------------------------------|-------------------------------------------------|
| **Element:** AIDAP Assistant (UI)  |                                                 |
| submitGradeRequest(friendId)       | Student submits request to view their friend's grades |
| displayAccessDeniedMessage()       | User receives a prompt stating their access is denied |
| certify(token)                     | User's credentials                              |
| **Element:** API Gateway   |                                                 |
|sendRequest(token) | Passes request information & credentials of user making the request |
| returnError("UnauthorizedAccess") | Returns authorization failure |
| **Element:** AUTH SSO | |
| validateAuthorization(token) | Compares and determines if logged in account and requested account are equal or vary |
| **Element:**  User Profile & Services | |
| fetchGradeIfAuthorized(friendID, token) | Attempts to fetch grade if credentials are equal, if not it denies access to information |
| accessDenied() | Replies with a denied access |
| **Element:** Monitoring & Analytics | |
| auditRequestANDLog() | Passes users request to DB to be logged and stored |



# 1.4.6 Step 7: Perform Analysis of Current Design and REview Iteration Goal and Achievement of Design Purpose 
In the following iteration, design decisions have been made to address QA -4. The following table summarizes the status of the different drivers and the decisions that were made during the iteration. Drivers that were completely addressed in the period's iteration have been removed from the table. 
| Not Addressed | Partially Addressed | Completely Addressed | Design Decisions Made During the Iteration |
|---------------|---------------------|----------------------|--------------------------------------------|
| | UC - 2 | | Required modules that support Student privacy have been identified. |
| | QA - 4 | | Security & Privacy partially addressed through the user authentication and audit process to help aid in denying unauthorized access. |
| | CRN - 2 | | The architectural layout of the system incorporates integrated protection practices, partially addressing the security concern with AI sensitive information responses. |
| | CRN - 7 | | Additional modules were identified, separating the user history and account information into its own location. |
| | | CON - 4 | Logically structured architecture with clearly defined layers separating the users information into its own module, with API gateway ensuring history is only accessible to the user with the correct login credentials (Token generated by SSO). |

