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
    RFC8205:   # BGPsec Protocol Specification

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

The objective of this document is to facilitate the deployment of BGPsec. This document modifies the non-transitive BGPsec_PATH into the transitive BGPsec_PATH and addresses the issues arising from this modification.

## Conventions and Definitions

{::boilerplate bcp14-tagged}

<!-- # Problem Statements -->
<!--
The global adoption of BGPsec faces significant barriers rooted in technical incompatibilities, operational complexities, and coordination dependencies. We have summaried them as follows.
-->
<!-- ## ​Mandatory Capability Negotiation​​ -->
<!--
1. BGPsec requires explicit capability negotiation between peering routers during session establishment. If either peer lacks BGPsec support, the protocol defaults to legacy BGP operation, creating fragmented security coverage in heterogeneous network environments. Thus, when an AS wants to deploy BGPsec, it must require its peers to also implement and deploy BGPsec. Otherwise, BGPsec will downgrade to traditional BGP and cannot transit transparently over legacy BGP areas.
-->
<!-- ## ​​Backward Compatibility Gaps​​ -->
<!--
2. The BGPsec_Path attribute replaces AS_PATH/AS4_PATH with cryptographically signed routing data. No graceful degradation mechanism exists for mixed BGPsec-legacy paths. Critical path attributes may be stripped or misinterpreted when traversing legacy nodes, potentially violating routing integrity.
-->
<!-- ## Degraded Path Visibility -->
<!--
3. The reliance of legacy systems on AS path information for critical functions (e.g., loop prevention, route prioritization) creates operational hazards when BGPsec_Path is uninterpreted. This may result in suboptimal path selection or unintended traffic blackholing.
-->
<!-- ## ​​Nonlinear Security Benefits​​ -->
<!--
4. End-to-end security guarantees are contingent on full-path BGPsec adoption. Partial deployment leaves unsigned AS-path segments vulnerable to forgery, creating a "weakest link" security model that undermines incentives for early adopters.
-->
<!-- ## ​​Operational Overhead and Infrastructure Demands​​ -->
<!-- ​​Computational Costs​​: -->
<!--
5. Cryptographic signing/validation of updates at each AS hop imposes substantial processing overhead, necessitating hardware upgrades and optimized signing algorithms for large-scale deployments.
-->
<!-- ​​PKI Governance Requirements -->
<!--
6. BGPsec requires the establishment of a globally trusted Public Key Infrastructure (PKI) for certificate issuance, revocation, and lifecycle management. Maintaining this PKI at internet-scale demands unprecedented cross-organizational coordination.
-->
<!-- ## ​​Coordination and Adoption Impediments​​ -->
<!-- ​​Universal Adoption Dilemma​​ -->
<!--
7. The protocol's security value is realized only through near-universal adoption, yet achieving consensus among autonomous network operators with divergent priorities remains a systemic challenge.
-->
<!-- ​​Resource Disparities -->
<!-- 8. Smaller networks face disproportionate financial and technical burdens in upgrading infrastructure, implementing PKI workflows, and maintaining signing operations, exacerbating adoption asymmetries. -->
<!--
This document mainly focuses on the technical incompatibilities to make BGPsec incrementally deployable.
-->

# BGPsec Negotiation

As stipulated in {{Sec. 2.2 of RFC8205}}, for a BGPsec router to successfully negotiate with its peer, it must transmit the BGPsec Capabilities that are in accordance with those of its peers in the BGP OPEN message. However, the transitive path attribute does not require the negotiation of capabilities with peers. In the event that a peer fails to comprehend the transitive path attribute, it merely forwards it to downstream BGP speakers.

In transitive BGPsec, there is no requirement to negotiate the BGPsec capability. However, a BGPsec router is still obligated to inform its peer that it has support for transitive BGPsec. In this scenario, the reuse of the BGPsec capability is essential. Nevertheless, there is no requirement to utilize the Dir field and the AFI field. Consequently, the version field of the BGPsec capability must be updated to 1, while the remaining fields should be filled with 0.

If a BGPsec router negotiates with its peers and the version value of the BGPsec capability is set to 1, this implies that the router supports transitive BGPsec. In this scenario, the BGPsec capability serves two purposes. Firstly, it notifies the peer that this particular BGP speaker is capable of supporting transitive BGPsec. Secondly, it differentiates transitive BGPsec from traditional BGPsec to ensure compatibility.

For the sake of compatibility, this document designates that it still supports traditional BGPsec in this version.

# The BGPsec_PATH Attribute

In traditional BGPsec, the BGPsec_PATH attribute is an optional non-transitive BGP path attribute. However, within this document, the BGPsec_PATH attribute is defined as an optional transitive BGP path attribute. It is important to note that the attribute code should remain the same as that of the traditional BGPsec_PATH attribute.

In transitive BGPsec, the format of the BGPsec_PATH attribute is reused.

The AS_PATH is a mandatory path attribute in the legacy BGP UPDATE message. It is of fundamental importance to achieve transparent traversal of legacy BGP. The AS_PATH attribute is retained. This implies that, in transitive BGPsec, both the AS_PATH and BGPsec_PATH attributes will co-exist within the BGP UPDATE message.


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
