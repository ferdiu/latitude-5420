# Dell Latitude 5420

After installing Fedora 41 the following hardware was not working as expected:

- [ ] Ethernet port: `lspci` output `00:1f.6 Ethernet controller: Intel Corporation Ethernet Connection (13) I219-LM (rev 20)`
- [X] Fingerprint reader: `lsusb` output `Bus 003 Device 004: ID 0a5c:5843 Broadcom Corp. BCM58200 ControlVault 3 (FingerPrint sensor + Contacted SmartCard)`

## ❌ Ethernet port

### The problem

The problem is that, while loading the appropriate module named `e1000e` by intel, the checksum check fail:

```log
[   14.664965] e1000e 0000:00:1f.6: The NVM Checksum Is Not Valid
[   14.723033] e1000e 0000:00:1f.6: probe with driver e1000e failed with error -5
```

### The fix

[This guy](https://www.dell.com/community/en/conversations/precision-mobile-workstations/precision-7560-e1000e-module-error-the-nvm-checksum-is-not-valid/647f9784f4ccf8a8dea83444?commentId=647f9d61f4ccf8a8de1b1341) seems to have the same laptop and did not manage to solve the problem :(

This discussion seems interessing: [https://superuser.com/questions/1104537/how-to-repair-the-checksum-of-the-non-volatile-memory-nvm-of-intel-ethernet-co](https://superuser.com/questions/1104537/how-to-repair-the-checksum-of-the-non-volatile-memory-nvm-of-intel-ethernet-co)

## ✅ Fingerpirnt reader

### The problem

Of course the driver are not available sine they are closed-source and Broadcome did not care enough to provide them for Linux.

### The fix

I created a COPR repo to install the needed driver, courtesy of ubuntu. Instructions here: [https://copr.fedorainfracloud.org/coprs/ferdiu/libfprint-tod/](https://copr.fedorainfracloud.org/coprs/ferdiu/libfprint-tod/)

## Other resources

### Dell Command | Configure (not tested yet)

[This tool](https://www.dell.com/support/kbdoc/en-us/000178000/dell-command-configure) can be used to control some BIOS settings from within Linux based OSes: for example can change the battery charging threshold.
What needs to be done here is to install the packages from the link above (`.rpm`s packages are available until version `4.11.0` at the time of writing) and then run somthing like:

```bash
sudo /opt/dell/dcc/cctk --PrimaryBattChargeCfg=Custom,50,80
```

