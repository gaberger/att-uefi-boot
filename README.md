# DRAFT

# att-uefi-boot

UEFI Setup for HTTP Boot of OS image installation

Currently leveraging IPXE chain loading (see https://ipxe.org/appnote/uefihttp)

## Setup
Update DELL Server BIOS with appropriate values.

Entries that are set include:

```XML
<SystemConfiguration>
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
</SystemConfiguration>	
```
```XML
<SystemConfiguration>
	<Component FQDD="RAID.Slot.6-1">
		<Attribute Name="RAIDresetConfig">True</Attribute>
		<Attribute Name="RAIDforeignConfig">Clear</Attribute>
		<Attribute Name="CurrentControllerMode">RAID</Attribute>
		<Attribute Name="RAIDrekey">False</Attribute>
		<Attribute Name="EncryptionMode">None</Attribute>
		<!-- <Attribute Name="KeyID"></Attribute> -->
		<!-- <Attribute Name="OldControllerKey">******</Attribute> -->
		<!-- <Attribute Name="NewControllerKey">******</Attribute> -->
		<Attribute Name="RAIDprMode">Automatic</Attribute>
		<Attribute Name="RAIDPatrolReadUnconfiguredArea">Enabled</Attribute>
		<Attribute Name="RAIDloadBalancedMode">Automatic</Attribute>
		<Attribute Name="RAIDccMode">Normal</Attribute>
		<Attribute Name="RAIDcopybackMode">On</Attribute>
		<Attribute Name="RAIDControllerBootMode">Continue Boot On Error</Attribute>
		<Attribute Name="RAIDEnhancedAutoImportForeignConfig">Disabled</Attribute>
		<Attribute Name="RAIDrebuildRate">80</Attribute>
		<Attribute Name="RAIDbgiRate">30</Attribute>
		<Attribute Name="RAIDccRate">30</Attribute>
		<Attribute Name="RAIDreconstructRate">30</Attribute>
		<Component FQDD="Disk.Virtual.0:RAID.Slot.6-1">
			<Attribute Name="RAIDaction">Create</Attribute>
			<Attribute Name="LockStatus">Unlocked</Attribute>
			<Attribute Name="RAIDinitOperation">None</Attribute>
			<!-- <Attribute Name="T10PIStatus">Disabled</Attribute> -->
			<Attribute Name="DiskCachePolicy">Default</Attribute>
			<Attribute Name="RAIDdefaultWritePolicy">WriteBack</Attribute>
			<Attribute Name="RAIDdefaultReadPolicy">ReadAhead</Attribute>
			<Attribute Name="Name">root</Attribute>
			<Attribute Name="Size">479559942144</Attribute>
			<Attribute Name="StripeSize">128</Attribute>
			<Attribute Name="SpanDepth">1</Attribute>
			<Attribute Name="SpanLength">2</Attribute>
			<Attribute Name="RAIDTypes">RAID 1</Attribute>
			<Attribute Name="IncludedPhysicalDiskID">Disk.Bay.0:Enclosure.Internal.0-1:RAID.Slot.6-1</Attribute>
			<Attribute Name="IncludedPhysicalDiskID">Disk.Bay.1:Enclosure.Internal.0-1:RAID.Slot.6-1</Attribute>
		</Component>
		<Component FQDD="Disk.Virtual.1:RAID.Slot.6-1">
			<Attribute Name="RAIDaction">create</Attribute>
			<Attribute Name="LockStatus">Unlocked</Attribute>
			<Attribute Name="RAIDinitOperation">None</Attribute>
			<!-- <Attribute Name="T10PIStatus">Disabled</Attribute> -->
			<Attribute Name="DiskCachePolicy">Default</Attribute>
			<Attribute Name="RAIDdefaultWritePolicy">WriteBack</Attribute>
			<Attribute Name="RAIDdefaultReadPolicy">ReadAhead</Attribute>
			<Attribute Name="Name">ceph</Attribute>
			<Attribute Name="Size">479559942144</Attribute>
			<Attribute Name="StripeSize">128</Attribute>
			<Attribute Name="SpanDepth">1</Attribute>
			<Attribute Name="SpanLength">2</Attribute>
			<Attribute Name="RAIDTypes">RAID 1</Attribute>
			<Attribute Name="IncludedPhysicalDiskID">Disk.Bay.2:Enclosure.Internal.0-1:RAID.Slot.6-1</Attribute>
			<Attribute Name="IncludedPhysicalDiskID">Disk.Bay.3:Enclosure.Internal.0-1:RAID.Slot.6-1</Attribute>
		</Component>
		<Component FQDD="Enclosure.Internal.0-1:RAID.Slot.6-1">
			<Component FQDD="Disk.Bay.0:Enclosure.Internal.0-1:RAID.Slot.6-1">
				<Attribute Name="RAIDHotSpareStatus">No</Attribute>
				<Attribute Name="RAIDPDState">Ready</Attribute>
			</Component>
			<Component FQDD="Disk.Bay.1:Enclosure.Internal.0-1:RAID.Slot.6-1">
				<Attribute Name="RAIDHotSpareStatus">No</Attribute>
				<Attribute Name="RAIDPDState">Ready</Attribute>
			</Component>
		</Component>
		<Component FQDD="Enclosure.Internal.0-1:RAID.Slot.6-1">
			<Component FQDD="Disk.Bay.2:Enclosure.Internal.0-1:RAID.Slot.6-1">
				<Attribute Name="RAIDHotSpareStatus">No</Attribute>
				<Attribute Name="RAIDPDState">Ready</Attribute>
			</Component>
			<Component FQDD="Disk.Bay.3:Enclosure.Internal.0-1:RAID.Slot.6-1">
				<Attribute Name="RAIDHotSpareStatus">No</Attribute>
				<Attribute Name="RAIDPDState">Ready</Attribute>
			</Component>
		</Component>
		<Attribute Name="RAIDremoveControllerKey">False</Attribute>
	</Component>
</SystemConfiguration>
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
 
 
