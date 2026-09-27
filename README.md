# Elastic watchtower v15 candidate

The offline payload cloud build passed and Red Hat hosted OVA compilation has been submitted; this candidate is not yet a boot-tested appliance.

## Non-negotiable delivery contract

The Red Hat hosted build must embed the entire payload container before delivering the OVA. The guest never downloads software and never asks for an external release ZIP. The single OVA carries all vendor software, custom setup tools, 26 curated on-premises integration packages and their signatures. Initial setup is run through /home/elastic/scripts/Start-Appliance.ps1. The operator supplies site identity and local credentials. No deployment credentials or private PKI are embedded.

## How the payload reaches Red Hat

The GitHub Actions workflow assembles a payload container on a GitHub cloud runner from five pinned official Elastic downloads, verified small release inputs, the pinned official EPR runtime, and 26 pinned integration ZIPs/signatures. It validates SHA-512 values and starts EPR to check that its catalog matches. It then pushes the container to GHCR. This does not compile the RHEL operating-system image. Red Hat Image Builder performs that compilation and embeds the immutable container digest.

The user authorized publication, and the public payload is available by immutable digest; anonymous manifest access and the cloud EPR catalog test passed. The blueprint template deliberately contains PAYLOAD_DIGEST_REQUIRED and must not be submitted. build/resolve-blueprint.py replaces it only with a real immutable image reference from the successful cloud payload build. The public build files contain no console password, user password, private key or account token. For the current build, the existing blueprint was updated through the Red Hat API with its existing console account preserved; exported hasPassword flags cannot create that credential in a new blueprint. The ordinary wizard may discard container and extra-file customizations, so verify the saved API blueprint before building.

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

## Current build identifiers

Payload workflow: https://github.com/TheGerberBaby/elastic-watchtower-v15/actions/runs/36343849039

Payload: `ghcr.io/thegerberbaby/elastic-watchtower-v15@sha256:d144fb50c4b1e300c21775b78683299d6e52b978dbc7a68224e4e7f566c17502`

Red Hat compose: `9945c70c-fce9-4890-bf36-e98979f6cd1f`; blueprint `0487de49-02ca-49a9-b103-d78060ca2bde`, internal revision 14, appliance name `elastic-watchtower-v15`. Final OVA size and offline guest behavior remain to be verified.
