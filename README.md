# mvno-platform-architecture (Draft)
this repository present a new design for an IT system between mvno and telecom infra

# operator-identity-api
Main entrance, it grant or deny the access to a session api.

```
mvno calls     +-------------------------+
 ------------> |  operator-identity-api  |
 <------------ +-------------------------+
               |   authent               |
               |   partitioning          |
               +-------------------------+
               | include:                |
               |   IA for session        |
               |   management            |
               +-------------------------+
               | returns:                |
               |   Endpoint to session   |
               |   api                   |
               +-------------------------+
```

# operator-session-api
This api are used to access to the database-api and the grid-api. it represents an api used manage N number of session instances. each session are associated to unique identifier.
it allow to enable/disable customer services based on customer info store on the database-api depending to mvno-session-permissions controlled by licensing 

```
mvno calls     +-------------------------+
 ------------> |   operator-session-api  |
 <------------ +-------------------------+
               |   customers/            |--------------> [ database-api ]
               |   subscriptions         |--------------> [ grid api     ] -> [nodes]
               +-------------------------+
               |   control db access     |
               |   control grid acces    |
               |   include permissions   |
               +-------------------------+
```
easch mvno session api will connect to master database api and if needed, it can connect to
the apropriate a custom database api for the a particular mvno 

# operator-db-api
store customer info, and otherthing

# operator-logging-api
a logger

# operator-grid-api
used managed managed node-api, route commands, etc... the grid api will send a command to a node and the node can reply using webhook long processsing.

# operator-grid-node-api
translate incomming commands to telecom infra system mobile network core 

# main-strucuture

```
 <->   operator-identity-api
              |
 <->   operator-session-api // main operator telecom session api
              |
              +---> database-api
              +---> logging-api
              +---> grid-api
                      +---> node-api -> mobile network core
                      +---> node-api -> mobile network core
                      +---> node-api -> mobile network core
                      +---> node-api -> mobile network core
                      +---> node-api -> mobile network core
                      +---> node-api -> mobile network core
                      +---> node-api -> mobile network core
                      +---> node-api -> mobile network core
                      +---> node-api -> mobile network core
                      +---> node-api -> mobile network core
```

# adapted-strucuture
This diagram represent a structure including a custom session and expose methods needed a particular mvno.

```
 <->   operator-identity-api -------------OR------------------+
              |                                               |
 <->    my-virtual-operator-session-api              operator-session-api
              |                                               |
              +---> database-api.master                       +---> database-api
              +---> logging-api                               +---> ....
              +---> grid-api                                  +---> ....
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

the my-virtual-operator-session-api and operator-session-api instances can share the same instance of database-api and the grid-api

we don't use a global directory here for authentication with it's own central database. adding a new entity will cause a restart.

each mvno xyz can call the operator-database-api.master and can call the operator-database-api.mvno.xyz dedicated only the mvno xyz.
We can add new mvno partenair at runtime without stopping system, and other mvno can stay in communication with the telecom system.

so here the custom operator-mvno-session-api.RT will call:
  * database.api.master
  * database.api.mvno   (common mvno db)
  * database.api.mvno.RT
  * logging.api
  * grid.api
  
```
[operator-identity-api]
        |
       \|/                      
[operator-mvno-session-api.RT] ----+---> [operator-database-api] 
                                   +---> [operator-database-api.mvno]
                                   +---> [operator-database-api.mvno.RabbitTelecom]
                                   +---> [operator-logging-api]
                                   +---> [operator-grid-api]
                                            +-> [operator-node.api] ---> mobile network core
                                            +-> [operator-node.api] ---> mobile network core
                                            +-> [operator-node.api] ---> mobile network core
                                            +-> [operator-node.api] ---> mobile network core
                                            +-> [operator-node.api] ---> mobile network core
                                            +-> [operator-node.api] ---> mobile network core
                                            +-> [operator-node.api] ---> mobile network core
                                            +-> [operator-node.api] ---> mobile network core
                                            +-> [operator-node.api] ---> mobile network core
 
```

Using single db ou multiple db ?
 -> use MongoDb or Sql ?
Use two directory ? public authen and internal authen ? -> find something better
 -> internal directory will use rosyln for creating dynamic entities used by mvno-session-api ?
Replace and eventing api + event persistance and remove logging api
add elastic search for telemetry ? do we need that ? with eventing-api we build stats.
