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
    fullname: Xiaoliang Wang
    organization: Tsinghua University
    email: wangxiaoliang0623@foxmail.com

normative:
    RFC4271:   # A Border Gateway Protocol 4 (BGP-4)
    RFC8205:   # BGPsec Protocol Specification

informative:
    RFC8374:   # BGPsec Design Choices and Summary of Supporting Discussions


--- abstract

This document describes a means to facilitate the deployment of BGPsec.

--- middle

# Introduction

The Border Gateway Protocol (BGP) {{RFC4271}} lacks inherent protection for its Autonomous Systems (ASes) path and origin prefix information, making it vulnerable to route leaks and hijackings.

BGPsec, defined in {{RFC8205}}, extends BGP to enhance security for AS path information. However, it employs an optional non-transitive BGP path attribute to carry digital signatures, complicating incremental deployment.

This document aims to facilitate the deployment of BGPsec.

## Conventions and Definitions

{::boilerplate bcp14-tagged}

# Problem Statement

The global adoption of BGPsec faces significant barriers rooted in technical incompatibilities, operational complexities, and coordination dependencies. It focuses on the technical incompatibilities in this document.

<!-- ## ​Mandatory Capability Negotiation​​ -->
1. BGPsec requires explicit capability negotiation between peering routers during session establishment. If either peer lacks BGPsec support, the protocol defaults to legacy BGP operation, creating fragmented security coverage in heterogeneous network environments. Thus, when an AS wants to deploy BGPsec, it must require its peers to also implement BGPsec. Otherwise, BGPsec will downgrade to traditional BGP and cannot transit transparently over legacy BGP areas.

<!-- ## ​​Legacy System Incompatibility​​ -->
2. The BGPsec_Path attribute replaces AS_PATH/AS4_PATH with cryptographically signed routing data. Legacy routers incapable of parsing BGPsec_Path will either discard updates or process them as invalid, risking routing inconsistencies.



<!--

## Degraded Path Visibility 

The reliance of legacy systems on AS_PATH for critical functions (e.g., loop prevention, route prioritization) creates operational hazards when BGPsec_Path is uninterpreted. This may result in suboptimal path selection or unintended traffic blackholing.

## ​​Operational Overhead and Infrastructure Demands​​:

​​Computational Costs​​:
: Cryptographic signing/validation of updates at each AS hop imposes substantial processing overhead, necessitating hardware upgrades and optimized signing algorithms for large-scale deployments.

​​PKI Governance Requirements​​:
: BGPsec requires the establishment of a globally trusted Public Key Infrastructure (PKI) for certificate issuance, revocation, and lifecycle management. Maintaining this PKI at internet-scale demands unprecedented cross-organizational coordination.

## ​​Nonlinear Security Benefits​​

End-to-end security guarantees are contingent on full-path BGPsec adoption. Partial deployment leaves unsigned AS-path segments vulnerable to forgery, creating a "weakest link" security model that undermines incentives for early adopters.

## ​​Coordination and Adoption Impediments​​

​​Universal Adoption Dilemma​​:
: The protocol's security value is realized only through near-universal adoption, yet achieving consensus among autonomous network operators with divergent priorities remains a systemic challenge.

​​Resource Disparities​​:
: Smaller networks face disproportionate financial and technical burdens in upgrading infrastructure, implementing PKI workflows, and maintaining signing operations, exacerbating adoption asymmetries.

## ​​Backward Compatibility Gaps​​

No graceful degradation mechanism exists for mixed BGPsec-legacy paths. Critical path attributes may be stripped or misinterpreted when traversing legacy nodes, potentially violating routing integrity.

## Conclusion

BGPsec’s security enhancements are fundamentally incompatible with incremental deployment due to their dependency on universal cryptographic validation and AS-path transparency. This creates a circular adoption barrier: operators lack motivation to implement BGPsec without demonstrable security gains, yet such gains remain unachievable without near-complete adoption. Addressing these challenges demands a paradigm shift in deployment methodologies, potentially combining phased adoption frameworks, hybrid transitional architectures (e.g., parallel AS_PATH/BGPsec_Path support), and cross-industry collaboration to standardize incentives for early adopters.

-->


# Transitive BGPsec



# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
