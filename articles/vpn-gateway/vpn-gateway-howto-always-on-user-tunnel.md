---
title: Konfigurace tunelového připojení VPN Always On pro VPN Gateway
description: Postup konfigurace tunelového připojení VPN uživatele Always On VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846469"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurace tunelového propojení uživatelů v síti VPN Always On

Jednou z nových funkcí klienta virtuální privátní sítě (VPN) Windows 10 je schopnost udržovat připojení VPN. Always On je funkce Windows 10, která umožňuje, aby se aktivní profil sítě VPN připojoval automaticky a zůstal připojený na triggerech – konkrétně přihlášení uživatele, změna stavu sítě nebo obrazovka zařízení aktivní.

Brány virtuální sítě Azure je možné používat s Windows 10 vždy k navázání trvalých tunelů uživatelů i tunelů zařízení do Azure. Tento článek vám pomůže nakonfigurovat tunelové propojení uživatelů VPN typu Always On.

Připojení k síti VPN Always On zahrnuje dva typy tunelů:

* **Tunel zařízení** se připojí k ZADANÝM serverům VPN, než se uživatelé přihlásí k zařízení. Scénáře připojení před přihlášením a účely správy zařízení používají tunelování zařízení.

* **Tunelování uživatele** se připojí až po přihlášení uživatele k zařízení. Tunelové propojení uživatelů umožňuje uživatelům přístup k prostředkům organizace prostřednictvím serverů VPN.

Tunelové propojení zařízení i tunelové propojení uživatelů fungují nezávisle na svých profilech sítě VPN. Můžou být připojené ve stejnou dobu a můžou podle potřeby používat jiné metody ověřování a další nastavení konfigurace VPN.

## <a name="1-configure-the-gateway"></a>1. Konfigurace brány

Nakonfigurujte bránu VPN na používání protokolu IKEv2 a ověřování založeného na certifikátech pomocí tohoto [článku Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. konfigurace tunelového propojení uživatelů

1. Nainstalujte klientské certifikáty na klienta Windows 10, jak je znázorněno v tomto [článku klienta VPN typu Point-to-site](point-to-site-how-to-vpn-client-install-azure-cert.md). Certifikát musí být v úložišti aktuálního uživatele.
2. Pomocí [těchto pokynů](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)NAKONFIGURUJTE klienta VPN Always On prostřednictvím PowerShellu, SCCM nebo Intune.

### <a name="configuration-example-for-user-tunnel"></a>Příklad konfigurace pro tunelové propojení uživatelů

Po nakonfigurování brány virtuální sítě a instalaci klientského certifikátu v úložišti místního počítače na klientovi Windows 10 použijte následující příklady ke konfiguraci tunelu klientského zařízení.

1. Zkopírujte následující text a uložte ho jako ***Usercert. ps1***.

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
1. Zkopírujte následující text a uložte ho jako ***VPNProfile. XML*** ve stejné složce jako **Usercert. ps1**. Upravte následující text tak, aby odpovídal vašemu prostředí.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. Spusťte PowerShell jako správce.

1. V PowerShellu přejděte do složky, kde se nachází **Usercert. ps1** a **VPNProfile. XML** , a spusťte následující příkaz:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Podívejte se na nastavení sítě VPN.

1. Vyhledejte položku **UserTest** a klikněte na **připojit**.

1. Pokud je připojení úspěšné, pak jste úspěšně nakonfigurovali tunelové propojení vždycky na uživatele.

## <a name="cleanup"></a>Vyčištění

Chcete-li odebrat profil, spusťte následující příkaz:

1. Odpojte připojení a zrušte tuto možnost připojit automaticky.

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![Vyčištění](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Další kroky

Řešení potíží najdete v tématu [problémy s připojením Point-to-site v Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) .
