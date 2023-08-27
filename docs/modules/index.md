---
layout: default
title: Built in modules
nav_order: 30
has_children: true
---
# Built in modules
OwlStreet is equipped with a comprehensive set of built-in modules, all managed through its central control panel. This role-based user interface offers a visual representation of integrations, allowing users to easily navigate and monitor their data workflows. Unlike traditional approaches, OwlStreet goes beyond hiding configuration files and transactions. It provides transparency by making these normally hidden aspects accessible for users to track and analyze. This empowers businesses to have full visibility and control over their integrations, simplifying management and troubleshooting processes while ensuring a seamless and efficient data integration experience.

## Tenant list
OwlStreet's tenant list feature provides a concise overview for logged-in users, displaying the tenants they have access to. Users can easily select the current tenant they wish to work with, and a default tenant is automatically chosen upon login. The tenant list ensures a clear separation between the staging and production environments, offering distinct environments for testing and live operations. This functionality streamlines user navigation, allowing for efficient management and utilization of different tenant environments within OwlStreet.


## Manage configuration
OwlStreet's tenant configuration feature offers a user-friendly, form-based setup for routing, filtering, transformations, and settings. Sensitive information is always encrypted, ensuring data security. Users can access and modify sensitive data only through decryption, ensuring privacy. This feature simplifies the configuration process, enhances security, and empowers users to efficiently manage and customize their data integration settings with confidence.

## Integration dashboard
OwlStreet's integration dashboard provides a comprehensive overview of message types, queue depths, job activity, and statistics for each integration within a tenant. Users can easily monitor and analyze integration performance. The dashboard allows selecting specific timeframes to investigate detailed statistics through interactive graphs, enabling users to gain insights and optimize integration processes for enhanced efficiency.

## Query
OwlStreet's query service enables users to search for product data and access the latest information related to product details, pricing, and inventory status. The service organizes and stores data from various source systems, grouping it per system and combining it into a consolidated and up-to-date record. This functionality empowers users to quickly retrieve accurate and comprehensive product information, streamlining decision-making, and ensuring reliable data for efficient operations.

## Job status
The job status feature in OwlStreet provides real-time visibility into currently running jobs, their progress, and details of completed jobs. Users can monitor job duration, the number of processed entries, and view detailed logs indicating the specific objects processed. This feature also allows users to cancel ongoing jobs if necessary. Failed jobs are highlighted on the main dashboard, ensuring prompt attention and enabling efficient troubleshooting and resolution of integration issues.

## Channel
The channel manager in OwlStreet enables the configuration of product availability, pricing, and inventory levels for specific channels. Users can define query filters to determine which products and variants are included for each channel or channel node in a tree structure. The channel manager leverages data from all source systems providing product information, allowing comprehensive filtering options. The results can be used for routing purposes and adding category information to target systems, ensuring accurate and targeted data distribution across channels.

## Logistics
The logistics fulfillment station in OwlStreet consolidates orders from multiple sources for efficient fulfillment. Warehouse staff can utilize a picking list that includes multiple orders, aided by barcode scanners, images, and color/sound indications to ensure correct product handling. The station facilitates the order fulfillment process, allowing for the booking and printing of shipping delivery slips and address labels. This comprehensive solution streamlines logistics operations, minimizing errors, and enhancing the overall efficiency of order fulfillment workflows.

## Stocktake
The stocktake function in OwlStreet enables barcode scanners, portable computers, mobile phones, and tablets to conduct inventory stocktakes efficiently. Users can perform stocktake with or without tracking inventory shelf locations. The module leverages images, barcodes, and additional data to ensure accurate counting by providing visual and barcode confirmation, minimizing errors during the stocktaking process. This functionality enhances inventory management, enabling businesses to maintain accurate stock levels and streamline their operations.

## Queue manager
OwlStreet's queue manager offers a comprehensive view of current messages awaiting processing on a queue. Users can inspect messages in their original format, view system origins, and timestamps of processing. Additionally, a configurable history view allows accessing messages from a specified number of past days. The search functionality further enables users to efficiently locate specific messages, providing a powerful tool for monitoring, troubleshooting, and analyzing data flows within the integration environment.

## Metadata manager
The metadata manager in OwlStreet provides a standardized approach to data mapping between systems. It extracts product attributes, currencies, categories, shipping methods, and more, facilitating the creation of transformation tables using our AI-enhanced configuration builder. The metadata manager also simplifies the identification of data identities in source and target systems, offering a streamlined solution for efficient data management and transformation, even without extensive knowledge of specific system structures.

## Transformation configuration
Our transformation configuration in OwlStreet offers zero-code configuration for seamless data transformation and mapping between source and target systems. It supports inheritance, allowing for efficient configuration reuse. While our platform supports JavaScript for advanced filters and transformations, there's a 2-second CPU limit for script execution. Debugging is made easier through visualizing the transformation steps from source to target. This feature is available for all message types. Additionally, our auto-configure function suggests configuration based on metadata and real data, streamlining the setup process for enhanced efficiency.

## Transformation tables
Our transformation tables in OwlStreet empower users to configure how values from source systems are transformed to their corresponding values in the target systems. Manual setup allows precise customization, while our wizard simplifies configuration based on metadata. Moreover, our AI-enhanced configuration suggests optimal transformations based on real data within the system. Importing and exporting tables to work with in spreadsheet software further enhances flexibility and ease of use. This comprehensive feature set enables efficient and accurate data transformation for seamless integration.

## Resource manager
Our resource manager in OwlStreet provides temporary storage for image and document assets, enabling data transportation between source and target systems. Resources are assigned an expiration date, ensuring accessibility for systems requiring the data, even without direct access to the PIM system resources. Unique links are generated for each resource, ensuring privacy and expiration after a designated time. For sensitive data like ledger documents, a one-time-access policy is available, ensuring secure and controlled access to confidential information. This robust resource management ensures seamless data transfer and privacy protection.

The resource manager feature in OwlStreet plays a crucial role in the fulfillment station, enabling the delivery of orders from various sales channels with specific documentation. For example, when fulfilling an order from Amazon, the resource manager facilitates attaching the Amazon delivery note. Simultaneously, for orders originating from the business's own sales channel, the resource manager allows generating or emailing the order confirmation using the sales channel's customized PDF template. This capability ensures accurate and tailored order documentation, enhancing the efficiency and professionalism of the fulfillment process.
