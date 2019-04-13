
## DHCP Settings - to allow FOG Server to do network imaging with PXE Boot

### DHCP Settings for Endian Firewall
```
option space PXE;
option PXE.mtftp-ip    code 1 = ip-address;
option PXE.mtftp-cport code 2 = unsigned integer 16;
option PXE.mtftp-sport code 3 = unsigned integer 16;
option PXE.mtftp-tmout code 4 = unsigned integer 8;
option PXE.mtftp-delay code 5 = unsigned integer 8;
option arch code 93 = unsigned integer 16; # RFC4578
use-host-decl-names on;
ddns-update-style interim;
ignore client-updates;
authoritative;
allow booting;
allow bootp;
option option-128 code 128 = string;
option option-129 code 129 = text;
next-server fog.ed;
#filename "pxelinux.0";
#filename "snponly.efi";
#filename "ipxe.efi";
option tftp-server-name "fog.ed";
#option bootfile-name "pelinux.0";
#option bootfile-name "undionly.kpxe";  # works for vmplayer
#option bootfile-name "snponly.efi";
#option bootfile-name "ipxe.efi";
#range dynamic-bootp 192.168.10.25 192.168.10.28;

class "UEFI-32-1" {
    match if substring(option vendor-class-identifier, 0, 20) = "PXEClient:Arch:00006";
    filename "i386-efi/ipxe.efi";
    }

    class "UEFI-32-2" {
    match if substring(option vendor-class-identifier, 0, 20) = "PXEClient:Arch:00002";
     filename "i386-efi/ipxe.efi";
    }

    class "UEFI-64-1" {
    match if substring(option vendor-class-identifier, 0, 20) = "PXEClient:Arch:00007";
     #filename "ipxe.efi";
     filename "snp.efi";  # works for hyperv
    }

    class "UEFI-64-2" {
    match if substring(option vendor-class-identifier, 0, 20) = "PXEClient:Arch:00008";
     #filename "ipxe.efi";
     filename "snp.efi"; # Works for hyper v
    }

    class "UEFI-64-3" {
    match if substring(option vendor-class-identifier, 0, 20) = "PXEClient:Arch:00009";
     #filename "ipxe.efi";
     filename "snp.efi"; # Works for hyperv
    }

    class "Legacy" {
    match if substring(option vendor-class-identifier, 0, 20) = "PXEClient:Arch:00000";
    filename "undionly.kkpxe";
    }
```