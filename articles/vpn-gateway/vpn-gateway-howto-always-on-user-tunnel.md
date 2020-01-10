---
title: Konfigurace tunelového propojení uživatelů při trvalém připojení
titleSuffix: Azure VPN Gateway
description: Tento článek popisuje, jak pro vaši bránu VPN nakonfigurovat uživatelské tunelové propojení VPN typu Always On.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: bff2ed48a78bfbae984dea5e5474971817023bc6
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75729317"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurace tunelu uživatele VPN AlwaysOn

Novou funkcí klienta sítě VPN s Windows 10, která je vždycky zapnutá, je možnost udržovat připojení VPN. Díky službě Always On se aktivní profil sítě VPN může připojit automaticky a zůstat připojený na triggerech, jako jsou přihlášení uživatele, změna stavu sítě nebo obrazovka zařízení aktivní.

Brány virtuální sítě Azure s Windows 10 se vždycky používají k navázání trvalých tunelů uživatelů a tunelových připojení zařízení do Azure. Tento článek vám pomůže nakonfigurovat tunelové propojení uživatelů VPN typu Always On.

Připojení k síti VPN Always On zahrnuje jeden ze dvou typů tunelů:

* **Tunelové propojení zařízení**: připojí se k zadaným serverům sítě VPN, než se uživatelé přihlásí k zařízení. Scénáře připojení před přihlášením a Správa zařízení používají tunelové zařízení.

* **Tunelové propojení uživatelů**: připojuje se až po přihlášení uživatelů k zařízení. Pomocí tunelů uživatelů můžete získat přístup k prostředkům organizace prostřednictvím serverů VPN.

Tunely zařízení a tunely uživatelů fungují nezávisle na jejich profilech sítě VPN. Můžou být připojené ve stejnou dobu a můžou podle potřeby používat jiné metody ověřování a další nastavení konfigurace VPN.

V následujících částech nakonfigurujete bránu VPN a tunel uživatele.

## <a name="step-1-configure-a-vpn-gateway"></a>Krok 1: Konfigurace služby VPN Gateway

Bránu VPN nakonfigurujete tak, aby používala ověřování IKEv2 a založený na certifikátech podle pokynů v tomto článku [Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="step-2-configure-a-user-tunnel"></a>Krok 2: konfigurace tunelového propojení uživatele

1. Nainstalujte klientské certifikáty na klienta Windows 10, jak je znázorněno v tomto článku [klienta VPN typu Point-to-site](point-to-site-how-to-vpn-client-install-azure-cert.md) . Certifikát musí být v úložišti aktuálního uživatele.

1. Nakonfigurujete klienta VPN Always On prostřednictvím PowerShellu, System Center Configuration Manager nebo Intune podle pokynů v tématu [Konfigurace připojení k síti VPN na straně klienta s Windows 10](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="example-configuration-for-the-user-tunnel"></a>Příklad konfigurace pro tunelové propojení uživatelů

Po nakonfigurování brány virtuální sítě a instalaci klientského certifikátu v úložišti místního počítače na klientovi Windows 10 nakonfigurujte tunelové zařízení klienta pomocí následujících příkladů:

1. Zkopírujte následující text a uložte ho jako *Usercert. ps1*:

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
1. Zkopírujte následující text a uložte ho jako *VPNProfile. XML* ve stejné složce jako *Usercert. ps1*. Upravte následující text tak, aby odpovídal vašemu prostředí:

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<PrefixSize>32</PrefixSize>     <= Subnet mask`

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

1. V PowerShellu přejděte do složky, kde se nachází *Usercert. ps1* a *VPNProfile. XML* , a spusťte následující příkaz:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. V části **nastavení sítě VPN**vyhledejte položku **UserTest** a pak vyberte **připojit**.

1. Pokud je připojení úspěšné, úspěšně jste nakonfigurovali tunelové propojení vždycky na uživatele.

## <a name="clean-up-your-resources"></a>Vyčištění prostředků

Pokud chcete profil odebrat, udělejte toto:

1. Spusťte následující příkaz:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Odpojte připojení a zrušte zaškrtnutí políčka **Připojit automaticky** .

![Vyčištění](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy s připojením, ke kterým může dojít, přečtěte si téma [problémy s připojením Point-to-site v Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
