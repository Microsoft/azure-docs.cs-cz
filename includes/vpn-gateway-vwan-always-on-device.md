---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371718"
---
Aby bylo možné úspěšně vytvořit tunelové propojení zařízení, musí být splněny následující požadavky:

* Zařízení musí být počítač spojený s doménou se systémem Windows 10 Enterprise nebo Education verze 1809 nebo novějším.
* Tunelové propojení je konfigurovatelné pouze pro integrované řešení VPN systému Windows a je vytvořeno pomocí protokolu IKEv2 s ověřováním o počítačových certifikátech.
* Na jedno zařízení lze nakonfigurovat pouze jeden tunelový propojení zařízení.

1. Nainstalujte klientské certifikáty do klienta Windows 10 pomocí článku [klienta VPN typu point-to-site.](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) Certifikát musí být v úložišti místního počítače.
1. Vytvořte profil sítě VPN a nakonfigurujte tunelové propojení zařízení v kontextu účtu LOCAL SYSTEM podle [těchto pokynů](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration).

### <a name="configuration-example-for-device-tunnel"></a>Příklad konfigurace pro tunelové propojení zařízení

Po konfiguraci brány virtuální sítě a instalaci klientského certifikátu v úložišti místních strojů v klientovi windows 10 použijte následující příklady konfigurace tunelového propojení klientských zařízení:

1. Zkopírujte následující text a uložte jej jako ***devicecert.ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Zkopírujte následující text a uložte jej jako ***SOUBOR VPNProfile.xml*** do stejné složky jako **devicecert.ps1**. Upravte následující text tak, aby odpovídal vašemu prostředí.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Stáhněte si **PsExec** ze [společnosti Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) a extrahujte soubory do **c:\PSTools**.
1. Z výzvy správce CMD spusťte PowerShell spuštěním:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. V Prostředí PowerShell přepněte do složky, ve které jsou umístěny **soubory devicecert.ps1** a **VPNProfile.xml,** a spusťte následující příkaz:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Spustit **rasphone**.

   ![rasphone](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Vyhledejte položku **MachineCertTest** a klepněte na tlačítko **Připojit**.

   ![Připojení](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Pokud je připojení úspěšné, restartujte počítač. Tunel se automaticky připojí.
