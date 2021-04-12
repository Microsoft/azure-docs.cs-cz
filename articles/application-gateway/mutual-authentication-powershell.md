---
title: Konfigurace vzájemného ověřování v Azure Application Gateway prostřednictvím PowerShellu
description: Naučte se nakonfigurovat Application Gateway, aby se vzájemně procházelo vzájemné ověřování prostřednictvím PowerShellu.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 95534760c09ca9e1f7f09d6079886216127c7eb0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231393"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>Konfigurace vzájemného ověřování pomocí Application Gateway prostřednictvím PowerShellu (Preview)
Tento článek popisuje, jak pomocí PowerShellu nakonfigurovat vzájemné ověřování v Application Gateway. Vzájemné ověřování znamená, Application Gateway ověřování klienta odesílajícího žádosti pomocí certifikátu klienta, který nahrajete do Application Gateway. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento článek vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

## <a name="before-you-begin"></a>Než začnete

Ke konfiguraci vzájemného ověřování pomocí Application Gateway potřebujete klientský certifikát pro nahrání do brány. Klientský certifikát se použije k ověření certifikátu, ke kterému bude klient Application Gateway. Pro účely testování můžete použít certifikát podepsaný svým držitelem. Nedoporučuje se ale pro produkční úlohy, protože jsou těžší je spravovat a nejsou úplně zabezpečené.

Další informace, zejména o tom, jaké typy klientských certifikátů můžete nahrát, najdete v tématu [Přehled vzájemného ověřování pomocí Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Nejdřív v předplatném vytvořte novou skupinu prostředků. 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nasaďte virtuální síť pro Application Gateway, která se má nasadit v.

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>Vytvoření veřejné IP adresy

Vytvořte veřejnou IP adresu pro použití s vaším Application Gateway. 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>Vytvoření konfigurace Application Gateway IP

Vytvořte konfiguraci protokolu IP a port front-endu. 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>Konfigurace protokolu SSL front-endu 

Nakonfigurujte certifikáty SSL pro váš Application Gateway.

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>Konfigurace ověřování klientů 

Nakonfigurujte ověřování klientů v Application Gateway. Další informace o tom, jak extrahovat řetězy certifikátů důvěryhodných klientů, které se mají použít, najdete v tématu [postup extrakce řetězů certifikátů certifikační autority důvěryhodných klientů](./mutual-authentication-certificate-management.md).

> [!IMPORTANT]
> Ujistěte se prosím, že jste nahráli celý řetěz certifikátů klientské certifikační autority do jednoho souboru a jenom jeden řetězec na soubor.  

> [!NOTE]
> Doporučujeme používat TLS 1,2 se vzájemným ověřováním, protože TLS 1,2 bude v budoucnu pověřit. 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>Konfigurace fondu a nastavení back-endu

Nastavte back-end fond a nastavení pro Application Gateway. Volitelně můžete nastavit důvěryhodný kořenový certifikát back-end pro komplexní šifrování SSL.  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>Konfigurovat pravidlo

Nastavte na svém Application Gateway pravidlo.

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>Nastavení výchozích zásad protokolu SSL pro budoucí naslouchací procesy

Při nastavování vzájemného ověřování jste nastavili zásady protokolu SSL specifické pro naslouchací proces. V tomto kroku můžete volitelně nastavit výchozí zásady protokolu SSL pro budoucí naslouchací procesy, které vytvoříte. 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>Vytvoření Application Gateway

Pomocí všeho, co jsme vytvořili, nasaďte Application Gateway.

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků, aplikační bránu a všechny související prostředky pomocí [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>Prodloužit platnost certifikátů certifikační autority klienta

V případě vypršení platnosti certifikátu klientské certifikační autority můžete certifikát v bráně aktualizovat pomocí následujících kroků: 

1. Přihlášení k Azure
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. Získat konfiguraci Application Gateway
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. Odebrat důvěryhodný klientský certifikát z brány 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. Přidat nový certifikát do brány 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. Aktualizace brány pomocí nového certifikátu 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>Další kroky

- [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)