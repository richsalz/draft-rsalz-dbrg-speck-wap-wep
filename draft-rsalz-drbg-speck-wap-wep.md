---
title: "No MTI Crypto without Public Review"
abbrev: crypto-pubreview
docname: draft-rsalz-drbg-speck-wap-wep-latest
date: 2016-03-21
category: bcp
ipr: trust200902

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: R. Salz
    name: Rich Salz
    org: Akamai Technologies
    email: rsalz@akamai.com


normative:
  RFC2119:

informative:
  RFC7258:
  RFC7366:
  RFC7693:
  RFC7696:
  acoustic:
    target: http://www.tau.ac.il/~tromer/papers/acoustic-20131218.pdf
    title: "RSA Key Extraction via Low-Bandwidth Acoustic Cryptanalysis"
    author:
      -
        name: Daniel Genkin
        org: Technion and Tel Aviv University
      -
        name: Adi shamir
        org: Weizmann Institute of Science
      -
        name: Eran Tromer
        org: Tel Aviv University
    date: 2013-12-18
  davis:
    target: http://world.std.com/~dtd/sign_encrypt/sign_encrypt7.PDF
    title: "Defective Sign & Encrypt in S/MIME, PKCS#7, MOSS, PEM, PGP, and XML."
    date: 2001-06-25
    author:
    seriesinfo:
      Usenix: Proc. Usenix Tech. Conf.

--- abstract

Cryptography is becoming more important to the IETF and its protocols,
and more IETF protocols are using, or looking at, cryptography to
increase privacy on the Internet {{RFC7258}}.

This document specifies a proposed best practice for any mechanism (or data
format) that uses cryptography; namely, that RFCs cannot specify an
algorithm as mandatory-to-implement (MTI) unless that algorithm has had
reasonable public review. This document also "sketches out" a rough
definition around what such a review would look like.

--- middle

# Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL
NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in RFC 2119 {{RFC2119}}.

The term mandatory to implement (MTI) is used in this document
to describe a cryptographic algorithm that is listed as a MUST
in an RFC.

The term "snake oil" is used as a pejorative for something
which appears to do its job acceptably, but actually does not; see
<https://en.wikipedia.org/wiki/Snake_oil_%28cryptography%29>.  It is a goal
of the IETF that we never be misled into being, or mistakenly taken as,
snake oil salesman.

# Introduction

Cryptography is becoming more important to the IETF and its protocols,
and more IETF protocols are using, or looking at, cryptography to
increase privacy on the Internet {{RFC7258}}.

This document specifies a proposed best practice for any protocol (or data
format) that uses cryptography. Namely, that such RFCs cannot specify an
algorithm as mandatory-to-implement (MTI) unless that algorithm has had
reasonable public review. This document also "sketches out" a rough
definition around what such a review would look like.

# Why is Cryptography Hard?

