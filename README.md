# Dell Latitude 5420

After installing Fedora 41 the following hardware was not working as expected:

- Ethernet port: `lspci` output `00:1f.6 Ethernet controller: Intel Corporation Ethernet Connection (13) I219-LM (rev 20)`
- Fingerprint reader: `lsusb` output `Bus 003 Device 004: ID 0a5c:5843 Broadcom Corp. BCM58200 ControlVault 3 (FingerPrint sensor + Contacted SmartCard)`

## Ethernet port

### The problem

The problem is that, while loading the appropriate module named `e1000e` by intel, the checksum check fail:

```log
[   14.664965] e1000e 0000:00:1f.6: The NVM Checksum Is Not Valid
[   14.723033] e1000e 0000:00:1f.6: probe with driver e1000e failed with error -5
```

### The fix

[This guy](https://www.dell.com/community/en/conversations/precision-mobile-workstations/precision-7560-e1000e-module-error-the-nvm-checksum-is-not-valid/647f9784f4ccf8a8dea83444?commentId=647f9d61f4ccf8a8de1b1341) seems to have the same laptop and did not manage to solve the problem :(

This discussion seems interessing: [https://superuser.com/questions/1104537/how-to-repair-the-checksum-of-the-non-volatile-memory-nvm-of-intel-ethernet-co](https://superuser.com/questions/1104537/how-to-repair-the-checksum-of-the-non-volatile-memory-nvm-of-intel-ethernet-co)
## Fingerpirnt reader

### The problem

Of course the driver are not available sine they are closed-source and Broadcome did not care enough to provide them for Linux.

### The fix

Canonical ported them to Linux (thanks) and mimiking the way they build them for Ubuntu I can do the same for Fedora.

The driver is provided already compiled in [this repo](https://git.launchpad.net/~oem-solutions-engineers/libfprint-2-tod1-broadcom/+git/libfprint-2-tod1-broadcom/).

```bash
git clone https://git.launchpad.net/~oem-solutions-engineers/libfprint-2-tod1-broadcom/+git/libfprint-2-tod1-broadcom/
```
everything is already compiled. Only copying the libraries in the proper directory will be needed for this to work.

These drivers depends on other libraries that can be found [here](https://launchpad.net/ubuntu/+source/libfprint/1:1.90.2+tod1-0ubuntu1~20.04.10):

```bash
wget --trust-server-names "https://launchpadlibrarian.net/635195496/libfprint_1.90.2+tod1.orig.tar.xz"
```

this time we need to compile the library using `meson` and `ninja`. To avoid stupid dependencies installation you can disable the documentation installation by modifying the file `meson_options.txt` by setting to `false` the _option_ `doc`.
After this, to compile:

```bash
mkdir builddir
cd builddir
meson ..
ninja
```

during the `meson` part you may need to install some _dev dependency_. Just do it until you have everything neede.

## Other resources

### Dell Command | COnfigure (not tested yet)

[This tool](https://www.dell.com/support/kbdoc/en-us/000178000/dell-command-configure) can be used to control some BIOS settings from within Linux based OSes: for example can change the battery charging threshold.
What needs to be done here is to install the packages from the link above (`.rpm`s packages are available until version `4.11.0` at the time of writing) and then run somthing like:

```bash
sudo /opt/dell/dcc/cctk --PrimaryBattChargeCfg=Custom,50,80
```

