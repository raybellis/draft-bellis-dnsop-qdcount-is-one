---
title: In the DNS, QDCOUNT is (usually) One
docname: draft-bellis-dnsop-qdcount-is-one-00
updates: RFC1035

submissiontype: IETF
ipr: trust200902
area: Internet
wg: DNSOP Working Group
kw: Internet-Draft
cat: std

coding: us-ascii
pi:
  - toc
  - symrefs
  - sortrefs

author:
  -
    ins: R. Bellis
    name: Ray Bellis
    org: Internet Systems Consortium, Inc.
    abbrev: ISC
    street: PO Box 360
    city: Newmarket
    code: NH 03857
    country: US
    phone: +1 650 423 1300
    email: ray@isc.org
  -
    ins: J. Abley
    name: Joe Abley
    org: Cloudflare
    city: Amsterdam
    country: NL
    phone: +31 6 45 56 36 34
    email: jabley@cloudflare.com

--- abstract

This document clarifies the allowable values of the QDCOUNT parameter
in DNS messages with OPCODE = 0 (QUERY) and specifies the required
behaviour when values that are not allowed are encountered.

--- middle

# Introduction

The DNS protocol {{!RFC1034}{{!RFC1035}} includes a parameter QDCOUNT in the
DNS message header, whose value is specified to mean the number
of questions in the Question Section of a message.

In a general
sense it seems perfectly plausible for the QDCOUNT parameter, an unsigned
16-bit value, to take a considerable range of values. However, in the
specific case of messages that encode DNS queries (messages with OPCODE
= 0) there are other limitations inherent in the protocol that constrain
values of QDCOUNT to be either 0 or 1. In particular, several parameters
specified for DNS response messages such as AA and RCODE have no defined
meaning when the message contains multiple queries, since there is no way
to signal which question those parameters relate to.

In this document we clarify the allowable values of the QDCODE parameter
in DNS messages with OPCODE = 0 (QUERY). We also briefly survey the use
of this parameter by other types of DNS messages with OPCODE = 

# Terminology used in this document

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in BCP 14
{{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all
capitals, as shown here.

# QDCOUNT in Queries is (almost always) one

{{!RFC1035}} significantly predates the use of normative requirements
keywords, and parts of it are consequently somewhat open to interpretation.

Section 4.1.2 ("Question section format") has this to say about QDCOUNT:

> The section contains QDCOUNT (usually 1) entries

However, the only documented exceptions within {{!RFC1035}} relate to
the IQuery Opcode, where the request has "an empty question section"
(QDCOUNT == 0), and "zero, one, or multiple domain names for the
specified resource as QNAMEs in the question section". The IQuery OpCode
was made obsolete in {{!RFC3425}}.

In the absence of clearly expressed normative requirements, we rely on
other text in {{!RFC1035}} that makes use of the definite article or
other text that implies a singuar question, and therefore QDCOUNT == 1.

For example, Section 4.1:

> the question for the name server

and:

> The question section contains fields that describe a question to a
> name server

and in Section 4.1.1. ("Header section format"):

> AA Authoritative Answer - this bit is valid in responses,
>    and specifies that the responding name server is an
>    authority for the domain name in question section.

## Exceptions

DNS Cookies {{?RFC7873}} in Section 5.4 allow a client to receive a
valid Server Cookie without sending a specific question by sending a
Query packet (OpCode 0) with QDCOUNT == 0, with the resulting response
also containing no question.

# Other DNS Opcodes

## DNS Notify

DNS Notify {{?RFC1996}} also lacks a clearly defined range of values
for QDCOUNT.  Section 3.7 says:

> A NOTIFY request has QDCOUNT > 0

but all other text in the RFC talks about the <QNAME, QCLASS, QTYPE>
tuple in the singular.

## DNS Update

DNS Update {{?RFC2136}} renames the QDCOUNT field to ZOCOUNT, but the
value is constrained to be one by Section 2.3 ("Zone Section"):

> All records to be updated must be in the same zone, and therefore the
> Zone Section is allowed to contain exactly one record.

## DNS Stateful Operations

DNS Stateful Operations {{?RFC8490}} (DSO - OpCode 6) attempts to
preserve compatibility with the standard DNS 12 octet header, and does
so by requiring that all four of the section count values be set to
zero.

# Conclusion

The only legal values of the 16-bit word contained in octets 4 and 5 of
the DNS header (usually called QDCOUNT) are zero (rarely) and one,
otherwise.

Future OpCodes may define other uses and legal values for the header
octets used by this field.

# Security Considerations

This document has no security implications.

# IANA Considerations

This document has no IANA actions.

--- back
