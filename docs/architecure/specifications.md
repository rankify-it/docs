# Specifications

This document outlines the detailed specifications for our project. Each section in this document corresponds to a different component of the system and is further divided into two categories: Functional Requirements and Security Requirements.

-   **Functional Requirements**: define the functionality that a system or system component must be able to perform. They describe what the system should do and include specifications for features, capabilities, and operational constraints.

-   **Security Requirements**: define the measures that are needed to protect the system from potential threats. They often involve access control, data protection, and system integrity. Security requirements are given per implementation given the context of the implementations as these requirements are not universal.

<!-- ## App Store -->

<!-- ### Repository

### Functional

- Must implement [IVFactory.sol](https://github.com/rankify-it/contracts/blob/23-v09-factory-specifications/src/interfaces/IVRepoFactory.sol)
- It allows only Rankify Multisig to access non-read interface methods
- It must emit `createRepo` when non-read interface methods are called
- Calling non-read interface methods must return address to newly created `IRepository`

### Security

- Factory implementation owed by Rankify DAO
- It must allow multiple -->

## Functional requirements

### Repositories

<!-- ### Functional -->

Functional interface requirements are described in [IRepository](https://github.com/rankify-it/contracts/blob/23-v09-factory-specifications/src/interfaces/IRepository.sol), functionality must fulfill following unit tests:

-   It emits `VersionCreated` when `createVersion` is called successfully
-   It reverts with `InvalidReleaseIncrement` if a release number is incremented by more than one
-   It reverts with `ReleaseZeroNotAllowed` if a release number is set to zero in `createVersion`
-   When no release nor version exists:
    -   It reverts with `VersionHashDoesNotExist`if `getVersion` is called
    -   It reverts with `ReleaseDoesNotExist` if `updateReleaseMetadata` is called
    -   It returns zero if `latestRelease` is called on repository with no releases
    -   It returns zero if `buildCount` is called on repository with no releases
    -   It reverts with `VersionHashDoesNotExist` if called `getLatestVersion(address)`
    -   It reverts with `ReleaseDoesNotExist` if called `getLatestVersion(uint8)`
-   When version and release exists:
    -   It reverts with `AlreadyInPreviousRelease` if `createVersion` if the same source address
    -   It returns correct release count for given major version upon calling `buildCount`
    -   It returns correct `Version` if `getVersion` is called with valid major and minor version
    -   It returns correct `Version` if `getLatestVersion(address)` is called
    -   It returns correct `Version` if `getLatestVersion(uint8)` is called
    -   It emits `ReleaseMetadataUpdated` when `updateReleaseMetadata` is called successfully

### Registry [TBD]

### Cells [TBD]

#### Rank NFT

-   Must implement [OpenSea compatible uri format](https://docs.opensea.io/docs/metadata-standards) that can resolve to title, Description and image
-   Must allow only owner to modify metadata
-   May implement additional [IRankToken](https://github.com/rankify-it/contracts/blob/main/src/interfaces/IRankToken.sol) interface

### Governance Token [TBD]

### App Instances

#### Installer

installer must implement [IRepoInstaller](https://github.com/rankify-it/contracts/blob/23-v09-factory-specifications/src/interfaces/IRepoInstaller.sol) interface, functionality must fulfill following unit tests:

-   it emits `RepositoryAdded` if `addRepository` is called successfully.
-   it emits `Instantiated` if `instantiate` is called successfully.
-   it reverts with `RepositoryDoesNotExist` if `instantiate` is called with non-existing repository.
-   it reverts with `VersionDoesNotMatchRequirement` if `instantiate` version does not match.
-   it emits `Repositor`Removed`when`removeRepository` is called successfully.
-   it emits `Instantiated` if `instantiateLatest` is called successfully.
-   it reverts with `RepositoryDoesNotExist` if `instantiateLatest` is called with non-existing repository.
-   when repository already exists:
    -   it emits `RepositoryRequirementUpdated` if `addRepository` is called successfully with already existing repository.
-   when `RepositoryRequirementUpdated` was emitted:
    -   it reverts with `VersionDoesNotMatchRequirement` if instance version does not match requirement anymore.
    -   it emits `Upgraded` if `upgrade` is called successfully.
    -   it reverts with `VersionDoesNotMatchRequirement` if `upgrade` is called with version that does not match the required version.
-   It returns correct `Repository` if `getRepository` is called with valid instance address
-   It returns all instances of a repository if `getInstances` is called with valid repository address
-   It returns all active repositories if `getRepositories` is called
-   It returns version tag if `getVersion` is called with valid instance address
-   It returns `VersionRequirement` if `getVersionRequirement` is called with valid instance address
-   It reverts with `InstanceDoesNotExist` if `getVersionRequirement` is called with non-existing instance address
-   It reverts with `InstanceDoesNotExist` if `getVersion` is called with non-existing instance address
-   It reverts with `RepositoryDoesNotExist` if `getRepository` is called with non-existing instance address
-   It reverts with `RepositoryDoesNotExist` if `getInstances` is called with non-existing repository address

### Instance [TBD]

## Security requirements

Security specifications outlined per contract instance are implementation agnostic. They are meant to be implemented in a way that is most suitable for the given context. Following definitions extend [ERC-2119](https://www.ietf.org/rfc/rfc2119.html) definitions:

-   **Access point**: A function or method that is part of the protected contract's interface.
-   **WHITELIST**: Set of addresses that are explicitly allowed on certain access points.
-   **BLACKLIST**: Set addresses that are explicitly disallowed on certain access points.
-   **RESTRICTED**: An access point that MUST enforce WHITELIST.
-   **UNRESTRICTED**: An access point that MAY enforce BLACKLIST.

  <!-- For every contract that is part of our infrastructure a modifier must be provided that can take full call data that includes `msg.data`, `msg.sender`, `msg.value` and `msg.sig`. This modifier must be used to implement security requirements by calling external security contract.

The modifier must reside on contract that manages state -->

### DAO Owned Repository

DAO controlled functional [Repository](specifications.md#functional-requirements) must fulfill following security requirements:

-   All non-read methods are RESTRICTED, where only WHITELIST member is [Rankify Multisig](specifications.md#rankify-multisig)
-   [Rankify Multisig](specifications.md#rankify-multisig) MAY assign WHITELIST members to each access point individually
-   [Rankify Multisig](specifications.md#rankify-multisig) MAY revoke WHITELIST members from each access point individually
-   All read methods are UNRESTRICTED, with no BLACKLIST members
-   No one may modify security settings except [Rankify Multisig](specifications.md#rankify-multisig)
-   [Rankify Multisig](specifications.md#rankify-multisig) MAY transfer it's privileges to another account

### Rankify Multisig

Multisig controlled functional [Safe Wallet](https://github.com/safe-global/safe-smart-account/blob/main/contracts/interfaces/ISafe.sol)

-   Initially deployed with a single signer and 1-of-1, Rankify DAO signer
-   Rankify DAO signer MAY add or remove signers
-   Rankify DAO signer MAY change the threshold
-   Rankify DAO signer MAY revoke it's own signer status

### App Instances

#### Installer [TBD]
