## Product Choice
Wildberries.ru
Wildberries.ru
A marketplace app with good scalability.
## Main Components
![Wildberries Component Diagram](/docs/diagrams/out/wildberries/architecture-component/Component%20Diagram.svg)
![Wildberries Component PlantUML](/docs/diagrams/src/wildberries/architecture-component.puml)
cache - stores frequently used data for very fast operations
push_service - provides notifications for the user in many occasions, e.g. their order is delivered
client_web - provides a webclient for end users
pay_service - a "coroutine" that processes the payments
catalog_service - lists products from the DB to the enduser

## Data flow
![Wildberries Sequence Diagram](/docs/diagrams/out/wildberries/architecture-sequence/Sequence%20Diagram.svg)
![Wildberries Sequence PlantUML](/docs/diagrams/src/wildberries/architecture-sequence.puml)
group #FFFFE0 4. Warehouse Fulfillment (Async)
    Kafka -->> InvSvc: Consume (Commit Reservation)
    
    Kafka -->> WMS: Consume (OrderPaid)
    activate WMS
    
    WMS -> DB: Look up Warehouse Location (Route logic)
    WMS -> WMS: Generate Pick List (Closest Warehouse)
    
    note right of WMS: Physical Process:\nRobot/Human picks item.
    
    WMS -> Kafka: Publish Event (Status: ASSEMBLY_COMPLETE)
    deactivate WMS
    
    Kafka -->> OMS: Update Status (READY_TO_SHIP)
    
    OMS -> Gateway: Push Notification (SSE)
    Gateway -->> App: Notification ("Goods on the way")
end

## Deployment
![Wildberries Deployment Diagram](/docs/diagrams/out/wildberries/architecture-deployment/Deployment%20Diagram.svg)
![Wildberries Deployment PlantUML](/docs/diagrams/src/wildberries/architecture-deployment.puml)
Mostly deployed in https

## Assumptions
1. Thanks to the use of redis and sharded DB, performance is very good
2. Shipment of product is quick thanks to robust routing service

## Open Questions
1. What protocols are used to protect sensitive data?
2. How does the app process what products to show to end user?