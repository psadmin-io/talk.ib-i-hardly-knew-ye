class: center, middle, blue
# IB Overview

---
class: center, middle
# IB Overview

![:img psadmin.io, 100%](images/ib-overview.png)

[PeopleBooks](https://docs.oracle.com/cd/F30998_01/pt858pbr2/eng/pt/tibr/concept_IntroductiontoPeopleSoftIntegrationBroker-076593.html?pli=ul_d76e35_tibr)

---
class: center, middle
# Integration Gateway

![:img psadmin.io, 70%](images/gateway.png)

[PeopleBooks](https://docs.oracle.com/cd/F30998_01/pt858pbr2/eng/pt/tibr/concept_IntegrationGatewayArchitecture-07658f.html?pli=ul_d76e35_tibr)

???

* The “web” part of IB
* Gateway Manager is the black box that does all the processing
* Everything else is modular

---
class: center, middle
# Integration Engine

![:img psadmin.io, 70%](images/engine.png)

[PeopleBooks](https://docs.oracle.com/cd/F30998_01/pt858pbr2/eng/pt/tibr/concept_IntegrationEngineArchitecture-076581.html?pli=ul_d76e35_tibr)

???

* The “app” part of IB
* Runs in an App Domain – PSAPPSRV

---
# Service Operations

A service operation in the PeopleSoft system contains the processing logic for an integration and determines if the integration is to be processed synchronously or asynchronously. 

A service operation definition contains the following definitions:

* Message
    * A message contains the payload of the integration.
* XML message schema
    * Message schemas provide the physical description of the data that is being sent, including descriptions of fields, field types, field lengths, and so on.
* Handler
    * A service operation handler contains the processing logic for the service operation.
* Routing
    * A routing definition specifies the direction of the integration (inbound or outbound), routing alias names, transformations, and more.


[PeopleBooks](https://docs.oracle.com/cd/F30998_01/pt858pbr2/eng/pt/tibr/concept_ServiceOperations-076580.html?pli=ul_d76e35_tibr)

???

TODO

---
# Asynchronous Services
## Brokers, contractors and queues

![:img psadmin.io, 100%](images/async-1.png)

???

* Publication broker 
    * Acts as the routing mechanism. When an asynchronous service operation arrives in its queue, the publication broker service runs the defined routing rules. 
    * If the service operation needs to be published to a remote node, it routes the service operation to the publication contractor service. 
    * If the service operation is subscribed to on the local node, then the publication broker routes the service operation to the subscription contractor service. 
    * Routing involves submitting either a subscription or publication contract to the appropriate contractor, followed by an asynchronous call to the contractor service notifying it that work is waiting in the queue.
* Publication contractor 
    * References the publication contract submitted by the publication broker service and performs an HTTP post of the publication service operation to the integration gateway. 
    * When the integration gateway sends a reply indicating that it received the publication service operation, the publication contractor service updates the publication contract with the status of subscription processing (Done or Retry). 
* Subscription contractor 
    * References the subscription contract submitted by the publication broker service and runs the appropriate notification PeopleCode. 
    * Then it updates the subscription contract concerning the status of the subscription processing. 

---
# Asynchronous Services
## Publication Operation Instances

![:img psadmin.io, 75%](images/async-2.png)

???

1. The service operation is published and enters the message queue. 
    * The instance is written to the PSAPMSGPUBHDR table in the database, but is not yet dispatched.
    * The broker dispatcher process picks up the service operation instance from its queue. 
    * During this stage, the service operation instance status in the Service Operations Monitor is New.
1. The broker dispatcher process passes the service operation instance to the broker handler process. 
    * During this stage, the service operation instance status in the Service Operations Monitor is Started.
1. The broker handler process accepts the service operation instance, reads the data, and runs the routing rules to determine where the publication needs to be delivered. 
    * The broker handler process then writes a publication contract in the PSAPMSGPUBCON table and notifies the publication contractor service that it has an item to process. 
    * During this stage, the service operation instance status in the Service Operation Monitor is Working.
1. After the service operation is stored in the publication contact queue, the status of the publication contract in the Service Operations Monitor is New, the service operation instance status is Done, and the publication dispatcher process picks up the publication contract from its queue. 
1. The publication dispatcher process passes the service operation instance to the publication handler process.
    * During this stage, the publication contract status in the Service Operations Monitor is Started.

---
# Asynchronous Services
## Publication Contract

![:img psadmin.io, 75%](images/async-3.png)

???
1. The publication dispatcher picks up the publication contract from the publication contract queue.
1. The publication contract is written to the PSAPMSGPUBCON table in the database, but is not yet dispatched. The publication dispatcher process passes the publication contract to the publication handler process. 
    * At this stage the status of the publication contract in the Service Operation Monitor is Started.
1. The publication handler process accepts the publication contract and attempts to deliver the service operation to the integration gateway. 
    * At this stage, the status of the publication contract in the Service Operations Monitor is Working.
1. The integration gateway attempts to pass the publication contract to the destination node.
1. The integration gateway passes the status of the publication contract back to the publication handler.
1. The publication handler updates the Service Operations Monitor with the status of the publication contract. The typical statuses that displays in the Service Operations Monitor are:
    * Done. The subscribing node successfully received the contract.
    * Timeout. The system timed out before the transaction processing was completed.
    * Retry. The system encountered and error. The retry is automatic.
        * When service operations have Retry status, the service operations are not resent until an internal ping is successful. This ping is similar to a node ping. The publication Contract dispatcher, as part of its on idle processing, pings a node that is in Retry status and verifies if the connection is reestablished. When the ping is successful the publication Contract dispatcher resends the service operation. The service operation goes back to the publication handler process and returns to Working status.

---
# Asynchronous Services
## Subscription Operation Instances

![:img psadmin.io, 70%](images/async-4.png)

???

1. The service operation enters the message queue. The instance is written to the database, but not yet dispatched The broker dispatcher process picks up the service operation instance from its queue. During this stage, the status of the service operation instance in the Service Operations Monitor is New.
1. The broker dispatcher process passes the service operation instance to the broker handler process. During this stage, the status of the service operation instance in the Service Operations Monitor is Started.
1. The broker handler process accepts the service operation instance, reads the data, and runs the subscription routing rules to determine if the service operation needs to be processed locally. During this stage, the status of the service operation instance in the Service Operations Monitor is Working.
1. The broker handler process then writes a subscription contract in the PSAPMSGPUBCON table (the subscription contract queue) and notifies the subscription contractor service that it has an item to process. During this stage, the status of the service operation instance in the Service Operations Monitor is Working.
1. Once the service operation is stored in the subscription contact queue, the status of the service operation instance in the Service Operations Monitor is Done. Processing of the subscription contract begins as the subscription dispatcher process picks up the subscription contract from its queue, and the status of the subscription contract in the Service Operations Monitor is New. In this example, at the point when the status of the asynchronous service operation instance is Done, the subscription contract status is New. Asynchronous subscription contract processing is described in the next section.

---
# Asynchronous Services
## Subscription Contract

![:img psadmin.io, 50%](images/async-5.png)

???

1. The subscription dispatchers picks up the contract from the subscription contract queue.
1. The subscription dispatcher process passes the subscription contract to the subscription handler process. At this stage the status of the subscription contract in the Service Operations Monitor is Started.
1. The subscription handler process accepts the subscription contract and runs the notification PeopleCode. 
1. In the example shown in the diagram, the notification PeopleCode then uses the service operation data to update application data tables. However, the notification PeopleCode can use the service operation data as input to   look up information, create and publish another service operation, and so forth. At this stage, the status of the publication contract in the Service Operations Monitor is Working. 
1. The subscription handler passes the status of the subscription contract to the Service Operations Monitor. The typical statuses that display in the Service Operations Monitor for an asynchronous subscription contract are:
    * Done. The notification PeopleCode ran successfully.   
    * Error. An error occurred.

---
# Synchronous Services
## Publication

![:img psadmin.io, 100%](images/sync-pub.png)

???

1. The integration engine sends the service operation to the integration gateway. 
1. The integration gateway attempts to deliver the service operation to the destination node.
1. The integration gateway sends back the status information to the integration engine
1. The integration engine updates the database tables as well as sends the status information to the Service Operations Monitor. The possible statuses in the Service Operations Monitor for a synchronous publication are:
    * Done. The integration gateway was able to deliver the service operation to the destination node.
    * Error. The integration gateway was not able to deliver the service operation to the destination node.

---
# Synchronous Services
## Subscription

![:img psadmin.io, 100%](images/sync-sub.png)

???

1. The integration gateway passes an inbound synchronous service operation to the integration engine. 
1. The integration engine runs an OnRequest PeopleCode event program. 
1. The OnRequest PeopleCode program attempts to update the application data tables. 
1. The integration engine updates the database tables as well as sends the status information to the Service Operations Monitor. The possible statuses in the Service Operations Monitor for a synchronous publication are:
    * Done. The integration gateway was able to deliver the service operation to the destination node.
    * Error. The integration gateway was not able to deliver the service operation to the destination node.
