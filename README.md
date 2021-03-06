## open-iscsi RPMs for Photon 4.0 VMs with SolidFire iSCSI SAN

VMware Photon 4.0 comes with iSCSI SRPMs. You can build your own or get these to experiment with
 
Why? Because then Photon 4.0 can be [used](https://scaleoutsean.github.io/2022/03/11/vmware-photon-iscsi-solidfire.html) with NetApp SolidFire or other iSCSI targets.

Tested with SolidFire 12.3, both direct OS access and containerized apps using NetApp Trident Docker Volume Plugin.

For those who haven't read the documentation: newer Linux systems attaching to SolidFire 12.3 and below must use MD5 as the CHAP authentication algorithm. Set it in iscsid.conf.

### Version 2.1.3-2 (recommended)

These binaries were built from VMware Photon OS open-iscsi SRPMs.

Get the v2.1.3-tagged archive file from [Releases](https://github.com/scaleoutsean/photon-solidfire/tags), unarchive and install with RPM. Configure as usual.

Known issues:

- sometimes iSCSI reports that only a limited number of iSCSI targets can be logged in to, which is likely Photon OS-related (same problem was reported by RancherOS users). At least one device always works, the second and above you may get errors

### Version 2.1.6-2 (experimental)

These were built from open-iscsi from Github using a hacked Photon 4.0 SPEC file. I thought 2.1.6 may work better but I'm not sure if it does (I didn't try enough to be able to tell).

Known issues:

- sometimes iSCSI reports that only a limited number of iSCSI targets can be logged in to, which is likely Photon OS-related (same problem was reported by RancherOS users). At least one device always works, the second and above you may get errors
- /usr/lib/systemd/system/iscsid.service, /usr/lib/systemd/system/iscsi.service, /usr/lib/systemd/system/iscsiuio.service have `@SBINDIR@` instead of `/usr/sbin` in them. Just replace former with the latter and reboot before you enable iSCSI services
- no man pages are included in these RPMs

The last two issues appear because the SRPMS spec file which I modelled after Photon OS's open-iscsi 2.1.3, needs changes to account for changes in open-iscsi 2.1.6:

- use "sbindir" for path in systemd service files
- add man page for the iscsi-gen-initiatorname script

**UPDATE:** I reluctantly documented the above details thinking they won't be needed in the long run, but it didn't take long - [Makefile](https://github.com/open-iscsi/open-iscsi/commit/fd14dd8316b1317d3ee2ff28b9d013198be40d70) was overhauled so this problem may be gone in post-v2.1.6 releases!

Get the v2.1.6-tagged archive file from [Releases](https://github.com/scaleoutsean/photon-solidfire/tags), unarchive and install with RPM. Then run a search-and-replace on service files to fix the SBINDIR issue above and you can start, enable and configure iSCSI.

