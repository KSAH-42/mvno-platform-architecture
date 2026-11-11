# mvno-platform-architecture (DRAFT)
This repository present a full web new design for an IT system between mvno and telecom infra.

~~~
                         T E L E C O M    O P E R A T O R     P L A T F O R M     F O R     M V N O S

     ║         +----------------------------------+           ║              +------------------------------+
     ║         | operator-registry.cli            |           ║              | operator.administration.front| ◂------------- internal access for management
     ║         +----------------------------------+           ║              +------------------------------+
     ║                                                        ║                          |
     ║                                                        ║                          ▼
 mvno-calls    +----------------------------------+           ║              +------------------------------+   
 ----║-------▸ | operator-registry-api            | ----------║------------▸ | operator-identity.api        |
 ◂---║-------- +----------------------------------+           ║              +------------------------------+
     ║                      ▲                                 ║                          ▲       
     ║                      |                                 ║                          | 
     ║                      |                                 ║    +---------------------+ 
     ║                      |                                 ║    |                     | 
     ║                      |                                 ║    |         +------------------------------+ 
     ║                      |                                 ║    |         |       operator-cli           | 
     ║                      |                                 ║    |         +------------------------------+ 
     ║                      |                                 ║    |                     | 
     ║                      |                                 ║    |         +------------------------------+ 
     ║                      |                                 ║    +-------▸ | operator-dispatch-api        | 
     ║                      |                                 ║    |         +------------------------------+ 
     ║                      |                                 ║    |                     |
     ║          . . . . . . . . . . . . . . . . . . .         ║    |         +------------------------------+      +---------------------------------------+
     ║          .   mvno operator apis              .         ║    +-------▸ | operator-datastore.api       | ◂--- | operator-transaction-pool-api         |
     ║          .           |                       .         ║    |         +------------------------------+      +---------------------------------------+
     ║          .           |                       .         ║    |                     |
     ║          .           |                       .         ║    |         +------------------------------+      +---------------------------------------+
 mvno-calls     .  +-----------------------------+  .         ║    +-------▸ | operator-memory-pool.api     | ◂--- | operator-datastore-migration.api      |
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
     ║                      |                                 ║    |               +------------------------+
     ║                      |                                 ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║                      |                                 ║    |               +------------------------+
     ║          . . . . . . . . . . . . . . . . . . .         ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║          .   mvno migration apis             .         ║    |               +------------------------+
     ║          .           |                       .         ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
     ║          .           |                       .         ║    |               +------------------------+
     ║          .           |                       .         ║    |               | operator-grid-node-api |   ◂--------▸  Mobile network core
external-calls  . +-------------------------------+ .         ║    |               +------------------------+
-----║--------▸ . | operator-data-factory-api     | . --------║----+               | operator-grid-node-api |   ◂--------▸  Small Mobile network core
◂----║--------  . |                               | .         ║    |               +------------------------+
     ║          . +-------------------------------+ .         ║    |                     
     ║          .                                   .         ║    |
     ║          .....................................         ║    |         +------------------------------+
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

The main idea it used communicated using http between processes and keep implementatation of each component simple and reduce projects dependencies on microservice projects. 

# operator-registry-cli
Console application used for:
 * register/register public api
 * changing registry configuration or api config
 * disable/enable session
 * querying the status of active session
 * querying the status of mnvo-api
 * enabling/disabling mnvo-api
 * changing token renewal timeout
 * send a request to a mvno-api

# operator-registry-api
Main entrance authenticate external applications using http will be redirected to the rigth api. It used to discover api depending to it's api key. Public apis can be located any where.

# operator-mvno-api
Dedicated implementation for a mvno partenair as a web api.
Provide a better isolation, we can add new mvno-partenar without interruption
Mvno partenair make http calls to this api.
It can handle  N number of internal memory session instances. 
It expose commands and queries, only the needs not necessary all features.
All operator-mvno-api must used HttpAuthenticateHeader (and use the appropriate scheme, or a support a custom scheme that can be different to public api) and HttpAuthorizationHeader. It may be possible that the mvno-api will not get the endpoints of some-api like the operator-dispatch-api.

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
Authenticate api and users, deliver permissions. Public api must authenticate to this api. It's a kind of directory, but it doesn't persist or managed business entities, only managed authentication, and system resources. Business entities are store on the datastore api.

This api provide also registration mecanism for apis. No external api, will be able to discovered internal/systems api through the operator-registry-api. This api must expose something to allow to retrieve the list of datastore ordered by a priority or something else.


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
 operator-cli.exe grid create-node --id "1234465B-1888-48E8-BB43-181D2C1F682F" --type rabbit.telecom/1.1
 operator-cli.exe grid start-node --id "1234465B-1888-48E8-BB43-181D2C1F682F"
 operator-cli.exe network enable-cellular --id "B8D531EB-18E8-47E5-B4B3-1D218C681FF2" --node 1234465B-1888-48E8-BB43-181D2C1F682F
 operator-cli.exe network set-cellular-bandwith --id "B8D531EB-18E8-47E5-B4B3-1D218C681FF2" --value 12345000 --node 1234465B-1888-48E8-BB43-181D2C1F682F
 operator-cli.exe logout
~~~

