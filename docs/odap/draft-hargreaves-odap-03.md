## Open Digital Asset Protocol
### draft-hargreaves-odap-03

#### M. Hargreaves (Quant Network)
#### T. Hardjono (MIT)
#### R. Belchior (INESC-ID, Instituto Superior Tecnico)


## Abstract

   This memo describes the Open Digital Asset Protocol (ODAP).  ODAP is
   an asset transfer protocol that operates between two gateway devices.
   The protocol includes a description of virtual or digital assets held
   on distributed ledgers in an open and interoperable format, a session
   negotiation part and message passing flows between gateways
   connecting disparate distributed ledger technologies (DLTs).

## Status of This Memo

   This Internet-Draft is submitted in full conformance with the
   provisions of BCP 78 and BCP 79.

   Internet-Drafts are working documents of the Internet Engineering
   Task Force (IETF).  Note that other groups may also distribute
   working documents as Internet-Drafts.  The list of current Internet-
   Drafts is at https://datatracker.ietf.org/drafts/current/.

   Internet-Drafts are draft documents valid for a maximum of six months
   and may be updated, replaced, or obsoleted by other documents at any
   time.  It is inappropriate to use Internet-Drafts as reference
   material or to cite them other than as "work in progress."

   This Internet-Draft will expire on May 5, 2021.

