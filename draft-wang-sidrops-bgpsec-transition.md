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

This document describes a means to facilitate the deployment of BGPsec.

--- middle

# Introduction

The Border Gateway Protocol (BGP) {{RFC4271}} lacks inherent security mechanisms, especially for its Autonomous Systems (ASes) path and origin prefix information, making it vulnerable to route leaks and hijackings.

BGPsec, defined in {{RFC8205}}, extends BGP to enhance security for AS path information. However, it employs an optional non-transitive BGP path attribute to carry digital signatures, complicating incremental deployment.

This document aims to facilitate the deployment of BGPsec.

## Conventions and Definitions

{::boilerplate bcp14-tagged}

# Problem Statements

The global adoption of BGPsec faces significant barriers rooted in technical incompatibilities, operational complexities, and coordination dependencies. We have summaried them as follows.

<!-- ## ​Mandatory Capability Negotiation​​ -->
1. BGPsec requires explicit capability negotiation between peering routers during session establishment. If either peer lacks BGPsec support, the protocol defaults to legacy BGP operation, creating fragmented security coverage in heterogeneous network environments. Thus, when an AS wants to deploy BGPsec, it must require its peers to also implement and deploy BGPsec. Otherwise, BGPsec will downgrade to traditional BGP and cannot transit transparently over legacy BGP areas.

<!-- ## ​​Backward Compatibility Gaps​​ -->
2. The BGPsec_Path attribute replaces AS_PATH/AS4_PATH with cryptographically signed routing data. No graceful degradation mechanism exists for mixed BGPsec-legacy paths. Critical path attributes may be stripped or misinterpreted when traversing legacy nodes, potentially violating routing integrity.

<!-- ## Degraded Path Visibility -->
3. The reliance of legacy systems on AS path information for critical functions (e.g., loop prevention, route prioritization) creates operational hazards when BGPsec_Path is uninterpreted. This may result in suboptimal path selection or unintended traffic blackholing.

<!-- ## ​​Nonlinear Security Benefits​​ -->
4. End-to-end security guarantees are contingent on full-path BGPsec adoption. Partial deployment leaves unsigned AS-path segments vulnerable to forgery, creating a "weakest link" security model that undermines incentives for early adopters.

<!-- ## ​​Operational Overhead and Infrastructure Demands​​ -->
<!-- ​​Computational Costs​​: -->
5. Cryptographic signing/validation of updates at each AS hop imposes substantial processing overhead, necessitating hardware upgrades and optimized signing algorithms for large-scale deployments.

<!-- ​​PKI Governance Requirements -->
6. BGPsec requires the establishment of a globally trusted Public Key Infrastructure (PKI) for certificate issuance, revocation, and lifecycle management. Maintaining this PKI at internet-scale demands unprecedented cross-organizational coordination.

<!-- ## ​​Coordination and Adoption Impediments​​ -->
<!-- ​​Universal Adoption Dilemma​​ -->
7. The protocol's security value is realized only through near-universal adoption, yet achieving consensus among autonomous network operators with divergent priorities remains a systemic challenge.

<!-- ​​Resource Disparities -->
<!-- 8. Smaller networks face disproportionate financial and technical burdens in upgrading infrastructure, implementing PKI workflows, and maintaining signing operations, exacerbating adoption asymmetries. -->

This document mainly focuses on the technical incompatibilities to make BGPsec incrementally deployable.

# Transitive BGPsec

TODO

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
