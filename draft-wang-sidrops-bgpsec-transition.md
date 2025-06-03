---
title: "Transition to Full BGPsec Deployment"
abbrev: "BGPsec Transition"
category: std

docname: draft-wang-sidrops-bgpsec-transition-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: ""
workgroup: "Secure Inter-Domain Routing"
keyword:
 - BGPsec
 - inter-domain routing
venue:
  group: "Secure Inter-Domain Routing"
  # type: ""
  # mail: "sidr@ietf.org"
  # arch: "https://mailarchive.ietf.org/arch/browse/sidr/"
  github: "FCBGP/BGPsec-Transition"
  latest: "https://FCBGP.github.io/BGPsec-Transition/draft-wang-sidrops-bgpsec-transition.html"

author:
  -
      fullname: Ke Xu
      org: Tsinghua University
      city: Beijing
      country: China
      email: xuke@tsinghua.edu.cn
  -
      fullname: Xiaoliang Wang
      org: Tsinghua University
      city: Beijing
      country: China
      email: wangxiaoliang0623@foxmail.com
  -
      fullname: Zhuotao Liu
      org: Tsinghua University
      city: Beijing
      country: China
      email: zhuotaoliu@tsinghua.edu.cn
  -
      fullname: Qi Li
      org: Tsinghua University
      city: Beijing
      country: China
      email: qli01@tsinghua.edu.cn

normative:
    RFC4271:   # A Border Gateway Protocol 4 (BGP-4)
    RFC4760:   # Multiprotocol Extensions for BGP-4
    RFC8205:   # BGPsec Protocol Specification
    RFC9774:   # Deprecation of AS_SET and AS_CONFED_SET in BGP

informative:
    RFC8374:   # BGPsec Design Choices and Summary of Supporting Discussions


--- abstract

This document describes a means to facilitate the deployment of BGPsec. It modifies the non-transitive BGPsec_PATH attribute to a transitive BGPsec_PATH attribute and addresses some problems this modification brings. It still aims to attest that every AS within the sequence of ASes enumerated in the UPDATE message has explicitly authorized the advertisement of the route.

--- middle

# Introduction

The Border Gateway Protocol (BGP) {{RFC4271}} is deficient in intrinsic security mechanisms, particularly regarding the Autonomous Systems (ASes) path information and origin prefix information. As a result, it is susceptible to route leaks and hijackings.

Traditional BGPsec, as defined in {{RFC8205}}, extends BGP to fortify the security of AS path information. Nevertheless, traditional BGPsec encounters challenges in achieving incremental deployment. It utilizes an optional non-transitive BGP path attribute to convey digital signatures, thereby complicating incremental deployment.

The principal objective of this document is to make BGPsec incrementally deployable. Driven by this aim, it modifies the traditional BGPsec into transitive BGPsec.

There exist at least two obstacles to transforming BGPsec into transitive BGPsec. During the OPEN phase, BGPsec necessitates that two BGPsec peers engage in the negotiation of BGPsec capability and multiprotocol capability. In the UPDATE phase, the BGPsec_PATH attribute, being non-transitive, cannot traverse transparently across the legacy BGP area.

The objective of this document is to facilitate the deployment of BGPsec with as minimal modifications as possible. This document modifies the non-transitive BGPsec_PATH into the transitive BGPsec_PATH and addresses the issues arising from this modification.

## Conventions and Definitions

{::boilerplate bcp14-tagged}

BGP:
: BGP, native BGP, and/or legacy BGP are the BGP version 4 defined in {{RFC4271}} and/or multiprotocol BGP defined in {{RFC4760}}. It does not support BGPsec or traditional BGPsec.

BGPsec:
: BGPsec, native BGPsec, and/or traditional BGPsec are the BGPsec approach defined in {{RFC8205}}.

T-BGPsec:
: T-BGPsec or transitive BGPsec means that the transitive BGPsec approach is defined in this document.

# BGPsec Negotiation

As stipulated in {{Section 2.2 of RFC8205}}, for a BGPsec router to successfully negotiate with its peer, it must transmit the BGPsec Capabilities that are in accordance with those of its peers in the BGP OPEN message. However, the transitive path attribute does not require the negotiation of capabilities with peers. In the event that a peer fails to comprehend the transitive path attribute, it merely forwards it to downstream BGP speakers.

In transitive BGPsec, there is no requirement to negotiate the BGPsec capability. However, a BGPsec router is still obligated to inform its peer that it has support for transitive BGPsec. In this scenario, the reuse of the BGPsec capability is essential. Nevertheless, there is no requirement to utilize the Dir field and the AFI field. Transitive BGPsec reuses the traditional BGPsec capability format. Consequently, the version field of the BGPsec capability must be updated to 1, while the remaining fields should be filled with 0.

If a BGPsec router negotiates with its peers and the version value of the BGPsec capability is set to 1, this implies that the router supports transitive BGPsec. In this scenario, the BGPsec capability serves two purposes. Firstly, it notifies the peer that this particular BGP speaker is capable of supporting transitive BGPsec. Secondly, it differentiates transitive BGPsec from traditional BGPsec to ensure compatibility.

