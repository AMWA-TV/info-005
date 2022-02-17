# NMOS Controller Implementation Guide

## Scope
This document is intended as a guide for implementers or users of Controllers within NMOS-enabled networked media systems. The document defines what a Controller is and outlines the requirements of a Controller with respect to each existing NMOS specification through references to the relevant sections of those documents.

The document focuses primarily on guidance for the core NMOS specifications of IS-04, IS-05 and BCP-003-01. However, this is a living document and it is intended that fuller guidance for other NMOS specifications be added in future.

## Use of Normative Language
This document is a guide to Controllers only and not a specification itself. It provides informational guidance around the normative requirements of the relevant specifications. If there are any inconsistencies between this guide and the specifications then, unequivocally, the specification is correct. Any normative language key words found in this document are not to be interpreted as RFC 2119 key words.

## Controller Definition
A Controller is Client software that interacts with the NMOS APIs to discover, connect and manage devices within a networked media system. The diagram below shows some of those API interactions with other NMOS system components. 

![Role of NMOS Controller in a Networked Media System](./images/NMOS_Controller.svg)

The EBU have also produced a useful diagram showing the role of Controllers within a wider context (link to be added).

This guide will cover only how the Controller interacts with the NMOS APIs. It will not include other aspects such as presentation or other features of the Client software.

Where a Controller is additionally acting as an Node (e.g. receiving monitoring information via IS-07), this guide will not cover any requirements relating to this.

This guide and the associated NMOS specifications often refer to the "user" of a Controller. This includes both human operators who drive the Controller manually and automation systems that drive the Controller programmatically.

## Controller Interactions
The sections below describe how Controllers interact with the NMOS suite of Interface Specifications (IS) and Best Current Practice (BCP) recommendations, with references to the relevant parts of these specifications where the normative language can be found. They also include some additional informative guidance to implementers of Controllers.

### Notes on Understanding the AMWA NMOS Specifications
All of the specifications referenced below can be accessed from the [AMWA NMOS](https://specs.amwa.tv/nmos/) website.

As well as reading the text of the specifications, a fuller understanding of the described API can be achieved by paying particular attention to the API section (for instance, the [IS-04 Query API](https://specs.amwa.tv/is-04/releases/v1.3.1/APIs/QueryAPI.html)).

APIs can be investigated by clicking on the HTTP methods for each endpoint listed (click [here](https://specs.amwa.tv/is-04/releases/v1.3.1/APIs/QueryAPI.html#senders_get) for an example).
This will open a window with a description of the Request and Response for that HTTP method.

These, along with the provided JSON schemas and examples, often contain canonical definitions not described in the supporting text.

Similarly be aware of the [NMOS Parameter Registers](https://specs.amwa.tv/nmos-parameter-registers/) as this contains constant definitions used by many of the specifications.

### IS-04 Discovery and Registration
> *Controllers use the **IS-04 Query API** to discover and obtain updates on the NMOS resources that are available on the network.*

The IS-04 specification describes the mechanism for the discovery and registration of NMOS resources within a media network. It comprises three APIs: the Registration API, the Query API and the Node API. Only the Query API has relevance to Controllers, but a brief summary of each API is given below for context.

The **IS-04 Registration API** is exposed by the Registry. When a Node first joins the network, it uses [DNS-SD](https://specs.amwa.tv/info-004/) to discover the URL of the Registration API. It then uses the Registration API to register information about itself and all its sub-resources (Devices, Senders, Receivers, Sources and Flows). Each Node then communicates with the Registration API with a regular heatbeat message to let the Registry know that it is still on the network. Nodes also use the Registration API to update the information about themselves in the Registry whenever something changes. 

The **IS-04 Query API** is also exposed by the Registry. Controllers discover the URL of the Query API using DNS-SD and then obtain an up-to-date list of all registered Nodes and sub-resources (Devices, Senders, Receivers, Sources and Flows) so that they can indicate to the user the currently available resources on the network. Controllers also use the Query API to set up websockets subscriptions to resources, so that they obtain updates every time something changes in the Registry.

The **IS-04 Node API** is exposed by Nodes. It is used by other Nodes to discover information about a Node directly when there is no Registry available on the network (in peer-to-peer mode).

The IS-04 specification describes the requirements for Controllers in the document entitled [Controllers](https://github.com/AMWA-TV/is-04/blob/nmos-controller/docs/Controllers.md) (link to be updated after IS-04 PR accepted).

### IS-05 Device Connection Management
> *Controllers use the **IS-05 Connection API** to make connections between Senders and Receivers.*

Nodes on a media network may contain one or more Devices, each of which may include any number of Senders and Receivers. The IS-05 specification describes the mechanism for making connections between Senders and Receivers.

The **IS-05 Connection API** is exposed by NMOS Devices. Controllers discover the URL of the Connection API through the list of `controls` for the Device. This is part of the information that will have been registered to the IS-04 Registry by the Device's parent Node. It is available to the Controller by querying the Registry using the IS-04 Query API and examining the information returned about the Device in question. 

Controllers make connections between Senders and Receivers by making calls to the Connection APIs of their parent Devices. A Controller first calls the Connection API for the Sender to update its transport parameters as required and then obtain its transport file. It then calls the Connection API for the Receiver to provide it with the transport file and complete the connection.

The IS-05 specification describes the requirements for Controllers in the document entitled [Controllers](https://github.com/jonathan-r-thorpe/is-05/blob/jonathan-r-thorpe-nmos-controller/docs/Controllers.md) (link to be updated after IS-04 PR accepted).

### IS-07 Event and Tally
> *Controllers are able to identify **IS-07** Senders and Receivers in the **IS-04** Registry and make connections between them using **IS-05**.*

### IS-08 Audio Channel Mapping
> *Controllers use the **IS-08 Channel Mapping API** to indicate the current mapping of audio channel inputs to outputs within a device and allow a user of the Controller to modify these.*

### IS-09 System Parameters
> *Controllers use the **IS-09 System API** to obtain information about the location of system logging servers to which they are able to post their logs.*

### IS-10 Authorization
> *Controllers use the **IS-10 Authorization API** to enable them to make authorized calls to all other APIs.*

### BCP-002-01 Natural Grouping
> *Controllers use the **BCP-002-01 Natural Grouping** best current practice recommendations to allow them to understand associations between logical groups of NMOS Senders or Receivers.*

### BCP-003-01 Secure Communications in NMOS Systems
> *Controllers use the **BCP-003-01 Secure Communications in NMOS Systems** best current practice recommendations to protect the API calls that they make with transport layer security.*

### BCP-003-02 Authorization in NMOS Systems
> *Controllers use the **BCP-003-02 Authorization in NMOS Systems** best current practice recommendations to ensure they meet the authorization requirements of the endpoints to which they are making API calls.*

### BCP-003-03 Certificate Provisioning in NMOS Systems
> *Controllers use the **BCP-003-03 Certificate Provisioning in NMOS Systems** best current practice recommendations to obtain and refresh Client certificates for **BCP-003-01** transport layer security.*

### BCP-004-01 Receiver Capabilities
> *Controllers use the **BCP-004-01 Receiver Capabilities** best current practice recommendations to determine whether a Receiver is capable of receiving a Flow from a Sender.*