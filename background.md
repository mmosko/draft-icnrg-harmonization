# Background

## CCNx 0.8: Origin and Point of Commonality

... Common point from which the two development streams emerged circa 2013 / CCNx 0.8 Reference Implementation ...

CCNx 0.8 as a common starting point

- Binary XML format

- Packet Naming 

    - Full name : "/foo/bar" + implicit digest
    - Exact name : "/foo/bar", 0 components after
    - Prefix name : "/foo/*", 0 or more components afterwards

- Initial set of naming convention to carry semantics of the name component contents

- Data retrieval
    - Data fetching using full, exact, and prefix names

- In-network name discovery

    * with Selectors support
    * data packet carrying “FreshnessSecond”, a relative time the packet is considered “Fresh”

- Opportunistic in-network caching

    * Each data packet can be cached with forwarded-defined policies
    * “Fresh”/”stale” semantics for the cached data

- Aggregation of similar Interests, allowing similar interests to pass through when close to lifetime expiration

- Nonce in Interest packets to detect and prevent Interest packet looping

## Recognized Issues with CCNx 0.8

## Summary of NDN and CCNx 1.0 Evolution

