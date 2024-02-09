---
title: Software Quality
author: John Doe
category: Compulsory Courses
layout: post
---

Quality in software development. Software measurement and metrics. Clean Code & SOLID principles. Code testing; unit tests, integration tests, user and acceptance testing. Performance engineering and testing. Development methods based on testing. Software quality management process. (PV260)

## Quality in Software Development
Quality Criteria:
- **Reliability** – The probability of correct/failure-free system operation.
- **Availability** – The degree to which a system is fully operational, i.e. up and running.
- **Security** – The ability of a system to prevent unauthorized access and protect the confidentiality, integrity and availability of data.
- **Safety** – The ability of a system to operate without the danger of causing serious harm (e.g. human injury).
- **Robustness** – Degree to which a system is able to withstand an unexpected event without quality degradation.
- **Resilience** – The ability of a system to recover quickly after a disaster

## Software Measurement and Metrics
Typical problems related to software measurement:
- How can I measure the maintainability of my software?
- Can I estimate the number of defects of my software?
- What is the productivity of my development team?
- Can I measure the quality of my testing process?

A **measure** is a mapping between:
- The real world
- The mathematical or formal world with its objects and relations

Different mappings give different views of the world depending on the context (height, weight, ...). The mapping relates attributes to mathematical objects; it does not relate entities to mathematical objects.

### Measures
**Code coverage** is a measure giving an indication of how much of the source code has been run ("covered") by running the tests.
- Statement coverage: the % of statements of the program covered by the tests
- Function coverage: the % of functions/methods covered by the tests
- Branch coverage: the % of branches of the control structures covered by the tests
- Condition coverage: % of each Boolean condition evaluated both as True/False

Martin Fowler: *Test coverage is a useful tool for finding untested parts of a codebase. Test coverage is of little use as a numeric statement of how good your tests are.*

### Measurement Scales

| Scale Type | Examples                         | Indicators of Central Tendency                |
|:-----------|:---------------------------------|:----------------------------------------------|
| Nominal    | Name of a programming language   | mode                                          |
| Ordinal    | Ranking of failures              | mode, median                                  |
| Interval   | Beginning, end date of activites | mode, median, arithmetic mean                 |
| Ratio      | LOC                              | mode, median, arithmetic mean, geometric mean |

*[LOC]: Lines of Code

### Direct / Indirect Measures
- **Direct** (*base*): from a direct process of measuring
  - number of known defects
- **Indirect** (*derived*): from a mathematical equation in the world of symbols
  - defects density (DD)

    $\text{DD} = \frac{\text{known defects}}{\text{product size}}$
  - COCOMO: measure of effort

*[COCOMO]: Constructive Cost Model

### Internal / External Attributes
- **Internal**: internal characteristics of product, process, and human resources
  - easier to collect: measures of length and complexity of the code
- **External**: due to external environment
  - measures of code quality

Mapping of internal attributes to external ones is not straightforward.

### Objective / Subjective Measures
- **Objective**: the same each time they are taken (LOC)
- **Subjective**: manually collected by individuals (time to use a functionality in an application)

### Software Metrics
#### Size
  - LOC: lines of code
  - CLOC: commented lines of code
  - NLOC: non-commented lines of code
  - NOC: number of classes
  - NOM: number of methods
  - NOP: number of packages

Size is used for normalization of existing measures: for example to compute comments density. It can give a rough initial estimation of effort, but measures of source code size should **never** be used to assess the productivity of developers.

#### Complexity
**Cyclomatic Complexity** (CC) represents the number of independent control flow paths.

Let $G = (N, E)$ be a graph representation of the control flow of a program, where $N$ is a set of nodes, $E$ a set of edges, and $P$ a number of connected components of $G$.

$$v(G) = |E| - |N| + 2P$$

#### Object Oriented Metrics
Chidamber & Kemerer suite:
- **WMC** (Weighted methods per class): sum of the complexities of methods of a class:

    $\text{WMC} = \sum_{i-1}^n c_i$, where $c_i$ is complexity of a method in the class
- **DIT** (Depth of Inheritance Tree): max inheritance level from the root to the class
- **NOC** (Number of Children): number of direct descendants of a class
- **CBO** (Coupling between object classes)
  - class $A$ is *coupled* with $B$ if $A$ is using methods/attributes of $B$
  - high CBO is undesirable: excessive coupling prevents reuse
