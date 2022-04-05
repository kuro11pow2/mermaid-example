# mermaid

```mermaid
sequenceDiagram
    autonumber
    Alice->>John: Hello John, how are you?
    loop Healthcheck
        John->>John: Fight against hypochondria
    end
    Note right of John: Rational thoughts!
    John-->>Alice: Great!
    John->>Bob: How about you?
    Bob-->>John: Jolly good!
```

```mermaid
classDiagram
    classA --|> classB : Inheritance
    classC --* classD : Composition
    classE --o classF : Aggregation
    classG --> classH : Association
    classI -- classJ : Link(Solid)
    classK ..> classL : Dependency
    classM ..|> classN : Realization
    classO .. classP : Link(Dashed)
```

```mermaid
stateDiagram-v2
    [*] --> Active

    state Active {
        [*] --> NumLockOff
        NumLockOff --> NumLockOn : EvNumLockPressed
        NumLockOn --> NumLockOff : EvNumLockPressed
        --
        [*] --> CapsLockOff
        CapsLockOff --> CapsLockOn : EvCapsLockPressed
        CapsLockOn --> CapsLockOff : EvCapsLockPressed
    }
```

# platuml

## component
```plantuml
@startuml

title Kubernetes
skinparam componentStyle uml2

package "Development" {
  component Developer
  component [Code\nVCS] as VCS
  component CI
  component CD
}

package "External Services" {
  component MQ
  component DB
}

package "Kubernetes Cluster" {
  component "Nodes/Pods/Containers" as Service
  component [Controller] as Controller
}

package Artifacts {
  component [Container Registry] as Registry
  component [Config VCS] as Config
}

package "External Network" {
  component [User] as WebBrowser
}

Developer -l-> VCS : commit
VCS -d-> CI : test & build
CI -r-> CD
CI -d-> Registry : upload image
CD -r-> Controller : deploy

WebBrowser -u-> Service

Service -u-> MQ
Service -u-> DB

Controller -r-> Registry : fetch
Controller -d-> Config : fetch

Controller -l-> Service : deploy

@enduml
```

## sequence
```plantuml
@startuml

title Data flow

entity "Data Source" as DataSource

box "Integration" #lightblue
  entity "fluent-bit" as fluentbit
  entity "envoy" as envoy
end box

box "Monitoring"
  entity "Prometheus" as Prometheus
  entity "Grafana" as Grafana
end box

box "Splunk Platform" #lightblue
  entity "Splunk\nHeavy Forwarder" as HF
  database "Splunk Index" as IDX
  entity "Splunk SHC" as SHC
end box

actor "User" as User

== Events ==

DataSource -> fluentbit
fluentbit -> envoy : HTTP-Proxy/HEC
envoy -> HF : HTTP/HEC
HF -> IDX : TCP/Splunk forward

== Metrics ==

envoy -> Prometheus : metrics
fluentbit -> Prometheus : Metrics over HTTP

== Reporting ==

Prometheus -> Grafana
Grafana -> User : HTTP
IDX -> SHC
SHC -> User : HTTP

@enduml
```

## timing
```plantuml
@startuml

robust "CPU0" as CPU0
robust "CPU1" as CPU1
concise "Task Progress" as TaskProgress
concise "Task Breakdown" as TaskBreakdown

@0
TaskProgress is Idle
TaskBreakdown is None
CPU1 is Idle
CPU0 is Idle

@100
TaskProgress is Running
TaskBreakdown is Serial
CPU0 is PreparingTask
CPU1 is Idle : Idle

CPU0@100 <-> @200 : 100 ms

@200
TaskBreakdown is Parallel
CPU0 is NumberCrunching
CPU0 -> CPU1 : Distribute task
CPU1 is NumberCrunching

@700
TaskProgress is Idle
TaskBreakdown is None
CPU1 -> CPU0 : Complete task
CPU0 is Idle
CPU1 is Idle

@1200
CPU0 is Idle
CPU1 is Idle
@enduml
```

## graph
```plantuml
digraph G {

  {node [label="Developer" style=filled color=skyblue] d1 d2 d3 d4 d5 }

  d1 -> d2 [len=2];
  d1 -> d3 [len=2];
  d1 -> d4 [len=2];
  d1 -> d5 [len=2];

  d2 -> d1 [len=2];
  d2 -> d3 [len=2];
  d2 -> d4 [len=2];
  d2 -> d5 [len=2];

  d3 -> d1 [len=2];
  d3 -> d2 [len=2];
  d3 -> d4 [len=2];
  d3 -> d5 [len=2];

  d4 -> d1 [len=2];
  d4 -> d2 [len=2];
  d4 -> d3 [len=2];
  d4 -> d5 [len=2];

  d5 -> d1 [len=2];
  d5 -> d2 [len=2];
  d5 -> d3 [len=2];
  d5 -> d4 [len=2];

}
```

