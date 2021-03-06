
<img src="images/IoTCrawler_Logo.png" width="200" height="34" />

* [IoTCrawler Framework](#iotcrawler-framework)
  * [MetaData Repository](#metadata-repository)
  * [Indexing](#indexing)
  * [Ranking](#ranking)
  * [Orchestrator](#orchestrator)
  * [Search Enabler](#search-enabler)
  * [Authorization Enabler](#authorization-enabler)
    * [XACML PAP PDP](#xacml-pap-pdp)
    * [Capability Manager](#capability-manager)
    * [PEP-PROXY](#pep-proxy)
    * [Security Facade](#security-facade)
    * [Keyrock](#keyrock)
  * [Semantic Enrichment](#semantic-enrichment)
    * [Pattern Extractor](#pattern-extractor)
* [Documentation](#documentation)
* [Contributors](#contributors)
* [License](#license)

# IoTCrawler Framework
IoTCrawler is a research project funded under the European Union's Horizon 2020 research and innovation program. The aim of the project is to develop a search engine for the Internet of Things (IoT), enabling search on it’s devices. IoTCrawler focuses on integration and interoperability across different platforms, dynamic and reconfigurable solutions for discovery and integration of data and services from legacy and new systems, adaptive, privacy-aware and secure algorithms and mechanisms for crawling, indexing, and search in distributed IoT systems. The project spans industry, universities and cities. It is comporised of multiple components:
## [MetaData Repository](https://github.com/IoTCrawler/ScorpioBroker)
The core component of the architecture of IoTCrawler is what we call MetaData Repository, which can be seen as a Context Broker with the features of distributing information following both query-response and publication-subscription patters among the other components of the IoTCrawler architecture. This Context Broker must be capable of representing semantic, linked data and property graphs. These features has been included in NGSI-LD, a new standard which has been conducted under the ETSI ISG CIM initiative. For this reason, we have selected this technology for the instantiation of our MetaData Repository. 

Scorpio is an NGSI-LD compliant context broker developed by NEC Laboratories Europe and NEC Technologies India. This project is part of [FIWARE](https://www.fiware.org/). For more information check the FIWARE Catalogue entry for [Core Context](https://github.com/Fiware/catalogue/tree/master/core). We have selected this broker for the instantiation of our MetaData Repository because of their features related to the representation of distribution and federation scenarios.

## [Indexing](https://github.com/IoTCrawler/Indexing)
The Indexing component  provides a means for clients to search for IoT entities efficiently. It focuses on IoT streams and sensors, where queries can be based on sensor type and absolute or relative location. To initiate the process of indexing, a platform manager needs to register a metadata broker (MDR) with the Indexing component. In turn this will trigger the subscription to sensors and streams at the registered MDR. As metadata descriptions are updated at the MDR, the Indexing component will be notified, and will then index the sensors and streams based on location. For scalability, the Indexing component can be configured so that the persistence it relies on (MongoDB) can be sharded. With respect to other components in the framework, the Ranking component relies on Indexing for generating the ranking of search results.
## [Ranking](https://github.com/IoTCrawler/Ranking)
The Ranking component facilitates ranking mechanism for IoT resources. Ranking and resource selection rely on the registry built (and constantly updated) by crawling and indexing methods. The purpose of Ranking is to aid users and applications to not only find a set of resources relevant to their needs, but also to select the best or most appropriate one(s) from that set. There are multiple criterias for ranking IoT resources such as data type, proximity, latency, availability. The Ranking component supports application-dependent,multi-criteria ranking.
## [Orchestrator](https://github.com/IoTCrawler/Orchestrator)
The orchestrator component is responsible for interactions with client IoT applications. It allows applications to subscribe to streams without having a public endpoint as well as tracks subscription requests. In case of stream failure, orchestrator is able to notify application and provide a list of alternative streams for subscription.

## [Search Enabler](https://github.com/IoTCrawler/Search-Enabler)
The GraphQL-based search enabler component is considered as a main search-component of IoTCrawler. It employs a query language (GraphQL) and a query processor, which works on top of NGSI-LD-compliant component (the ranking Component or MDR). The  component eliminates the lack of expressivity and functional capabilities which prevent NGSI-LD from being the main search interface the large-scale IoT metadata deployments gathered in the IoTCrawler platform. The search enablerb fills the gap between low-level sensors and high-level domain semantics about sensors data and deals with the context-dependent entities by maintaining the context in the IoTCrawler platform. 
## Authorization Enabler
The purpose of this enabler is twofold: on the one hand for a secure communication between the IoTCrawler components, and on the other, for controlling the access of the registered users to the resources stored in the IoTCrawler platform. It instantiates the Distributed Capability-Based Access Control (DCapBAC) technology by using both an XACML framework, and a Capability Manager. This latter is responsible for issuing authorisations tokens which must be present in each of the requests aimed at the MetaData Repository.

### [XACML PAP PDP](https://github.com/IoTCrawler/XACML_PAP_PDP)
This element corresponds to the implementation of the XACML framework. It comprises a Policy Administration Point (PAP) which is responsible for managing the authorisation policies, and a Policy Decision Point (PDP), responsible for issuing possitive/negativer verdicts whenever an authorisation request is received. 
The PAP presents a GUI for managing the XACML policies. They must be defined according to a triplet (subject, resource, action).

### [Capability Manager](https://github.com/IoTCrawler/Capability-Manager)
This component is the contact point for services and users that intend to access the resources stored in our IoTCrawler platform. It provides a REST API for receiving authorisation queries, which are tailored and forwarded to the XACML PDP for a verdict. When a positive verdict is received, the Capability manager issues an authorisation token called Capability Token which is a signed JSON document which contains all the required information for the authorisation, such as the resource to be accessed, the action to be performed, and also a time interval during the Capability Token is valid.

### [PEP-PROXY](https://github.com/IoTCrawler/PEP-Proxy)
Since the DCapBAC technology decouples the authorisation in two phases: one for issuing an authorisation token, and a second one when the authorisation token is validated, we need the figure of a Policy Enforcement Poing (PEP). This is the purpose of the PEP-Proxy. It is responsible for receiving the queries aimed at the MetaData Repository that must be accompanied by the corresponding Capability Token. The PEP-Proxy validates this token, and in case the evaluation is positive, it forwards the messages to the MetaData Repository, as well as the responses back to the requester.

### [Security Facade](https://github.com/IoTCrawler/Security-Facade)
This component has been designed as an endpoint for performing both authentication and authorisation operations in an transparent way for the requester. By receiving the corresponding request it interacts with both Identity Manager and Authorisation Enabler, and in case the requester owns the appropriate permissions for the request, it sends the authorsation token (Capability Token) back to the requester.
### [Keyrock](https://github.com/IoTCrawler/Keyrock)
Keyrock is the FIWARE component responsible for Identity Management. Using Keyrock (in conjunction with other security components such as PEP Proxy and Authzforce) enables you to add OAuth2-based authentication and authorization security to your services and applications.

## [Semantic Enrichment](https://github.com/IoTCrawler/SemanticEnrichment)
The Semantic Enrichment (SE) component is responsible for annotating data streams with Quality of Information (QoI). To calculate the QoI the SE subscribes to the MDR for changes in IoTStreams. When receiveing notifications for a stream it takes the related metadata of the stream and generates the QoI annotation, which is stored in the MDR afterwards to be accessible by other components of the framework.

### [Pattern Extractor](https://github.com/IoTCrawler/Pattern-Extractor)
As part of the Semantic Enrichment component, the Pattern Extraction module enables the generation of higher-level context by analysing the annotated IoT data streams retrieved from IoT data sources that are pushed to the Metadata Repository. The Pattern Extractor uses the ```iot-stream:StreamObservations``` to detect higher-level events, which are then published to the broker. A machine learning method is used to analyse a group of ```sosa:observesProperties```. This analysis model is registered as a new subscription in the broker. This subscription includes spatial and temporal and the data type specifications for data streams. Once a stream matches the specifications of a registered process, the pattern extractor then caches the observations that are necessary to perform the analysis. When the observations are received for each stream in the group, the pattern extractor constructs a time window to analyse the data. The result of the analysis is a label for a pattern of data. This label, together with the start and end times of the pattern, is then represented as an ```iot-stream:Event``` NGSI-LD entity and published into the broker. The broker then makes these patterns searchable.
# Documentation
Please find further information about the architecture, getting started and tutorials on our online documentattion.
* [latest](https://iotcrawler.readthedocs.io/en/latest/introduction.html)
# Contributors
* **Universidad de Murcia (Spain)** is contributing with a research group with expertise in security in network infrastructure and the integration of intelligent techniques and agents based middleware platforms.
* **University of Surrey (United Kingdom)** will contribute to the development of crawling, indexing, search and ranking mechanisms in IoTCrawler.
* **University of Applied Sciences Osnabrück (Germany)**‘s expertise in quality analysis and testing will contribute to the framework and define the requirements for sensor modelling, monitoring and fault recovery and the design of Data attributes and QoI metrics.
* **Aarhus University (Denmark)** will be contributing to the business model innovation and general business development of the IoTCrawler showcases and technologies.
* **Siemens AG Österreich (Austria)** will contribute to the development of semantic discovery mechanisms. Based on these technologies, SIEMENS will realise a use case in the context of smart energy systems.
* **NEC Corporation (Germany)** will be mainly involved in establishing trust in IoT devices by leveraging trusted execution environments. NEC will also contribute in securing the IoT platform by leveraging its in-depth experience with the blockchain technology and policy compliance checking.
* **AGT Group (R&D) GmbH (Germany)** will contribute to the definition of requirements, to the architecture, to the development of crawling and discovery algorithms and implementations, and the design, implementation and evaluation of use case scenarios and exploitation in its product portfolio.
* **Digital worx GmbH (Germany)** focuses on the development of software interfaces, user interaction and software development for mobile applications and applications in the Internet of Things (IoT). They will take the lead on the overall technical coordination.
* **Odin Solutions S.L (Spain)** will contribute to the security and privacy solution for sensors and smart objects in the project based on their experience on security and privacy framework for constrained devices in scenarios like smart cities and industry 4.0.
* **City of Aarhus (Denmark)** will, as the only city in the project, works as a test bed for the IoTCrawler and the use cases developed. They will help define the requirements for the use cases through co-creation workshops with citizens, city stakeholders and other relevant actors in this field.

# License
The information here is made available under the Apache License, Version 2.0 (Apache-2.0), located in the [LICENSE](LICENSE) file. The source code files of different components of IoTCrawler are provided under EUPL-1.2, Apache-2.0 and MIT licenses. You will find more information in respective component repository.