## Copyright Notice

   Copyright (c) 2020 IETF Trust and the persons identified as the
   document authors.  All rights reserved.

   This document is subject to BCP 78 and the IETF Trust's Legal
   Provisions Relating to IETF Documents
   (https://trustee.ietf.org/license-info) in effect on the date of
   publication of this document.  Please review these documents
   carefully, as they describe your rights and restrictions with respect
   to this document.  Code Components extracted from this document must
   include Simplified BSD License text as described in Section 4.e of
   the Trust Legal Provisions and are provided without warranty as
   described in the Simplified BSD License.


## 1. Introduction

There is a lack of interoperability between individual blockchains, but also a general difficulty building open DLT networks.	Extant networks are custom built and relatively closed, usually limited to networks of a single DLT type.

This memo proposes at DLT-agnostic protocol in order to allow the creation of business applications that use and modify multiple DLTs, through a single programmatic interface.

The target DLTs can be of any type, operated by different owners and managed using different DLT interoperability / management platforms that implement ODAP interfaces.

These platforms may act as gateways or relays for the application to interact with the hosted DLTs.	They are referred to herein as DLT Gateways.

When correctly implemented and deployed, the protocol should provide the basis for solutions involving asset migration between two DLT systems, as well as use-cases when one side is non-DLT system (e.g. legacy system).


## 2. Conventions used in this document

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in RFC 2119 [RFC2119].

In this document, these words will appear with that interpretation only when in ALL CAPS.	Lower case uses of these words are not to be interpreted as carrying significance described in RFC 2119.


## 3. Terminology

The following are some terminology used in the current document. Further terminology can be found in [Arch].

- Client application: This is the application employed by a user to interact with a gateway node.

- Gateway: The node of the DLT system functionally capable of acting as a gateway in an asset transfer.

- Sender gateway: The gateway that initiates a unidirectional asset transfer.

- Recipient gateway: The gateway that is the recipient side of a unidirectional asset transfer.

- DLT resources: The various interior protocols, data structures and cryptographic constructs that are a core part of a DLT system.

- Off-DLT resources: The various resources that are outside a DLT system, and are not part of the operations of the DLT system.

- Role: As in the classic client-server roles.	In the gateway-to- gateway interaction, one gateway will take the role of the client while the other takes the role of the server, depending on the type of interaction flow.

- Claim: An assertion made by an Entity [JWT].

- Claim Type: Syntax used for representing a Claim Value [JWT].

- DLT Claim: An assertion made by a Gateway regarding the status or condition of resources (e.g. asset, public keys, etc.) accessible to that gateway within its DLT system.


## 4. Open Digital Asset Protocol

### 4.1.	Overview

The Open Digital Asset Protocol (ODAP) is a gateway-to-gateway protocol used by a sender gateway with a recipient gateway to perform a unidirectional transfer of a virtual asset [Arch].

The protocol defines a number of API endpoints, resources and identifier definitions, and message flows corresponding to the asset transfer between the two gateways.


### 4.2.	ODAP Model

Following the gateway interoperability architecture [Arch], the model for ODAP is shown in Figure 1.

The Client (application) interacts with its local gateway (G1) over an interface (API Type-1) in order to provide instructions to the gateway with regards to actions related resources located in the local DLT system (L1) and resources located in remote DLT system (L2).

Gateways interact with each other over a gateway interface (API Type- 2).	A given gateway may be required to access resources that are not located in DLT system L1 or DLT system L2.	Access to these types of resources are performed over an Off-DLT interface (API Type-3).


### 4.3.	Types of APIs

The following are the types of APIs in ODAP:

- Gateway APIs for client (API Type-1): This the REST APIs that permit a Client (application) to interact with a local gateway, and issue instructions for actions pertaining to resources accessible to the gateway in the local DLT system.

- Gateway APIs for peer gateways (API Type-2): This is the REST APIs employed by two (2) peer gateways in performing unidirectional asset transfers.

- APIs for validation of off-DLT resources (API Type-3): This is the REST APIs made available by a resource server (resource owner) at which a gateway can access Off-DLT resources.

The use of these APIs is dependent on the mode of access and the type of flow in question.


### 4.4.	Types of Flows

The ODAP protocol defines the following three (3) flows:

- Transfer Initiation flow: This set of flow deals with the asset profile verification, asset ownership evidence verification and identities verification. It also boostraps the next phases, by explicitly defining the set of operations to be conducted by involved gateways.

- Lock-Evidence Verification flow: This set of floxw deals with the conveyance of evidence regarding the lock (escrow) status of an asset by one gateway, and the verification of the evidence by the other gateway.

- Commitment Establishment flow: This set of flow deals with the asset transfer and commitment establishment between two gateways on behalf of their DLT systems.

These flow will be discussed below.


### 4.5.	Resources and Identifiers

- Resource addressing for DLTs, using the URL syntax.

- Client identification based on the URN format.	These are for identifying clients (developers and applications) who access these resources, and which in some use-cases require access authorization.
  
- Protocol message family for negotiating authentication, authorisation, and parameters for confidential channel establishment.

- Resource discovery mechanism for developers and applications to discover DLT-based resources hosted at a DLT gateway.	The gateway response is subject to the level of access granted to that developer or application.

### 4.6.	Access Modes

This draft proposes three (3) distinct mode of operation for Clients when accessing resources recorded a DLT.	These modes make use of a gateway, with the assumption that a gateway has full access to the DLT behind the gateway.

#### 4.6.1.	Direct Mode: Simple Client to Gateway

In this mode, the client uses its local gateway known to the client in order to access (e.g. local transactions to) the local DLT.	This is shown in Figure 2.

In the direct mode, the simplest case, a client application interacts with a single DLT gateway in order to interact with the DLTs hosted behind the gateway.
  
The application must be recognized and authorized by the gateway. Asset transfers between the DLTs behind the gateway are possible, and the set of operations specified in section 5.10 MUST be supported by the DLT Gateway.

Additional operations specific to DLT or Gateway implementations MAY also be available.

#### 4.6.2.	Direct Mode: Client to Multiple Gateway

In this mode, the client is interacting with multiple gateways simultaneously in order to access the DLTs behind each of those gateways.	The client is assumed to be performing the synchronization of actions while interacting these gateways.	This is illustrated in Figure 3.

Direct mode can support connections from a single application to multiple DLT gateways.	The applications may assert different identities with each gateway, provided it has the relevant credentials.

The applications can interact with the DLTs behind each gateway according to the authorizations granted by the gateways.

Asset transfers between/across DLTs hosted behind different gateways are not supported in this mode.

#### 4.6.3.	Relay Mode: Client-initiated Gateway to Gateway

In this mode, the application interacts with a single Gateway, and that Gateway acts as an intermediary to other Gateways.

Connection types and security methods used in the application to gateway connection can differ from those used in the gateway to gateway connection(s).	The authorization method and credentials presented on behalf of the application must be acceptable to the final target gateway(s).

In Relay Mode, additional functionality is available.	Asset transfers, based on a two/three phase commit are available.	These rely on evidence of locks on source DLTs, which can be passed from Gateway to Gateway, insulating the application from the additional complexity and keeping the lock data private from the application.

Compliant Gateways MUST implement these operations, in order to support Relay Mode.

Multi-hop connections between gateways are out of scope of this document.




## 5. ODAP Message Format, Identifiers and Descriptors

### 5.1.	Overview

This section describes (i) the phases of the ODAP protocol; (ii) the format of ODAP messages; (iii) the format for resource descriptors;
(iv) a method for gateways to implement access controls; (iv) protocol for negotiating security capabilities; (v) discovery and accessing resources and provisions for backward compatibility with existing systems.


### 5.2.	ODAP Message Format

ODAP messages are exchanged between applications (clients) and DLT gateways (servers).	They consist of protocol negotiation and functional messages.

Messages are JSON format, with protocol specific mandatory fields, support for arbitrary authentication and authorization schemes and support for a free format field for plaintext or encrypted payloads directed at the DLT gateway or an underlying DLT.
An ODAP message is encoded in the JSON format, and is composed by an ODAP Header and an ODAP Payload.


The ODAP header is composed by the following mandatory fields:
- Version: ODAP protocol Version (major, minor).

- Session ID: unique identifier (UUIDv2) representing a session

- Sequence Number: monotonically increasing counter that uniquely represents a message from a session. 
  
- ODAP Phase: current ODAP phase

- Message_signature: Gateway EDCSA signature over the ODAP header and ODAP payload. 

The ODAP Payload has the following mandatory fields:
- Payload: Payload for POST, responses, and native DLT txns. The payload is specific to the current ODAP phase.

- Payload Hash: hash of the current message payload.


Other relevant attributes need to be captured for logging purposes [ODAP-2PC].

### 5.3.	Digital Asset Resource Descriptors

Resources are identified by URL [RFC 1738] as described below:
- The type is new: application/odapres
- The access protocol is ODAP.

Data included in the URL includes the following:

#### 5.3.1.	Organisation Identifier

This Legal Entity Identifier (LEI) or other identifier linking resource ownership to real world entity.	Any scheme for identifying DLT Gateway owners may be implemented (e.g.	LEI directory, closed user group membership, SWIFT BIC, etc.).

The developer or application MAY validate the identity with the issuing authority.	The identifier is not a trusted identity, but MAY be relied on where trust has been established between the two parties (e.g. in a closed user group).

The mechanisms to determine organizations identifiers is out of scope for the current specification.

#### 5.3.2.	DLT Gateway / Endpoint ID

FQDN of the ODAP compliant DLT gateway.	Required to establish IP connectivity.	This MUST resolve to a valid IP address.

#### 5.3.3.	DLT Identifier

Specify to gateway behind which the target DLTs operates.	This field is local to the DLT gateway and is used to direct ODAP interactions to the correct underlying DLT.

For example: "Hyperledger1", "Bitcoin, "EU-supply-chain".

#### 5.3.4.	Resource

Specifies a resource held on the underlying DLT.	This field must be meaningful to the DLT in question but is otherwise an arbitrary string.	The underlying object it points to may be a DLT address, block, transaction ID, alias, etc. or a future object type not yet defined.
  
#### 5.3.5.	Examples 

odapres://quant/api.gateway1.com/ripple

odapres://quant/api.gateway1.com/bitcoin/xxxxxADDRESSxxxxx



### 5.4.	Digital Asset Resource Client Descriptors 

Resources are identified by URN as described below:
 - The type is new: application/odapclient

The URN format does not imply availability or access protocol. 

Data included in the URN includes the following:

##### 5.4.1 Organization Identifier

Legal Entity Identifier (LEI) or other identifier linking resource ownership to real world entity.	Any scheme for identifying DLT Gateway owners may be implemented (e.g.	LEI directory, closed user group membership, BIC, etc.).

The DLT Gateway MAY validate the identity with the issuing authority. The identifier is not a trusted identity, but MAY be relied on where trust has been established between the two parties (e.g. in a closed user group).


##### 5.4.2.	DLT Gateway / Endpoint ID
Multi-DLT applications can operate in a mode whereby the application connects to its local DLT gateway, which then forwards application traffic to local DLTs and to remote DLTs via other ODAP gateways.

Where this is the case, this field identifies the "home" gateway for this application.	This may be required to carry out Gateway to Gateway handshaking and protocol negotiation, or for the server to look up use case specific data relating to the client.


##### 5.4.3.	Organizational Unit
The organization unit within the organization that the client (application or developer) belongs to.	This assertion should be backed up with authentication via the negotiated protocol.

The purpose of this field is to allow DLT gateways to maintain access control mapping between applications and resources that are independent of the authentication and authorization schemes used,
  
supporting future changes and supporting counterparties that operate different schemes.


##### 5.4.4.	Name
A locally unique (within the OU) identifier, which can identify the application, project or individual developer responsible for this client connection.	This is the most granular unit of access control, and DLT Gateways should ensure appropriate identifiers are used for the needs of the application or use case.


##### 5.4.5.	Examples 

odapclient:quant/api.overledger.quant.com/research/luke.riley


### 5.5.	Gateway Level Access Control

Gateways can enforce access rules based on standard naming conventions using novel or existing mechanisms such as AuthZ protocols using the resource identifiers above, for example:

 - odapclient://hsbc/api.overledger.hsbc.com/lending/eric.devloper 
can READ/WRITE
 - odapres://quant/api.gateway1.com/bitcoin
AND
 - odapres://quant/api.gateway1.com/ripple

These rules would allow a client so identified to access resources directly, for example:

 - odapres://quant/api.gateway1.com/bitcoin/xxxxxADDRESSxxxxx

This example could be an client subscribing to or writing to an address associated with a smart contract as part of its functionality.

This method allows resource owners to easily grant access to individuals, groups and organizations.	Individual gateway implementations may implement access controls, including subsetting and supersetting or applications or resources according to their own requirements.




### 5.6.	Negotiation of Security Protocols and Parameters

#### 5.6.1.	TLS Established

TLS 1.2 or higher MUST be implemented to protect gateway communications.	TLS 1.3 or higher SHOULD be implemented where both gateways support TLS 1.3 or higher.


##### 5.6.2.	Client offers supported credential schemes

Capability negotiation prior to data exchange, follows a scheme similar to the Session Description Protocol [RFC 5939].	Initially the client (application) sends a JSON block containing acceptable credential schemes, e.g.	OAuth2.0, SAML in the "Credential Scheme" field of the ODAP message.

### 5.6.3.	Server selects supported credential scheme

The server (DLT Gateway) selects one acceptable credential scheme from the offered schemes, returning the selection in the "Credential Scheme" field of the ODAP message.

If no acceptable credential scheme was offered, an HTPP 511 "Network Authentication Required" error is returned in the Action/Response field of the ODAP message.

#### 5.6.4.	Client asserts of proves identity

The details of the assertion / verification step are specific to the chosen credential scheme and are out of scope of this document.


#### 5.6.5.	Sequence numbers initialized

Sequence numbers are used to allow the server to correctly order operations from the client, some of which may be asynchronous, synchronous, idempotent with duplicate requests handled in different ways according to the use case.

The initial sequence number is proposed by the client (Application) after the finalization of credential verification.	The server (DLT gateway) MUST respond with the same sequence number to indicate acceptance.

The client (application) increments the sequence number with each new request.	Sequence numbers can be reused for retries in the event of a gateway timeout.

#### 5.6.6.	Messages can now be exchanged

Handshaking is complete at this point, and the client (application) can send ODAP messages to perform actions of DLT resources, which MAY reference the ODAP Payload field.


### 5.7.	Asset Profile Negotiation
TBD

### 5.8.	Application Profile Negotiation
Where an application relies on specific extensions for operation, these can be represented in an Application Profile.

For example, a payments application tracks payments through the use of a cloud based API and will only interact with Gateways that log messages to that API, a resource profile can be established:

- Application Name: TRACKER
- X-Tracker_URL: https://api.tracker.com/updates 
- X-Tracking-Policy: Always

As Gateways implement this functionality, they support the TRACKER application profile, and the application is able to expand its reach by periodically polling for the availability of the profile.

This is an intentionally generalized extension mechanism for application or vendor specific functionality.


### 5.9 Digital Asset Resource Discovery

Applications MUST be able to discover which resources they are authorized to access to the level of individual DLTs.	They MAY be able to discover lower level resources.

Resource discovery is handled by the DLT gateway, for instance a GET request against the gateway URL with no DLT or resource could return a list of URLs available to the requester to DLT level.	This list is subject to the access controls above.

DLT Gateways MAY allow applications to discover resources they do not have access to, this should be indicated the free text field, and they should implement a process for applications to request access.
  
Formal specification of supported resource discovery methods is out of scope of this document.

### 5.10 Accessing Resources via a DLT Gateway

The Action field is used to access resources via the gateway.	We suggest these interactions use REST semantics however a detailed API specification is out of scope of this memo.

In general, we suggest exposing a common subset of functionality via API using the Action field, augmented with DLT specific or smart contract specific functionality as needed.


#### 5.10.1 CREATE

Create an object on the target DLT.


#### 5.10.2 WRITE

Write to a location on the target DLT.

#### 5.10.3. INVOKE

Invoke code on the target DLT (typically a smart contract).

#### 5.10.4.	LOCK

Lock an object on the target DLT.

#### 5.10.5.	UNLOCK

Unlock an object on the target DLT.

#### 5.10.6.	TRANSFER

Transfer an object from one DLT to another.

#### 5.10.7.	SUBSCRIBE

Subscribe to be notified of transaction affecting an object on the target DLT.

#### 5.10.8.	DESTROY

Destroy an object on the target DLT.
  
#### 5.10.9.	READ

Read an object from the target DLT.

#### 5.10.10.	NATIVE_TXN

Send a signed native transaction of any kind to the target DLT. Payload consists of the native transaction.


### 5.11.	Response Codes

The DLT Gateway MUST respond with return codes indicating the failure or success of the operation.	For DLTs with slow consensus mechanism, the Gateway may return codes indicating the operation has been submitted.	The application may carry out further operation in future to determine the ultimate status of the operation.

For Non-native transactions, the Gateway is responsible for translating the request into the appropriate native format and ensuring correct signing takes place.

### 5.12.	Backward Compatibility

It is also possible to send a fully formatted native message to the underlying DLT in the Payload field using the NATIVE_TXN operation, directed to a resource URL.	This allows existing DLT native code to be ported to ODAP infrastructures with minimal change.




## 6. Transfer Initiation Flows (Phase 1) 

This section describes ODAP initialization phase, where a sender gateway interacts with a target gateway, proposing
a session. For this, several artifacts need to be validated: asset profile, asset ownership evidence, identities, and 
logging-related operations (log profile, access control profile [ODAP-2PC]). 

In this phase, gateways implement the Transfer Initiation Flows endpoint.

In the following, the sender gateway takes instructions from a client application, while the recipient gateway may act on behalf of clients.

The flow follows a request-response model. The sender gateway makes a request (POST) to the Transfer Initiation endpoint at the recipient gateway.

Gateways MUST support the use of the HTTP GET and POST methods defined in RFC 2616 [RFC2616] for the endpoint.

Clients MAY use the HTTP GET or POST methods to send messages in this phase to the server. If using the HTTP GET method, the request parameters maybe serialized using URI Query String Serialization.

The client and server may be required to sign certain messages in order to provide standalone proof (for non-repudiation) independent of the secure channel between the client and server.	This proof maybe required for audit verifications post-event.

(NOTE: Flows occur over TLS. Nonces are not shown).

### 6.1 Initialization Request Message
This message is sent from the sender gateway to the recipient gateway.
Note that a client (application) can issue an asset transfer, that is sent to the gateway and converted into an Initialization Request Message.

The purpose of this message is for the client to initiate an asset transfer via its gateway. 
Depending on the proposal, multiple rounds of  communication between clients and gateways, and between gateways may happen.

The parameters of this message consists of the following:


- version: ODAP protocol Version (major, minor).

- developer_urn: Assertion of developer / application identity.

- resource urn: Location of Resource to be accessed.

- credential_profile: Specify type of auth (e.g.	SAML, OAuth, X.509).

- credential_block: Credential token, certificate, string
  
- asset_profile: Specify asset-related information (e.g., type).

- application_profile: Vendor or Application specific profile.

* log_profile REQUIRED: contains the profile regarding the logging procedure. Default is local store.

* access_control_profile REQUIRED: the profile regarding the confidentiality of the log entries being stored. Default is only the gateway that created the logs can access them.

* source_gateway_pubkey REQUIRED: the public key of the gateway initiating a transfer.

* source_gateway_dlt_system REQUIRED: the ID  of the source DLT.

* source_gateway_dlt_system_configuration REQUIRED: the configuration file of the source gateway DLT system.

* recipient_gateway_pubkey REQUIRED: the public key of the gateway involved in a transfer.

* recipient_gateway_dlt_system REQUIRED: the ID of the recipient gatewayinvolved in a transfer.

* recipient_gateway_dlt_system_configuration REQUIRED: the configuration file of the recipient gateway DLT system.

* transfer _profile: details if interaction is a single time transfer or multiple time transfer, and in which terms does it occur.

* history: provides an history of the escrow, in case it has previously been initialized.
This includes a list of the transactions on that escrow (transaction ID) and which action it performed (ActionCategory),
  the origin and destination, balance, current status, and ActionLockSpecificParameters.

* message_type: the message type. 

* Message signature REQUIRED: Gateway ECDSA signature over this message (HASH160)

The sender gateway makes the following HTTP request using TLS (with extra line breaks for display purposes only):

```
   POST /init HTTP/1.1
     Host: server.example.com
     Authorization: Basic awHCaGRSa3F0MzpnWDFmQmF0M2ZG
     Content-Type: application/x-www-form-urlencoded

         {
           "session_id": "1",
           "version": "1.0.0",
           "developer_urn": "odapres://quant/api.gateway1.com/sessions/:session_id/dev/xxxxxDEVIDxxxxx",
           "credential_profile": "odapres://quant/api.gateway1.com/sessions/:session_id/oauthv1.0a",
           "asset_profile": "odapres://quant/api.gateway1.com/assetprof/fungible1",
           "application_profile": "odapres://quant/api.gateway1.com/sessions/:session_id/approfile/fungible1",
         "log_profile": "odapres://quant/api.gateway1.com/sessions/:session_id/logprofile/dltBased",
         "access_control_profile": "odapres://quant/api.gateway1.com/sessions/:session_id/acp/ssibac",
         "source_gateway_pubkey": "xxxxxPUBKEYxxxxx",
         "source_gateway_dlt_system": "urn:ietf:odap:ledgers:FabricV2.3",
          "source_gateway_dlt_system_configuration": "odapres://quant/api.gateway1.com/sessions/:session_id/ledgers/xxxxxPUBKEYxxxxx",
         "recipient_gateway_pubkey": "xxxxxPUBKEYxxxxx",
         "recipient_gateway_dlt_system": "urn:ietf:odap:ledgers:BesuV20.10",
         "recipient_gateway_dlt_system_configuration": "odapres://swisscom/api.gateway1.com/ledgers/xxxxxPUBKEYxxxxx",
         "escrow_type": "urn:ietf:odap:escrowtype:escrowedcrossclaim",
         "originator_pubkey":"xxxxxPUBKEYxxxxx",
         "beneficiary_pubkey": "xxxxxPUBKEYxxxxx",
         "client_identity_pubkey":"xxxxxPUBKEYxxxxx",
         "server_identity_pubkey":"xxxxxPUBKEYxxxxx",
         "expiry_time": "xxxDATExxx",
         "multiple_claims_allowed": "true",
         "multiple_cancels_allowed": "false",
         "permissions": "odapres://quant/api.gateway1.com/sessions/:session_id/permissions/:session_id/permissionlist",
         "escrow_origin": "odapres://quant/api.gateway1.com/sessions/:session_id/escroworigin",
         "escrow_destination": "odapres://quant/api.gateway1.com/sessions/:session_id/escrowdestination",
         "subsequent_actions": "odapres://quant/api.gateway1.com/sessions/:session_id/subsequentactions",
         "history": "odapres://quant/api.gateway1.com/sessions/:session_id/history",
          "message_type": "urn:ietf:odap:msgtype:transfer-initiation-msg",
          "message_hash": "xxxHASHxxx"
         }

```

In this example, we show a hypotethical asset transfer between Quant and Swisscom.

#### 6.1.1 Detailed description of profiles

##### 6.1.2 Transfer Profile
* escrow type: faucet, timelock, hashlock, hashtimelock, multi-claim PC, destroy/burn (escrowed cross-claim).

* expiry time: Specifies the expiration time for an escrow.

* multiple claims allowed: true/false.

* multiple cancels allowed: true/false.

* permissions: list of identities (addresses/X.509 certificates) that can perform operations on the escrow.

* escrow_origin: along with the source gateway DLT, allows identifying from where are the funds escrowed/provided.

* escrow_destination: along with the recipient gateway DLT, allows identifying to where are the escrowed funds going.

* subsequent calls: details possible escrow actions.

### 6.2  Initialization Request Message Response (ACK)
After receiving an Initialization Request Message, the recipient gateway needs to validate the profiles. 
This validation could be performed automatically (using a defined set of rules), or by requiring approval by a client application. 

If one of the profiles is rejected, the recipient gateway constructs a Initialization Denied Message, stating what was rejected, and proposing an alternative (if applicable). 

Otherwise, if approved, the recipient gateway constructs a Initialization Request Message Response.
The purpose of this message is for the server to indicate agreement to proceed with the proposed operations, under the proposed profiles.
This message states that the next phase may begin.

This message is sent from the recipient gateway to the sender gateway in response to a Initialization Request from the sender gateway.

The message must be signed by the server.

The parameters of this message consists of the following:

- session_id: unique identifier (UUIDv2) representing a session (received from previous message)

- sequence_number: monotonically increasing counter that uniquely represents a message from a session.

- transfer_commence_msg_hash REQUIRED: the hash of the proposal

* received_transfer_commence_msg_timestamp REQUIRED: timestamp referring to when the Initialization Request Message was received 

* message_type REQUIRED: type of ODAP message.

* message_hash REQUIRED: hash of the message.



Example:

```
   POST /initAck HTTP/1.1
     Host: server.example.com
     Authorization: Basic awHCaGRSa3F0MzpnWDFmQmF0M2ZG
     Content-Type: application/x-www-form-urlencoded
         
{
           "session_id": "1",
           "sequence_number": "1",
           "transfer_commence_msg_hash": "xxxxHASHxxxx",
           "received_transfer_commence_msg_timestamp": "xxxxDatexxxx",
          "message_type": "urn:ietf:odap:msgtype:transfer-initiation-msg-ack",
          "message_hash": "xxxHASHxxx"
}
```



## 7. Lock-Evidence Verification Flows (Phase 2)

This section describes the conveyance of claims regarding to the status of assets or resources from a sender gateway to a recipient gateway.

In this phase, gateways implement the Lock-Evidence Agreement endpoint.

In the following, the sender gateway takes the role of the client while the recipient gateway takes the role of the server.

The flow follows a request-response model. The client makes a request (POST) to the Lock-Evidence Agreement endpoint at the server.

Gateways MUST support the use of the HTTP GET and POST methods defined in RFC 2616 [RFC2616] for the endpoint.
  
Clients MAY use the HTTP GET or POST methods to send messages in this phase to the server. If using the HTTP GET method, the request parameters maybe serialized using URI Query String Serialization.

The client and server may be required to sign certain messages in order to provide standalone proof (for non-repudiation) independent of the secure channel between the client and server.	This proof maybe required for audit verifications post-event.

(NOTE: Flows occur over TLS. Nonces are not shown).

### 7.1. Transfer Commence Message

This message is sent from the client (sender gateway) to the server (recipient gateway).	

The purpose of this message is to indicate to the server that the client has accepted the parameters agrees upon in Phase 1 and that the client ready to start the transfer of the digital asset.

It must be signed by the client.

The parameters of this message consists of the following:

- message_type REQUIRED.	MUST be the value urn:ietf:odap:msgtype:transfer-commence-msg

- originator_pubkey REQUIRED.	This is the public key of the asset owner (originator) in the origin DLT system.

- beneficiary_pubkey REQUIRED.	This is the public key of the beneficiary in the destination DLT system.

- sender_dlt_system REQUIRED.	This is the identifier of the origin DLT system behind the client.

- recipient_dlt_system REQUIRED.	This is the identifier of the destination DLT system behind the server.

- client_identity_pubkey REQUIRED. The client who sent this message.

- server_identity_pubkey REQUIRED. The server for whom this message is intended.

- hash_asset_profile REQUIRED.	This is the hash of the asset profile previously agreed upon in Phase 1 (e.g., SHA256).

- asset_unit OPTIONAL.	If applicable this is the unit amount of the asset being transferred, previously agreed upon.

- hash_prev_message REQUIRED. The has of the last message in Phase 1.

- client_transfer_number OPTIONAL.	This is the transfer identification number chosen by the client.	This number is meaningful only the client.

- client_signature REQUIRED. The digital signature of the client.

For Example:

The client makes the following HTTP request using TLS
   (with extra line breaks for display purposes only):

```
   POST /token HTTP/1.1
     Host: server.example.com
     Authorization: Basic awHCaGRSa3F0MzpnWDFmQmF0M2ZG
     Content-Type: application/x-www-form-urlencoded

    
         {
         "originator_pubkey":"zGy89097hkbfgkjvVbNH",
         "beneficiary_pubkey": "mBGHJjjuijh67yghb",
         "sender_dlt_system": "originDLTsystem",
         "recipient_dlt_system":"recipientDLTsystem",
         "client_identity_pubkey":"fgH654tgeryuryuy",
         "server_identity_pubkey":"dFgdfgdfgt43tetr535teyrfge4t54334",
         "hash_asset_profile":"nbvcwertyhgfdsertyhgf2h3v4bd3v21",
         "asset_unit": "ghytredcfvbhfr",
         "hash_prev_message":"DRvfrb654vgreDerverv654nhRbvder4",
         "client_transfer_number":"ji9876543ewdfgh",
         "client_signature":"fdw34567uyhgfer45",
         "message_type": "urn:ietf:odap:msgtype:transfer-commence-msg",
         "message_hash": "xxxHASHxxx"
}
```

### 7.2. Transfer Commence Response Message (Ack)

The purpose of this message is for the server to indicate agreement to proceed with the asset transfer.

This message is sent from the server (recipient gateway) to client (sender gateway) in response to a Transfer Commence Request from the client.

The message must be signed by the server. 

The parameters of this message consists of the following:


- client_identity_pubkey REQUIRED. The client for whom this message is intended.

- server_identity_pubkey REQUIRED. The server who sent this message.

- hash_commence_request REQUIRED. The hash of previous message.

- server_transfer_number OPTIONAL.	This is the transfer identification number chosen by the server.	This number is meaningful only to the server.

- server_signature REQUIRED. The digital signature of the server.

- message_type REQUIRED urn:ietf:odap:msgtype:transfer-commenceack-msg

- message_hash REQUIRED: hash of the message


An example successful response:

```
     HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "client_identity_pubkey":"fgH654tgeryuryuy",
       "server_identity_pubkey":"dFgdfgdfgt43tetr535teyrfge4t54334",
       "hash_commence_request":"DRvfrb654vgreDerverv654nhRbvder4",
       "server_transfer_number":"ji9876543ewdfgh",
       "server_signature":"aaw34567uyhgfer66"
       "message_type": "urn:ietf:odap:msgtype:transfer-commence-msg-ack",
       "message_hash": "xxxHASHxxx"
     }
```

### 7.3. Lock Evidence Message

The purpose of this message is for the client (sending gateway) to deliver the relevant asset-lock (or escrow) evidence to the server (recipient gateway). 

The format of the evidence is dependent on the DLT fronted by the client and is outside the scope of this specification.

The message must be signed by the client.

The parameters of this message consists of the following:


- client_identity_pubkey REQUIRED. The client who sent this message.

- server_identity_pubkey REQUIRED. The server for whom this message is intended.

- lock_evidence_claim REQUIRED. The lock or escrow evidence (on the ledger L1 fronted by the client G1).

- lock_claim_format OPTIONAL. The format of the evidence.

- lock_evidence_expiration REQUIRED. The duration of time of lock on ledger L1 (after which the lock is released).

- hash_commence_ack_request REQUIRED. The hash of previous message.

- client_transfer_number OPTIONAL.	This is the transfer identification number chosen by the client.	This number is meaningful only to the client.

- client_signature REQUIRED. The digital signature of the client.

- message_type REQUIRED urn:ietf:odap:msgtype:lock-evidence-req-msg

- message_hash REQUIRED: hash of the message

Example:

```
     HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

            {
              "client_identity_pubkey": "xxxxPUBKEYxxxx",
              "server_identity_pubkey": "xxxxPUBKEYxxxx",
              "lock_evidence_claim": "odapres://quant/api.gateway1.com/sessions/:session_id/lockevidence",
              "lock_claim_format": "odapres://quant/api.gateway1.com/sessions/:session_id/lockevidenceformat",
              "lock_evidence_expiration": "xxxxDATExxxx",
             "hash_commence_ack_request": "xxxHASHxxx",
              "client_transfer_number": "123",
              "client_signature": "xxxxSIGNxxxx",
             "message_type": "urn:ietf:odap:msgtype:lock-evidence-msg",
             "message_hash": "xxxxHASHxxxx"
            }
```

#### 7.3.1 lock_evidence_claim format for Hyperledger Fabric v2
The lock_evidence_claim (or transaction receipt) is composed of the following attributes:

* hash of the transaction

* block number

#### 7.3.2 lock_evidence_claim format for Ethereum
The lock_evidence_claim (or transaction receipt) is composed of the following attributes:

* certificate of the block creator

* signature of the block creator

* block number

* transaction id

* chaincode name and version

* client application signature 

* endorsement list 
### 7.4. Lock Evidence Response Message (Ack)

The purpose of this message is for the server (recipient gateway) to indicate accaptance of the asset-lock (or escrow) evidence delivered by the client (sending gateway) in the previous message.

The message must be signed by the server.

The parameters of this message consists of the following:


- client_identity_pubkey REQUIRED. The client for whom this message is intended.

- server_identity_pubkey REQUIRED. The server who sent this message.

- hash_lockevidence_request REQUIRED. The hash of previous message.

- server_transfer_number OPTIONAL.	This is the transfer identification number chosen by the server.	This number is meaningful only to the server.

- server_signature REQUIRED. The digital signature of the server.

- message_type REQUIRED urn:ietf:odap:msgtype:lock-evidence-ack-msg

- message_hash REQUIRED: message hash

Example:

```
     HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache
         {
           "client_identity_pubkey": "xxxxPUBKEYxxxx",
           "server_identity_pubkey": "xxxxPUBKEYxxxx",
          "hash_lockevidence_request": "xxxHASHxxx",
           "server_transfer_number": "123",
           "server_signature": "xxxxSIGNxxxx",
          "message_type": "urn:ietf:odap:msgtype:lock-evidence-msg-ack",
          "message_hash": "xxxxHASHxxxx"
         }
```

## 8.	Commitment Establishment Flows (Phase 3) 

This section describes the transfer commitment agreement between the sender gateway to a recipient gateway.

This phase must be completed within the asset-lock duration time specificied in the previous lock_evidence_expiration parameter (message 7.3).

In this phase gateways implement the Transfer Commitment endpoint.

In the following, the sender gateway takes the role of the client while the recipient gateway takes the role of the server.

The flow follows a request-response model. The client makes a request (POST) to the Transfer Commitment endpoint at the server.

Gateways MUST support the use of the HTTP GET and POST methods defined in RFC 2616 [RFC2616] for the endpoint.
  
Clients MAY use the HTTP GET or POST methods to send messages in this phase to the server. If using the HTTP GET method, the request parameters maybe serialized using URI Query String Serialization.

The client and server may be required to sign certain messages in order to provide standalone proof (for non-repudiation) independent of the secure channel between the client and server.	This proof maybe required for audit verifications post-event.

(NOTE: Flows occur over TLS. Nonces are not shown).


### 8.1 Commit Preparation Message

The purpose of this message is for the client to indicate its readiness to begin the commitment of the transfer.

The message must be signed by the client.

The parameters of this message consists of the following:

- message_type REQUIRED.	MUST be the value urn:ietf:odap:msgtype:commit-prepare-msg

- client_identity_pubkey REQUIRED. The client who sent this message.

- server_identity_pubkey REQUIRED. The server for whom this message is intended.

- hash_lockevidence_ack REQUIRED. The hash of previous message.

- client_transfer_number OPTIONAL.	This is the transfer identification number chosen by the client.	This number is meaningful only the client.

- client_signature REQUIRED. The digital signature of the client.


### 8.2 Commit Preparation Response

The purpose of this message is for the server to indicate to the client its readiness to proceed with the commitment finalization step.

The message must be signed by the server.

The parameters of this message consists of the following:

- message_type REQUIRED.	MUST be the value urn:ietf:odap:msgtype:commit-prepare-ack-msg

- client_identity_pubkey REQUIRED. The client for whom this message is intended.

- server_identity_pubkey REQUIRED. The server who sent this message.

- hash_commitprep REQUIRED. The hash of previous commit preparation message.

- server_transfer_number OPTIONAL.	This is the transfer identification number chosen by the server.	This number is meaningful only the server.

- server_signature REQUIRED. The digital signature of the server.


### 8.3 Commit Final Message

The purpose of this message is for the client to indicate to the server that the client (sender gateway) has completed local extinguishment of the asset on its DLT (L1), and that now on its part the server (recipient gateway) must re-generated the asset on its DLT (L2).	

The message must contain claims related to the extinguishment of the asset by the client. It must be signed by the client.

The parameters of this message consists of the following:

- message_type REQUIRED.	MUST be the value urn:ietf:odap:msgtype:commit-final-msg

- client_identity_pubkey REQUIRED. The client who sent this message.

- server_identity_pubkey REQUIRED. The server for whom this message is intended.

- commit_final_claim REQUIRED.	This is one or more claims signed by the client that the asset in question has been extinguished by the client in its local DLT.

- commit_final_claim_format OPTIONAL. This is the format of the claim provided by the client in this message.

- hash_commitprepare_ack REQUIRED. The hash of previous message.

- client_transfer_number OPTIONAL.	This is the transfer identification number chosen by the client.	This number is meaningful only the client.

- client_signature REQUIRED. The digital signature of the client.


### 8.4 Commit Final Response Message

The purpose of this message is for the server to indicate to the client that it has completed the asset re-generation at its DLTS (L2).

The message must contain claims related to the re-generated of the asset by the server. It must be signed by the server.

The parameters of this message consists of the following:

- message_type REQUIRED.	MUST be the value urn:ietf:odap:msgtype:commit-final-ack-msg

- client_identity_pubkey REQUIRED. The client for whom this message is intended.

- server_identity_pubkey REQUIRED. The server who sent this message.

- commit_acknowledgement_claim REQUIRED.	This is one or more claims signed by the server that the asset in question has been regenerated by the server in its local DLT.

- commit_acknowledgement_claim_format OPTIONAL. This is the format of the claim provided by the server in this message.

- hash_commitfinal REQUIRED. The hash of previous commit final message.

- server_transfer_number OPTIONAL.	This is the transfer identification number chosen by the server.	This number is meaningful only the server.

- server_signature REQUIRED. The digital signature of the server.


### 8.5 Transfer Complete Message

The purpose of this message is for the client to indicate to the server that the asset transer has been completed and that no further messages are to be expected from the client in regards to this transfer instance. 

The message closes the first message of Phase 2 (Transfer Commence Message) . It must be signed by the client.

The parameters of this message consists of the following:

- message_type REQUIRED.	MUST be the value urn:ietf:odap:msgtype:commit-transfer-complete-msg

- client_identity_pubkey REQUIRED. The client who sent this message.

- server_identity_pubkey REQUIRED. The server for whom this message is intended.

- hash_commit_final_ack REQUIRED. The hash of previous message.

- hash_transfer_commence REQUIRED. The hash of the Transfer Commence message at the start of Phase 2 (see section 7.1).

- client_transfer_number OPTIONAL.	This is the transfer identification number chosen by the client.	This number is meaningful only the client.

- client_signature REQUIRED. The digital signature of the client.

## 9 Examples
We provide examples of a Gateway to Gateway Asset Transfer by access mode. 

### 9.1 Direct Mode: Simple Client to Gateway

**Participants:**
Sender Gateway: Gateway 1 (G1)
Sender client: U1
Log storage API: Log API

**Access Mode:** Direct Mode
**Crash Recovery Mechanism:** Self Healing. 
**Logging model:** decentralized log storage L or local log accessible by G1. 
**Crash Recovery Strategy:** upon crashing, a gateway waits until it is restarted. Upon restart, it checks its state against the one saved in the decentralized storage or local log. If there are differences, the recovered gateway updates its state.

**Assumptions:**
Each gateway has an API of each Type (Type-1, Type-2).
Each gateway has a user connecting to it via API Type-2.

A client-initiated gateway transfer happens as follows:

1. U1 sends a request to G1 to issue a transaction against a DLT.
2. G1 verifies U1's identity and pre-prepares the transaction. A confirmation is sent to the user.
3. U1 confirms the transaction and sends it to G1.
4. G1 logs the decision and issues the transaction.
5. G1 returns a response to the client.

### 9.2  Direct Mode: Client to Multiple Gateway

**Participants:**
Sender Gateways: Gateway 1...n (G1, G2, ..., GN)
Sender client: U1
Log storage API: Log API

**Access Mode:** Direct Mode: Client to Multiple Gateway
**Crash Recovery Mechanism:** Self Healing. 
**Logging model:** decentralized log storage L shared by all gateways. 
**Crash Recovery Strategy:** upon crashing, a gateway waits until it is restarted. Upon restart, it checks its state against the one saved in the decentralized storage. If there are differences, the recovered gateway updates its state.

**Assumptions:**
Each gateway has an API of each Type (Type-1, Type-2).
Each gateway has a user connecting to it via API Type-2.

A client-initiated gateway transfer using multiple gateways happens as follows:

1. U1 sends a request to a set of gateways to issue a set of transactions against a set of DLT.
2. Each verifies U1's identity and pre-prepares the transaction. A confirmation is sent to the user.
3. U1 confirms the transaction and sends it to the respective gateway. In case atomic transactions are needed, some synchronization is needed between gateways. The client handles this.
4. Each gateway logs the decision and issues the transaction.
5. Each gateway returns a response to the client.



### 9.3 Client-initiated Gateway to Gateway 

**Participants:**
Sender Gateway: Gateway 1 (G1)
Recipient Gateway: Gateway 2 (G2)
Sender client: U1
Recipient client: U2
Log storage API: Log API

**Access Mode:** Client-initiated Gateway to Gateway 
**Crash Recovery Mechanism:** Self Healing. 
**Logging model:** decentralized log storage L shared by G1 and G2. 
**Crash Recovery Strategy:** upon crashing, a gateway waits until it is restarted. Upon restart, it checks its state against the one saved in the decentralized storage. If there are differences, the recovered gateway updates its state.

**Assumptions:**
Each gateway has an API of each Type (Type-1, Type-2, Type-3).
Each gateway has a user connecting to it via API Type-2 (U1 and U2 for G1 and G2, respectively).

A client-initiated gateway to gateway asset transfers happens as follows:

1. U1 communicates with GA via API Type-2 requesting for a transfer to U2. 
2. Validation of input occurs (e.g., that U1 has enough amount X of asset Y to be transferred). A confirmation of the transfer is asked to the client.
3. The client confirms. Otherwise, the transaction is aborted.
  

4. G1 asks to initiate an ODAP session with G2 via API Type-1. Provides several validation information (e.g., identity).
5. G2 accepts to take part in the ODAP session. Otherwise, the session is canceled.



5. G1 and G2 start the transfer initiation flow phase.
6. Checks are done from G2 via API Type-3
7. G2 acknowledges that it can continue the session.
7. G1 asks G2 to start the Lock-Evidence Verification Flow
8. G2 accepts to take part in the Lock-Evidence Verification Flow. Otherwise, the session is canceled.

9. G1 locks X amount of asset Y that is to be transferred on behalf of U1 in the Lock-Evidence Verification Flow. The proof is generated.
10. G1 sends the proof to G2.
11. G2 logs and validates the proof received by G1 (if possible).
12. G2 acknowledges this to G1
13. G1 initiates the Commitment Establishment Flow
14. G2 commits to the transfer in the Commitment Establishment Flow. (Optionally, G2 verifies the lock at G1 and creates a representation of the asset on the recipient DLT)

15. G1 and G2 finalize the transfer. 

16. G2 communicates the result to U2.


     ,--.                                     ,--.                               ,--.          ,--.                       
     |U1|                                     |G1|                               |U2|          |G2|                       
     `--'                                     `--'                               `--'          `--'                       
      | [1]: transfer X amount of asset Y to U2|                                  |             |                         
      | --------------------------------------->                                  |             |                         
      |                                        |                                  |             |                         
      |      [2]: transfer is ok. Proceed?     |                                  |             |                         
      | <---------------------------------------                                  |             |                         
      |                                        |                                  |             |                         
      |                 [3]: ok                |                                  |             |                         
      | --------------------------------------->                                  |             |                         
      |                                        |                                  |             |                         
      |                                        |            [4] initiate ODAP session           |                         
      |                                        | ----------------------------------------------->                         
      |                                        |                                  |             |                         
      |                                        |                     [5] ok       |             |                         
      |                                        | <-----------------------------------------------                         
      |                                        |                                  |             |                         
      |                                        |        [6] start transfer initation flow       |                         
      |                                        | ----------------------------------------------->                         
      |                                        |                                  |             |                         
      |                                        |                                  |             |----.                    
      |                                        |                                  |             |    | [7] validity checks
      |                                        |                                  |             |<---'                    
      |                                        |                                  |             |                         
      |                                        |                     [8] ok       |             |                         
      |                                        | <-----------------------------------------------                         
      |                                        |                                  |             |                         
      |                                        |            [9] start lock-evidence             |                         
      |                                        |             verification flow    |             |                         
      |                                        | ----------------------------------------------->                         
      |                                        |                                  |             |                         
      |                                        |                     [10] ok      |             |                         
      |                                        | <-----------------------------------------------                         
      |                                        |                                  |             |                         
      |                                        |----.                                           |                         
      |                                        |    | [11] lock X amount of asset Y             |                         
      |                                        |<---'                                           |                         
      |                                        |                                  |             |                         
      |                                        |                 [12] send proof  |             |                         
      |                                        | ----------------------------------------------->                         
      |                                        |                                  |             |                         
      |                                        |                                  |             |----.                    
      |                                        |                                  |             |    | [13] store proof.  
      |                                        |                                  |             |<---'  validate proof    
      |                                        |                                  |             |                         
      |                                        |                                  |             |                         
      |                                        |                [14] transfer ok. |             |                         
      |                                        | <-----------------------------------------------                         
      |                                        |                                  |             |                         
      |                                        |            [15] initiate commitment            |                         
      |                                        |             establishment flow   |             |                         
      |                                        | ----------------------------------------------->                         
      |                                        |                                  |             |                         
      |                                        |                     [16] ok      |             |                         
      |                                        | <-----------------------------------------------                         
      |                                        |                                  |             |                         
      |                                        |             [17] transfer finalized            |                         
      |                                        | ----------------------------------------------->                         
      |                                        |                                  |             |                         
      |                                        |                     [18] ok      |             |                         
      |                                        | <-----------------------------------------------                         
     ,--.                                     ,--.                               ,--.          ,--.                       
     |U1|                                     |G1|                               |U2|          |G2|                       
     `--'                                     `--'                               `--'          `--'                       

  
## 10.	Security Consideration

Although the current interoperability architecture for blockchain gateways assumes the externalization of the value of assets, as a blockchain system holds an increasing number of virtual assets it becomes attractive to attackers seeking to obtain cryptographic keys of its nodes and its end-users.

Gateway nodes are of particular interest to attackers because they enable the transferal of virtual assets to external blockchain systems, which may or may not be regulated.	As such, hardening technologies and tamper-resistant crypto-processors (e.g.	TPM, SGX) should be used for implementations of gateways [HS19].

Due to the consensus-based nature of the underlying DLT technologies, gateway responses may be conditional and require verification, for instance if the DLT is undergoing a byzantine attack at the time of the request.

The application must evaluate the correctness of responses from the gateway in context and may need to perform further verification steps with later ODAP calls.	The application may base this evaluation on the number of DLT nodes the gateway has interacted with in order to fulfil the request.



## 11.	IANA Consideration (TBD)


## 12.	References

### 12.1.	Normative References
  
[RFC2119]	Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119,
DOI 10.17487/RFC2119, March 1997,
<https://www.rfc-editor.org/info/rfc2119>.

[RFC2234]	Crocker, D., Ed. and P. Overell, "Augmented BNF for Syntax Specifications: ABNF", RFC 2234, DOI 10.17487/RFC2234,
November 1997, <https://www.rfc-editor.org/info/rfc2234>.

[RFC7519]	Jones, M., Bradley, J., and N. Sakimura, "JSON Web Token (JWT)", RFC 7519, DOI 10.17487/RFC7519, May 2015,
<https://www.rfc-editor.org/info/rfc7519>.

### 11.2.	Informative References

[Arch]	Hardjono, T., Hargreaves, M., and N. Smith, "An Interoperability Architecture for Blockchain Gateways. draft-hardjono-blockchain-interop-arch-01", October 2020,
<https://www.ietf.org/archive/id/draft-hardjono- blockchain-interop-arch-01.txt>.

[HS2019]	Hardjono, T. and N. Smith, "Decentralized Trusted Computing Base for Blockchain Infrastructure Security, Frontiers Journal, Sepcial Issue on Blockchain Technology, Vol. 2, No. 24", December 2019,
<https://doi.org/10.3389/fbloc.2019.00024>.

[NIST]	Yaga, D., Mell, P., Roby, N., and K. Scarfone, "NIST Blockchain Technology Overview (NISTR-8202)", October 2018, <https://doi.org/10.6028/NIST.IR.8202>.

[ODAP-2PC] Belchior, R., Correia, M., & Hardjono, T. (2021). Gateway Crash Recovery Mechanism draft v1. https://datatracker.ietf.org/doc/draft-belchior-gateway-recovery/

[RFC5939]	Andreasen, F., "Session Description Protocol (SDP) Capability Negotiation", RFC 5939, DOI 10.17487/RFC5939, September 2010, <https://www.rfc-editor.org/info/rfc5939>.


