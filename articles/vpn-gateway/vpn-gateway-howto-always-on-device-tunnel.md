---
title: Konfigurace tunelu Always On VPN pro VPN Gateway
description: Postup konfigurace tunelu Always On VPN pro VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 81822297dcf9370fc8ce7f7ce0285689c31606ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695831"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurace tunelu zařízení VPN AlwaysOn

Mezi nové funkce klienta Windows 10 virtuální privátní sítě (VPN) je schopnost zachovat připojení k síti VPN. Always On je funkce Windows 10, která umožňuje active profil sítě VPN pro připojení automaticky a zůstanou připojené založené na aktivačních událostí – konkrétně, přihlášení, změny stavu sítě nebo aktivní obrazovce zařízení.

Branám virtuálních sítí Azure je možné s Windows 10 Always On k navázání tunely trvalá, jakož i tunely zařízení do Azure. Tento článek vám pomůže nakonfigurovat zařízení tunelové propojení sítě VPN Always ON.

Připojení Always On VPN zahrnují dva typy tunelová propojení:

* **Tunelové propojení zařízení** předtím, než se uživatelé registrují zařízení se připojí k zadané servery sítě VPN. Scénáře připojení před přihlášením a účely správy zařízení pomocí tunelového připojení zařízení.

* **Uživatel tunel** pouze po přihlášení uživatele zařízení připojí. Tunel uživatele umožňuje uživatelům přístup k prostředkům organizace pomocí serverů VPN.

Tunelové propojení zařízení a uživatele tunel pracovat nezávisle na sobě s jejich profilů sítě VPN. Lze připojit současně a podle potřeby můžete použít různé metody ověřování a další nastavení konfigurace sítě VPN.

## <a name="1-configure-the-gateway"></a>1. Konfigurace brány

Konfigurace brány VPN pomocí IKEv2 a použití této funkce ověřování prostřednictvím certifikátu [point-to-site článku](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Konfigurace uživatele tunelového propojení

1. Nainstalujte klientské certifikáty na straně klienta Windows 10, jak je znázorněno v tomto [článku klienta VPN typu point-to-site](point-to-site-how-to-vpn-client-install-azure-cert.md). Certifikát musí být ve Store aktuálního uživatele
2. Konfigurace klienta Always On VPN pomocí Powershellu, SCCM nebo Intune [tyto pokyny](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

## <a name="3-configure-the-device-tunnel"></a>3. Konfigurace zařízení tunelového propojení

Aby bylo možné úspěšně vytvořit tunelové propojení zařízení musí být splněny následující požadavky:

* Zařízení musí být připojené k doméně počítač domény se systémem Windows 10 Enterprise nebo Education verze 1709 nebo novější.
* Tunelové propojení pouze se dají konfigurovat pro integrované řešení VPN Windows a je vytvořeno pomocí ověřování certifikátů počítače IKEv2. 
* Na zařízení se dá nakonfigurovat jenom jedno zařízení tunelu.

1. Nainstalujte klientské certifikáty na straně klienta Windows 10, jak je znázorněno v tomto [článku klienta VPN typu point-to-site](point-to-site-how-to-vpn-client-install-azure-cert.md). Certifikát se musí být v úložišti místního počítače.
1. Použití [tyto pokyny](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) vytvoření profilu sítě VPN a konfigurace zařízení tunelu v kontextu účtu místního systému.

### <a name="configuration-example-for-device-tunnel"></a>Příklad konfigurace pro zařízení tunelového propojení

Poté, co jste nakonfigurovali bránu virtuální sítě a nainstalovali klientský certifikát v úložišti místního počítače na klientovi Windows 10, použijte následující příklady konfigurace tunelu klienta zařízení.

1. Zkopírujte následující text a uložte ho jako ***devicecert.ps1***.

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
1. Zkopírujte následující text a uložte ho jako ***VPNProfile.xml*** ve stejné složce jako **devicecert.ps1**. Upravte následující text, aby odpovídaly vašemu prostředí.

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
1. Stáhněte si **PsExec** z [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) a rozbalte soubory do **C:\PSTools**.
1. Z příkazového řádku správce řádku spusťte PowerShell spuštěním:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Prostředí PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. V Powershellu přejděte do složky, kde **devicecert.ps1** a **VPNProfile.xml** nacházejí, a spusťte následující příkaz:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Spustit **rasphone**.

   ![Rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Hledat **MachineCertTest** položku a klikněte na tlačítko **připojit**.

   ![Připojení](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Pokud se připojování povede, restartujte počítač. Tunelové propojení se automaticky připojí.

## <a name="cleanup"></a>Vyčištění

Chcete-li odebrat profil, spusťte následující příkaz:

![Vyčištění](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Další postup

Řešení potíží, najdete v části [problémy s připojením Azure point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
