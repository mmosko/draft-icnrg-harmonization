# Background

## CCNx 0.8: Origin and Point of Commonality

The CCNx 0.8 research prototype produced by PARC embodied a set of network functionality and application functionality that reflects a strong application layer framing approach.  At the network level, a forwarder provides several services: (a) Interest aggregation, (b) Interest forwarding, (c) Content Object forwarding along the Interest reverse path, (d) Content Object caching, (e) routing strategies, and (f) in-network discovery of content via partial Interest name matching.  The application domain has all other functionality, such as reliable delivery, Interest retransmissions, signature verification, encryption, and name discovery techniques.

CCNx 0.8 matches a Content Object to an Interest (the Content Objec satisfies the Interest) based on several attributes of the Content Object and parameters in the Interest.  There is not a one-to-one correspondence, so several different Content Objects could match the same Interest or the same Content Object could match several Interests; in the latter case, we call the Interests similar.  Because there is not a simple rule to determine if two interests are similar, the CCNx forwarder uses a simplification: two Interests are called similar if all Content Object matching terms are equal.  This was acomplished by using a hash table over the wireformat of those matching terms in the Interest.

In CCNx 0.8, a Content Object name is a totally ordered hierarical namespace.  At the network layer, each name component is a variable length byte string of 0 or more bytes.  Name components use a shortlex comparison: if component A is shorter than component B, then A comes before B, otherwise if they are the same length use a lexicographic order.  Two names are sorted based on where the first difference occurs in their name components.  For Names, the ordering is just based on the ordering of the first component where they differ.  If one name is a proper prefix of the other, then it comes first.  This ordering is called the canonical name order.

A name comes in three types.  A 'prefix' name is used in name discovery.  It is not intended to match any specific Content Object, but rather to illicit a response of likely Content Objects.  An 'exact' name in an Interest exactly matches a name in a Content Object.  A 'full' name is not used in discovery: it should specifically identify a single Content Object because it includes the cryptographic hash of the Content Object.

The explicit name in a Content Object has 0 or more name components assigned by the application.  Some of these may be used by routing and some may be used by application-defined protocols, such as versioning or segmentation.  A Content Object name has one terminal implicit name component: the so-called implicit hash.  This is the SHA-256 hash of the Content Object itself (and thus cannot be explicit).  A forwarder, when handling a Content Object, always considers the Content Object to have the implicit name component.  This means that both 'prefix' and 'exact' names do in-network discovery because they are always including at least one extra name component.  For example, if a Content Object has a name /foo/bar, then it's full name is /foo/bar/(hash_value).  A prefix name could be /foo (matching 0 or more suffix components), the exact name is /foo/bar (matching exactly 1 suffix component) and the full name is /foo/bar/(hash_value) (matching 0 additional suffix components).  The restrictions on the number of additional suffix components is critical in the type of name and the expected matching (the details in an Interest are below).

Because names are totally ordered, one can exploit this in a name discovery protocol.  A consumer application may emit an Interest whose name is a prefix of one or more Content Objects stored at a forwarderd (or peer application).  The discovery protocol allows the consumer to walk the name tree rooted at the Interest prefix.  The consumer can as for left-most-child or right-most-child and it can also specify Exclusions that move a notional cursor through the sub-namespace.  In addition to range exclusion, an Interest may exclude individual Content Objects.  Based on the name prefix, the child direction preference, and the exclusions, a forwarder (or peer application) responds with the first Content Object in the canonical name order.  One issue that was never fully worked out is what happens when there are two different Content Objects with the same name.

A CCNx 0.8 Interest is defined as Interest ::= Name, MinSuffixComponents?, MaxSuffixComponents?, PublisherPublicKeyDigest?, Exclude?, ChildSelector?, AnswerOriginKind?, Scope?, InterestLifetime?, Nonce?, FaceID?.  We discuss these parameters in the following.

### Interest name matching
An Interest has so-called 'selectors' that determine how a Content Object name matches the Interest name prefix.  The selectors are: MinSuffixComponents?, MaxSuffixComponents?, Exclude?, ChildSelector?.  These are all optional. If none are given in an Interest, the Interest will match any suffix of the Interest's name prefix (including 0 suffix components).  

MinSuffixComponents specifies the minimum additional suffix components necessary to match and MaxSuffixComponents is similarly the maximum allowed.  An exact name would specify (1,1), that is it need the implicit hash and only the implicit hash.  A full name, which already has the implicit hash, would specify (0,0).  A typical type of discovery is the so-called Get Latest Version, where a name is understood to be of the form /a/b/c/version/segment/(hash_value).  An application emits an Interest with prefix /a/b/c with (3, 3) for the suffix components and asks for the right-most-child.  This says that the application knows that /a/b/c is the specific prefix it wants, and it only needs to discover the latest (right-most) version name component.

The ChildSelector works as we have illustrated it above.  An Interest name, as limited by MinSuffixComponents and MaxSuffixComponents, will induce a totally ordered subset of names rooted at the Interest name.  They are totally ordered because they include the implicit hash terminal component and we will assume there are no collisions.  The ChildSelector defaults to left-most-child (the first of the set).  If one is only interested in the largest name name, one can specify the right-most-child.

The Exlude term filters out results from the totally ordered subset of names rooted at the Interest name.  It can include 0 or more range restrictions and 0 or more singleton restrictions.  In typical use in discovery it is a single range restrictions to keep walking through the subset.  Another common usage is to exclude specific implicit hash name components because they are not the desired result (e.g. the signature is invalid).  The exclude filter only applies to the next name component after the Interest prefix.  For example, if the Interest name is /a/b/c, then the Exclude will only apply to the name component after /c.  Thus, if one wants to Get Lastest Version, the Interest name is /a/b/c and the Exclude range would apply to the version component of a name like /a/b/c/version/segment/(hash_value).

There are several subtleties to walking the name space via exclusions.  One must not assume there is only one Content Object with a given exact name.  For example, due to an error or design, a given publisher might publish two Content Objects with the same exact name, or due to malice, an attacker could forge a name. 
    Because the Exclude component only applies to the next name componet, when one performs a Get Latest Version, one needs at least two different Interests.  The first walks the name space /a/b/c with a range Exclude and the second checks for duplicate Content Objects that differ only in hash, for example /a/b/c/5/100.

At this point, it is useful to walk through an example of name discovery.  We will use the Get Latest Version query, as that is a very common usage.

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

