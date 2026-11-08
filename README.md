# mvno-platform-architecture (Draft)
This repository present a full web new design for an IT system between mvno and telecom infra

~~~
                         T E L E C O M    O P E R A T O R    P L A T F O R M    F O R     M V N O S

     ║         +----------------------------------+           ║              +------------------------------+
     ║         | operator-mvno-cli                |           ║              | operator.administration.front| ◂------------- internal access for management
     ║         +----------------------------------+           ║              +------------------------------+
     ║                                                        ║                          |
     ║                                                        ║                          ▼
 mvno-calls    +----------------------------------+           ║              +------------------------------+
 ----║-------▸ | operator-mvno-registry-api       + ----------║------------▸ | operator-identity.api        |
 ◂---║-------- +----------------------------------+           ║              +------------------------------+
     ║                      ▲                                 ║                          ▲
     ║                      |                                 ║                          |
     ║                      |                                 ║                          |
     ║                      |                                 ║              +------------------------------+      +---------------------------------------+
     ║                      |                                 ║              | operator-cli                 |      | operator-cli-gr                       | 
     ║                      |                                 ║              +------------------------------+      +---------------------------------------+
     ║                      |                                 ║                          |
     ║          . . . . . . . . . . . . . . . . . . .         ║              +------------------------------+ 
     ║          .   mvno operator apis              .         ║    +-------▸ | operator-datastore.api       |
     ║          .           |                       .         ║    |         +------------------------------+
     ║          .           |                       .         ║    |                     |
     ║          .           |                       .         ║    |         +------------------------------+      +---------------------------------------+
 mvno-calls     .  +-----------------------------+  .         ║    +-------▸ | operator-memory-pool.api     | ◂--- | operator-transaction-pool-api         |
 ----║--------▸ .  | operator-mvno-api           |  . --------║----+         | store: scripts, objects,etc. |      +---------------------------------------+
 ◂---║--------- .  |                             |  .         ║    |         +------------------------------+
     ║          .  +-----------------------------+  .         ║    |                     |
     ║          .           |                       .         ║    |         +------------------------------+
     ║          .           |                       .         ║    |         | operator-monitoring.api      |
 mvno-calls     .  +-----------------------------+  .         ║    |         +------------------------------+
 ----║--------▸ .  | operator-mvno.api.AbcTelecom|  . --------║----+                     | 
 ◂---║--------- .  |                             |  .         ║    |         +------------------------------+
     ║          .  +-----------------------------+  .         ║    +-------▸ | operator-grid.api            | // located in France
     ║          .           |                       .         ║    |         +------------------------------+
     ║          .           |                       .         ║    |                     |      ▼  ▲
 mvno-calls     .  +-----------------------------+  .         ║    |               +------------------------+
 ----║--------▸ .  | operator-mvno-api.DefTelecom|  .         ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
 ◂---║--------- .  |                             |  . --------║----+               +------------------------+
     ║          .  +-----------------------------+  .         ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║          .                                   .         ║    |               +------------------------+
     ║          . . . . . . . . . . . . . . . . . . .         ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |               | operator-grid-node-api |   ◂--------▸  Small Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |                     
     ║                                                        ║    |         +------------------------------+
     ║                                                        ║    +-------▸ | operator-grid.api            | // located in France (outside Europe)
     ║                                                        ║              +------------------------------+
     ║                                                        ║                          |      ▼  ▲
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                                                        ║                    +------------------------+

~~~

# operator-mvno-cli
Console application used for:
 * disable/enable session
 * changing registry configuration or api config
 * querying the status of active session
 * querying the status of mnvo-api
 * enabling/disabling mnvo-api
 * changing token renewal timeout
 * send a request to a mvno-api

# operator-mvno-registry-api
Main entrance authenticate mvnno an redirect to the rigth api. It's act as mvno directory.
The mvno-api can be located in France, or elsewhere.

