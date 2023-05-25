---
layout: default
title: Multi Tenant
parent: Integration Platform iPaaS
nav_order: 2
has_children: false
---
# Multi tenant
OwlStreet adopts a multi-tenant structure, wherein each customer is treated as a separate tenant. This structure ensures that each customer's data and configurations are securely isolated and managed independently. Within this multi-tenant environment, OwlStreet provides dedicated staging and production environments for each tenant.

The staging environment serves as a testing ground where customers can verify and validate their configurations before deploying them to the production environment. This allows customers to ensure that their integrations and workflows are functioning as intended without impacting live operations. The staging environment provides a safe and controlled space for configuration testing and fine-tuning.

Once the configurations in the staging environment have been thoroughly tested and validated, customers can confidently deploy them to the production environment. The production environment is the live environment where actual data flows and integrations take place. OwlStreet ensures that the production environment remains stable, reliable, and optimized for seamless data exchange and system integration.

Within each tenant, there can be multiple integrations established. These integrations represent the connections between various systems, applications, and endpoints. Messages, representing data or events, can be routed among these integrations within the same message type. This means that messages of a specific type can be efficiently transmitted and processed among the interconnected integrations, allowing for streamlined communication and data flow within the tenant's ecosystem.

By providing dedicated staging and production environments, along with the ability to route messages among integrations within the same message type, OwlStreet ensures a robust and flexible framework for managing customer configurations, testing, deployment, and smooth data exchange. This multi-tenant structure enables customers to have control over their specific configurations while maintaining scalability, security, and reliability within their own isolated environments.

## Security based on Auth0
OwlStreet prioritizes the security of its platform and leverages Auth0, a leading authentication and authorization platform, to ensure robust security measures. Auth0 provides a comprehensive set of features and capabilities that enhance the login and authentication process, offering a secure environment for users.

With Auth0, OwlStreet offers multiple authentication options to its users. Users can log in using traditional username and password credentials, which are securely stored and encrypted. Additionally, OwlStreet supports multi-factor authentication (MFA), adding an extra layer of security to the login process. MFA typically requires users to provide additional verification, such as a temporary code sent to their mobile device, to validate their identity.

Furthermore, OwlStreet's integration with Auth0 extends beyond traditional login methods. Users can also leverage integrations with platforms such as GitHub, enabling them to authenticate and access the OwlStreet platform using their existing credentials from those platforms. This integration simplifies the login process for users and ensures a seamless user experience while maintaining security.

Auth0 provides industry-leading security standards and compliance certifications to safeguard user data. It employs advanced security protocols and encryption algorithms to protect sensitive information and prevent unauthorized access. Additionally, Auth0 offers features like identity management, access control, and audit logging, further enhancing the security posture of OwlStreet.

By leveraging Auth0 as the authentication and authorization platform, OwlStreet ensures that user logins and access to the platform are protected by industry-standard security measures. This partnership allows OwlStreet to focus on providing a secure and reliable integration experience, while users can enjoy the convenience and peace of mind knowing that their data is protected by a trusted and robust security solution.

In summary, OwlStreet's security framework is fortified by integrating Auth0 as the authentication and authorization platform. Auth0's industry-leading features and security measures, including various login methods, MFA, and integration with platforms like GitHub, ensure a secure login experience for users. OwlStreet and Auth0 work together to prioritize data protection and user security, allowing users to confidently access and utilize the integration platform.

## Partner friendly
OwlStreet offers a partner-friendly solution that allows customers to invite their partners to access their integrated solution. This partnership functionality enables seamless collaboration and information sharing between customers and their trusted partners. 

Within OwlStreet's control panel, customers have the capability to grant access to specific integrations or provide their partners with access to the entire solution. This access control ensures that partners can view and interact with the integrations and data that are relevant to their role and responsibilities.

By extending an invitation, partners can access the integrated solution using a single login. This streamlines the authentication process and eliminates the need for multiple credentials. Once logged in, partners can conveniently navigate the control panel and access the shared data and integrations, gaining insights and contributing to the collaboration seamlessly.

The partner-friendly solution ensures that partners have a unified view of the data within the shared environment. This unified access enables efficient communication, data analysis, and decision-making between customers and their partners. It promotes transparency, enhances productivity, and fosters effective teamwork in managing the integrated solution.

OwlStreet's partner-friendly approach empowers customers to collaborate closely with their partners while maintaining control over data security and access rights. It enables customers to harness the expertise and resources of their partners, leading to more effective integration management and business outcomes.

In summary, OwlStreet's partner-friendly solution allows customers to invite partners to access their integrated solution through the control panel. Partners can view and interact with specific integrations or the entire solution using a single login. This unified access promotes collaboration, transparency, and productivity between customers and their partners, enabling them to work together seamlessly in managing the integrated solution.