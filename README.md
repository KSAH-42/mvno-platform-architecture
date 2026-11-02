# mvno-platform-architecture
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
               |   customers/            |--------------> [ database api ]
               |   subscriptions/        |--------------> [ grid api     ] -> [nodes]
               +-------------------------+
               |   control db access     |
               |   control grid acces    |
               |   include permissions   |
               +-------------------------+
```

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
              +---> database-api                              +---> database-api
              +---> logging-api                               +---> ....
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