# operator-mvno-api
Dedicated implementation for a mvno partenair as a web api.
Provide a better isolation, we can add new mvno-partenar without interruption
Mvno partenair make http calls to this api.
It can handle  N number of internal memory session instances. 
It expose commands and queries, only the needs not necessary all features.
The mvno-session-api A and mvno-session-api B can have totaly different contracts/swaggers.
All operator-mvno-api must expose some methods like OpenSession(string source, string password, TimeSpan renewalExpirationTimeout /* throw exception if it's greater the timeout settings from the registry or localsettings */)  and CloseSession() methods.

# operator.administration.front
Reprensent a web front used by telecom operator staff for administration.
This front make http calls to internal web api, for:
 * configuration
 * viewing status
 * view logs
 * changing customers properties
 * add new customers
 * maintenance 
 * ...

Mvno partenairs will not have access to it from mvno-api.

# operator-identity-api
Authenticate api and users, deliver permissions.

# operator-cli
Console application used control telecom infra by making only http calls. It can do the following tasks:
 * send command to grid
 * making crud operations
 * query node status
 * getting logs
 * deactivate a grid-node
 * registerNode
 * changing node settings
 * creating a transaction store in the memory pool
 * cancel a transaction
 * pause a transaction, even rollback a transaction (a transaction contains a list of steps and the step class will expose a method execute and a method call rollback, we can it forward for executing and moving backward on the collection and call the rollback methods)
 * import/export datastore contents
 * create SIM
 * create subscriber
 * deactivate SIM
 * setUserMobileBandwith
 * etc... 

~~~
 operator-cli.exe logon -u "admin" -p "password"
 operator-cli.exe grid create-node --id "1234465B-1888-48E8-BB43-181D2C1F682F"
 operator-cli.exe grid start-node --id "1234465B-1888-48E8-BB43-181D2C1F682F"
 operator-cli.exe network enable-cellular --id "B8D531EB-18E8-47E5-B4B3-1D218C681FF2" --node 1234465B-1888-48E8-BB43-181D2C1F682F
 operator-cli.exe network set-cellular-bandwith --id "B8D531EB-18E8-47E5-B4B3-1D218C681FF2" --value 12345000 --node 1234465B-1888-48E8-BB43-181D2C1F682F
 operator-cli.exe logout
~~~

# operator-datastore-api
Store entities and settings using a mongodb in a single place. One datastore per environnement DEV,PROD,etc... for failover and restoration and backup.


# operator-memory-pool-api
In case of bulk commands, the memory-pool store transactions including the status of each steps. Transaction represent the commands/workflow to execute. Each transaction store in the memory-pool is just object with a id, a type, a list of step with status, and the transaction settings.
the memory pool can be used to store any kind of objects
structure

~~~
 [application-source|application-type|time-stamp|object-type|payload|state]
~~~

the state are used to mark the object for deletion,or anything.

# operator-transaction-pool-api
this api peek a transaction, play a transaction, start, pause, stop or cancel a transaction. A transaction can be:
 * a class
 * a class load on different assembly
 * a script

Depending of the implementation of transaction-workflow, it can a be also class a script that make http call on web-api. It store the results/status/errors, etc... abd the transaction can implement retries mecanisms.
The transaction must save it's status, that mark as restauration point in case of critical failure occurs. The after restart the api, we can restore the position of the transaction, and continue the process.

# operator-grid-api
Used managed managed node-api, route commands, etc... the grid api will send a command to a node and the node can reply using webhook long processsing.
No MOM is used here: events driven are the requests sended by mvno-api or transaction-api or the adminstration-front.
This api must expose resgisterNode and unregisterNode
grid-api must have it'on entity stored into the datastore.

# operator-grid-node-api
Translate incomming commands to telecom infra and communication provide metrics.

# Temp
Addinng a telecom.sdk ?
Add elastic search for telemetry ? do we need that ? with eventing-api we build stats.
fuse the operator-transaction-pool-api and operator-memory-pool-api ???
find something about a web compiler, or web clr
user can select a transaction from a catalog / datastore ?
