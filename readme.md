<!--
SPDX-FileCopyrightText: 2021-2022 Andre 'Staltz' Medeiros

SPDX-License-Identifier: CC-BY-4.0
-->

# SSB URI Specification

Version: 1.2

Author: Andre 'Staltz' Medeiros <contact@staltz.com>

License: This work is licensed under a Creative Commons Attribution 4.0 International License.

## Abstract

The `ssb` scheme is registered as [provisional under the IANA](https://www.iana.org/assignments/uri-schemes/prov/ssb), but its format is not yet specified accessibly and clearly. This documents aims at providing the first specification of SSB URIs, as they are used in existing applications and will be adopted increasingly in more use cases.

## Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

## Prior work

There is a thread on SSB, `%g3hPVPDEO1Aj/uPl0+J2NlhFB2bbFLIHlty+YuqFZ3w=.sha256`, where several participants gave their thoughts on the design of the SSB URI format, with several proposals put into consideration. The format of Query-only URIs were inspired by [Magnet URIs](https://www.iana.org/assignments/uri-schemes/prov/magnet).

[ssb-uri](https://github.com/fraction/ssb-uri) is one outcome of that discussion, and is referred to in the IANA page. The `ssb-uri` library is depended on in [Patchwork](https://github.com/ssbc/patchwork/). As an alternative library, [ssb-custom-uri](https://git.sr.ht/~soapdog/ssb-custom-uri) is used in [Patchfox](https://github.com/soapdog/patchfox/). [Patchfoo](https://git.scuttlebot.io/%25YAg1hicat%2B2GELjE2QJzDwlAWcx0ML%2B1sXEdsWwvdt8%3D.sha256) supports both `ssb-uri` and `ssb-custom-uri`. [Manyverse](https://manyver.se) supports the URIs in this specification via the library [ssb-uri2](https://github.com/staltz/ssb-uri2).

The specification in this document is compatible with `ssb-uri` while adding support for expressing more resources.

## List

- **Canonical SSB URIs**
  - `ssb:message/classic/<MSGID>`
  - `ssb:message/bendybutt-v1/<MSGID>`
  - `ssb:message/gabbygrove-v1/<MSGID>`
  - `ssb:message/buttwoo-v1/<MSGID>`
  - `ssb:message/indexed-v1/<MSGID>`
  - `ssb:feed/classic/<FEEDID>`
  - `ssb:feed/bendybutt-v1/<FEEDID>`
  - `ssb:feed/gabbygrove-v1/<FEEDID>`
  - `ssb:feed/buttwoo-v1/<FEEDID>`
  - `ssb:feed/buttwoo-v1/<FEEDID>/<PARENTMSGID>`
  - `ssb:feed/indexed-v1/<FEEDID>`
  - `ssb:blob/classic/<BLOBID>`
  - `ssb:address/multiserver?multiserverAddress=<MSADDR>`
  - `ssb:encryption-key/box2-dm-dh/<KEY>`
  - `ssb:identity/po-box/<KEY>`
  - `ssb:identity/fusion/<KEY>`
- **Experimental SSB URIs**
  - `ssb:experimental?action=claim-http-invite&invite=<CODE>&multiserverAddress=<MSADDR>`
  - `ssb:experimental?action=consume-alias&alias=<A>&userId=<UID>&signature=<SIG>&roomId=<RID>&multiserverAddress=<MSADDR>`
  - `ssb:experimental?action=start-http-auth&sid=<SID>&sc=<SC>`
- **Deprecated SSB URIs**
  - `ssb:message/sha256/<MSGID>`
  - `ssb:feed/ed25519/<FEEDID>`
  - `ssb:blob/sha256/<BLOBID>`

## Overview

There are two main categories of SSB URIs: **Canonical** and **Experimental**. The conceptual difference between these two is that canonical SSB URIs make use of the **path component** in the URI to specify the resource, while experimental SSB URIs use *only* the **query component** to specify the resource.

The third category, **Deprecated**, is used to denote canonical SSB URIs that MUST still be supported by application, but SHOULD be avoided whenever possible.

### Canonical SSB URIs

These SSB URIs are to be considered stable and universally acceptable by all SSB applications that support SSB URIs. They utilize the path component to specify "refs", i.e. identifiers for resources. SSB messages, feeds, and blobs **SHOULD** follow the respective syntaxes:

```
ssb:message/classic/<MSGID>
ssb:message/bendybutt-v1/<MSGID>
ssb:message/gabbygrove-v1/<MSGID>
ssb:message/buttwoo-v1/<MSGID>
ssb:message/indexed-v1/<MSGID>
ssb:feed/classic/<FEEDID>
ssb:feed/bendybutt-v1/<FEEDID>
ssb:feed/gabbygrove-v1/<FEEDID>
ssb:feed/buttwoo-v1/<FEEDID>
ssb:feed/buttwoo-v1/<FEEDID>/<PARENTMSGID>
ssb:feed/indexed-v1/<FEEDID>
ssb:blob/classic/<BLOBID>
```

Where `<MSGID>`, `<FEEDID>`, `<BLOBID>` are *URI-safe Base64 encoded* strings that identify those refs. URI-safe Base64 is equivalent to Base64 where `+` characters are replaced with `-`, and `/` characters are replaced with `_`.

In the special case of `ssb:feed/buttwoo-v1/<FEEDID>/<PARENTMSGID>`, the `<PARENTMSGID>` is the URI-safe Base64 encoded data of a buttwoo-v1 message URI (i.e. `<MSGID>` from `ssb:message/buttwoo-v1/<MSGID>`).

**Examples:**

```
ssb:message/classic/g3hPVPDEO1Aj_uPl0-J2NlhFB2bbFLIHlty-YuqFZ3w=
ssb:message/bendybutt-v1/PR2-btDEO1AjXuPl0TJ2N_hFB2bbFLIHlty0VF1ncty=
ssb:feed/classic/-oaWWDs8g73EZFUMfW37R_ULtFEjwKN_DczvdYihjbU=
ssb:feed/bendybutt-v1/APaWWDs8g73EZFUMfW37RBULtFEjwKNbDczvdYiRXtA=
ssb:feed/gabbygrove-v1/FY5OG311W4j_KPh8H9B2MZt4WSziy_p-ABkKERJdujQ=
ssb:blob/classic/sbBmsB7XWvmIzkBzreYcuzPpLtpeCMDIs6n_OJGSC1U=
```

There are also new concepts related to [Private Groups](https://github.com/ssbc/private-group-spec) which need identifiers, such as:

- `ssb:encryption-key/box2-dm-dh/<KEY>`
- `ssb:identity/po-box/<KEY>`

Where `<KEY>` are *URI-safe Base64 encoded* strings, similar to the previous canonical SSB URIs.

**Multiserver address:**

```
ssb:address/multiserver?multiserverAddress=<MSADDR>
```

Where `<MSADDR>` is a [multiserver address](https://github.com/ssbc/multiserver-address) string, but [percent-encoded according to RFC3986 2.1](https://tools.ietf.org/html/rfc3986#section-2.1), for example:

```
ssb:address/multiserver?multiserverAddress=net%3Awx.larpa.net%3A8008~shs%3ADTNmX%2B4SjsgZ7xyDh5xxmNtFqa6pWi5Qtw7cE8aR9TQ%3D
```

### Colon-separated canonical SSB URIs

Canonical SSB URIs **SHOULD** use `/` to separate the parts of the path component, but they **MAY** also use `:` to separate the parts. The following syntax is acceptable (and is currently what `ssb-uri` utilizes, exclusively):

```
ssb:message:classic:<MSGID>
ssb:message:bendybutt-v1:<MSGID>
ssb:feed:classic:<FEEDID>
ssb:feed:bendybutt-v1:<FEEDID>
ssb:blob:classic:<BLOBID>
ssb:address:multiserver?multiserverAddress=<MSADDR>
```

### Experimental SSB URIs

These SSB URIs are free-form and allow developers to pass any parameters to SSB applications without requiring consensus among the SSB applications. These URIs have an *empty authority* component and path component always matching `experimental` (this is to support Firefox's handling of custom schemes) but have a *non-empty query* component. The query parameters are the only mechanism through which information is conveyed. They satisfy the syntax below (any number of parameters allowed, but we show two, for illustration):

```
ssb:experimental?<key1>=<value1>
ssb:experimental?<key1>=<value1>&<key2>=<value2>
```

#### Known experimental SSB URIs

Experimental SSB URIs do not need to be included in this specification before they can be used, but we encourage developers to submit all known experimental SSB URIs depended by applications. This facilitates the future canonicalization of new SSB URIs. Please feel free to submit new entries here, as long as you know they are used in applications:


**Action to join a room:**

Some experimental SSB URIs have the query `action` to describe intents to perform certain tasks in the SSB application.

A `action=claim-http-invite` experimental URI is used to refer to the consumption of a [Rooms 2](https://github.com/ssb-ngi-pointer/rooms2) invite code, syntax as follows, which includes a `multiserverAddress` query like the canonical multiserver address URI has too:

```
ssb:experimental?action=claim-http-invite&invite=<CODE>&multiserverAddress=<MSADDR>`
```

**Action to consume an alias:**

A URI with query `action=consume-alias` allows us to refer to the processing [consuming a room alias](https://github.com/ssb-ngi-pointer/rooms2/blob/573cc4b3afc08a4eccaea530104524aa7f60af9f/docs/Alias/Alias%20consumption.md), syntax as follows:

```
ssb:experimental?action=consume-alias&alias=<A>&userId=<UID>&signature=<SIG>&multiserverAddress=<MSADDR>&roomId=<RID>
```

**Action to start HTTP authentication:**

A URI with query `action=start-http-auth` is for initiating [Sign-in with SSB](https://github.com/ssb-ngi-pointer/rooms2/blob/573cc4b3afc08a4eccaea530104524aa7f60af9f/docs/Setup/Sign-in%20with%20SSB.md) (HTTP Authentication) with an SSB remote server. The syntax is as follows:

```
ssb:experimental?action=start-http-auth&sid=<SID>&sc=<SC>
```

### Process to canonicalize SSB URIs

When experimental SSB URIs are adopted by SSB applications, if they are deemed useful and long-term URIs, there should be discussion among stakeholders (SSB application developers, and protocol developers) to specify a canonical SSB URI format (using path component) to replace the experimental SSB URI. Then, the new canonical SSB URI should be specified in this document.

## Grammar

The following grammar specifies all SSB URIs (both canonical and any experimental) using [Nearley](https://nearley.js.org):

```nearley
uri -> scheme delimiter body

scheme -> "ssb"

delimiter -> ":"

separator -> [:/]

body -> parts1 (queries):? | parts3 (queries):? | parts4 (queries):?

parts1 -> type1
parts3 -> type3 separator alg3 separator value
parts4 -> type4 separator alg4 separator value separator value

queries -> "?" (query):+
query -> queryKey "=" queryVal
queryKey -> [a-zA-Z] ([^=]):*
queryVal -> [a-zA-Z0-9] ([^&]):*

type1 -> "experimental"
type3 -> "message" | "feed" | "blob" | "address" | "encryption-key" | "identity"
type4 -> "feed"

alg3 -> "classic" | "multiserver" | "bendybutt-v1" | "gabbygrove-v1" | "buttwoo-v1" | "indexed-v1" | "box2-dm-dh" | "po-box" | "fusion"
alg4 -> "buttwoo-v1"

value -> ([0-9a-zA-Z\-\_\=]):+
```
