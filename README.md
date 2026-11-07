# mvno-platform-architecture (Draft)
This repository present a new design for an IT system between mvno and telecom infra

~~~

     ║         +----------------------------------+           ║              +------------------------------+
     ║         | mvno-session-cli                 |           ║              | operator.administration.front| <------------- internal access for management
     ║         +----------------------------------+           ║              +------------------------------+
     ║                                                        ║                         \|/
 mvno-calls    +----------------------------------+           ║              +------------------------------+
 ----║-------->| mvno-session-registry-api        | ----------║------------> | operator-identity.api        +
 <---║-------- +----------------------------------+           ║              +------------------------------+
     ║                     /|\                                ║                         /|\
     ║                      |                                 ║                          |
     ║                      |                                 ║                          |
     ║          ....................................          ║                          |
     ║          .   mvno sessions apis             .          ║              +------------------------------+
     ║          .           |                      .          ║              | operator-cli                 |
     ║          .           |                      .          ║              +------------------------------+
     ║          .           |                     .           ║                          |
 mvno-calls     .  +-----------------------------+ .          ║              +------------------------------+
 ----║--------> .  | mvno-session-api.AbcTelecom | . ---------║----+-------> | operator-datastore.api       |
 <---║--------- .  |                             | .          ║    |         +------------------------------+
     ║          .  +-----------------------------+ .          ║    |
     ║          .           |                      .          ║    |         +------------------------------+
     ║          .           |                      .          ║    |         | operator-monitoring.api      +
 mvno-calls     .  +-----------------------------+ .          ║    |         +------------------------------+
 ----║--------> .  | mvno-session-api.DefTelecom | . ---------║----|
 <---║--------- .  |                             | .          ║    |         +------------------------------+
     ║          .  +-----------------------------+ .          ║    +-------> | operator-grid.api            +
     ║          .           |                      .          ║    |         +------------------------------+
     ║          .           |                      .          ║    |                     \|/        /|\
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
Mvno make http calls to this api. It can handle  N number of internal memory session instances. 
And expose commands and queries.
The mvno-session-api A and mvno-session-api B can have controllers which are totally differents, it must be adapted to needs of mvno partenairs

# operator.administration.front
Reprensent a web front used by telecom operator staff for administration. This front make http calls to internal web api, for configuration, viewing status, view logs, ...

Mvno will not have access to it.

# operator-identity-api
Authenticate api and users, deliver permissions.

# operator-datastore-api
Store entities using a mongodb.

# operator-grid-api
Used managed managed node-api, route commands, etc... the grid api will send a command to a node and the node can reply using webhook long processsing.

# operator-grid-node-api
Translate incomming commands to telecom infra.

# mvno-cli
Console application used to send command to grid, making crud operations, view status, viewing api logs, etc... 

# mvno-session-cli
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
add elastic search for telemetry ? do we need that ? with eventing-api we build stats.
find something about a web compiler, or web clr
