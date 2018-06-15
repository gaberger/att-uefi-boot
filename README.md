# att-uefi-boot

UEFI Setup for HTTP Boot of OS image installation

Currently leveraging IPXE chain loading (see https://ipxe.org/appnote/uefihttp)

## Setup
Update DELL Server BIOS with appropriate values.

Entries that are set include:

```XML
<Attribute Name="BootMode">Uefi</Attribute>
<Attribute Name="HttpDev1EnDis">Enabled</Attribute>
<Attribute Name="HttpDev1Interface">NIC.Integrated.1-3-1</Attribute>
<Attribute Name="HttpDev1Protocol">IPv4</Attribute>
<Attribute Name="HttpDev1VlanEnDis">Enabled</Attribute>
<Attribute Name="HttpDev1VlanId">41</Attribute>
<Attribute Name="HttpDev1Uri">http://135.16.101.85:9080/EFI/BOOT/grubx64.efi</Attribute>
```       

## Update IPXE chain loading image
```make bin-x86_64-efi/ipxe.efi EMBED=../../script.ipxe```


## ISC-DHCPD Configuration

```
 if option client-architecture = encode-int ( 16, 16 ) {
     option vendor-class-identifier "HTTPClient";
     filename "http://my.web.server/ipxe.efi";
  } else {
     filename "http://my.web.server/script.ipxe";
  }
 ``
