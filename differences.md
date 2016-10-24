# Discussion of Individual Architecture & Design Commonalities and Differences per NDN and CCNx 1.0 Development paths

... below we take in succession individual topics that capture the points where the differences between the NDN and CCNx 1.0 approaches are relevant and important to discuss.  The topics listed below are taken from our prior discussions and are included for completeness only. We can reorganize, add or delete items as we see fit. The structure of each section will be the same as suggested in 4.1...

<!-- ################################################################### -->

## Packet encoding

Use of TLV encodings

### NDN

.. Details and motivation...

### 	CCNx 1.0

... Details and motivation ...

<!-- ################################################################### -->

## Packet structure (network adaptation, link adaptation, information layers)

### NDN
... Details and motivation ...
### CCNx 1.0
... Details and motivation ...

<!-- ################################################################### -->

## Naming

NDN and CCNx 1.0 has the same definitions of naming and extended it

+ Explicitly typed components

// definition of Data digest

In CCNx 1.0, full name combines of "Name" and "digest" but is not logically tied together

<!-- ################################################################### -->

## Data retrieval

### NDN

Data can be retrieved by full, exact, and prefix name.
NDN includes an assumption that exact names are not intentionally reused by different data

### CCNx 1.0

Data can be retrieved only using full or exact name.

<!-- ################################################################### -->

## Data retrieval scoping

### NDN

Name based scoping using a set of naming conventions, including "/localhost" and "/localhop"

### CCNx 1.0

An option to scope interest forwarding using HopLimit field

<!-- ################################################################### -->

## Opportunistic in-network caching

Both protocols include ability to cache each forwarded data packet with forwarded-defined policies

### NDN

"Fresh"/"stale" semantics for the cached data
(CS can keep stale packet and satisfy Interests that do not request "fresh" data)

### CCNx 1.0

alive/dead semantics: Requirement that CS cannot use "dead" data to satisfy interests
(current spec only) CS alive/dead decision requires absolute time synchronization within required discovery resolution
Requirement for Cache verification: if Interest specifies KeyRestriction, cache cannot satisfy the interest without verification

<!-- ################################################################### -->

## In-network name discovery
### NDN

Selector support

- As a temporary mechanism to implement in-network name discovery
- Open research for the adequate replacement

"FreshnessPeriod" in Data packets as a relative time to treat Data "fresh" for discovery purposes

### CCNx 1.0

App-defined name discovery:

- Manifests for static data
- Encoding Selectors as part of the Interest name

<!-- ################################################################### -->

## Forwarding Loop Management

### NDN

...NDN assumes networks without guarantees for loopless routing (assumes that routing either don’t exist or have high chance to result in looping paths)...

PIT state to stop the interest from forwarding

"Nonce" to detect potentially duplicated interests with ability to prune "duplicate" paths

"HopLimit" to kill interest loops in special cases

### CCNX 1.0

CCNx 1.0 assumes (but no requirements) that routing system will provide mostly loopless forwarding paths

PIT state to stop interests from forwarding further

"HopLimit" to kill loops

<!-- ################################################################### -->

## Similar Interest Aggregation

### NDN

Exponential-back off interval to allow interest retransmission

### CCNx 1.0

... Re-expressed interest detection ...

<!-- ################################################################### -->

## Interest Payloads

<!-- ################################################################### -->

## Data Security

### NDN

- Exploring signature formats: RSA, ECDSA, HMAC
- Command (Signed) Interests
- Trust schema
- Name based access control

### CCNx 1.0

...

<!-- ################################################################### -->

## Fragmentation

Hop by hop fragmentation when necessary

<!-- ################################################################### -->

## Indirect data retrieval

### NDN

LINK object

### CCNx 1.0

Special handling of Data packets that do not include "Name" field (=retrieved using data digest)

Data is matched against "restriction" field; name is completely ignored

<!-- ################################################################### -->

## 	Sync

### NDN

ChronoSync, RepoSync, ChronoSync 2.0, PartialSync "refs"

### CCNx 1.0

Manifest-based synchronization "refs"
