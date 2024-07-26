---
title: "Problem Statement and Requirements for an Improved DNS Delegation Mechanism abbrev: DNS DELEG Requirements"
abbrev: "DELEG Requirements "
category: info

docname: draft-wirelela-deleg-requirements-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
keyword: Internet-Draft
stand_alone: true
ipr: trust200902
cat: info
submissiontype: IETF
area: Internet
wg: DELEG Working Group


lang: en
kw:
  - dns
  - delegations


author:
- name: David Lawrence
  org: Salesforce
  email: tale@dd.org
- name: Ed Lewis
  email: eppdnsprotocols@gmail.com
- name: Jim Reid
  email: jim@rfc1035.com
- name: Tim Wicinski
  org: Cox Communications
  email: tjw.ietf@gmail.com


--- abstract


Authoritative control of parts of the Domain Name System namespace are indicated with a special record type, the NS record, that can only indicate the name of the server which a client resolver should contact for more information. Any other features of that server must then be discovered through other mechanisms.  This draft considers the limitations of the current system, benefits that could be gained by changing it, and what requirements constrain an updated design.


--- middle


# Introduction


In the Domain Name System {{?STD13}}, subtrees within the domain name hierarchy are indicated by delegations to servers which are authoritative for their portion of the namespace.  The DNS records that do this, called NS records, can only represent the name of a nameserver.  In practice, clients can expect nothing out of this delegated server other than that it will answer DNS requests on UDP port 53.


As the DNS has evolved over the past four decades, this has proven to be a barrier for the efficient introduction of new DNS technology, particularly for interacting with servers other than via UDP or TCP on port 53.  Many features that have been conceived come with additional overhead as they are limited by this least common denominator of nameserver functionality.


Various mechanisms have been proposed for communicating additional information about authoritative nameservers.  This document investigates problems that could be addressed with a new delegation mechanism and the factors that need to be considered in the design of a solution.


# Terminology

This document assumes familiarity with DNS terms as defined in {{?BCP219}}.    Additionally, the following new terms are introduced:


{:vspace}
DELEG
: A new method of DNS delegation, matching the requirements in this document but not presuming any particular mechanism, including previous specific proposals that used this name


{:vspace}
Zone Operator
: The person or organization responsible for the nameserver which serves the zone


{:vspace}
Service Access Point
: The network address tuple at which a zone is served


# Requirements Framework


The requirements constraining any proposed changes to DNS delegations fall broadly into two categories.


"Hard requirements" are those that must be followed by a successful protocol {{?RFC5218}}, because violating them would present too much of an obstacle for broad adoption.  These will primarily be related to the way the existing Domain Name System functions at all levels.


"Soft requirements" are those that are desirable, but the absence of which does not intrinsically eliminate a design.  These will largely be descriptive of the problems that are trying to be addressed with a new method, or features that would ease adoption.


The context used here will be for the Domain Name System as it exists under the ICANN root and the Registry/Registrar/Registrant model (reference), and some conditions will only be relevant there. While it is expected that any design which satisfies the requirements of put forth here would be broadly applicable for any uses of the DNS outside of this environment, such uses are not in scope.


## Hard Requirements

The following strictures are necessary in a new delegation design.


* DELEG must not disrupt the existing registration model of domains.  Reservation of a name at a registry will still use the relevant registrar system to indicate the authorized registrant.

* DELEG must be backwards compatible with the existing ecosystem. Legacy zone data must function identically with both DELEG-aware and DELEG-unaware software. Nameserver (NS) records will continue to define the delegation of authority between a parent zone and a child zone exactly as they have.

* DELEG must not negatively impact most DNS software.  This is intentionally a bit vague with regard to "most", because it can't be absolutely guaranteed for all possible DNS software on the network.  However, the working group should strive to test any proposed mechanism against a wide range of legacy software and come to a consensus as to what constitutes adherence to this requirement.


* DELEG must be able to secure delegations with DNSSEC.  Ideally it should be able to convey an even stronger security model for delegations than currently exists.

* DELEG must support updates to delegation information with the same relative ease as currently exists with NS records.   Changes should take the same amount of time (eg, controlled by a DNS TTL) and allow a smooth transition between different operational platforms.


## Soft Requirements


The following items are the aspirational goals for this work, describing the features that are desired beyond what current NS-based delegations provide.
* DELEG should facilitate the use of new DNS transport mechanisms, including those already defined by DNS-over-TLS (DoT {{?RFC7858}}), DNS-over-HTTPS (DoH {{?RFC8484}}), and DNS-over-QUIC (DoQ {{?RFC9520}}).  It should easily allow the adoption of new transport mechanisms.


* DELEG should make clear all of the necessary details for contacting a Service Access Point -- its protocol, port, and any other data that would be required to initiate a DNS query.


* DELEG should minimize transaction cost in its usage.  This includes, but is not limited to, packet count, packet volume, and the amount of time it takes to resolve a query.


* DELEG should enable a DNS operator to manage DNS service more completely on behalf of domain administrators. For example, DELEG could address long-standing issues of DNSSEC record maintenance that now often depend on registrant / registrar interaction. Similarly, DELEG could allow new transports to be deployed by an operator or nameserver names to be changed, without necessitating that delegation information be modified by the domain administrator.


* DELEG should allow for backward compatibility to the conventional NS-based delegation mechanism.  That is, a Zone Operator who wants to maintain a single source of truth of delegation information using DELEG should be able to easily have Do53 delegations derived from it.


* DELEG should be easily extensible, much like Extension Mechanisms for DNS {{?RFC6891}}, allowing for the incremental addition of new parameters without needing all of the heavy lifting that is expected for the initial deployment.


* DELEG should support an in-band means for the child to signal to the parent that parent-side records related to the child should be updated, akin to CDS/CDNSKEY {{?RFC8078}}.


# IANA Considerations {#IANA}


This memo includes no request to IANA.


# Security Considerations {#Security}


Updating the means by which DNS delegation information is communicated has tremendous implications for the security of the Internet.  This section will be made more robust in future drafts.  Contributions welcome.


--- back


# Acknowledgements {#Acknowledgements}
{: numbered="false"}
