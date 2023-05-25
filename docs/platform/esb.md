---
layout: default
title: Enterprise Service Bus
parent: Integration Platform iPaaS
nav_order: 1
has_children: false
---
# The Enterprise Service Bus pattern
An Enterprise Service Bus (ESB) is a software architecture that facilitates communication and integration between different applications and services within an organization. It acts as a central hub, enabling seamless data exchange, message routing, and service orchestration. The benefits of an ESB include improved interoperability, scalability, and flexibility. It simplifies integration complexities, reduces point-to-point connections, and promotes reusability of services. With an ESB, organizations can enhance agility, streamline operations, and achieve a unified and efficient IT infrastructure. Additionally, an ESB facilitates real-time data synchronization, enables seamless collaboration, and supports the implementation of service-oriented architectures (SOA).

## Queues

{% mermaid %}
graph LR;
    Order <--> GW[owlstreet];
    Product <--> GW;
    Price <--> GW;
    Inventory <--> GW;
    GW <--> Shipment;
    GW <--> Customer;
    GW <--> GL[G/L Document];
{% endmermaid %}


OwlStreet leverages a queue-based architecture to facilitate the smooth delivery of data between systems. When a source system sends data, it is placed in a queue where it awaits processing and onward transmission. This queue serves as the integration endpoint, ensuring orderly and reliable data transfer.

Within the queue, OwlStreet performs essential functions like data routing, filtering, and transformation. These capabilities enable the system to determine the appropriate destination for each piece of data, apply relevant filters to ensure data integrity and security, and transform the data into the required format for the target system.

By using queues, OwlStreet ensures that data is processed and delivered to the target systems in the order it was sent from the source system. This sequencing ensures data consistency and maintains the integrity of business processes relying on accurate and timely information.

Furthermore, the queue-based architecture of OwlStreet enables efficient data retrieval by systems that subscribe to specific types of information. Systems subscribing to particular data can access the target queue and retrieve the relevant data in a controlled and orderly manner. This approach ensures that subscribed systems receive the necessary information they require for their operations, promoting seamless collaboration and data synchronization across the organization.

Overall, OwlStreet's utilization of queues as integration endpoints allows for orderly data delivery, filtering, transformation, and retrieval. This approach ensures data integrity, maintains the order of data transmission, and enables efficient access to subscribed information by the target systems, contributing to streamlined data integration and improved system interoperability.


## Routing

{% mermaid %}
graph LR;
    ECOMORDER[e-commerce order]-->GW[Owlstreet orders queue];
    GW-->ERPSE[ERP Sweden];
    GW-->CRM;
{% endmermaid %}


OwlStreet's routing capabilities play a pivotal role in enabling efficient message distribution and synchronization among various systems and endpoints. With OwlStreet, routing is designed to accommodate complex scenarios, allowing integrations to publish messages to potential subscribers, ensuring seamless data flow and synchronization across systems.

For instance, when a sales order is generated from a web store, OwlStreet's routing mechanism enables the order to be sent to multiple subscribers simultaneously, such as the ERP system and the CRM system. This ensures that the order is registered in the ERP system for further processing and that an order confirmation is sent to the CRM system to facilitate customer relationship management.

Moreover, OwlStreet's routing functionality enables messages from a single e-commerce store to be intelligently directed to different companies within an ERP system. This capability is particularly useful in scenarios where the e-commerce store caters to multiple brands or operates separate B2C and B2B stores. By leveraging routing, OwlStreet can deliver messages to the relevant companies or business units within the ERP system, ensuring accurate data segmentation and targeted processing.

Additionally, routing ensures that messages about order status are correctly routed back to the system where the order was initially created. This is essential in cases where multiple brand stores or separate B2C and B2B stores are involved. OwlStreet intelligently routes order status messages, allowing them to be accurately delivered back to the respective system, maintaining data integrity and ensuring that the right information reaches the right destination.

By leveraging OwlStreet's routing capabilities, businesses can achieve seamless integration and data synchronization across their entire ecosystem. Routing enables messages to be efficiently distributed to potential subscribers, ensuring accurate data processing and synchronization between systems. Whether it involves multiple subscribers for sales orders, routing to different companies within an ERP system, or correct message delivery based on order origin, OwlStreet's routing functionality ensures precise and reliable data flow for optimized business operations.


