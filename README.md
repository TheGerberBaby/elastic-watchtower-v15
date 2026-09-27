# Elastic watchtower v15 candidate

This candidate is not a built or boot-tested appliance yet.

## Non-negotiable delivery contract

The Red Hat hosted build must embed the entire payload container before delivering the OVA. The guest never downloads software and never asks for an external release ZIP. The single OVA carries all vendor software, custom setup tools, 26 curated on-premises integration packages and their signatures. Initial setup is run through /home/elastic/scripts/Start-Appliance.ps1. The operator supplies site identity and local credentials. No deployment credentials or private PKI are embedded.

## How the payload reaches Red Hat

The GitHub Actions workflow assembles a payload container on a GitHub cloud runner from five pinned official Elastic downloads, verified small release inputs, the pinned official EPR runtime, and 26 pinned integration ZIPs/signatures. It validates SHA-512 values and starts EPR to check that its catalog matches. It then pushes the container to GHCR. This does not compile the RHEL operating-system image. Red Hat Image Builder performs that compilation and embeds the immutable container digest.

Red Hat must be able to pull the container. Public publication requires the user's approval. No repository, package, or build has been published by preparing these files. The blueprint template deliberately contains PAYLOAD_DIGEST_REQUIRED and must not be submitted. build/resolve-blueprint.py replaces it only with a real immutable image reference from the successful cloud payload build. The public build files contain no console password, user password, private key or account token. Configure a console administrator in the Red Hat wizard before building; exported hasPassword flags are not reusable credentials.

## Payload and startup

The embedded image contains Elastic 9.5.4 Elasticsearch/Kibana RPMs, Linux and Windows x86_64 Agent archives with signatures and checksums, Winlogbeat, signing keys, Winlogbeat assets, custom scripts, and the curated EPR registry. The first-boot service extracts and verifies its local contents without pulling anything. Missing content stops setup instead of attempting an internet download.

The setup menu reuses the keyboard-driven host form, then asks for site name, retention, internal time sources, allowed LAN networks and a named Kibana administrator password. It installs the embedded RPMs, generates unique PKI, configures Elasticsearch/Kibana, starts local Fleet Server and the artifact HTTPS service, and copies the generated Winlogbeat ZIP into /home/elastic/kits. Site secrets remain under /etc/elastic-kit; configuration, state and logs are linked from /home/elastic. Vendor services retain their standard paths. The artifact service has a service-readable copy of its helper outside protected /home.

The candidate adds a 30 GiB /srv/elastic-data mount, 8 GiB /home and 12 GiB /var; these are evaluation minima, not production retention sizing. Allocate at least 12 GiB RAM and 4 vCPUs for initial evaluation. No fixed IP is embedded. All deployment settings and passwords are entered after boot. AD and SMB require working site infrastructure and are optional. AD/SMB validation is separate from local stack setup.

## Remaining validation gates

1. Approve and complete cloud payload publication, check the actual EPR catalog, and resolve the immutable reference.
2. Import the resolved candidate into Red Hat Image Builder, configure console access, and confirm container embedding is accepted for the selected RHEL8 VMware output.
3. Build the OVA only in Red Hat; verify successful build and the actual downloaded artifact hash.
4. Boot with internet unavailable, run the full interview, validate services and EPR/EAR, inspect Fleet, generate Winlogbeat, and test Windows event delivery.
5. Reboot and repeat acceptance checks before calling this appliance complete.

Automated certificate renewal remains unimplemented; the menu labels inspection and recovery limitations. The verified Microsoft PSTools ZIP, including PsExec and its license, is embedded and copied beside the generated Winlogbeat ZIP. This candidate makes no claim of a completed offline boot or production STIG certification.
