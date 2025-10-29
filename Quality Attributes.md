# Quality Attributes for AI-Powered Digital Assistant Platform (AIDAP)

| **ID** | **Quality Attribute (QA)** | **Scenario** | **Associated Use Case** |
|--------|-----------------------------|---------------|--------------------------|
| QA-1 | Performance | When the system is handling up to 5,000 concurrent users, it must respond to queries within an average of 2 seconds while maintaining 99.5% uptime per month. | UC-5 – High Traffic Operations |
| QA-2 | Availability | During maintenance or unexpected failures, the system automatically activates fail-over mechanisms to ensure continuous operation. | UC-4 – Server Maintenance, UC-5 – High Traffic Operations |
| QA-3 | Usability | Students should be able to interact naturally via text, voice, or dashboard without training, following conversational-design standards. | UC-1 – Merge Multiple Calendars, UC-6 – Referencing Chat History |
| QA-4 | Privacy and Security | When a user tries to access unauthorized academic information, the system denies access and logs the attempt, enforcing institutional privacy policies. | UC-2 – Maintaining Student Privacy |
| QA-5 | Scalability | The cloud-native architecture must dynamically scale resources during peak periods to maintain stable performance with <= 10% degradation. | UC-5 – High Traffic Operations |
| QA-6 | Maintainability | System Maintainers can deploy updates with zero downtime and rollback within five minutes, ensuring the service remains available. | UC-4 – Server Maintenance |
| QA-7 | Adaptability | The assistant analyzes previous user interactions to personalize responses and improve relevance over time. | UC-6 – Referencing Chat History |
