```text
Title: EaseUS Partition Master EuGdiDrv.sys null SystemBuffer read

Vendor: EaseUS (CHENGDU YIWO Tech Development Co., Ltd.)
Product: EaseUS Partition Master
Component: EuGdiDrv.sys
SHA-256: 57930fe83eec60b384532659462eeb42a9e885c78fcf1a007f8e1d6177ab9e69
Size: 14472 bytes
PE timestamp: 2009-09-15 07:13:53 UTC
Version resource: none
Signer: CN="CHENGDU YIWO Tech Development Co., Ltd.", thumbprint 34589F1FB41AAC7C3542B4016C8B0D6C88DF399D
Device: \\.\eausNatvGdi1

Description:
EuGdiDrv.sys reads SystemBuffer on IOCTLs 0x80092ec4, 0x80092ec8, 0x80092ed8, and 0x80092edc without checking that the pointer is non-null. A METHOD_BUFFERED request with input and output length 0 leaves SystemBuffer null. After an administrator loads the driver, a local caller who can open \\.\eausNatvGdi1 can send one of these codes with length 0 and bugcheck the host.

Access:
Tested on 2026-10-22 on an isolated Windows 10 virtual machine. An administrator loaded the driver. The device security descriptor grants Everyone (WD) access mask 0x1201bf:
O:BAG:SYD:(A;;0x1201bf;;;WD)(A;;FA;;;SY)(A;;FA;;;BA)(A;;0x1200a9;;;RC)S:AI(ML;;NW;;;LW)
Local account aida-low, which is not in Administrators, then opened \\.\eausNatvGdi1 and sent IOCTL 0x80092ec4. Loading the driver still requires an administrator. Sending these IOCTLs does not.

Result:
An earlier session on the same isolated virtual machine sent IOCTL 0x80092ec4 with input and output length 0, and the guest went down. The other three IOCTLs share the same missing null check.

This note covers that file and those four IOCTL codes. CVE-2026-12781 identifies epmntdrv.sys. CVE-2026-12782 identifies EUEDKEPM.sys. Those are different files.

The installer version that contained this exact file was not recorded by this test.

Suggested fix: reject these IOCTLs when the input length is 0, and do not read SystemBuffer until it is known to be non-null.

CVE:
```
