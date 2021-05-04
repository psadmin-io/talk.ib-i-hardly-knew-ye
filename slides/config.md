class: center, middle, blue
# IB Configurations

---
# IB Configurations

* IB Gateway
    * PSIGW
* IB Engine
    * APPSRV
    * PUBSUB

---
# IB Gateway

* Central Gateway
* Local Gateways
* Remote Gateways

???

# IB Gateway
## Central Gateway

* Used as `Local` Gateway in all environments
* Gateway configured with all Nodes
* All Nodes use `Local` gateway

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

---
# IB Engine
## APPSRV

* Set in integrationGateway.properties
* Connects via JOLT to PSAPPSRV 
* Does NOT need to be the same App Domains PIA uses
* JOLT needs to be enabled on domain
* Comma separated list for LB/failover

???

---
# IB Engine
## PUBSUB

* Run in App domain with PubSub enabled
* Domain Status needs to be Active
* Failover can be used to handle Activation


???