Cryptography is hard because it is not like traditional IETF protocol
deployments.  In this classic situation, if one party implements
a protocol incorrectly, it usually becomes obvious as interoperability
suffers or completely fails.  But with cryptography, one party can have
implementation defects, or known exploitable weaknesses, that expose the
entire communication stream to an attacker.  Open source and code reviews are
not a panacea here, but using only widely-accepted cryptographic mechanisms
(e.g., avoiding facilities like <https://en.wikipedia.org/wiki/Dual_EC_DRBG>)
will reduce the attack surface.

Cryptography is hard because subtle design characteristics can have
disastrous consequences.  For example, the US Digital Signature Algorithm
requires the random nonce to be protected and never re-used.  If those
requirements are not met, the private key can be leaked.

Cryptography is hard because adversaries design new attacks and refine
existing ones.  Attacks get better over time; they never get worse.
For example, it is now de riguer to protect against CPU timing attacks,
even when the device is only viewable over a network.  A recent paper
{{acoustic}}
(XXX reference) can identify a private key if your smartphone is just laid
next to an innocuous charging device.  We understand power differential
attacks, timing attacks, and perhaps cache line attacks; we now have to
think about RFI emissions from our phone.

Cryptography is hard because the order of operations can matter.  It is
not intuitively obvious to most developers, which should come first among
signing, compression and encryption. This issues was first raised in Spring
of 2001 {{davis}} but was only addressed in TLS by {{RFC7366}} more than a
dozen years later.

Getting the cryptography right is important because the Internet, and
therefore the work of the IETF, has become a tempting target for all types
of attackers, from individual "script kiddies," through criminal commercial
botnet and phishing ventures, up to national-scale adversaries operating
on behalf of their nation-state.

# Things to avoid

"Sunlight is said to be the best of disinfectants; electric light the most
efficient policeman." -- Louis Brandeis, *Other People's Money and How Bankers
Use it,* first published as a set of articles in _Harper's Weekly_ in 1914.

Cryptography that is developed in private, such as among an industry
consortium is a bad idea.  Notable examples of this include:

* A5/1 and A5/2 for GSM-based mobile phones.

* WEP and WPA for WiFi access.

* SSLv2, while published, was developed by a private group at an Internet
startup.  It had security flaws that had global effects decades later,
see <https://drownattack.com/>.

It is hard to get good public review of patented cryptography, unless there
is a strongly compelling need.  For example, decades ago RSA was the only
practial public-key mechanism available. Part of the concern about patented
cryptography is that the patent-holder has every incentive to provide that
their system is good, while the rest of the world generally has little
interest in proving that their commercial venture is bad.  Examples of this
include:

* Algebraic Eraser, presented at IETF-xx (XXX reference), and since then
has had a number of effective attacks.

* XXX STILL MORE NEEDED

# Why limit to MTI?

There is an argument that any new RFC not classified as "historical"
should not specify or recommend insufficiently-reviewed cryptography,
whether it MTI or not.  This document limits itself to MTI for a couple
of reasons.

* Informational RFCs often document how to interoperate with other systems,
and this is useful.  As examples of this, see the Internet-Drfats on
scrypt and {{RFC7693}}.

* Putting insufficiently-reviewed algorithms into an RFC can be one way to
spur interest in getting more reviews.
This MUST NOT be the primary motivation for inclusion, but it can be a
useful side-effect, and might lead to future "promotion" to MTI.
Note that waiting through draft and last-call state, then claiming "nobody
broke it" MUST NOT be used as the rationale; this is using the IETF
to host a "proof by contest."

* Drawing a strict boundary just around MTI is a tractable problem.
Drawing a similar boundary around all potential IETF uses of cryptography
is bound to have mistakes and errors, any one of which can has the
potential to make the IETF look bad, if not incompetent.

* Requiring MTI to have public review also pressures everyone to conform
and raise the bar. Imagine a hypothetical national security body that has a new
cryptographic algorithm, Military Top-secret Encryption, or MITE.
If MITE is not MTI, then that government might be hard-pressed to get it
accepted into off-the-shell offerings.  If it is MTI without sufficient
review, then they have good reason to keep flaws in existing cryptography
private.  To avoid both situations, the that government should work to
get MITE as an MTI, and would now have the burden to make sure it receives
sufficient analysis.

# How to Do it Right

Cryptographic agility, {{RFC7696}}, is probably a MUST.
While it has its detractors, there are no known (to the author)
practical considerations to evolving a deployed based to stronger
crypto, while still maintaining interoperability with existing 
entities.
This requires being able to make informed choices about when to use old
weak crypto, and when to use the "latest and greatest," and while not much
software, and essentially no end-users, are capable of making that choice,
it seems sadly the best we can do.

The second consideration is to avoid temptation and premature optimization.
Do not adopt an algorithm just because it seems "small and fast" or comes
from "someone I respect."

What constitutes sufficient public review?  It is hard to say.  An open
competition, such as those that led to AES (XXX ref) and SHA-3 (XXX ref)
seem to be good, even when they come from sources that are under widespread
suspicion, like the US Government. These efforts, like the Password Hashing
Competition <https://password-hashing.net/>, had
wide international participation and analysis by many noted exports.

Papers presented in the various Crypto conferences (XXX need list) are
good.  Same for various Usenix workshops.

Proof by contest -- "Nobody's Claimed my $200 reward" -- are generally
useless, for a number of reasons.
They tend to be promoted by amateur cryptographers as a way to get
attention, and if someone actually looks at them they are always cracked.
Numerical analysis is a better approach, albeit much harder work.
Contests designed to show the amount of "brute-force" work needed, such
as the old RSA factoring challenges, can be useful.
But they do not show, for example, if the cryptography under test is
fundamentally flawed or not.

# Acknowledgements

Thanks to Stephen Farrell for instigating this.
