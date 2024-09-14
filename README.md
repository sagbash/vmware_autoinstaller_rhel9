# vmware_autoinstaller_rhel9
This is the configuration of a RHEL9 server and its services to install VMWare on bare-metal servers.

I wanted to install VMWare Esxi on my bare-metal servers with full-automated installation. In order to do this I have utilized PXEBoot and followed the VMWare documentation for it.

For this job three services are needed. DHCP, TFTP and HTTP.
1 server machine has been used, the services are installed and configured under it.

The DHCP server needs to on the same LAN with the bare-metal server.

tftp service serves the directory /var/lib/tftpboot
DHCP is for assigning IP address and guide to server to PXE boot with the options 66,67.
The bare-metal server will get the necessary bootloader and VMWare files from the TFTP server.
At the end the kickstart.cfg file (ks.cfg) will be provided from http server.



This is the file structure of the tftboot directory.
I have mounted the vmware esxi iso under the vmware directory.
I have copied the necessary efi and cfg files from vmware/efi/boot to tftboot/ directory.
boot.cfg is edited and ks parameter is added. prefix may be edited if it is necessary.
Note: Prefix is added before the module names for example my modules are under /vmware directory so my prefix is vmware. The module names had a leading / in their names I have removed all of the leading / . 
VMWare documentation asked bootx64.efi file to be renamed mboot.efi
I have find mboot.c32 file from SYSLINUX-3.86

```
└── var
    └── lib
        └── tftpboot
            ├── crypto64.efi
            ├── mboot.c32
            ├── mboot.efi
            ├── boot.cfg
            ├── pxelinux.cfg
                └── default
			      │
            └── vmware
                ├── efi
                │   └── boot
                │       └── crypto64.efi
		        │       └── boot.cfg
		        │       └── safeboot.efi
		        │       └── bootx64.efi
                └── mboot.c32
                └── (vmware library files)
                

```

This is the pxelinux.cfg/default file..
```
DEFAULT esxi
        LABEL esxi
        kernel mboot.c32
        append -c boot.cfg
        ipappend 2
```

append -c boot.cfg gives the boot.cfg under the directory /var/lib/tftpboot

