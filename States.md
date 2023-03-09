# TCP States

## 1. CLOSED: 
This is the initial state of a TCP connection. In this state, the socket is closed, and no data can be sent or received.
## 2. LISTEN: 
The LISTEN state is the initial state of a passive open connection. In this state, the server socket waits for an incoming connection request from a client.

When a client initiates a connection request, it sends a SYN (Synchronize) segment to the server. The server socket then transitions to the SYN-RECEIVED state, sends a SYN-ACK (Synchronize-Acknowledgement) segment to the client, and waits for an ACK (Acknowledgment) segment from the client to complete the connection establishment process.

During the LISTEN state, the server socket is not actively transmitting or receiving data. It is only monitoring the network for incoming connection requests. Once the server receives a connection request, it can transition to a different state to handle the request and exchange data with the client.
## 3. SYN-SENT: 
```mermaid
graph LR
  A[Local] -->|SYN, Seq=X| B[Remote]
  A -->|Timeout| A
  B -->|ACK, Seq=Y, Ack=X+1| C((ESTABLISHED))
  B -->|SYN, Seq=Y, Ack=X+1| D((SYN-SENT))
```
The SYN-SENT state is the state of an active open connection when the client sends a SYN (Synchronize) segment to the server to initiate a connection request.

When a client wants to establish a connection with a server, it sends a SYN segment to the server, which includes an initial sequence number (ISN). The client then enters the SYN-SENT state, waiting for a SYN-ACK (Synchronize-Acknowledgement) segment from the server.

If the server accepts the connection request, it will send a SYN-ACK segment back to the client, including its own ISN. At this point, the client enters the ESTABLISHED state, sends an ACK segment back to the server to acknowledge the SYN-ACK, and begins transmitting data.

If the server does not respond with a SYN-ACK segment, or if the client receives a RST (Reset) segment from the server indicating that the connection request was rejected or the server is unavailable, the client will close the connection by sending a FIN (Finish) segment and transitioning to the CLOSED state.

During the SYN-SENT state, the client is actively transmitting data (the SYN segment) and waiting for a response from the server.
## 4. SYN-RECEIVED: 
```mermaid
graph LR
  A[Local] -->|SYN, Seq=X| B[Remote]
  B -->|SYN, Seq=Y, Ack=X+1| A
  B -->|ACK, Seq=Z, Ack=X+1| C((SYN-RECEIVED))
```
The SYN-RECEIVED state is the state of a passive open connection when the server receives a SYN (Synchronize) segment from a client to initiate a connection request.

When a client sends a SYN segment to the server, the server socket enters the SYN-RECEIVED state and sends a SYN-ACK (Synchronize-Acknowledgement) segment back to the client, which includes the server's own initial sequence number (ISN). The server then waits for an ACK (Acknowledgment) segment from the client to complete the connection establishment process.

If the client acknowledges the SYN-ACK segment by sending an ACK segment to the server, the server transitions to the ESTABLISHED state and can begin transmitting and receiving data. If the client does not send an ACK segment, or if the server does not receive the ACK segment, it will eventually time out and close the connection.

During the SYN-RECEIVED state, the server is actively transmitting data (the SYN-ACK segment) and waiting for a response from the client to complete the connection establishment process.

## 5. ESTABLISHED:
```mermaid
graph LR
  A[Local] -->|Data, Seq=X| B[Remote]
  B -->|ACK, Seq=Y, Ack=X+n| A
  A -->|Data, Seq=X+m| B
```
The ESTABLISHED state is the state when a connection has been established between two endpoints, and both the client and the server are ready to transmit and receive data.

Once the three-way handshake is completed (SYN, SYN-ACK, ACK), both the client and server enter the ESTABLISHED state. In this state, both endpoints can transmit and receive data packets to and from each other.

In the ESTABLISHED state, the sequence numbers are used to keep track of the transmitted and received data. The data is transmitted in segments, and each segment is assigned a sequence number to keep track of it. The receiver acknowledges the receipt of each segment by sending an acknowledgement (ACK) packet with the next expected sequence number.

If there is no activity for a specified period of time, either endpoint can send a keep-alive message to ensure that the connection remains open. When either endpoint is done with the connection, it sends a FIN (Finish) packet to initiate the connection termination process and transition to the FIN-WAIT-1 state.

