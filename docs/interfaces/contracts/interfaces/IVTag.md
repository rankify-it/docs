
# 
### public struct Tag

The struct describing the tag of a version obtained by a release and build number as `RELEASE.BUILD`.

| Input | Type | Description |
|:----- | ---- | ----------- |

*Releases mark incompatible changes (e.g., the plugin interface, storage layout, or incompatible behavior) whereas builds mark compatible changes (e.g., patches and compatible feature additions).*

```solidity
struct Tag {
  uint8 release;
  uint16 build;
}
```

# 
### public struct Version

The struct describing a plugin version (release and build).

| Input | Type | Description |
|:----- | ---- | ----------- |

```solidity
struct Version {
  struct Tag tag;
  address source;
  bytes buildMetadata;
}
```