- **RFC** (Response for a Class): the number of methods of a class that can be invoked in response of a call to a method of a class

    $\text{RFC} = |M_c \cup M_e|$
  - $M_c$: number of methods that can be executed by a class $A$ responding to a message
  - $M_e$: sum of all external calls to other methods
- **LCOM** (Lack of cohesion in methods): how closely the local methods are related to the local instance variables in the class, we use a negative measure - the lack of cohesion

## Clean Code & SOLID Principles
1. Name things right
  - reveal intent, write self-documenting code
2. Balance code structure
  - no God classes, long methods and other bad code smells

### Naming
- Proper class names - name them after their purpose, do not use generic names
- Methods returning boolean - call them `hasX` or `isX`
- Getters retrieve internal state, setters modify internal state
  - and should not modify anything else
  - setters should have no return value
- No boolean parameters

  ~~~ javascript
  user.setAdminStatus(false);
  user.setAdminStatus(true);
  ~~~
  vs.
  ~~~ javascript
  user.revokeAdminRights();
  user.grantAdminRights();
  ~~~
- Classes are nouns, methods start with verbs
- Use consistent terminology for actions
  - different actions need distinguishable names (`list.add` and `list.append` are not distinguishable)
- **Short** and precise **public API**, **long private** method names to communicate their intent and behaviour
- Lots of small descriptive methods
- Variables: descriptive function parameters
  - **big** scope = **long** name, **short** scope = **short** name
- Comments: should not be needed with self-documenting code

### 1. Single Responsibility Principle
> There should never be more than one reason for a class to change.
{: .block-warning}

- The more **responsibilities**, the more **dependencies**.
- The more **dependencies**, the higher risk of **change propagation**.
- The bigger **change propagation**, the higher **risk of error**.

Following SRP leads to **lower coupling** and **higher cohesion**. Many small classes with distinct responsibilities result in a more flexible design.

### 2. Open / Closed Principle
> Classes should be open to extension, and closed to modification.
>- **Open to Extension**: New behavior can be added in the future
>- **Closed to Modification**: Changes to source or binary code are not required
{: .block-warning}

We want to avoid introducing changes that cascade through many modules in our application.
Writing new classes is less likely to introduce problems.

Conformance to OCP yields flexibility, reusability, and maintainability. Know which changes to guard against, and resist premature abstraction.

### 3. Liskov Substitution Principle
> Subtypes must be substitutes for their base types (Barbara Liskov).
{: .block-warning}

#### Substitutability
- Child classes must not:
  - remove base class behaviour
  - violate base class invariants
- Must not require calling code to know they are different from their base type.
- To follow LSP, derived classes must not violate any constraints defined on the base classes.
- Child class should satisfy the *is-substitutable-for* relation, not *is-a* relation.

Non-substitutable code breaks polymorphism - client code expects child classes to work in place of their base classes.
Trying to fix the problems by adding `if`-`then` statements checking the type violates OCP.

LSP violation:
~~~java
foreach (var emp in Employees) {
  if(emp is Manager) {
    printer.PrintManager(emp as Manager);
  }
  else {
    printer.PrintEmployee(emp);
  }
}
~~~

#### Refactoring
- Create a third class that both classes can derive from.
- Ensure substitutability is retained between each class and the new base.

### 4. Interface Segregation Principle
> Clients should not be forced to depend on methods they do not use.
{: .block-warning}

Prefer small cohesive interfaces to "fat" interfaces.

#### The Problem
- Client references a class but uses only small portion of it.
- Interface segregation violations result in classes that depend on things they do not need, increasing coupling and reducing flexibility and maintainability.

- Keep interface small, cohesive, and focused.
- Whenever possible, let the client define the interface.
- Whenever possible, package the interface with the client.

### 5. Dependency Inversion
> High-level modules should **not depend** on low-level modules. Both should **depend on abstractions**.
{: .block-warning}

Traditionally, high level modules call low level modules. Class instantiation is scattered through all modules - violation of single responsibility principle.

#### The Problem
- Dependencies flow toward infrastructure
- Core/Business/Domain classes depend on implementation details
- Result
  - Tight coupling
  - No way to change implemenetation details without recompiling
  - Difficult to test

