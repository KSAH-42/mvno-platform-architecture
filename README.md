# mvno-platform-architecture (Draft)
this repository present a new design for an IT system between mvno and telecom infra

~~~

     ║                                                        ║              +------------------------------+
     ║                                                        ║              | operrator.adminstration.front| <------------- internal access for management
     ║                                                        ║              +------------------------------+
     ║                                                        ║                         \|/
 mvno-calls    +----------------------------------+           ║              +------------------------------+
 ----║-------->| mvno-session-registry-api        | ----------║------------> | operator-identity.api        +
 <---║-------- +----------------------------------+           ║              +------------------------------+
     ║                      |                                 ║                         /|\
     ║                      |                                 ║                          |
     ║                      |                                 ║                          |
     ║          ....................................          ║                          |
     ║          .   mvno sessions apis             .          ║                          |
     ║          .           |                      .          ║                          |
     ║          .           |                      .          ║                          |
     ║          .          \|/                     .          ║                          |
 mvno-calls     .  +-----------------------------+ .          ║              +------------------------------+
 ----║--------> .  | mvno-session-api.AbcTelecom | . ---------║----+-------> | operator-datastore.api       |
 <---║--------- .  |                             | .          ║    |         +------------------------------+
     ║          .  +-----------------------------+ .          ║    |
     ║          .           |                      .          ║    |         +------------------------------+
     ║          .          \|/                     .          ║    |         | operator-monitoring.api      +
 mvno-calls     .  +-----------------------------+ .          ║    |         +------------------------------+
 ----║--------> .  | mvno-session-api.DefTelecom | . ---------║----|
 <---║--------- .  |                             | .          ║    |         +------------------------------+
     ║          .  +-----------------------------+ .          ║    +-------> | operator-grid.api            +
     ║          .           |                      .          ║    |         +------------------------------+
     ║          .          \|/                     .          ║    |                     \|/        /|\
 mvno-calls     .  +-----------------------------+ .          ║    |               +------------------------+
 ----║--------> .  | mvno-session-api.GhiTelecom | .          ║    |               | operator-grid-node-api |   <-------->  Mobile network core
 <---║--------- .  |                             | . ---------║----+               +------------------------+
     ║          .  +-----------------------------+ .          ║                    | operator-grid-node-api |   <-------->  Mobile network core
     ║          .                                  .          ║                    +------------------------+
     ║          ....................................          ║                    | operator-grid-node-api |   <-------->  Mobile network core
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

# mvno-session-registry-api
Main entrance authenticate mvnno an redirect to the rigth sesssion api

# mvno-session-api
Dedicated implementation of a mvno partenair as a web api.
mvno make http calls to this api. It can handle  N number of internal memory session instances. 
And expose commands and queries.
the mvno-session-api A and mvno-session-api B can have controllers which are totally differents, it must be adapted to needs of mvno partenairs

# operrator.adminstration.front
reprensent a web front used by operator staff for adminsitration. this front make http calls to internal web api, for configuration, viewing status, view logs
mvno will not have access.

# operator-identity-api
authenticate api and users, deliver permissions 

# operator-datastore-api
store entities using a mongodb

# operator-grid-api
used managed managed node-api, route commands, etc... the grid api will send a command to a node and the node can reply using webhook long processsing.

# operator-grid-node-api
translate incomming commands to telecom infra system mobile network core 

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
add elastic search for telemetry ? do we need that ? with eventing-api we build stats.
find something about a web compiler, or web clr
