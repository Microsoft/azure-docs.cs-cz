---
title: 'Kurz: vylepšení přístupu k webovým aplikacím – Azure Application Gateway'
description: V tomto kurzu se dozvíte, jak vytvořit automatické škálování, redundantní Aplikační bránu s rezervovanou IP adresou pomocí Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2a756313a4659dfc531289c2c86890371f700367
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452284"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Kurz: vytvoření aplikační brány, která vylepšuje přístup k webové aplikaci

Pokud jste správce IT a chcete zlepšit přístup k webovým aplikacím, můžete optimalizovat Aplikační bránu tak, aby se škálovat na základě poptávky zákazníků, a zahrnovat více zón dostupnosti. Tento kurz vám pomůže nakonfigurovat funkce Application Gateway Azure, které to dělají: automatické škálování, redundance zóny a rezervované VIP (statická IP adresa). K vyřešení problému budete používat rutiny Azure PowerShell a model nasazení Azure Resource Manager.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)
> * Vytvoření virtuální sítě automatického škálování
> * Vytvoření vyhrazené veřejné IP adresy
> * Nastavení infrastruktury služby Application Gateway
> * Určení automatického škálování
> * Vytvoření služby Application Gateway
> * Otestování aplikační brány

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento kurz vyžaduje, abyste spustili relaci Azure PowerShell pro správu místně. Musíte mít nainstalovanou verzi modulu Azure PowerShell 1.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Po ověření verze PowerShellu spusťte příkaz `Connect-AzAccount`, abyste vytvořili připojení k Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků v jednom z dostupných umístění.

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)

V případě použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem. Pro účely tohoto kurzu vytvoříte certifikát podepsaný svým držitelem (self-signed certificate) pomocí rutiny [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). K exportu souboru pfx z certifikátu můžete použít rutinu [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) s vráceným kryptografickým otiskem.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Měli byste získat podobný výsledek:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Pomocí kryptografického otisku vytvořte soubor PFX. Nahraďte *\<password>* heslem, které jste si zvolili:

```powershell
$pwd = ConvertTo-SecureString -String "<password>" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```


## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s jednou vyhrazenou podsítí pro automatické škálování aplikační brány. Aktuálně je možné v každé vyhrazené podsíti nasadit jenom jednu automaticky škálovanou Application Gateway.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Vytvoření vyhrazené veřejné IP adresy

Zadejte metodu alokace PublicIPAddress as **static**. Virtuální IP adresa automaticky škálované Application Gateway může být jenom statická. Dynamické IP adresy nejsou podporované. Podporovaná je jenom standardní skladová položka PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard -Zone 1,2,3
```

## <a name="retrieve-details"></a>Načtení podrobností

Načte podrobnosti o skupině prostředků, podsíti a IP adrese v místním objektu, aby se vytvořily podrobnosti konfigurace protokolu IP pro aplikační bránu.

```azurepowershell
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="create-web-apps"></a>Vytváření webových aplikací

Nakonfigurujte dvě webové aplikace pro fond back-end. Nahraďte *\<site1-name>* a *\<site-2-name>* jedinečnými názvy v `azurewebsites.net` doméně.

```azurepowershell
New-AzAppServicePlan -ResourceGroupName $rg -Name "ASP-01"  -Location $location -Tier Basic `
   -NumberofWorkers 2 -WorkerSize Small
New-AzWebApp -ResourceGroupName $rg -Name <site1-name> -Location $location -AppServicePlan ASP-01
New-AzWebApp -ResourceGroupName $rg -Name <site2-name> -Location $location -AppServicePlan ASP-01
```

## <a name="configure-the-infrastructure"></a>Konfigurace infrastruktury

Nakonfigurujte konfiguraci protokolu IP, front-endové konfiguraci IP adres, back-end fond, nastavení HTTP, certifikát, port, naslouchací proces a pravidlo ve stejném formátu na stávající standardní Aplikační bránu. Nová skladová položka se řídí stejným modelem objektu jako standardní skladová položka.

V definici proměnné $pool nahraďte své dva plně kvalifikované názvy domén webové aplikace (například: `mywebapp.azurewebsites.net` ).

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses <your first web app FQDN>, <your second web app FQDN>
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled -PickHostNameFromBackendAddress
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Určení automatického škálování

Nyní můžete zadat konfiguraci automatického škálování pro aplikační bránu. 

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```
V tomto režimu provádí Application Gateway automatické škálování podle vzoru provozu aplikací.

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Vytvořte Aplikační bránu a zahrňte do ní záložní zóny a konfiguraci automatického škálování.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

K získání veřejné IP adresy služby Application Gateway použijte Get-AzPublicIPAddress. Zkopírujte veřejnou IP adresu nebo název DNS a pak vložte do adresního řádku prohlížeče.

```azurepowershell
$pip = Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
$pip.IpAddress
```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejprve Prozkoumejte prostředky, které byly vytvořeny pomocí aplikační brány. Pokud už je nepotřebujete, můžete `Remove-AzResourceGroup` k odebrání skupiny prostředků, služby Application Gateway a všech souvisejících prostředků použít příkaz.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření aplikační brány s pravidly směrování založenými na cestě URL](./tutorial-url-route-powershell.md)