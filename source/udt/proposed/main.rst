Proposed (UDT Restructuring)
============================

.. note::

    I have been working on this project over the last few months, current progress is available here: `(GitHub branch) <https://github.com/odysseus654/vircadia/tree/feature/udt>`_

This is an attempt to restructure the base networking layer of Vircadia to be one based on a more full-fledged implementation of UDT.  While High Fidelity
originally started out with a UDT implementation, they slimmed it down and tore out all nonessential bytes and packets to improve the amount of data that can
be placed in a single packet.  The efforts here are in the hope that a less-efficient but better-behaving network layer can improve performance and flexibility.

UDT (originally named SABUL) was originally crafted as a high-speed data transfer protocol using UDP and rewriting the assumptions that TCP uses to maintain a connection.
High Fidelity most likely chose it due to its custom networking properties being unknown to most firewalls and thus more likely to penetrate them.  The protocol is defined
by a series of RFCs (which have all expired) and a reference implementation (which contains details not in the spec and does not fully conform to it).  These can be found here:

- `Homepage <https://udt.sourceforge.io/>`_
- `Last RFC <https://www.ietf.org/archive/id/draft-gg-udt-03.txt>`_ (referencing v4 of the protocol)
- `Reference implementation <https://github.com/odysseus654/udt>`_ (converted from CVS and merged with some few stray Git commits)

The efforts here have taken the RFC and reference implementaton, initially creating a partial implementation in Go, then porting that implemenation into C++.  As such, it is
a lot more thread-heavy than the reference implemenation and also hopefully separated more from the original implemenation decisions of the original reference implemenation
(HiFi's implementation cuts half the code out but still uses familiar variable names).  Where descrepancies between the RFC and the ref implemenation exist I have gone with
the reference implemenation.  I have also tweaked the protocol a bit to promote MTU discovery, but in a way that shouldn't cause incompatibilities with an unmodified system
(hopefully?)

.. toctree::
    :maxdepth: 2
    :titlesonly:
    :caption: Contents:

    layout
    behavior