# DNS MAXFR

The DNS uses a collection of standard and proprietary technologies to
transfer zones between servers. These include the standards-based
AXFR, IXFR, NOTIFY, and TSIG, as well as various extensions like
PowerDNS "supermasters".

Over the years, a number of extensions have been proposed (and even
implemented), such as IXFR-ONLY, MIXFR, catalog zones, and (more
recently) XoT (XFR over TLS) and XuD (XFR over DNS Stateful
Operations).

This is an effort to investigate whether it makes sense to continue an
evolutionary approach to DNS zone transfers, or whether it makes sense
to pursue a radical redesign, or both, or something in-between.

# Requirements

## Compatibility

Any replacement or evolution needs to at least be able to do what the
current XFR system does. This includes:

* Ability to pull complete zone (AXFR)
* Ability to pull updates to a zone (IXFR)
* Ability for primaries to let secondaries know a zone is updated (NOTIFY)
* Ability to authenticate primary and secondary (TSIG)
* Integrity of session (TSIG)

One thing that we may be able to omit is the "compressed IXFR", where
a number of updates is collected into a single update.

## Currently Missing Functionality

There are some things very much missing from the existing XFR system,
including:

* Ability to encrypt the session
* Ability to efficiently synchronize large numbers of zones
* Efficient operation for DNSSEC-signed zones
* Provisioning support (for example, adding and removing zones)

It may not be possible to standardize all of this (for example name
server software may have particular authorization models for adding
& removing zones, or managing ACLs, or so on). This is okay, although
documenting areas likely to have proprietary implementations seems
reasonable.

# Principles for Additional Work

We should adopt a few pragmatic principles to guide any new or changed
protocols:

* It should re-use as much existing technology as possible.
* It should be able to operate in conjunction with existing XFR protocols.

# Open Questions

* Do we need to care about SOA values like timers and serial numbers?
  _(Maybe, depending on how compatible we want to be with existing XFR
  protocols.)_

* Should we consider asking primaries to order data in ways to make
  streaming more efficient? _(Right now the secondary has to store
  entire copies of the zone in certain circumstances, or do elaborate
  tricks to avoid this.)_

* Is it a requirement that we are stateless on the primary like today?
  _(This has nice properties but may also limit design options.)_

* Likewise, do we have any requirements on state at the secondary?

* Should the protocol somehow be extensible? If so, we would probably
  want capabilities negotiation. Also, we would probably need to
  consider proprietary extensions.

# Random Notes

* We might want to consider a set of supporting drafts, to allow
  implementors to adopt the features that they need for their users.
  For example, recommendations about TCP and XFR, versus methods of 
  encrypting DNS transfers

* There is a lot of "low-hanging fruit", a lot of which is in the
  MIXFR draft, but also things like removing duplicate data in RRset.

# Next Steps

The next step is to gather some interested folks for brainstorming,
followed by some proof-of-concept (possibly toy) implementations.
Depending on the shape of those implementations, we can decide what
specific protocol proposals are desirable.

# References

## RFCs

* IXFR https://tools.ietf.org/html/rfc1995
* NOTIFY https://tools.ietf.org/html/rfc1996
* TSIG https://tools.ietf.org/html/rfc2845
* AXFR https://tools.ietf.org/html/rfc5936

## Drafts, past and present

* https://tools.ietf.org/html/draft-kerr-ixfr-only
* https://tools.ietf.org/html/draft-mekking-mixfr
* https://tools.ietf.org/html/draft-muks-dnsop-dns-catalog-zones
* https://tools.ietf.org/html/draft-hzpa-dprive-xfr-over-tls
* https://tools.ietf.org/html/draft-zatda-dprive-xfr-using-dso
