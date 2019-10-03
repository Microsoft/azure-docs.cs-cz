---
title: Konfigurace tunelového připojení VPN Always On pro VPN Gateway
description: Postup konfigurace tunelu Always On VPN pro VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: bc2ec2b952b4f0c6e61fc4953559fa882edfff09
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841142"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurace tunelu zařízení VPN AlwaysOn

Jednou z nových funkcí klienta virtuální privátní sítě (VPN) Windows 10 je schopnost udržovat připojení VPN. Always On je funkce Windows 10, která umožňuje, aby se aktivní profil sítě VPN připojoval automaticky a zůstal připojený na triggerech – konkrétně přihlášení uživatele, změna stavu sítě nebo obrazovka zařízení aktivní.

Brány virtuální sítě Azure je možné používat s Windows 10 vždy k navázání trvalých tunelů uživatelů i tunelů zařízení do Azure. Tento článek vám pomůže nakonfigurovat tunelové propojení zařízení VPN typu Always ON.

Připojení k síti VPN Always On zahrnuje dva typy tunelů:

* **Tunel zařízení** se připojí k ZADANÝM serverům VPN, než se uživatelé přihlásí k zařízení. Scénáře připojení před přihlášením a účely správy zařízení používají tunelování zařízení.

* **Tunelování uživatele** se připojí až po přihlášení uživatele k zařízení. Tunelové propojení uživatelů umožňuje uživatelům přístup k prostředkům organizace prostřednictvím serverů VPN.

Tunelové propojení zařízení i tunelové propojení uživatelů fungují nezávisle na svých profilech sítě VPN. Můžou být připojené ve stejnou dobu a můžou podle potřeby používat jiné metody ověřování a další nastavení konfigurace VPN.

## <a name="1-configure-the-gateway"></a>1. Konfigurace brány

Nakonfigurujte bránu VPN na používání protokolu IKEv2 a ověřování založeného na certifikátech pomocí tohoto [článku Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-device-tunnel"></a>2. konfigurace tunelového připojení zařízení

Aby bylo možné úspěšně vytvořit tunelové zařízení, musí být splněny následující požadavky:

* Zařízení musí být počítač připojený k doméně, na kterém běží Windows 10 Enterprise nebo vzdělávací verze 1709 nebo novější.
* Tunelové propojení je konfigurovatelné pouze pro integrované řešení sítě VPN systému Windows a je vytvořeno pomocí protokolu IKEv2 s ověřováním certifikátů počítače. 
* Pro každé zařízení se dá nakonfigurovat jenom jedno tunelové zařízení.

1. Nainstalujte klientské certifikáty na klienta Windows 10, jak je znázorněno v tomto [článku klienta VPN typu Point-to-site](point-to-site-how-to-vpn-client-install-azure-cert.md). Certifikát musí být v úložišti místního počítače.
1. Pomocí [těchto pokynů](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) můžete vytvořit profil sítě VPN a nakonfigurovat tunel zařízení v kontextu místního systémového účtu.

### <a name="configuration-example-for-device-tunnel"></a>Příklad konfigurace pro tunelové zařízení

Po nakonfigurování brány virtuální sítě a instalaci klientského certifikátu v úložišti místního počítače na klientovi Windows 10 použijte následující příklady ke konfiguraci tunelu klientského zařízení.

1. Zkopírujte následující text a uložte ho jako ***devicecert. ps1***.

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
1. Zkopírujte následující text a uložte ho jako ***VPNProfile. XML*** ve stejné složce jako **devicecert. ps1**. Upravte následující text tak, aby odpovídal vašemu prostředí.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

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
1. Stáhněte si **PsExec** ze společnosti [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) a extrahujte soubory do **C:\PSTools**.
1. Z příkazového řádku správce spusťte PowerShell spuštěním příkazu:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Prostředí](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. V PowerShellu přejděte do složky, kde se nachází **devicecert. ps1** a **VPNProfile. XML** , a spusťte následující příkaz:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Spusťte **Rasphone**.

   ![Firma](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Vyhledejte položku **MachineCertTest** a klikněte na **připojit**.

   ![Propojení](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Pokud je připojení úspěšné, restartujte počítač. Tunel se automaticky připojí.

## <a name="cleanup"></a>Vyčištění

Chcete-li odebrat profil, spusťte následující příkaz:

![Vyčištění](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Další kroky

Řešení potíží najdete v tématu [problémy s připojením Point-to-site v Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) .
