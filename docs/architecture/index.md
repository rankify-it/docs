# Smart contract infrastructure

The Rankify smart contract infrastructure is built upon a modular design, emphasizing trustless operations to facilitate progressive decentralization within organizations and to mitigate some of the roadblocks on wider adoption of decentralized governance.

Adhering to systems theory principles, the architecture can be understood as a black box, responding to stimuli inputs with defined output reactions, while its key feature is the support for recursive patterns, enabling iterative processes on both input and output levels.

Including governing body, infrastructure as consisting of two main pieces:

-   **App Store**: Registry of different ranking system applications that may be installed on cells
-   **Cell infrastructures**: Dimensions for ranking that participants produce.

![high-level-infrastructure](../assets/high-level-architecture.png)

!!! note

    The rankify community governing DAO and multi-sig in the image above can be seen as an instance of _Cell_ infrastructure.

## Cells

A single cell is an autonomous governance unit, structured as an NxM matrix of inputs and outputs connected to a multisig-wallet. Each cell, representing a specific topic or workgroup, leverages a modular and customizable infrastructure consisting of:

-   **Rank NFT**: A semi-fungible ERC1155 token signifying the bearer's rank within the subject.
-   **Governance Token**: An ERC20 token used for governance, minted by exchanging Rank NFTs.
-   **DAO contract**: [OSx DAO Contract](https://devs.aragon.org/docs/osx/reference-guide/core/dao/) governed entirely by holders of the governance token.
-   **App Instance**: Ranking app, implements the autonomous competence identification protocol.
-   **Multisig Wallet**: A wallet adhering to the Gnosis [ISafe](https://github.com/safe-global/safe-smart-account/blob/main/contracts/interfaces/ISafe.sol), connecting governing bodies and facilitating decision-making.

![cell level infrastructure](../assets/cell-inrastructure.png)

Protocol is modular and trends to support already existing multi-sig infrastructure as well as experimentation hence protocol deployed, established, and modified cells, that are referred as _Primordial_, _Generated_, and _Mutated_ accordingly.

Above diagram describes infrastructure _Generated_ cells adhere, while diagram involving governing body (green blocks) on top of this page describes _primordial_ cell, which yet has no Rank NFT asset to complete it, as well as it is not listed in any auto-generation registries.

<!-- - **Primordial**: Existing multisig wallets incorporating logic of protocol without having support for immutable head
- **Complete**: Cells deployed in strict compliance with protocol
- **Mutated**: Cells that are intentionally modifying rankify protocol for sake of research and further development
  Initial cell input, also referred as "_head_" -->

## Distribution system

The core concept of scaling Rankify is to give ability for organizations to easily spawn whole pieces of new infrastructure, such as new cells, or new applications that form organizations unique offerings to others in the network.

Thus we develop a distribution system with goal to provide as generic and permission less approach as possible with yet a security guarantees between different organizations, that are translated trough curating own application infrastructure. Application infrastructure consist of:

-   **Repository**: Provides single address contracts to have versioned source control.
-   **Installer**: Target specific interface that consumes distributions.

App developer either deploys plain source or sets up a repository for his work and deploys his source to that to provide version control interface. Then he can propose any organization to add his repository to their Installer Plugin to have his application available for installation.
If it succeeds, organization members now can create their own instances of applications that all will share same source code and version.

<!-- -   **Distributor**: Combines sources with installation instruction into distribution. -->

<!-- ![app store infra](../assets/Appfra.png){: style="height:auto;width:500px"} -->

```puml
@startuml
!pragma teoz true
actor "App Developer" as AD
participant "Repository" as R
participant "Organization" as O
participant "Installer Plugin" as IP
actor "Users" as U
AD -> R: add version
AD --> O: Propose
activate O
note right: DAO votes
O -> O: Add Repository?
O -> IP: addSource
deactivate O
U -> IP: newInstall
activate IP
IP -> IP: check
note left: Check requested source & version are OK
IP -> R: get source
R -> IP: src
&  IP -> instance ** : create
instance -> IP: address
IP -> U: Ok
deactivate IP
activate instance
U --> instance: interact
activate IP
instance -> IP: DAO action
IP -> IP: check
note left: Check instance source & version are OK
IP -> O: exec()
@enduml
```

<!-- ### Factory

The App Factory serves as a source for app installations, guaranteeing deterministic logic source in deployed applications. This enables the creation of interconnected ecosystems where trust is built on a shared understanding of app functionality. -->

<!-- ![App factory](../assets/PluginFactory.png) -->

Anyone can create their own repository, and become distributor, however Rankify DAO curates its own distribution contract, listing its own resources and community-approved third-party applications.
[IVRepoFactory](https://github.com/rankify-it/contracts/blob/23-v09-factory-specifications/src/interfaces/IVRepoFactory.sol) is provided for convenience of creating new repositories.

### Repositories

The App Repositories are built upon the [IRepository](https://github.com/rankify-it/contracts/blob/23-v09-factory-specifications/src/interfaces/IRepository.sol) interface, which mirrors the functionality of the [OSx IPluginRepo](https://github.com/aragon/osx/blob/develop/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol). It provides a versioned repository for source non-permissive control implementations.
Users of that repository can be sure that every time they request specific application version, same address will be returned and keep track of newest releases.

!!! Note

    The developer source code does not need to be aware of the repository structure, already existing contracts may be added to the repository.

<!-- ## Registry

Unlike immutable Repositories, the Registry is a mutable source of trust, allowing external applications to verify attestations from Rankify on [ENS](https://attest.org/). While developers can deploy their own Repository contracts freely, the DAO curates it's attestations, listing its own resources and community-approved third-party applications. -->

<!-- ### Distributions registry

!!! warning "v2 feature"

    This feature is still TBD, it's being discussed for future release. Please reach if you have an opinion on this feature.

Distributor is a contract that combines sources with installation instructions into a distribution. It must implement [IDistributor]() interface, and it's only interface dependency is [Repository](index.md#repositories) contract.
DAO curated distributions registry is also able to act as source of trust, showing which applications are approved by the community. -->

### Installer

Installer is an `abstract` integration contract that takes distributions and installs them into target organization. It must implement [IInstaller]() interface, and it's only interface dependency is [Repository](index.md#repositories) contract. It is an abstract that must be extended to meet specific target interface requirements.

<!-- To maximize flexibility for both users and developers, we anticipate that applications installed within organizations will often require internal encapsulation.

![installer plugin](../assets/InstallerPlugin.png){: style="height:auto;width:500px"}

In order to accommodate for that, Installer provides a way to instantiate multiple instances of the same application, each with its own unique configuration. This allows for a more modular and flexible system, where applications can be tailored to the specific needs of each organization. -->

!!! Note

     Application might have time-zone specific properties, making it necessary to have multiple instances of the same application, each with its own unique configuration.

!!! Warning

    Installer main use case assumes ability to instantiate multiple instances, it does not track permissions to install applications nor it enforces their count. Such limits can be introduced in the instantiation logic `InstallationPlan` requirements that are approved by DAO.

#### OSx Installer

A concrete installer version, "OSx Installer" plugin is provided to support integration with [Aragon OSx](https://aragon.org/aragonosx) framework. This Installer must be authorized by the DAO, maintains a list of approved distributors as well it is the only system component that must be aware of installation target specific interfaces. It simplifies the process for users to launch whitelisted applications, acting as a proxy between instances and the DAO contract as well as generalizes apps development.

<!-- ## Security

We aim to provide clear and encapsulated separation between functional components and security measures. This allows for a more modular and flexible system, where security can be tailored to the specific needs of each component without affecting functional requirements.

Defense-in-depth onion approach is used as described in [Smart contract layers](https://github.com/peersky/smart-contract-layers/tree/main), each secured functional component is encapsulated in a proxy that implements security measures.

![security is an onion!](../assets/security-onion.png){: style="height:auto;width:500px"}

Each call to any functional element, no matter is a view or non-view, is routed through a proxy that implements security measures. It receives full call data including sender, signature, call data and values, and can decide to allow or deny the call based on the security requirements. -->

<!-- ```puml
@startuml
scale 600 width
!theme carbon-gray
actor "User" as U
actor "Auditor" as A
participant "Factory" as F
participant "Audited Contract" as AC
participant "Bridge" as B
actor "Bridge Operator" as BO

A -> F: Register Auditor Signing Key
A -> U: sig(bytecode,calldata)
U -> F: deploy(bytecode,calldata,sig)
activate F
F <-> F: verify key
F -> AC: deploy
activate AC
F <- AC: address
deactivate AC
BO -> B: Register Trusted App Factory
F <-> F: register address
U <- F: address
deactivate F
U -> B: Register token with audit
activate B
B -> F: verify address (eip-1271)
activate F
F -> B: response
deactivate F
B <-> B: Activate token
B -> U: Response
deactivate B
U -> B: Bridge token -->

<!-- ``` -->

<!-- note left of U: This is a note. -->
