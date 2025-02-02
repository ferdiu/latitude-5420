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

## Fingerpirnt reader

### The problem

### The fix