During the ESTABLISHED state, both endpoints are actively transmitting and receiving data as required by the application protocols running on top of TCP.
## 6. FIN-WAIT-1:
```mermaid
graph LR
  A[Local] -->|FIN, Seq=X| B[Remote]
  A -->|ACK, Seq=X+1, Ack=Y+1| C((FIN-WAIT-2))
  B -->|ACK, Seq=Y+1, Ack=X+1| D((TIME-WAIT))
  B -->|FIN, Seq=Y| E((CLOSE-WAIT))
```
The FIN-WAIT-1 state is the state when an endpoint has sent a FIN (Finish) segment to initiate the connection termination process, but has not yet received an ACK (Acknowledgment) segment from the other endpoint to confirm the receipt of the FIN segment.

When an endpoint wants to terminate a connection, it sends a FIN segment to the other endpoint. The endpoint then transitions to the FIN-WAIT-1 state and waits for an ACK segment from the other endpoint to confirm the receipt of the FIN segment.

If the other endpoint acknowledges the FIN segment with an ACK segment, it transitions to the CLOSE-WAIT state, indicating that it has received a FIN segment and will no longer send any data. If the other endpoint sends any more data, it will be ignored.

If the other endpoint does not send an ACK segment in response to the FIN segment, the endpoint will eventually time out and transition to the FIN-WAIT-2 state.

During the FIN-WAIT-1 state, the endpoint is waiting for the other endpoint to acknowledge the receipt of the FIN segment and confirm the termination of the connection. If the endpoint receives any data from the other endpoint, it will acknowledge the data with an ACK segment and transition to the ESTABLISHED state.
## 7. FIN-WAIT-2:
```mermaid
graph LR
  A[Local] -->|ACK, Seq=X+1, Ack=Y+1| B[Remote]
  A -->|FIN, Seq=X+1| C((TIME-WAIT))
  B -->|FIN, Seq=Y| D((TIME-WAIT))
```
The FIN-WAIT-2 state is the state when an endpoint has received a FIN (Finish) segment from the other endpoint and sent an ACK (Acknowledgment) segment in response, but has not yet received a FIN segment from the other endpoint.

When an endpoint receives a FIN segment from the other endpoint, it sends an ACK segment in response and transitions to the FIN-WAIT-2 state. The endpoint waits for the other endpoint to send a FIN segment to confirm the termination of the connection.

If the other endpoint sends a FIN segment, the endpoint transitions to the TIME-WAIT state, which is a brief period during which the endpoint waits to ensure that all packets in the connection have been delivered and acknowledged. After the TIME-WAIT state, the endpoint transitions to the CLOSED state.

If the other endpoint does not send a FIN segment, the endpoint will eventually time out and transition to the CLOSED state.

During the FIN-WAIT-2 state, the endpoint has acknowledged the receipt of the FIN segment and sent an ACK segment in response. It is waiting for the other endpoint to confirm the termination of the connection by sending a FIN segment.
## 8. CLOSE-WAIT:
```mermaid
graph LR
  A[Local] -->|ACK, Seq=X+1, Ack=Y+1| B[Remote]
  A -->|FIN, Seq=X| C((LAST-ACK))
```
The CLOSE-WAIT state is the state when an endpoint has received a FIN (Finish) segment from the other endpoint and has acknowledged it with an ACK (Acknowledgment) segment, but still has some data to send before terminating the connection.

When an endpoint receives a FIN segment from the other endpoint, it acknowledges the receipt of the FIN segment with an ACK segment and transitions to the CLOSE-WAIT state. In the CLOSE-WAIT state, the endpoint may continue to send data to the other endpoint.

Once the endpoint has finished sending data, it sends a FIN segment to the other endpoint to initiate the connection termination process. The endpoint then transitions to the LAST-ACK state and waits for an ACK segment from the other endpoint to confirm the receipt of the FIN segment.

If the other endpoint acknowledges the FIN segment with an ACK segment, the endpoint transitions to the CLOSED state. If the other endpoint does not acknowledge the FIN segment, the endpoint will eventually time out and transition to the CLOSED state.

During the CLOSE-WAIT state, the endpoint has received a FIN segment from the other endpoint and acknowledged it with an ACK segment, but still has some data to send before terminating the connection. Once the endpoint has finished sending data, it initiates the connection termination process by sending a FIN segment to the other endpoint.

## 9. CLOSING:

The CLOSING state is the state when an endpoint has sent a FIN (Finish) segment to initiate the connection termination process and has received an ACK (Acknowledgment) segment from the other endpoint, but has not yet received a FIN segment from the other endpoint to confirm the termination of the connection.

When an endpoint sends a FIN segment to initiate the connection termination process, it transitions to the FIN-WAIT-1 state. If the other endpoint acknowledges the FIN segment with an ACK segment, it transitions to the FIN-WAIT-2 state. However, if the other endpoint also sends a FIN segment in response to the FIN segment, it transitions to the CLOSING state.

