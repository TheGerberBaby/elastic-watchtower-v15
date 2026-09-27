# DVD size target — build remains paused

Target: one standard single-layer 4.7 GB DVD containing the complete offline appliance artifact (not merely the EPR); leave room for disc filesystem overhead.

Measured local inputs:

| Input | Bytes |
| --- | ---: |
| Official EPR runtime saved archive, v1.40.0 | 32,522,240 |
| Selected 26 integration ZIPs | 23,993,315 |
| Matched Elastic 9.5.4 offline release files | 2,055,371,068 |
| Microsoft PSTools ZIP | 5,154,340 |
| Previously downloaded official EPR Lite 9.5.4 saved archive, NOT selected for v15 | 4,309,463,040 |

The runtime archive and selected integration ZIPs total about 56.5 MB, before small signature/configuration overhead; this is a measured input total, not a measured final image layer size.

The v15 candidate already derives its registry from Elastic's official package-registry runtime pinned by digest, with the 26 selected on-premises packages rather than the full/Lite distribution catalog. No third-party EPR implementation is needed. Our custom payload container remains necessary to carry the Windows/Linux binaries and setup source through the hosted build.

The RHEL base, PowerShell, container storage, disk-image metadata and compression determine the remaining delivered OVA size. The 79 GiB sum of requested filesystem minima is virtual guest capacity, not an OVA download-size prediction. DVD fit is unverified until the actual complete artifact is built and measured; do not remove required payloads or move them into a separate after-boot transfer to satisfy the target.

Official references:
- https://github.com/elastic/package-registry
- https://www.elastic.co/docs/reference/fleet/air-gapped

No publishing or build resumed while checking these alternatives.