## Filtering

{% mermaid %}
graph LR;
    ECOMORDER[e-commerce order]-->GW[Owlstreet orders queue];
    GW-->ERPSEFILTER{Country == SE};
    ERPSEFILTER-->ERPSE[ERP Sweden];
    GW-->ERPUSFILTER{Country is<br /> CA or US};
    ERPUSFILTER-->ERPUS[ERP Inc];
    GW-->CRM;
{% endmermaid %}


Filtering enhances the routing capabilities of OwlStreet by allowing for more targeted and precise message routing. With filtering, only messages that meet specific criteria are routed to their designated destinations, providing greater flexibility and control over data flow.

For example, filtering enables the selection of messages based on specific conditions, such as orders in a particular status or orders intended for a specific country. This means that only orders that satisfy the defined criteria will be routed to the target queue or system. By utilizing filtering, businesses can streamline their data processing workflows and ensure that relevant information reaches the appropriate destinations.

By leveraging filtering, OwlStreet enables the utilization of the same message types for similar purposes. For instance, message types like sales orders, invoices, credit memos, and purchase orders can be routed and processed using the same message structure. Filtering allows for the customization of routing rules based on the specific attributes or criteria associated with each message type. This ensures that the right messages are directed to the appropriate queues or systems, even if they belong to the same general message type.

This capability simplifies the integration process and reduces the need for creating separate message types for similar purposes. It promotes reusability and standardization of message structures, making it easier to manage and maintain integrations across the organization.

Overall, filtering enhances the routing capabilities of OwlStreet by enabling selective message routing based on specific criteria. By leveraging filtering, businesses can ensure that only relevant messages are sent to the target queues or systems, based on conditions such as order status or country. This flexible filtering mechanism allows for the utilization of the same message types for similar purposes, providing efficiency, consistency, and streamlined data processing.

## Transformations


{% mermaid %}
graph LR;
    Order[Order for <br />warehouse BODEN] --> GW[owlstreet];
    GW --> TRW(Warehouse Code<br />BODEN);
    TRW --> LOOKUP[Lookup table<br/>BODEN=1<br />GÖTEBORG=2<br/>MORGONGÅVA=3];
    LOOKUP --> ERP[ERP<br />Warehouse 1];
{% endmermaid %}


Transformations play a crucial role in OwlStreet by enabling the conversion and manipulation of values to ensure compatibility and seamless communication between systems. With transformations, OwlStreet provides a neutral value in the middle to which all systems can transform their respective values, promoting standardized connectors without the need for custom code.

One key aspect of transformations is the ability to convert values between different systems using lookup tables. This is particularly useful for fields such as delivery methods, categories, warehouses, price list identifiers, and other similar attributes. By utilizing lookup tables, OwlStreet can map and transform values from one system to another, ensuring that the data is accurately interpreted and understood by the receiving system. This allows for seamless integration and communication between systems with varying data representations.

Additionally, transformations can involve adding constant values, modifying values using regular expressions, or even running custom JavaScript code to perform calculations. This flexibility enables businesses to manipulate data according to their specific requirements. For example, a transformation may involve adding a constant value to a field, extracting or modifying specific information using regular expressions, or performing calculations based on custom JavaScript code. These transformations allow for data enrichment, standardization, and customization, ensuring that the data meets the specific needs of each system.

The ability to perform transformations without custom code is highly valuable as it simplifies integration processes and reduces dependencies on technical resources. With OwlStreet's transformation capabilities, standardized connectors can be established between systems without the need for extensive programming or customization. This promotes agility, scalability, and efficiency in integrating different systems and allows for seamless data exchange without complex development efforts.

In summary, transformations within OwlStreet enable the conversion, manipulation, and enrichment of values to facilitate seamless communication between systems. By providing a neutral value in the middle, transformations ensure standardized connectors without the requirement for custom code. Lookup tables, constant value additions, regular expression modifications, and custom JavaScript code enable the transformation of values between systems, allowing for standardized integration and data processing across the organization.