In the CLOSING state, the endpoint has sent a FIN segment and received an ACK segment from the other endpoint, indicating that the other endpoint has received the FIN segment. However, the endpoint is still waiting for the other endpoint to send a FIN segment to confirm the termination of the connection.

If the other endpoint sends a FIN segment in response to the FIN segment, the endpoint transitions to the TIME-WAIT state, which is a brief period during which the endpoint waits to ensure that all packets in the connection have been delivered and acknowledged. After the TIME-WAIT state, the endpoint transitions to the CLOSED state.

If the other endpoint does not send a FIN segment, the endpoint will eventually time out and transition to the CLOSED state.

During the CLOSING state, the endpoint has sent a FIN segment and received an ACK segment from the other endpoint, indicating that the other endpoint has received the FIN segment. However, the endpoint is still waiting for the other endpoint to confirm the termination of the connection by sending a FIN segment.
## 10. LAST-ACK:
```mermaid
graph LR
  A[Local] -->|ACK, Seq=X+1, Ack=Y+1| B[Remote]
  A((CLOSED)) -->|FIN, Seq=X| C((LAST-ACK))
  B -->|ACK, Seq=Y+1, Ack=X+1| D((CLOSED))
```
The LAST-ACK state is the state when an endpoint has sent a FIN (Finish) segment to initiate the connection termination process and has received an ACK (Acknowledgment) segment and a FIN segment from the other endpoint.

When an endpoint sends a FIN segment to initiate the connection termination process, it transitions to the FIN-WAIT-1 state. If the other endpoint acknowledges the FIN segment with an ACK segment, it transitions to the FIN-WAIT-2 state. If the other endpoint also sends a FIN segment in response to the FIN segment, it transitions to the CLOSING state, and once it receives an ACK segment from the other endpoint, it transitions to the LAST-ACK state.

In the LAST-ACK state, the endpoint has sent a FIN segment to initiate the connection termination process and has received an ACK segment and a FIN segment from the other endpoint. The endpoint acknowledges the FIN segment from the other endpoint with an ACK segment and waits for the other endpoint to acknowledge the FIN segment it sent.

Once the other endpoint acknowledges the FIN segment with an ACK segment, the endpoint transitions to the CLOSED state, indicating that the connection has been terminated.

If the other endpoint does not acknowledge the FIN segment with an ACK segment, the endpoint will eventually time out and transition to the CLOSED state.

During the LAST-ACK state, the endpoint has sent a FIN segment to initiate the connection termination process and has received an ACK segment and a FIN segment from the other endpoint. It acknowledges the FIN segment from the other endpoint with an ACK segment and waits for the other endpoint to acknowledge the FIN segment it sent before transitioning to the CLOSED state.
## 11. TIME-WAIT:
```mermaid
graph LR
  A[Local] -->|ACK, Seq=X+1, Ack=Y+1| B[Remote]
  A((CLOSED)) --> C((TIME-WAIT))
  B --> D((CLOSED))
  C -->|Time-out| E((CLOSED))
```
The TIME-WAIT state is a brief period during which an endpoint waits to ensure that all packets in the connection have been delivered and acknowledged before terminating the connection.

When an endpoint sends a FIN (Finish) segment to initiate the connection termination process, it transitions to the FIN-WAIT-1 state. If the other endpoint acknowledges the FIN segment with an ACK segment, it transitions to the FIN-WAIT-2 state. If the other endpoint also sends a FIN segment in response to the FIN segment, it transitions to the CLOSING state, and once it receives an ACK segment from the other endpoint, it transitions to the LAST-ACK state. Once the other endpoint acknowledges the FIN segment with an ACK segment, it transitions to the TIME-WAIT state.

In the TIME-WAIT state, the endpoint waits for a period of time to ensure that all packets related to the connection have been delivered and acknowledged. This is to prevent any stray packets related to the connection from being mistaken for new connections.

During the TIME-WAIT state, the endpoint cannot initiate any new connections or receive any new packets related to the previous connection. However, it can continue to receive and process any packets that were sent before the connection was terminated.

Once the TIME-WAIT state period expires, the endpoint transitions to the CLOSED state and the connection is fully terminated.

The duration of the TIME-WAIT state varies depending on the implementation and configuration of the TCP protocol, but it is typically set to twice the maximum segment lifetime (2MSL), which is the maximum amount of time that a TCP segment can remain in the network before being discarded.
