# DRAFT

# att-uefi-boot

UEFI Setup for HTTP Boot of OS image installation

Currently leveraging IPXE chain loading (see https://ipxe.org/appnote/uefihttp)

## Setup
Update DELL Server BIOS with appropriate values.

Entries that are set include:

```XML
<Component FQDD="BIOS.Setup.1-1">
<Attribute Name="BootMode">Uefi</Attribute>
<Attribute Name="HttpDev1EnDis">Enabled</Attribute>
<Attribute Name="HttpDev1Interface">NIC.Integrated.1-7-1</Attribute>
<Attribute Name="HttpDev1Protocol">IPv4</Attribute>
<Attribute Name="HttpDev1VlanEnDis">Enabled</Attribute>
<Attribute Name="HttpDev1VlanId">41</Attribute>
<Attribute Name="HttpDev1Uri">http://135.16.101.85:9080/EFI/BOOT/ipxe.efi</Attribute>
</Component>

<Component FQDD="iDRAC.Embedded.1">
 <Attribute Name="IPMILan.1#Enable">Disabled</Attribute>
 <Attribute Name="Redfish.1#Enable">Enabled</Attribute>
</Component>
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
 ```
 
 ## Control Flow
 
 1. UEFI BIOS harnesses the UEFIBootPath to chainload `ipxe.efi` from web server
 2. iPXE NBP queries DHCP server for NIC parameters and boot settings via DHCP options
 3. DHCP server responds based on pattern matching vendor-class-identifier in control flow
 
 
