# mvno-platform-architecture (Draft)
This repository present a new design for an IT system between mvno and telecom infra

~~~
                P   L   A   T   F   O   R   M     T   E   L   E   C   O   M     O   P   E   R   A   T   O   R

     ║         +----------------------------------+           ║              +------------------------------+
     ║         | operator-mvno-cli                |           ║              | operator.administration.front| <------------- internal access for management
     ║         +----------------------------------+           ║              +------------------------------+
     ║                                                        ║                          |
     ║                                                        ║                         \|/
 mvno-calls    +----------------------------------+           ║              +------------------------------+
 ----║-------->| operator-mvno-registry-api       | ----------║------------> | operator-identity.api        |
 <---║-------- +----------------------------------+           ║              +------------------------------+
     ║                     /|\                                ║                         /|\
     ║                      |                                 ║                          |
     ║                      |                                 ║                          |
     ║          ....................................          ║                          |
     ║          .   mvno operator apis             .          ║              +------------------------------+
     ║          .           |                      .          ║              | operator-cli                 |
     ║          .           |                      .          ║              +------------------------------+
     ║          .           |                      .          ║                          |
 mvno-calls     .  +-----------------------------+ .          ║              +------------------------------+
 ----║--------> .  | operator-mvno-api           | . ---------║----+-------> | operator-datastore.api       |
 <---║--------- .  |                             | .          ║    |         +------------------------------+
     ║          .  +-----------------------------+ .          ║    |                     |
     ║          .           |                      .          ║    |         +------------------------------+
     ║          .           |                      .          ║    |         | operator-monitoring.api      |
 mvno-calls     .  +-----------------------------+ .          ║    |         +------------------------------+
 ----║--------> .  | operator-mvno.api.AbcTelecom| . ---------║----+                     | 
 <---║--------- .  |                             | .          ║    |         +------------------------------+
     ║          .  +-----------------------------+ .          ║    +-------> | operator-grid.api            | // located in France
     ║          .           |                      .          ║    |         +------------------------------+
     ║          .           |                      .          ║    |                         \|/        /|\
 mvno-calls     .  +-----------------------------+ .          ║    |               +------------------------+
 ----║--------> .  | operator-mvno-api.DefTelecom| .          ║    |               | operator-grid-node-api |   <-------->  Mobile network core
 <---║--------- .  |                             | . ---------║----+               +------------------------+
     ║          .  +-----------------------------+ .          ║    |               | operator-grid-node-api |   <-------->  Mobile network core
     ║          .                                  .          ║    |               +------------------------+
     ║          ....................................          ║    |               | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |               | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |               | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |               | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |               | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |               | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║    |               +------------------------+
     ║                                                        ║    |
     ║                                                        ║    |         +------------------------------+
     ║                                                        ║    +-------> | operator-grid.api            | // located in France (outside Europe)
     ║                                                        ║              +------------------------------+
     ║                                                        ║                              \|/        /|\
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║                    +------------------------+
     ║                                                        ║                    | operator-grid-node-api |   <-------->  Mobile network core
     ║                                                        ║                    +------------------------+

~~~

# operator-mvno-registry-api
Main entrance authenticate mvnno an redirect to the rigth api

# operator-mvno-api
Dedicated implementation of a mvno partenair as a web api.
Mvno make http calls to this api. It can handle  N number of internal memory session instances. 
And expose commands and queries.
The mvno-session-api A and mvno-session-api B can have controllers which are totally differents, it must be adapted to needs of mvno partenairs

# operator.administration.front
Reprensent a web front used by telecom operator staff for administration. This front make http calls to internal web api, for configuration, viewing status, view logs, changing customers settings, ...

Mvno will not have access to it.

# operator-identity-api
Authenticate api and users, deliver permissions.

# operator-datastore-api
Store entities using a mongodb.

# operator-grid-api
Used managed managed node-api, route commands, etc... the grid api will send a command to a node and the node can reply using webhook long processsing.
It can send event to other api when receiving a commands. It used an internal scheduler or an operator-scheduler-api to repeat commands to the node until it succeed.
the operator-grid--scheduler-api can repeat and used undefinitely or a max counter if it it reach it can report an emergency error and stop depending to the command settings
no MOM is used, but commands and query to this api are persisted into it's own db which are not represented. this api must expose resgisterNode and unregisterNode
grid-api must view as entity stored in the database.

# operator-grid-node-api
Translate incomming commands to telecom infra and communication provide metrics.

# operator-cli
Console application used to send command to grid, making crud operations, view status, viewing api logs, deactivate a grid-node, cancel commands, registerNode, etc... 

# operator-mvno-cli
Console application used disable/enable session, viewing actives sessions, changing api settings, starting session api, etc... 

# strucuture
This diagram represent a structure including a custom session and expose methods needed a particular mvno.

```

 <->   operator-identity-api -------------OR------------------+
              |                                               |
 <->    my-virtual-operator-session-api              operator-session-api
              |                                               |
              +---> datastore-api                             +---> datastore-api
              +---> grid-api                                  +---> grid-api
                      +---> node-api -> cœur de réseau               +---> ...
                      +---> node-api -> cœur de réseau               +---> ...
                      +---> node-api -> cœur de réseau               +---> ...
                      +---> node-api -> cœur de réseau               +---> ...
                      +---> node-api -> cœur de réseau               +---> ...
                      +---> node-api -> cœur de réseau               +---> ...
                      +---> node-api -> cœur de réseau               +---> ...
                      +---> node-api -> cœur de réseau               +---> ...
                      +---> node-api -> cœur de réseau               +---> ...
                      +---> node-api -> cœur de réseau               +---> ...
                      +---> node-api -> cœur de réseau               +---> ...

```


Using single db ou multiple db ?
 -> use MongoDb or Sql ?
Use two directory ? public authen and internal authen ? -> find something better
 -> internal directory will use rosyln for creating dynamic entities used by mvno-session-api ? 
     -> search for existing db patterns before
Replace and eventing api + event persistance and remove logging api

Addinng a telecom.sdk ?

Add elastic search for telemetry ? do we need that ? with eventing-api we build stats.
find something about a web compiler, or web clr