For the sake of compatibility, this document designates that it still supports traditional BGPsec in this version.

# The BGPsec_PATH Attribute


Given that the objective is to expedite the deployment of BGPsec, this document will effectuate the least possible modifications to BGPsec. Consequently, all of the packet formats of the BGPsec_PATH attribute defined in {{RFC8205}} are reutilized in the establishment of transitive BGPsec, including the BGPsec_PATH attribute format, Secure_Path format, Secure_Path Segment format, Signature_Block format, and Signature_Block Segment format.

In traditional BGPsec, the BGPsec_PATH attribute is an optional non-transitive BGP path attribute. However, within this document, the BGPsec_PATH attribute is defined as an optional transitive BGP path attribute. It is important to note that the attribute code should remain the same as that of the traditional BGPsec_PATH attribute.

# BGPsec UPDATE Messages

## General Guidance

Transitive BGPsec obeys the traditional BGPsec rules except that the AS_PATH is mutually exclusive with the BGPsec_PATH attribute.

The AS_PATH is a mandatory path attribute in the legacy BGP UPDATE message. It is of fundamental importance to achieve transparent traversal of legacy BGP. In the event that the subsequent BGP speakers do not support BGPsec, the traditional BGPsec will revert to BGP. Moreover, it will restore the AS_PATH from the BGPsec_PATH.

The AS_PATH attribute is retained in transitive BGPsec; both the AS_PATH and BGPsec_PATH attributes will co-exist within the BGP UPDATE message. It will simplify the operations requiring the AS_PATH attribute and transparently transit over legacy BGP speakers. The processing of AS_PATH is the same as in the legacy BGP.

The AS_SET and AS_CONFED_SET have been deprecated in {{RFC9774}} in BGP, so transitive BGPsec would not consider this scenario where AS_SET or AS_CONFED_SET co-exists with the BGPsec_PATH attribute.

## Constructing the Transitive BGPsec_PATH Attribute

The generation of the transitive BGPsec_PATH attribute is similar to traditional BGPsec. The transit bit is set as transitive to render this attribute as a transitive path attribute.

If the ASes in the BGPsec_PATH attribute are the same as those in the AS_PATH attribute, i.e., the BGPsec_PATH is complete, the signature generation follows the traditional BGPsec. Otherwise, in the BGPsec_PATH broken case, where some BGP speakers don't support transitive BGPsec, the digital signature is computed as follows:

- In order to construct the digital signature for Signature Segment N (that is produced by the current AS), first construct the sequence of octets to be hashed as shown in {{fig-sequence-of-octets-to-be-hashed}}. This sequence of octets includes all the data that the Nth AS attests to by adding its digital signature in the UPDATE message that is being forwarded to a BGPsec speaker in the (N+1)th AS.

- The elements in this sequence MUST be ordered exactly as shown in {{fig-sequence-of-octets-to-be-hashed}}. The 'Target AS Number' is the AS to whom the transitive BGPsec speaker intends to send the UPDATE message.  (Note that the 'Target AS Number' is the AS number announced by the peer in the OPEN message of the BGP session within which the UPDATE message is sent.)  The Secure_Path Segment N is obtained from the BGPsec_PATH attribute. The Previous AS Number N-1 is obtained from the AS_PATH attribute, which is the AS number of the previous hop. Finally, the Address Family Identifier (AFI), Subsequent Address Family Identifier (SAFI), and NLRI fields are obtained from the MP_REACH_NLRI attribute {{RFC4760}}. Additionally, in the Prefix field within the NLRI field (see {{Section 5 in RFC4760}}), all of the trailing bits MUST be set to 0 when constructing this sequence.

- Apply to this octet sequence (in {{fig-sequence-of-octets-to-be-hashed}}) the digest algorithm (for the algorithm suite of this Signature_Block) to obtain a digest value.

- Apply to this digest value the signature algorithm (for the algorithm suite of this Signature_Block) to obtain the digital signature.

~~~~~~
   +------------------------------------+
   | Target AS Number                   |
   +------------------------------------+
   | Secure_Path Segment: N             |
   +------------------------------------+
   | Previous AS Number: N-1            |
   +------------------------------------+
   | Algorithm Suite Identifier         |
   +------------------------------------+
   | AFI                                |
   +------------------------------------+
   | SAFI                               |
   +------------------------------------+
   | NLRI                               |
   +------------------------------------+
~~~~~~
{: #fig-sequence-of-octets-to-be-hashed title="Sequence of Octets to Be Hashed"}

## Processing Instructions for Confederation Members

It is REQUIRED NOT to utilize the transitive BGPsec approach within AS confederations. In other words, within an AS confederation, it MUST employ legacy BGP.

# Processing a Received Transitive BGPsec UPDATE Message

Transitive BGPsec also obeys most of the traditional BGPsec rules in processing a received transitive BGPsec UPDATE message.

Unless the BGPsec_PATH is broken and the corresponding  BGPsec signature is missing, a BGPsec speaker MUST utilize the AS path information in the Secure_Path in most cases, as many as possible, where it would otherwise use the AS path information in the AS_PATH attribute.

# Operational Considerations

TBD.

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
