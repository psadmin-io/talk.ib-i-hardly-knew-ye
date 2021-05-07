class: center, middle, blue
# Tips & Tricks

---
# Demo

* Gateway Load Balancer
???
* Does nothing besides editing `integrationGateway.properties`
* Check, then add HR and FS, going into properties
* Ping - works from actual Gateway - not what you are editing!!!
* Switch URL from HR to FS, then try FS ping again
* Change back to HR
--
* Remote Gateway
???
* Add new `FS`
* `http://fs.lab.conf.oraclevcn.com:8000/PSIGW/PeopleSoftListeningConnector`
* Add gateway to `PSFT_EP` node
   * **SETUP PORTAL**
     * http://hr.lab.conf.oraclevcn.com:8000/psc/ps/
     * http://hr.lab.conf.oraclevcn.com:8000/psc/ps/
   * Ping Node with Local
   * Change to FS
   * Ping again
   * You can ping a node from Properties file, but why not just from Node page?
   * Good if you are in lower tools and IBG is higher tools (psvault diff!)
--
* IB Node Network
???
* Good for managing IB, but also for UniNav
    * A lot of "magic" when adding nodes to network
* Show node routings status
* Add FS to Network
* Show routings status again
* Show SSO ???
  * Setup SSO in FS
--
* Introspection and Deployment
???
* Navigator > PeopleTools > Integration Broker > Integration Network > Introspection and Deployment
* When nodes are in network, we can do more "magic"
* `PTPN_IB_PUBLISH`
* Deploy all to Remote nodes as needed
--
* Managing Domain Status
???
* Only have SINGLE Active
* Failover demo
* Walk through ACM Plugin
--
* Logging
???
* [IDDA](https://psadmin.io/2015/06/22/peoplesoft-instrumented-development-diagnostic-aid-idda/)
* [IB Logging](https://peoplesoftwiki.com/books/integration-broker/page/integration-broker-logging)
--
* Anything else?
???
* Anyone want to see anything else?
* Questions?

---
# JSMPros
## Integration Training

https://learn.jsmpros.com/courses/id21

---
# PeopleTools Integration Broker
## The Missing Manual

https://ib.books.cedarhillsgroup.com

