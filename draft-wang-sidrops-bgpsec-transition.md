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

This document deliberates on the reasons why it is unfeasible to render BGPsec as a transitive approach for traversing undeployment areas while maintaining compatibility with native BGPsec.


--- middle

# Introduction

The Border Gateway Protocol (BGP) {{RFC4271}} is deficient in intrinsic security mechanisms, particularly regarding the Autonomous Systems (ASes) path information and origin prefix information. As a result, it is susceptible to route leaks and hijackings.

Native BGPsec, as defined in {{RFC8205}}, extends BGP to fortify the security of AS path information. Nevertheless, native BGPsec encounters challenges in achieving incremental deployment. It utilizes an optional non-transitive BGP path attribute to convey digital signatures, thereby complicating incremental deployment.

The principal objectives are to make BGPsec incrementally deployable and transit transparently over BGPsec-unemployed areas.

There are at least two obstacles to transforming BGPsec into transitive BGPsec. During the OPEN phase, BGPsec necessitates that two BGPsec peers engage in the negotiation of BGPsec capability and multiprotocol capability. In the UPDATE phase, the BGPsec_PATH attribute, being non-transitive, cannot traverse transparently across the legacy BGP area.

In order to transit across areas where BGPsec has not been deployed, the most straightforward approach is to transform the native BGPsec into transitive BGPsec. However, this document will illustrate that it is unfeasible to render BGPsec as a transitive approach for traversing undeployment areas while still using BGPsec_PATH type code in BGP UPDATE and maintaining compatibility with native BGPsec.

## Conventions and Definitions

{::boilerplate bcp14-tagged}

BGP:
: BGP, native BGP, and/or legacy BGP are the BGP version 4 defined in {{RFC4271}} and/or multiprotocol BGP defined in {{RFC4760}}. It does not support BGPsec or native BGPsec.

BGPsec:
: BGPsec, native BGPsec, and/or traditional BGPsec are the BGPsec approach defined in {{RFC8205}}.

T-BGPsec:
: T-BGPsec or transitive BGPsec means that the transitive BGPsec approach is defined in this document.

# Transitive BGPsec Modifications

Transitive BGPsec, in essence, describes that the BGPsec_PATh must be transitive and compliant with native BGPsec.

## Transitive BGPsec Negotiation

As stipulated in {{Section 2.2 of RFC8205}}, for a BGPsec router to successfully negotiate with its peer, it must transmit the BGPsec Capabilities that are in accordance with those of its peers in the BGP OPEN message. However, the transitive path attribute does not require the negotiation of capabilities with peers. In the event that a peer fails to comprehend the transitive path attribute, it merely forwards it to downstream BGP speakers.

In transitive BGPsec, there is no requirement to negotiate the BGPsec capability. However, a BGPsec router is still obligated to inform its peer that it has support for transitive BGPsec. In this scenario, the reuse of the BGPsec capability is essential. Nevertheless, there is no requirement to utilize the Dir field and the AFI field. Transitive BGPsec reuses the native BGPsec capability format. Consequently, the version field of the BGPsec capability must be updated to 1, while the remaining fields should be filled with 0.

If a BGPsec router negotiates with its peers and the version value of the BGPsec capability is set to 1, this implies that the router supports transitive BGPsec. In this scenario, the BGPsec capability serves two purposes. Firstly, it notifies the peer that this particular BGP speaker is capable of supporting transitive BGPsec. Secondly, it differentiates transitive BGPsec from native BGPsec to ensure compatibility.

For the sake of compatibility, this document designates that it still supports native BGPsec in this version.

## The Transitive BGPsec_PATH Attribute

Given that the objective is to expedite the deployment of BGPsec, this document will effectuate the least possible modifications to BGPsec. Consequently, all of the packet formats of the BGPsec_PATH attribute defined in {{RFC8205}} are reutilized in the establishment of transitive BGPsec, including the BGPsec_PATH attribute format, Secure_Path format, Secure_Path Segment format, Signature_Block format, and Signature_Block Segment format.

In native BGPsec, the BGPsec_PATH attribute is an optional non-transitive BGP path attribute. However, within this document, the BGPsec_PATH attribute is defined as an optional transitive BGP path attribute. It is important to note that the attribute code should remain the same as that of the native BGPsec_PATH attribute.

## Updating and Receiving BGP UPDATE message

<!-- It is unnecessary to elaborate on the processing of the BGP UPDATE messages. -->
TBD.

# Question: Compatibility with Native BGPsec

Taking the topology presented in {{fig-mix-deployment}} as an example:
- AS A, AS B, and AS C are regions with continuous native BGPsec deployments. Specifically, AS A, AS B, and AS C implement native BGPsec. Additionally, AS C also implements transitive BGPsec.
- AS D and AS E are areas with legacy BGP deployments. These areas do not implement native BGPsec, transitive BGPsec, or any other related security mechanisms.
- AS F and AS G are areas where either BGPsec or transitive BGPsec can be deployed. That is to say, they have the option to implement either native BGPsec or transitive BGPsec.

~~~~~~
+---+     +---+     +---+     +---+     +---+     +---+     +---+
| A | --> | B | --> | C | --> | D | --> | E | --> | F | --> | G |
+---+     +---+     +---+     +---+     +---+     +---+     +---+
|                       |     |             |     |             |
|                       |     |             |     |             |
\                       /     \             /     \             /
 BGPsec Continuous Area      Non-BGPsec Area      (T-)BGPsec Area
~~~~~~
{: #fig-mix-deployment title="Example: Mix Deployment Scenario"}

AS A is capable of negotiating the BGPsec Capability with AS B. AS A disseminates a route prefix through a BGPsec UPDATE message to AS B.

AS B has the ability to negotiate the BGPsec Capability independently with both AS A and AS C. It receives the BGPsec UPDATE message from AS A and validates it in accordance with native BGPsec. Subsequently, it modifies the BGPsec UPDATE message by incorporating its own information and forwards it to AS C.

AS C obtains the BGPsec UPDATE message from AS B. AS C validates the BGPsec UPDATE message and makes the necessary modifications. In this scenario, AS C and AS D are unable to negotiate the BGPsec Capability. Consequently, AS C converts this message into a transitive BGPsec UPDATE message.

AS D and AS E refrain from processing this transitive BGPsec message and instead forward it to the subsequent hop.

AS F receives this transitive BGPsec UPDATE message from AS E.

In the event that AS F implements transitive BGPsec while AS G only deploys native BGPsec, AS F can downgrade the transitive BGPsec message to a legacy BGP message to ensure compatibility.

When both AS F and AS G deploy native BGPsec, AS G will conduct a syntax check, which will fail due to the transitive nature of the transit bit. In this situation, the BGPsec is deemed invalid. Here, it is inferior to Legacy BGP in the context of BGP route selection. It remains an undefined behavior as to whether AS H should include the transitive BGPsec_PATH when propagating to downstream autonomous systems.

It is not possible to ensure that there are no native BGPsec areas subsequent to Transitive BGPsec nodes.

Consequently, transitive BGPsec is not a viable option. There is a requirement for a new optional transitive path attribute.

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
