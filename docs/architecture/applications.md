# Applications

## Overview

While application distribution system can, and is encouraged, to be used for pretty much anything, Rankify main goal is to provide necessary infrastructure for [Autonomous Competence identification (ACID) protocol](https://www.rankify.it/research/paper/) installation.

In our implementation the asset is being decoupled from governing body to allow for more flexible and dynamic governance and to encourage experimentation without sacrificing for governance security. The representation of comptence is done via [Rank NFTs](index.md#rank-nft).

Hence it is required that applications are able to mint and burn [Rank NFTs](index.md#rank-nft) assets, they can do this by using installation proxy as their mint and burn target. When called, the proxy will mint or burn the asset on behalf of the application.

<!-- TODO: FIX LINK FOR RANK NFTS -->

This approach allows applications to be versioned, prevents outdated instances from being used.

!!! tip

    You are welcome to write your own application, if it meets community standards, it can be included in the Rankify ecosystem, or used to create your own DAO ecosystem autonomously!

## Best list challenge

The best list challenge is a simple example of an application that can be built on top of the Rankify infrastructure. It allows users to submit their ideas and vote on them to create a ranked list of ideas.
Protocol is designed in accordance to autonomous competence identification protocol: participation is anonymous, time continuous and results are verifiable.
