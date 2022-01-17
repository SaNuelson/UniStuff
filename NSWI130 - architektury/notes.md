Software Architectures
===

### Software architecture

- is an abstraction, a way to deal with complex systems in an orderly manner
- a set of architectural software structures, relations between them and properties of both structures and relations
- 3 types of architectural structures are:
  1. Modules
    - **"parts of the system"**
    - each module has some computational responsibilities
    - module offers some functionality (either to the user or other modules)
    - by modularizing the system, modules can then be assigned to programming teams to be actually implemented, tested and integrated
    - they are **static structures** (as opposed to for example "modes" which can change dynamically during runtime)
  2. Components
    - **"parts of the system in runtime"**
    - structures which focus on how software elements interact with each other in runtime
    - they are made up of compiled programs from various modules
  3. Allocations
    - **"how modules and components are split to environment"**
    - they can be **organizational**, **developmental** (which teams work on which modules...), **installational** (where and how to install individual parts of the system's software...), and **executional** (how to start the software, what's the workflow...).

### IEEE 1471 - Conceptual Framework

- a standard for software architecture terminology
- it takes into account all sizes and natures of SW systems.
  - **System**
    - the central component of a software system (obviously)
  - **Mission** 
    - the goals the system aims to accomplish (create an easy-to-use social network, enable users to post pictures, like them and comment on them...)
  - **Environment** 
    - real-life entities connected to the system in any way (organization, legislations, customers, partners...)
    - it both affects the system (system needs to conform to laws, customers need to be satisfied...), and is affected by the system itself (if some parts of the system are automated, workers in that department are no longer needed...)
  - **Stakeholder**
    - Individuals and/or groups which have some intrest in the system (users, managers, admins, owners, service providers, testers, designers...)
    - these influence many aspects of the system (affordability, ease of use, security...)
    - often these intrests are in conflict with one another (affordability vs. pretty much anything else)
  - **Concern**
    - Requirements and wishes of the stakeholders as well as constraints created by the environment (laws, hardware limits)
    - they affect key characteristics of the system (usability, testability, reliability, security, performance...)
  - **Architecture**
    - should be documented in the form of **Architectural description**
    - it identifies stakeholders, their concerns, environment and its restrictions
    - it tries to address these by shaping the system

### Architectural description
- it is **complete** iff it addresses all **identified** concerns of the software system (as it's fairly hard to address non-identified ones).
- it is organized into **multiple architectural views**

### View

- **"represents the architecture from the perspective of a related set of concerns"**
- it reduces complexity and makes the architecture manageable
- each view is relative to an **architectural viewpoint**.

### Viewpoint
- ****
- it is a set of types of elements and relationships to describe views, along with rules on how to use and present them.
- viewpoints are essentially a "rulebook" saying which views are best for which purposes.

### Model

- **""**