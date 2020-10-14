---
title: Per-Application Networking Considerations
abbrev: Per App Considerations
docname: draft-per-app-networking-considerations-latest
category: info

ipr: trust200902
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: L. Colitti
    name: Lorenzo Colitti
    org: Google
    street: Shibuya 3-21-3
    city: Shibuya, Tokyo  150-0002
    country: Japan
    email: lorenzo@google.com
 -
    ins: T. Pauly
    name: Tommy Pauly
    org: Apple Inc.
    street: One Apple Park Way
    city: Cupertino, California 95014
    country: United States of America
    email: tpauly@apple.com

--- abstract

This document describes considerations for and implications of using application identifiers as a method of differentiating traffic on networks. Specifically, it discusses privacy considerations, possible mitigations, and considerations for user experience and API design.

--- middle

# Introduction {#introduction}

There are a number of use cases where network operators, or applications, might desire for application traffic to be treated differently by the network. Some examples are:

- Network-specific services. Applications might want to access local resources on a network that does not otherwise provide Internet access (for example, an entertainment system on an airplane).

- Per-application private networks. Certain applications, such as enterprise applications, might want to connect directly to the enterprise network in a secure fashion without using a device-wide VPN.

- Mobile network services. In mobile networks, applications like voice over LTE, IMS and RCS often use a different virtual network than general Internet traffic.

- Applications with specific performance requirements. Certain applications would benefit from particular scheduling or QoS policies - for example applications requiring low latency such as voice might be scheduled and queued differently from latency-insensitive traffic.

- Local breakout. In a mobile networks, applications might want to access resources through a different network interface (e.g., one that uses IPv6 addresses that are local to a specific area, and do not have a wide mobility).

- Zero-rating traffic. As allowed by regulators, certain classes of traffic (e.g., messaging or streaming video) might be exempt from metering on networks that are otherwise metered.

In existing networks, this is sometimes implemented by the network using deep packet inspection (e.g., flow tracking coupled with inspection of the SNI handshake). This is complex, implicates public policy concerns, and generally conflicts with the recommendations in {{?RFC7258}}. The move towards encrypted protocols such as {{?RFC8484}} and {{?I-D.ietf-tls-esni}} will make this more difficult for some operators. Thus, if an application is to receive different treatment, the host or the application itself should be involved in requesting specific network treatment. This document explores the implications.

In this document, the term “application” refers to an application as understood by the user of the device.

## Conventions and Definitions {#conventions}

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

# Requesting differential treatment

There are already mechanisms for applications to request and obtain particular treatment by the network, or to communicate application identity to the network in order to obtain particular treatment. These include:

- Diffserv
- APN6
- Network tokens
- Slicing in 3GPP 5G networks
- Explicit application selection of a given PVD {{?RFC8801}}

# Open Internet considerations

In certain regulatory regimes, networks that provide general Internet access may not be permitted to discriminate between traffic sent to or from different lawful applications or websites, or such discrimination may be prohibited if commercially based. In a situation where the network operator has influence on the implementation of the user host (e.g., mobile networks where the handset is sold by the carrier), the device may be able to implement network policies directly, and thus may be impacted by neutrality considerations.

Neutrality concerns can be addressed by providing user control over assignment of particular applications to the particular network resources available to that user. Further, network neutrality implications may be reduced or avoided in some jurisdictions if the differential treatment occurs between different classes of traffic with different network requirements (e.g., bandwidth-intensive traffic vs. low-latency traffic) as opposed to between different applications with similar network requirements, and thus, by ensuring that the mechanism used to communicate requests to the network only specifies traffic classes and not individual applications.

# Privacy considerations

IETF guidance to avoid pervasive monitoring {{?RFC7258}} is for network protocols to expose as little information as possible. Some of the proposed technologies for application signalling rely on the application exposing its identity to the network so that the network can then implement appropriate policies. This may provide the network with much more information than is needed to implement the desired behaviour. Information about which users are using specific applications, or visiting certain destinations, and when, can be highly privacy-sensitive.

Note that application identity can be exposed to the network even in the absence of explicit signalling. For example, if the host were to implement a network-set policy that requires that traffic from application X be sent on a different network path than all other traffic, the identity of application X would be exposed to the network as soon as it sends traffic.

Privacy concerns may also be reduced or avoided if the mechanism to request a different class of service only specifies the class of service (e.g., “low latency” or “streaming video”) instead of the application originating the traffic.

In a situation where the network operator has influence over the implementation of the user host, the operator can still impose policies on what requests are possible - for example, the operator might choose to limit access to specialized services such as carrier messaging only to carrier applications. It is possible for such policies to preserve privacy if the policies specify general categories of traffic as opposed to specifying applications.

# Mitigating implications via traffic categories

Many of these implications can be mitigated If the mechanism to request a different class of service specifies general categories of traffic instead of particular application.

TODO: fill in

# User experience considerations

Privacy and neutrality concerns can be mitigated if the host’s user is informed that particular applications are seeking or designated for particular treatment and consents to it. In order for consent to be meaningful, the user should be presented with a message that they understand. It may be difficult to balance the goal of providing complete and accurate information with the goal of ensuring that the user understands the implications.

TODO: this text needs to be improved.

# API considerations

It is desirable to provide an API layer that is not tied to specific network technologies (e.g., URSP, VPN, etc.). PVDs could provide a useful layer of abstraction.

TODO: add text

Any API should not involve revealing an application or user identity to the network via metadata without network authentication. Instead, the API should allow a given setting to be conditional on the identity of the network. For example, an application should express “use the zero-rated service for my app when on a particular carrier network”, instead of blindly saying “this is my application identifier”.
