class: center, middle, blue
# IB Configurations

???

* MANY different ways to do this
* Best way... it depends!

---
# IB Configurations

* IB Gateway
    * PSIGW
* IB Engine
    * APPSRV
    * PUBSUB

???

* 2 main categories split by web/app
* App has 2 sub categories basically Sync/Async

---
# IB Gateway

* Central Gateway
* Local Gateways
* Remote Gateways

???

* No official names for any of these configurations
* How to setup gateway might be biggest choice
* Types
    * Central - 1 gateway used by all
    * Local - Each env. has their own local with all nodes
    * Remote - Each env has local node in local gateway, remote nodes use remote gateway

---
# IB Gateway
## Central Gateway

* Used as `Local` Gateway in all environments
* Gateway configured with all Nodes
* All Nodes use `Local` gateway
* `PSIGW` running in a "non-PORTAL" PIA domain
* PeopleTools version should match highest level

---
# IB Gateway
## Central Gateway

![:img psadmin.io, 100%](images/config-central.png)

???

---
# IB Gateway
## Local Gateways

* Each environment's PIA is their `Local` Gateway 
* Gateway configured with all Nodes
* All Nodes use `Local` gateway

---
# IB Gateway
## Local Gateways

![:img psadmin.io, 100%](images/config-local.png)

???

---
# IB Gateway
## Remote Gateways
    
* Each environment's PIA is their `Local` Gateway 
* Gateways configured only with *Local Default Node*
* *Local Default Node* uses `Local` Gateway
* *Remote Nodes* use a `Remote` Gateway

---
# IB Gateway
## Remote Gateways

![:img psadmin.io, 100%](images/config-remote-gateways.png)

---
# IB Gateway
## Remote Gateways

![:img psadmin.io, 100%](images/config-remote.png)

---
# IB Gateway
## Remote Gateways

![:img psadmin.io, 70%](images/config-remote-nodes.png)

???

---
# IB Engine

* APPSRV
* PUBSUB

???

* The App side of IB
* For the most part, APPSRV = Sync and PUBSUB = Async
    * As mentioned above, APPSRV is needed to "queue up" Async messages, then PUBSUB takes over

---
# IB Engine
## APPSRV

* Set in `integrationGateway.properties`
* Gateway connects to Nodes via `JOLT`
* Does NOT need to be the same as `PORTAL` App domains
* `JOLT` needs to be enabled on App domain
* Comma separated list for LB/failover
   * `//server01:9000,//server02:9000`

???

---
# IB Engine
## PUBSUB

* Run in App domain with `Pub/Sub Servers` enabled
* Domain Status needs to be `Active`
* [Failover](https://docs.oracle.com/cd/F40609_01/pt859pbr1/eng/pt/tiba/task_SettingUpDomainFailover-047e12.html?pli=ul_d74e228_tiba) can be used to handle activation
* [Primary-Secondary Dispatcher](https://docs.oracle.com/cd/F40609_01/pt859pbr1/eng/pt/tiba/task_ImplementingPrimary-SecondaryDispatchers-fe7e1e.html?pli=ul_d74e228_tiba)
   * Formerly called `Master-Slave`
   * Primary domain allocates work to secondary domains
   * Each secondary domain can be assign Queues to process

???

* A lot of domains? failover can do groups and priority

---
# Primary-Secondary
## Secondary Types

* Dynamic 
    * Can change from primary to secondary
    * Used with Domain Failover
* Static
    * Can't change to primary without manual configuration change
* Template
    * Import a primary domain as a secondary domain via PSADMIN
    * Brings in all Sub/Sub process and queue list settings

???
