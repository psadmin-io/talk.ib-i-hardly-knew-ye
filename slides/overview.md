class: center, middle, blue
# IB Overview

---
class: center, middle
# IB Overview

![:img psadmin.io, 100%](images/ib-overview.png)

[PeopleBooks](https://docs.oracle.com/cd/F30998_01/pt858pbr2/eng/pt/tibr/concept_IntroductiontoPeopleSoftIntegrationBroker-076593.html?pli=ul_d76e35_tibr)

???

* High level, this is how it lays out
  * Gateway in GREEN
  * Engine in YELLOW

---
class: center, middle
# Integration Gateway

![:img psadmin.io, 70%](images/gateway.png)

[PeopleBooks](https://docs.oracle.com/cd/F30998_01/pt858pbr2/eng/pt/tibr/concept_IntegrationGatewayArchitecture-07658f.html?pli=ul_d76e35_tibr)

???

* The “web” part of IB
* Gateway Manager is the black box that does all the processing
* Everything else is modular
* You can create your own connectors, if you want
  * PeopleSoft and HTTP are mainly used

---
class: center, middle
# Integration Engine

![:img psadmin.io, 70%](images/engine.png)

[PeopleBooks](https://docs.oracle.com/cd/F30998_01/pt858pbr2/eng/pt/tibr/concept_IntegrationEngineArchitecture-076581.html?pli=ul_d76e35_tibr)

???

* The “app” part of IB
* Runs in an App Domain – PSAPPSRV
* Not sure if PUBSUB is technically part of this, but I lump it in
* Modular as well
* Handlers are main areas of focus for delivered and custom business logic, etc
* Transformation engine is another big area

---
# Service Operations

* Message
    * A message contains the payload of the integration.
* XML message schema
    * Message schemas provide the physical description of the data that is being sent.
* Handler
    * A service operation handler contains the processing logic for the service operation.
* Routing
    * A routing definition specifies the direction of the integration, and more.

[PeopleBooks](https://docs.oracle.com/cd/F30998_01/pt858pbr2/eng/pt/tibr/concept_ServiceOperations-076580.html?pli=ul_d76e35_tibr)

???

A service operation in the PeopleSoft system contains the processing logic for an integration and determines if the integration is to be processed synchronously or asynchronously. 

---
class: center, middle, gray
# Asynchronous and Synchronous

???

Service Operations come in 2 main types
* Async sends the request and moves one
* Sync send the request and waits for response

---
# Asynchronous Services
## Brokers, contractors and queues

![:img psadmin.io, 100%](images/async-1.png)

???

* Publication broker 
    * Acts as the routing mechanism, running routing rules. 
    * If published to a remote node, it routes to the publication contractor service. 
    * If subscribed to on the local node, it routes  to the subscription contractor service. 
    * Routing involves 
        1. Submitting either a subscription or publication contract
        1. Asynchronous call to the contractor service notifying it that work is waiting in the queue.
* Publication contractor 
    1. Performs an HTTP post of the publication service operation to the integration gateway. 
    1. Gateway replies indicating that it received the publication service operation
    1. Publication contract is updated with the status of subscription processing (Done or Retry). 
* Subscription contractor 
    1. Runs the appropriate handler (notification PeopleCode) 
    1. Subscription contract updates the status of the subscription processing

---
# Asynchronous Services
## Publication Operation Instances

![:img psadmin.io, 75%](images/async-2.png)

???

1. Published to the message queue
    * The instance is written to the PSAPMSGPUBHDR table
        * Not yet dispatched.
    * The broker dispatcher process picks up the service operation instance from its queue. 
    * Status is New.
2. Broker dispatcher process passes to the broker handler process. 
    * Status is Started.
3. Broker handler process accepts runs the routing rules
    * The contract is written to the PSAPMSGPUBCON table and notifies the publication contractor
    * Status is Working.
4. Now in publication contact queue
    * Status of the publication contract is New
    * Status of the service operation instance is Done
    * Publication dispatcher process picks up the publication contract from its queue. 
5. Publication dispatcher process passes to the publication handler process.
    * Status is Started.

---
# Asynchronous Services
## Publication Contract

![:img psadmin.io, 75%](images/async-3.png)

???
1. The publication dispatcher picks up the publication contract queue.
2. The publication contract is written to the PSAPMSGPUBCON table in the database
    * Passes the publication contract to the publication handler process. 
    * Status is Started.
3. Handler accepts and attempts to deliver the service operation to the integration gateway. 
    * Status is Working
4. Gateway attempts to pass the publication contract to the destination node.
5. Gateway passes the status of the publication contract back to the publication handler.
6. Handler updates the Service Operations Monitor with the status of the publication contract. 
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

![:img psadmin.io, 70%](images/sync-pub.png)

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

![:img psadmin.io, 50%](images/sync-sub.png)

???

1. The integration gateway passes an inbound synchronous service operation to the integration engine. 
1. The integration engine runs an OnRequest PeopleCode event program. 
1. The OnRequest PeopleCode program attempts to update the application data tables. 
1. The integration engine updates the database tables as well as sends the status information to the Service Operations Monitor. The possible statuses in the Service Operations Monitor for a synchronous publication are:
    * Done. The integration gateway was able to deliver the service operation to the destination node.
    * Error. The integration gateway was not able to deliver the service operation to the destination node.
