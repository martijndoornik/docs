# WARNING
This is proposal and is not applied as of today. 

# 1. SSI-protocol

## 1.1 Purpose

The Me app has functionality other apps may want to use. And we do not desire to create a vendor lock-in. To achieve this, we put the way our apps communicate into a protocol: the Self-Sovereign Identity protocol, or SSI for short. The SSI-protocol defines what actions can be taken into an Self-Sovereign Identity app, such as the Me app, and how (web) applications can communicate with the identity of a user.

The main goal is to someday let apps work decentrentralized. We wish the apps to communicate without server. Although this is not 100% possible, we made the SSI-protocol compatible with a small API-call, but this can also be done via the SHH-whisper protocol on Ethereum nodes. 

## 1.2 Requirements



# 2. Grammar

The messages formed in a certain way. This is formed via URI schemes in the following URL: 

`ssi://<request>/?<data>`

Requests can be as per documentation in section 3: Requests. Data is given in key-pair values seperated by ampersand (&). The required data is found in 2.1: Persistant Data. Data required by certain requests is found in the correspondig request in 3.1: Request types.

## 2.1 Persistant data

Each request has set data that must be given by a request or data that is shared among requests. This section will cover the data, the types and a description of it.

#### Request ID (`id`)

The semi-unique identifier of the request. Mainly important for asynchronous request handling. 

#### Communication key (`key`)

The key that should be used to communicate with the client of the application. This can be  

#### Is legacy key (`legacy`)

Optional. Assumed `false` if left empty. 

## 2.2. Feedback protocols

There are multiple ways in which the Identity application can send data back to the user. For this, the identity application will send the data via a feedback protocol. Each feedback protocol works differently and in this section, each will be described. 

Providing multiple will use the first found in the list below (e.g. providing data for SSH and legacy, only SSH will be attempted).

### 2.2.1. SSH/Whisper

The preferred way. This protocol uses an Ethereum node with SHH enabled and Web3 to receive Whisper messages as feedback. 

#### Whisper enabled (`useSsh`)

This must be `true` to use SSH. 

#### Whisper Private Key (`privKey`)

As per Whisper documentation. 

### 2.2.2. Direct websocket

The most direct solution. Connect directly to the client application. Noth that this will result in security risks. **Word of advice:** immediately close the websocket connection when feedback is given or received.

#### NOTICE

This solution is unacceptable in web applications. 

#### Use WebSocket (`useWs`)

This must be `true` to use Websocket feedback.

#### Websocket Connection String (`wsUrl`)

The connection string to the client application. 

### 2.2.3. Communication server (legacy)

The legacy solution to feedback. Have a server (or part of a server) dedicated for received data from the identity application. Note that the data will always be encrypted when communication servers are used. 

#### Use legacy (`useLegacy`)

This must be `true` in order to use legacy communication servers.

#### Legacy URL (`legacyUrl`)

# 3. Requests



## 3.1: Request types

#### Login (login)

Log in into a web application using an identity. 

##### address (string) The address of the identity logging in. 

#### Request for data (request)

Let the application read some of the claims of the identity.

##### claimAddress (string)

# 4. Responses

## 3.1 Status response

### 3.1.1 Status Codes

Status codes are bit flags, counting from right. A binary code of `01` has the first flag, but not the second one. The returned statuscode can be integer 0, which means there are no errors. But on a statuscode 5, this means the binary code of `101`, which has the first and third flag on. 

This section will cover the flags that are know to the system as of now. The sections is numbered per flag, followed by the name of the flag.

The first four bits are health checks for your request. These are to check if the connection you made can actually be made, before any logic is executed. 

#### 1. Failed Execution

This flag marks whether the requested action has failed. A status can have active flags, but that does not mean that the action has automatically mean it has failed. If this flag is active, the transaction has actually not be executed because of failure. 

Tip: `const error = response.statusCode % 2 === 1;`

#### 2. Request rejected

The app has no handle for the request: it is not implemented, not supported and simply not found. 

#### 3. Not authenticated

This flag marks that the application has no user logged it, where is might be required. Some web applications require no log in, in which case this flag will always be on, but it is recommended to keep it on. 

Top: if your application does not require users to log in, a status code of 4 is a request with no errors, rathar than 0. 

#### 4. Already executed?