Class constructors should require any dependecies the class needs. Classes whose constructors make this clear have **explicit** dependencies. Classes that do not have **implicit** (hidden) dependencies.

#### Summary
- Depend on abstractions.
- Don't force high-level modules to depend on low-level modules through direct instantiation or static method calls.
- Declare class dependencies explicitly in their constructors.
- **Dependency injection**: Inject dependencies via constructor, property, or parameter injection.

## Code Testing
Testing is the **process** of **exercising or evaluating** a system or system component by manual or automated means to verify that it **satisfies specified requirements**.

- **Failure**:
  - (A) Termination of the ability of a product to perform a required function or its inability to perform within previously specified limits.
  - (B) An event in which a system or system component does not perform a required function within specified limits.
  - A failure may be produced when a fault is encountered.
- **Fault**: a manifestation of an error in software
- **Defect**: an imperfection or deficiency in a work product where that work product does not meet its requirements or specifications and needs to be either repaired or replaced.
- **Error**: a human action that produces an incorrect result.

### Principles of Testing
- **Sensitivity**: better to fail every time than sometimes
  - a test selection criterion works better if every selected test provides the same result
  - if the program fails with one of the selected tests, it fails with all of them (reliable criteria)
- **Redundancy**: making intentions explicit
  - Redundant checks can increase the capabilities of catching specific faults early or more efficiently.
  - Static type checking is redundant with respect to dynamic type checking, but it can reveal many type mismatches earlier and more efficiently.
  - Validation of requirement specifications is redundant with respect to validation of the final software, but can reveal errors earlier and more efficiently.
  - Testing and proof of properties are redundant, but are often used together to increase confidence
- **Restrictions**: making the problem easier
- **Partition**: divide and conquer
- **Visibility**: making information accessible
- **Feedback**: applying lessons from experience in process and techniques

### Testing Levels
- **Acceptance testing:** A system is tested for acceptability. Aim: evaluate the system’s compliance with the **business requirements** and ready for delivery.
- **System testing:** A complete, integrated system/software is tested. Aim: evaluate the system’s compliance with the **specified requirements**.
- **Integration testing:** Individual units are combined and tested as a group. Aim: expose faults in the **interaction between integrated units**.
- **Unit testing:** A level of the software testing process where individual units/components of a software/system are tested – Validate that **each unit** performs as designed

### Unit Tests
A level of the software testing process where individual units/components of a software/system are tested – Validate that **each unit** performs as designed.

Tests must
- be fast
- be simple
- not include duplication of implementation logic
- be readable
- be deterministic
- be part of the build process
- use mocks (test doubles)
- have consistent naming conventions

#### AAA Pattern
1. **Arrange:** set up the conditions for the test
2. **Act:** run the code under test
3. **Assert:** verify the behaviour

#### Test Double
Test double is a replacement for a dependent component or a module that is used in a unit test.

- **Dummy objects:** items passed around but never used
- **Fake objects:** have working implementations but are not suitable for production use (e.g., an in-memory database)
- **Stubs:** provide constrained answers to calls made during the test, not responding to anything outside of the tests
- **Spies:** stubs that also record information based on how they were called (e.g., a stub e-mail service that logs number of e-mails sent)
- **Mocks:** objects pre-programmed with expectations which form a specification of the calls they are expected

### Integration Tests
Individual units are combined and tested as a group. Aim: expose faults in the **interaction between integrated units**.

The goal is to test "whether many separately developed modules work together as expected".

- Integration tests use external dependencies.
- Integration Tests verify several modules at once.
- Slower and more complex than Unit tests.

### User and Acceptance Testing
Acceptance tests ensure that a software system meets the **requirements** from the **customer**.

The customer can write what he expects from the implementation. Frameworks: FitNesse, Selenium

## Performance Engineering and Testing
Load/Stress Testing: verify the system under heavy loads

- **Load Testing**: evaluating system while exposed to the expected volume of traffic under peak conditions
  - load = number of concurrent users accessing the application, amount of transactions completed over a set time
- **Stress Testing**: assessing the upper limits of the system by simulating amount of traffic beyond the expected peak levels
  - provides info about maximum capabilities, scalability, breaking points
- **Soak Testing**: system exposed to a constant, average level of stress for an extended period of time, while also simulating periodic traffic peaks
  - assess memory leaks, performance degradation after long continuous use