## flow
```plantuml
@startuml
:Perform task;
note right
E.g. Manually resize a volume
end note
-> 
if (OK?) then
  -[#blue]-> p=**0.97**;
  :Task complete;
  -[#blue]-> Info;
  #Lightblue:Success1 = 0.97;
  -[#blue]->
else
  -[#red]-> p=**0.03**;
  :Emergency with plan;
  note right: Follow rollback procedure
  -[#red]->
  if (Recover?) then
    -[#green]-> p=**0.90**;
    #Lightgreen:Abort1 = (0.03 * 0.90);
    -[#green]->
    stop
  else
    -[#red]-> p=**0.10**;
    :Emergency without plan;
    -[#red]->
    if (Recover?) then
      -[#green]-> p=**0.70**;
      #Lightgreen: Abort2 = (0.03 * 0.10 * 0.70);
      -[#green]->
      stop
    else
      -[#red]-> p=**0.30**;
      #Pink: Failure1 = (0.03 * 0.10 * 0.30);
      -[#red]->
      stop
    endif
  endif
endif
-[#blue]->;
stop

@enduml
```


## C4 example - container
```plantuml
@startuml
 
!include C4-PlantUML/C4_Container.puml
!define ICONS https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/master/
 
!include ICONS/font-awesome-5/user_tie.puml
 
LAYOUT_AS_SKETCH()
LAYOUT_LEFT_RIGHT()
LAYOUT_WITH_LEGEND()
 
System_Ext(cc, "Contact Center", "Contact point, switches customer calls.")
Person_Ext(manager, "Manager", "Makes a decision from statistics provided by CLOVA AiCall", "user_tie")
 
System_Boundary(clova_ai_call, "CLOVA AiCall") {
 
  Container(pa, "Protocol Adapter", "SIP, gRPC client", "Converts voice streams into supported format.")
  Container(vsg, "Voice Streaming Gateway", "gRPC server", "Manages call sessions and handle voice streaming.")
  Container(cic, "CIC", "CIC Protocol", "Processes AI requests")
  Container(interface, "AiCall Interface", "Kafka, REST API", "Provides interfaces for AiCall console")
  Container(console, "AiCall Console", "Web app", "Provides UI for retrieving statistics.")
  ContainerDb(db, "Database", "Redis, MySQL", "Stores activities, events, and call information")
       
  Rel(cc, pa, "Forwards voice stream data to", "SIP trunk")
  Rel_L(pa, vsg, "Forwards converted voice stream to", "gRPC call")
 
  Rel(vsg, cic, "Requests voice recognition and processing corresponding works to", "REST/HTTPS 2.0")
  Rel(vsg, interface, "Sends event log to", "REST API call")
  Rel(manager, console, "Retrieves statistics from", "HTTP")
  Rel(console, interface, "Queries statistic data from", "REST API call")
 
  Rel_L(interface, db, "Reads from and writes to", "JDBC")
}
 
@enduml
```


## C4 example - component
```plantuml
@startuml
 
!include C4-PlantUML/C4_Component.puml
!define ICONS https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/master/
 
!include ICONS/govicons/user_suit.puml
!include ICONS/font-awesome-5/user_tie.puml
!include ICONS/material/dialer_sip.puml
 
LAYOUT_AS_SKETCH()
LAYOUT_WITH_LEGEND()
 
Container_Ext(vsg, "Voice Streaming Gateway", "gRPC server", "Manages call sessions and handle voice streaming.")
Container_Ext(console, "AiCall Console", "Web app", "Provides UI for retrieving statistics.")
ContainerDb_Ext(db, "Database", "Redis, MySQL", "Stores activities, events, and call information")
 
Container_Boundary(ai_call_interface, "AiCall Interface") {
 
  Component(signin, "Sign In Controller", "Spring MVC REST Controller", "Allows users to sign in to AiCall Console.")
  Component(query, "Query Controller", "Spring MVC REST Controller", "Allows users to Retrieves statistics data with query.")
  Component(log, "Event Log Controller", "Spring MVC REST Controller", "Allows other components to store their events logs to DB.")
  Component(auth, "Auth Component", "Spring Bean", "Provides functionality related to authentication")
  Component(db_control, "DB Controller", "Spring Bean", "Provides functionality related to DB access.")
 
}
 
Rel(vsg, log, "Makes API calls to", "JSON/HTTPS")
Rel_R(console, signin, "Makes API calls to", "JSON/HTTPS")
Rel_R(console, query, "Makes API calls to", "JSON/HTTPS")
Rel(signin, auth, "Uses")
Rel(query, db_control, "Uses")
Rel(log, db_control, "Uses")
Rel(auth, db_control, "Uses")
Rel(auth, db_control, "Uses")
Rel(db_control, db, "Reads from and writes to", "JDBC")
 
@enduml
```