a package repository is not present in the diagram. So the grid-api must have controller with several methods downloading  and install node. or even the cli must communicate with a hidden agent-api.

# operator-dispatch-api
This api used to publish/subscribe to send and receive event for applications, it can expose the following event handling mecanisms:

 * send event to any application using http webhooks: sendevent method will used a http client and post a message, or enqueue a message
 * receive event using long pooling mecanism: by making http call on controller and pass a timeout value, the controller will block on concurrent queue until an event(s) as enqueue

the event must be persisted. This api must be used only to status changed, configuration changed. And never to send a action to the grid-api. the grid-api can be notified to changed. the mvno-api must never send a message to this api for sending or broacast an action to all grid-api. the mvno-api must get the list of grid-api and send or not a request to a grid-api.

it may be possible to introduce an operator-dispatcher-api reserved for publish/subscribe event messages to applications using webhook, etc... we can used signal-r for instance for publish/subscribe, but here is to show how we can do without any nuget packgages. 

the cli can expose messaging features, in this example if the target is not specified , we broadcast the message
~~~
 operator-cli.exe send-message --target "B538ED1B-1478-8EE5-2183-1DCB4B68F21F" \
   --topic "event.bandwith.changed" \
   --message \
    {
        "node": "B8D531EB-18E8-47E5-B4B3-1D218C681FF2"
        "bandwith": "12345000",
    }
~~~

and may predifined message type like 
~~~
 operator-cli.exe send-message --tyoe-bandwih-changed --target "B538ED1B-1478-8EE5-2183-1DCB4B68F21F" --Node  "B8D531EB-18E8-47E5-B4B3-1D218C681FF2" --value "12345000"
~~~

# operator-datastore-api
Store entities and settings using a mongodb in a single place. One datastore per environnement DEV,PROD,etc... for failover and restoration and backup.
for sensitive data like credit card, the real value must be store on operator-vault-api which are not represent here. the CreditCardEntity can expose a kind of id-pointer to the vault or something similar.

the cli can expose features

~~~
 operator-cli.exe datastore backup --file "my-backup.backup"
~~~

# operator-datastore-migration-api

This api must expose method to run or cancel a migration and configure the how the migration will be executed. For instance, a controller can expose a method to configure by passing migration settings, transformations settings. For instance, when launching the migration, this api will read data on the memory-pool-api and select memmory bocks passed on the configuration settings. Then the migration will create/delete/udapte/transform entities on a parallel datastore-api. it's a second datastore-api reserved for migration. After the migration will completed, it pusblish a message to the dispatcher and a migration result status on saved on the datastore. From the operator-administration-api, people can this view the migration result and decided to swap datastores by changing endpoints / or priority on datastore endpoint lists registered on the identity api. 

In this case we can have an operator-data-factory-api that write only to the operator-memory-pool-api. and the migration-api will read only on the memory-pool

~~~
 operator-cli.exe datastore run-migration --target DATASTORE~1
~~~
or
~~~
 operator-cli.exe datastore run-migration --target DATASTORE[1]
~~~

and only the order and the content of the main datastore to keeps previous data
~~~
 operator-cli.exe datastore swap --from DATASTORE[1] --to DATASTORE[0]
~~~

# operator-transaction-pool-api
This api used to control a transaction by making:
 * start a transaction
 * stop a transaction
 * pause a transaction
 * execute step by step the next task of transaction
   
A transaction can be:
 * a class
 * a class load on different assembly
 * a script C#

This api will get an entity transcaction from the datastore api. it will browse the task list call the execute or rollback method. Depending of the implementation of transaction-workflow, it can a be also class a script that make http call on web-api. It store the results/status/errors, etc... and the transaction can also implement retries mecanisms on the task execute method
The transaction must save it's status, that mark as restauration point in case of critical failure occurs. The after restart the api, we can restore the position of the transaction, and continue the process.

# operator-memory-pool-api
In case of bulk commands, the memory-pool store transactions including the status of each steps. Transaction represent the commands/workflow to execute. Each transaction store in the memory-pool is just object with a id, a type, a list of step with status, and the transaction settings.
the memory pool can be used to store any kind of objects
structure

An example
~~~  
 public class MemoryBlock
 {
     public string Id { get; set; }
     public string Type { get; set; }
     public string Owner { get; set; }      // must not be expose the by api it should expose by the identity api
     public string[] Readers { get; set; }  // must not be expose the by api it should expose by the identity api
     public string[] Writers { get; set; }  // must not be expose the by api it should expose by the identity api
     public Dictionary<string,string> Headers { get; set; }
     public Stream Content { get; set; }
 }
~~~

the memory-pool-api must controlled by permissions on create/remove/read/write/update.

The cli can expose features

~~~
  operator-cli.exe memory-pool clear
~~~

# operator-data-factory-api
It's an example of an external system used to publish data, in this case it publish data migration and write on the memory-pool-api.

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
fuse the operator-transaction-pool-api and operator-memory-pool-api ?
 -> so no, we can have a data system that write a data for migration and let trigger a migration transaction that can be controlled step by step with a completion percentage. juste make clean seperation of concerns. the memory-pool can be used by different api. 
the agent-api for deployment can use os containerization sdk ?
