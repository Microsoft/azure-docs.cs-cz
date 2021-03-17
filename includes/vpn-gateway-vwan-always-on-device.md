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
ms.openlocfilehash: 34e841a5f17d589c4fbef54a4a8674a99ac6c640
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027059"
---
Aby bylo možné úspěšně vytvořit tunelové zařízení, musí být splněny následující požadavky:

* Zařízení musí být počítač připojený k doméně, na kterém běží Windows 10 Enterprise nebo vzdělávací verze 1809 nebo novější.
* Tunelové propojení je konfigurovatelné pouze pro integrované řešení sítě VPN systému Windows a je vytvořeno pomocí protokolu IKEv2 s ověřováním certifikátů počítače.
* Pro každé zařízení se dá nakonfigurovat jenom jedno tunelové zařízení.

1. Nainstalujte klientské certifikáty na klienta Windows 10 pomocí článku [klienta VPN typu Point-to-site](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) . Certifikát musí být v úložišti místního počítače.
1. Pomocí [těchto pokynů](/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)vytvořte profil sítě VPN a nakonfigurujte tunel zařízení v kontextu místního systémového účtu.

### <a name="configuration-example-for-device-tunnel"></a>Příklad konfigurace pro tunelové zařízení

Po nakonfigurování brány virtuální sítě a instalaci klientského certifikátu v úložišti místního počítače na klientovi Windows 10 použijte následující příklady ke konfiguraci tunelu klientského zařízení:

1. Zkopírujte následující text a uložte ho jako ***devicecert.ps1** _.

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
1. Zkopírujte následující text a uložte ho jako _*_VPNProfile.xml_*_ ve stejné složce jako _ * devicecert.ps1 * *. Upravte následující text tak, aby odpovídal vašemu prostředí.

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
1. Stáhněte si **PsExec** ze společnosti [Sysinternals](/sysinternals/downloads/psexec) a extrahujte soubory do **C:\PSTools**.
1. Z příkazového řádku správce spusťte PowerShell spuštěním příkazu:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Snímek obrazovky s příkazem pro spuštění 64 verze prostředí PowerShell zobrazuje okno příkazového řádku.](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. V PowerShellu přejděte do složky, kde jsou umístěné **devicecert.ps1** a **VPNProfile.xml** a spusťte následující příkaz:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![Snímek obrazovky zobrazuje okno prostředí PowerShell, které spouští MachineCertTest pomocí skriptu devicesert.](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Spusťte **Rasphone**.

   ![Snímek obrazovky s vybraným dialogovým oknem Rasphone zobrazuje dialogové okno spuštění.](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Vyhledejte položku **MachineCertTest** a klikněte na **připojit**.

   ![Snímek obrazovky se zobrazí dialogové okno Síťová připojení s vybraným MachineCertTest a tlačítkem připojit.](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Pokud je připojení úspěšné, restartujte počítač. Tunel se automaticky připojí.