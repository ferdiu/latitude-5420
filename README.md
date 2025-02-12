# Dell Latitude 5420

After installing Fedora 41 the following hardware was not working as expected:

- [X] Ethernet port: `lspci` output `00:1f.6 Ethernet controller: Intel Corporation Ethernet Connection (13) I219-LM (rev 20)`
- [X] Fingerprint reader: `lsusb` output `Bus 003 Device 004: ID 0a5c:5843 Broadcom Corp. BCM58200 ControlVault 3 (FingerPrint sensor + Contacted SmartCard)`

## ✅ Ethernet port

### The problem

The problem is that, while loading the appropriate module named `e1000e` by intel, the checksum check fail:

```log
[   14.664965] e1000e 0000:00:1f.6: The NVM Checksum Is Not Valid
[   14.723033] e1000e 0000:00:1f.6: probe with driver e1000e failed with error -5
```

The ethernet controller is properly working: just the NVM check is failing.

### The fix

#### TL;DR

The soultion is to install the patched kernel module provided [here](https://copr.fedorainfracloud.org/coprs/ferdiu/kernel-modules/), using akmod.

#### Long story

[This guy](https://www.dell.com/community/en/conversations/precision-mobile-workstations/precision-7560-e1000e-module-error-the-nvm-checksum-is-not-valid/647f9784f4ccf8a8dea83444?commentId=647f9d61f4ccf8a8de1b1341) seems to have the same laptop and did not manage to solve the problem :(

This discussion seems interessing: [https://superuser.com/questions/1104537/how-to-repair-the-checksum-of-the-non-volatile-memory-nvm-of-intel-ethernet-co](https://superuser.com/questions/1104537/how-to-repair-the-checksum-of-the-non-volatile-memory-nvm-of-intel-ethernet-co)

The problem can be fixed just ignoring the bad result of the checksum validation (this is the behaviour of the same drivers on Windows). The in-tree kernel module `e1000e` can be patched to ignore this problem. Of course this workaround works only if the adapter is working: if the validation failed because of a faulty hardware this won't help.

On my hardware, no matter what, I could not rewrite NVM to fix the problem so I had to make the _temporary_ solution _definitive_ by patching the kernel module in a maintainable way: I created an akmod package for this driver [here](https://github.com/ferdiu/akmod-e1000e-no-nvm-check).

## ✅ Fingerprint reader

### The problem

Of course the driver are not available sine they are closed-source and Broadcome did not care enough to provide them for Linux.

### The fix

I created a COPR repo to install the needed driver, courtesy of ubuntu. Instructions here: [https://copr.fedorainfracloud.org/coprs/ferdiu/libfprint-tod/](https://copr.fedorainfracloud.org/coprs/ferdiu/libfprint-tod/)

## Other resources

### ✅ Dell Command | Configure

[This tool](https://www.dell.com/support/kbdoc/en-us/000178000/dell-command-configure) can be used to control some BIOS settings from within Linux based OSes: for example can change the battery charging threshold.
What needs to be done here is to install the packages from the link above (`.rpm`s packages are available until version `4.11.0` at the time of writing) and then run somthing like:

```bash
sudo /opt/dell/dcc/cctk --PrimaryBattChargeCfg=Custom:50-80
```

see [this](https://www.dell.com/support/manuals/it-it/command-configure-v4.1/dcc_cli_4.1.0/dell-command-|-configure-options?guid=guid-3bf52184-7423-4b6b-8aba-4c1c61c96770&lang=en-us) for reference.
