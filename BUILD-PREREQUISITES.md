# Reproducing the v15 build at another site

This is a build-time workflow: the delivered appliance must not download software after boot.

The person reproducing the build needs:

1. A Red Hat account with permission to use hosted Image Builder and its required RHEL repositories.
2. A GitHub account able to create or fork the source repository, run GitHub Actions, and publish a GHCR container package readable by Red Hat Image Builder.
3. Approved browser access to GitHub and the Red Hat console, plus approved access for the cloud build services to GHCR, Elastic's artifact/container/package registries, Microsoft's package repository, and the Microsoft Sysinternals download site.
4. A preparation workstation with the documented tools and sufficient storage for the final OVA; the RHEL image is compiled by Red Hat, not on that workstation.
5. A site-approved process to import the final OVA into the destination environment, and suitable VMware capacity.

Do not assume these services or artifact transfers are allowed on NIPR: the site's administrators must confirm access and the approved transfer process before following this workflow there.

The normal reproduction route will fork the public build source, run the payload workflow, record its immutable GHCR digest, resolve/import the blueprint, configure console access, and build in Red Hat. A GitHub sign-in alone is insufficient if Actions or package-publishing permissions are unavailable. The CLI token encountered here could not create a repository, so creation was performed through the signed-in browser; CLI write/workflow permissions still require verification.

The existing PDF/PowerPoint describe the previous separate-payload route and are not the final v15 printed SOP; they must be revised after the v15 hosted build and offline acceptance succeed.
