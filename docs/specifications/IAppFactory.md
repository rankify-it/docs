# App factory



### Requirements

- Must allow anyone
### Interface

```solidity
interface IFactoryAssetsManager {
  function deployAsset(bytes32 assetUri, bytes32 assetType, bytes calldata instantiationPayload) external;

  function deployAssetManager(address sAddr, bytes32 strategyId, bytes calldata instantiationPayload) external;

  function isAssetManager(address maybeManager) external view returns (bool);

  function isManagedAsset(address maybeAsset) external view returns (bool);

  function getAsset(address manager) external view returns (address);

  function getAssetType(address asset) external view returns (bytes32);

  function getAssetUri(address asset) external view returns (bytes32);

  event AssetDeployed(address indexed asset, bytes32 indexed assetUri, bytes32 indexed assetType);
  event AssetManagerDeployed(address indexed asset, address indexed manager, bytes32 indexed strategyId);
}
```

## Rationale

**Combining asset factory with manager instance factory** - It could be possible to isolate functionality in two different contracts. Such design would imply that rank token factory may be called by either owner or game instance factory. Hence they are sharing same security level, and since factory deploys instances already tied to a particular rank token, they would become decoupled from token factory in case if token factory was swapped. Same can be achieved by deploying one factory as diamond contract and using Instance & Token deployments factory
