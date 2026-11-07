# mvno-platform-architecture (Draft)
this repository present a new design for an IT system between mvno and telecom infra

~~~

     ║                                                        ║
     ║                                                        ║
 mvno-calls    +----------------------------------+           ║              +------------------------------+
 ----║-------->| mvno-session-registry-api        | ----------║------------> | operator-idientity.api       +
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
 ----║--------> .  | mvno-session-api.AbcTelecom | . ---------║----+-------> | operator-database.api       |
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
This api are used to access to the internal apis. it represents an api used manage N number of session instances. 
Session api host the business code for the mvno
Session api can now access to the datastore api where business api are stored and execute custom task

# operator-identity-api
authenticate api and users, deliver permissions 

# operator-database-api
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