## Development Methods Based on Testing
### Test Driven Development (TDD)
Steps:
1. Create a failing test
2. Code it to make it pass
3. Refactor other code and tests

Tests have to be:
- **fast**: short time to run
- **independent**: not depend on other tests, components, database, etc.
- **repeatable**: deterministic
- **self-checking**: a test must be able to check its own state
- **timely**: test must come before the implementation

### Behaviour Driven Development (TDD)
With the customer, develop the **requirements** as **scenarios**.
1. Developers use the **scenarios** for implementation.
2. Testers use the **scenarios** for testing.

Run tests based on scenarios according to **Given**, **When**, **Then** constructs.

Example:
> **Scenario**: When a user adds a product to the shopping cart, the product should be
included in the user's shopping cart. \
  **Given** a user, \
  **Given** a shopping cart, \
  **Given** a product, \
  **When** the user adds the product to the shopping cart, \
  **Then** the product must be included in the list of the shopping cart's entries.
{: .block-warning}

## Software Quality Management Process
> Software quality management (SQM) is the collection of all processes that ensure that software products, services, and life cycle process implementations meet organizational software quality objectives and achieve stakeholder satisfaction. (SWEBOK 3.0)
{: .block-warning}

SQM defines processes, process owners, requirements for the processes, measurements of the processes and their outputs, and feedback channels throughout the whole software life cycle.

### SQM Categories
#### 1. Software quality planning (SQP)
Concerned about which **quality standards** are to be used, defining specific **quality goals**, and **estimating** the effort and schedule of software quality activities.

A quality plan defines how an organization will reach the quality objectives.
Usually covers:
- quality objectives and goals
- quality management scope
- organisation & responsibilities
- resource requirements
- cost benefit analysis
- activites and deliberables
- schedule
- risk analysis

#### 2. Software quality assurance (SQA)
**Define and assess** the adequacy of software processes to provide evidence that **establishes confidence** that the software processes are appropriate for and produce software products of **suitable quality** for their **intended purpose**.

SQA means to **constantly monitor** the software engineering process to ensure that the approaches/methods/processes applied **lead to quality** within the project.

SQA with Agile:
- the product backlog is the contract, the standards help in defining the role of the backlog as contract
- non-conformances to standards are inserted in the backlog and addressed in sprints in which they are scheduler
- acceptance is a continuous process

#### 3. Software quality control (SQC)
Activities examine specific project artifacts (documents and executables) to determine whether they **comply with standards established** for the project (including requirements, constraints, designs, contracts, and plans).

SQC means to **constantly monitor** the software engineering process/product to **check for conformance to applied standards** or produced artifacts.

Examples of methods:
- **Goal Questions Metrics** approach: define a *goal* with respect to quality, use a set of *questions* to characterize the way of the assessment of a specific goak, associate a *set of data* with every question in order to answer it in a quantitative way
- **Plan-Do-Check-Act** method: plan a change, test the change, review the test, take action based on the review - if the change did not work, use a different plan; if it was successful, incorporate it into wider changes

#### 4. Software process improvement (SPI)
The activities in the category seek to **improve process effectiveness**, efficiency, and other characteristics with the ultimate goal of **improving software quality**.

Software process improvement practices are very often embedded within the process (e.g. capability models).

Methods:
- Capability Maturity Model, Capability Maturity Model Integration
  - Level 0: **Incomplete Process** - the process is not implemented or fails to achieve the purpose
  - Level 1: **Performed Process** - the process is achieving the purpose
  - Level 2: **Managed Process** - the process is now running in a managed way (planned, monitored, adjusted) – work products are established, controlled and maintained
  - Level 3: **Established Process** - the process is implemented using a defined process capable of achieving process outcomes
  - Level 4: **Predictable Process** - the process now operates within defined limits to achieve its process outcomes
  - Level 5: **Optimizing Process** - the predictable process is continuously improved to meet relevant current and projected business goals
- ISO/IEC 15504 (SPICE)
- ISO 9001
- Personal Software Process

## Other Sources
- [https://hackmd.io/@rsss-statnice2022/BkZiek-Yq](https://hackmd.io/@rsss-statnice2022/BkZiek-Yq){:target="_blank"}