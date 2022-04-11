Stable API Tools
==============

Stable API Tools aims to make it easier for developers to create and use API contracts that change over time. 

This project provides tools that will help developers automate many of the most difficult and tedious
parts of maintaining a stable API contract. These include:  

- Check api contract changes for backwards compatibility
- Recommend a convention for managing stable api contract schemas
- Generate server and client implementation code stubs for common application frameworks
- Reverse-engineer an api contract from existing web frameworks source code

[Project notes](docs/project.md) describes how to contribute and the plan
to implement these features. 

This project is Copyright (c) by Jonathan Hess <jonathan.hess@gmail.com> and is 
licensed under the Apache 2.0 license. See [LICENSE](LICENSE) and [NOTICE](NOTICE).

## Introduction 

Compatibility between a service and its clients has been a hard problem from the moment there was
more than software program in the world. It is simply very difficult to make sure that one program understands
the output of a second program, especially over the long-term when each program must change to meet 
new business needs.  

Since the era of The Web, this problem grew to the size of the web. People and companies publish REST services
on the internet for others to use. These services are used by clients beyond the control of the
team who built the service. The services are expected to be "always on," meaning no 
scheduled downtime is allowed.

At the same time, the team who creates the service is expected to constantly upgrade their service, 
building new features and fixing bugs. And do that without requiring downtime from their clients. 
And do that without requiring clients to constantly upgrade their code to match the new API contract.

Many strategies have been tried. This set of tools hopes to provide an opinionated way to handle compatibility
of a REST API contract over many generations of changes.  

Software must change over time to accommodate changes in the environment around it: business requirements, 
infrastructure changes, bug fixes, scale changes, etc. When the behavior of a service changes, this often
changes the API contract that the service provides to its clients. Thus, when the service API contract
changes, then the client software that use the service must also change in response.

### Stable and Unstable API Contracts

A **Stable API Contract** is a software contract guarantees to its clients that even as the service and its
API contract changes over time, the service will continue to support older API contracts so that
clients can upgrade in a reasonable time period with a reasonable amount of development effort. This way the service
may upgrade its implementation and API contract without risk of breaking existing clients. 

An **Unstable API Contract** is a contract that makes no such guarantee. Any time the service implementation changes,
the service's api contract may change in ways that break clients. Clients must synchronize their changes with the 
service contract change or risk breaking.

## Safe software upgrades

Safe software upgrades are critical to change a dynamic system as the software improves.  

### Unstable API Contract

When there is an unstable API contract, the best way to handle changes to the implementation of a client and service
is by synchronizing the upgrade of the client and server code. 
The server and clients always use the same API contract. 
When the api contract changes, both server and client must be upgraded simultaneously. 
If the service is an online system, that means that you must take a downtime while the service
and all clients are upgraded simultaneously. 

### Stable API Implementation Strategy 1: Never Break the Existing API

The simplest way to accomplish the goal of a stable API contract is to
prohibit any changes to the API contract that are incompatible with any
previously published contract.

To accomplish this:

- Use this tool to make sure there are no breaking structural changes to
  the API schema definition.
- Use unit and integration tests to check that changes to application
  logic don't change the contract of the previous revisions of the
  service.

As the service grows and changes over time, this approach will drag on
the development team. It will become harder to make changes that never
break the existing API.

### Stable API Implementation Strategy 2: Service supports multiple API versions

Business requirements will change. In response the service
will need to introduce new features and deprecate parts of the API
contract. Some of those changes will not be backwards compatible.

However, the service must be run in a way that preserves all existing
API contracts. Somehow, the service will need to support two or more
separate revisions of the api contract. The service will need to support
the many published contracts until all clients use the newer revisions,
and the older revisions are finally decommissioned.

Here are two implementation approaches for the service code:

1.  **Multi-version services:** Keep a single branch of the service code
    in production. Implement all versions of the API inside the same
    service codebase.
2.  **Forked Code:** Fork the service code. Implement the new contract
    on a new branch, while maintaining the old contract on the old
    branch. Deploy both old and new versions to production. Use an api
    gateway to send traffic to the correct service.

Experience has demonstrated that multi-version services is dramatically
less effort to maintain than forked code in large projects. This is
especially true when:

- Many teams are contributing API changes
- More than 2 api versions must be supported
- Old api versions must be supported in production for more than 1 day.
- The service maintains state.
- The service executes long-running business logic processes.

Forked Code can be less development effort. It works well when the
cut-over period is less than a day, and the old and new services can run
side-by-side.

To accomplish the multi-version service:

- Define your multi-version API schema in OpenAPI following this tool's
  conventions.
- Use this tool to generate Rest Controller stub interfaces for your
  service.
- Use this tool to make sure there are no breaking structural changes to
  the API schema definition.
- Use unit tests to make sure the business logic served by API contracts
  is preserved in old and new API contract revisions.

