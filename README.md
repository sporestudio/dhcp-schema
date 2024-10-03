# DHCP Schema

**Our diagram is based on the following assumption:**

A new user joins a network. We assume the network has 2 DHCP servers. 

- The user sits at their machine and turns on their computer. 
- They work for 1 hour connected to DHCP server #1. 
- The user experiences a 3-minute outage. The user reconnects to the network after 3 minutes. Server #1 is down. 
- The user remains active for 12 hours. 
- The user disconnects for 1 hour. 
- The user reconnects to the network.
- The user stays on the network for 5 hours and then disconnects permanently.

<br/>

>[!NOTE]
> *We assume that our **DHCP server** operates with average lease times (8 hours).*


## Diagram

```mermaid
sequenceDiagram
    participant server1
    participant client
    participant server2
   

    Note over client: User turns on their computer

    client->>server1: DHCPDISCOVER (Broadcast message)
    client->>server2: DHCPDISCOVER (Broadcast message)
    server1->>client: DHCPOFFER (8h max. use)
    server2->>client: DHCPOFFER (8h max. use)

    client->>server1: DHCPREQUEST (Broadcast message) 
    client->>server2: DHCPREQUEST (Broadcast message) 
    server1->>client: DHCPACK (Server 1 provides an IP to client)

    client->>client: The connection gets interrupted for 3 minutes
    
    note over client: server1 is down.

    client->>server1: DHCPREQUEST
    note over client: No answer from server 1.


    client->>server2: DHCPDISCOVER (Broadcast message)
    server2->>client: DHCPOFFER (8h max. use)
    client->>server2: DHCPREQUEST (Broadcast message) 
    server2->>client: DHCPACK (Server 2 provides an IP to client)

    note over client: After 4 hours the client has to ask again for the ip availability.

    client->>server2: DHCPREQUEST

    server2->>client: DHCPACK (The client can continue to use the same IP)


    client->>client: After 8 hours the lease time finish



    client->>server2: DHCPDISCOVER (Broadcast message)
    server2->>client: DHCPOFFER (8h max. use)
    client->>server2: DHCPREQUEST (Broadcast message)
    server2->>client: DHCPACK (Server 2 provides an IP to client)


    

    note over client: client turns off computer after 4 hours.

    note over client: The client start the computer after 1 hour. 3 lease hours remains.


    client->>server2: DHCPREQUEST

    server2->>client: DHCPACK (Server 2 provides client with the same IP)

    client->>client: After 3 hours the lease time finish


    client->>server2: DHCPDISCOVER (Broadcast message)
    server2->>client: DHCPOFFER (8h max. use)
    client->>server2: DHCPREQUEST (Broadcast message)
    server2->>client: DHCPACK (Server 2 provides an IP to client)

    note over client: After 4 hours the client has to ask again for the ip availability.


    client->>server2: DHCPREQUEST

    server2->>client: DHCPACK (Server 2 provides client with the same IP)

    note over client: The user is permanently disconnected after 5 hours.
```