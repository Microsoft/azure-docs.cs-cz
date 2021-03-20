---
title: Použití API Management v Virtual Network s Application Gateway
titleSuffix: Azure API Management
description: Přečtěte si, jak nastavit a nakonfigurovat Azure API Management v interních Virtual Network s Application Gateway (WAF) jako front-endu.
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 3db1c8bfc3a11151342589af0873d88e3d90c6a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91825632"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrace služby API Management v interní virtuální síti se službou Application Gateway

## <a name="overview"></a><a name="overview"></a> Přehled

Službu API Management lze nakonfigurovat v Virtual Network v interním režimu, který je přístupný pouze v rámci Virtual Network. Azure Application Gateway je služba PAAS, která poskytuje nástroj pro vyrovnávání zatížení vrstvy 7. Funguje jako služba reverzního proxy serveru a nabízí z nabídky bránu firewall webových aplikací (WAF).

Kombinování API Management zřízené v interní virtuální síti s Application Gateway front-endu umožňuje následující scénáře:

* Použijte stejný prostředek API Management pro spotřebu interních i externích spotřebitelů.
* Použijte jeden prostředek API Management a mít podmnožinu rozhraní API definovanou v API Management k dispozici pro externí příjemce.
* Poskytněte způsob, jak přepnout přístup k API Management z veřejného internetu na a vypnout.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete postupovat podle kroků popsaných v tomto článku, musíte mít:

* Musíte mít aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certifikáty-PFX a CER pro název hostitele rozhraní API a PFX pro název hostitele portálu pro vývojáře.

## <a name="scenario"></a><a name="scenario"></a> Scénář

Tento článek popisuje, jak používat jednu API Management službu pro interní i externí spotřebitele a učinit ji jako jeden front-end pro místní i cloudová rozhraní API. Také se dozvíte, jak vystavit jenom podmnožinu vašich rozhraní API (v příkladu, kde jsou zvýrazněna zeleně) pro externí spotřebu pomocí funkce směrování dostupné v Application Gateway.

V prvním příkladu instalace jsou všechna vaše rozhraní API spravovaná jenom v rámci vašeho Virtual Network. Interní spotřebitelé (zvýrazněné oranžová) mají přístup ke všem vašim interním a externím rozhraním API. Provoz nikdy nepřijde na Internet. Připojení s vysokým výkonem se dodává přes okruhy Express Route.

![trasa URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"></a> Než začnete

* Ujistěte se, že používáte nejnovější verzi prostředí Azure PowerShell. Pokyny k instalaci najdete v tématu [instalace Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co je potřeba k vytvoření integrace mezi API Management a Application Gateway?

* **Fond back-end serverů:** Toto je interní virtuální IP adresa služby API Management.
* **Nastavení fondu back-end serverů:** Každý fond má nastavení, jako je port, protokol a spřažení na základě souborů cookie. Tato nastavení se aplikují na všechny servery v rámci fondu.
* **Port front-end:** Toto je veřejný port, který se otevírá ve službě Application Gateway. Provoz se přesměruje na jeden ze serverů back-end.
* **Naslouchací proces:** Naslouchací proces má front-end port, protokol (http nebo https, u těchto hodnot se rozlišují malá a velká písmena) a název certifikátu TLS/SSL (Pokud se konfiguruje přesměrování zpracování TLS).
* **Pravidlo:** Pravidlo váže naslouchací proces na fond back-end serverů.
* **Vlastní sonda stavu:** Application Gateway ve výchozím nastavení používá ke zjištění, které servery v BackendAddressPool jsou aktivní, nástroje testy založené na IP adresách. Služba API Management reaguje jenom na žádosti se správnou hlavičkou hostitele, takže výchozí sondy selžou. Je potřeba definovat vlastní sondu stavu, která bude pomáhat aplikační bráně zjistit, jestli je služba aktivní, a měla by překládat požadavky.
* **Vlastní certifikáty domény:** Chcete-li získat přístup k API Management z Internetu, je třeba vytvořit mapování CNAME svého názvu hostitele na Application Gateway název DNS front-endu. Tím se zajistí, že záhlaví a certifikát hostitele odeslaného do Application Gateway, který předáte do API Management, je jedním APIM, který může rozpoznat jako platný. V tomto příkladu použijeme dva certifikáty – pro back-end a portál pro vývojáře.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"></a> Kroky vyžadované pro integraci API Management a Application Gateway

1. Vytvoření skupiny prostředků pro Resource Manager
2. Pro Application Gateway vytvořte Virtual Network, podsíť a veřejnou IP adresu. Vytvořte další podsíť pro API Management.
3. Vytvořte službu API Management v podsíti virtuální sítě vytvořené výše a ujistěte se, že používáte interní režim.
4. Nastavte vlastní název domény ve službě API Management.
5. Vytvořte objekt konfigurace Application Gateway.
6. Vytvořte prostředek Application Gateway.
7. Vytvořte CNAME z veřejného názvu DNS Application Gateway k názvu hostitele API Management proxy serveru.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Zpřístupnění portálu pro vývojáře externě prostřednictvím Application Gateway

V této příručce zveřejníme také portál pro **vývojáře** pro externí cílové skupiny prostřednictvím Application Gateway. Vyžaduje další kroky pro vytvoření naslouchacího procesu, sondy, nastavení a pravidel portálu pro vývojáře. Všechny podrobnosti jsou k dispozici v příslušných krocích.

> [!WARNING]
> Pokud používáte ověřování Azure AD nebo třetí strany, povolte v Application Gateway funkci [spřažení relace na základě souborů cookie](../application-gateway/features.md#session-affinity) .

> [!WARNING]
> Chcete-li zabránit Application Gateway WAF z narušení stahování specifikace OpenAPI na portálu pro vývojáře, je nutné zakázat pravidlo brány firewall `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` .
> 
> Application Gateway WAF pravidla, která můžou narušit funkčnost portálu, zahrnují:
> 
> - `920300`, `920330` , `931130` , `942100` , `942110` , `942180` , `942200` , `942260` , `942340` , `942370` pro režim správy
> - `942200`, `942260` , `942370` , `942430` , `942440` pro publikovaný portál

## <a name="create-a-resource-group-for-resource-manager"></a>Vytvoření skupiny prostředků pro Resource Manager

### <a name="step-1"></a>Krok 1

Přihlaste se k Azure.

```powershell
Connect-AzAccount
```

Proveďte ověření pomocí vašich přihlašovacích údajů.

### <a name="step-2"></a>Krok 2

Vyberte požadované předplatné.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Krok 3

Vytvořte skupinu prostředků (pokud používáte některou ze stávajících skupin prostředků, můžete tenhle krok přeskočit).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. To slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Ujistěte se, že všechny příkazy k vytvoření služby Application Gateway používají stejnou skupinu prostředků.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Vytvoření Virtual Network a podsítě pro aplikační bránu

Následující příklad ukazuje, jak vytvořit Virtual Network pomocí Správce prostředků.

### <a name="step-1"></a>Krok 1

Přiřaďte proměnné podsítě rozsah adres 10.0.0.0/24, který se má použít pro Application Gateway při vytváření Virtual Network.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Krok 2

Přiřaďte k proměnné podsítě rozsah adres 10.0.1.0/24, který se má použít pro API Management při vytváření Virtual Network.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Krok 3

Vytvořte Virtual Network s názvem **appgwvnet** ve skupině prostředků **APIM-appGw-RG** pro oblast západní USA. Použijte předponu 10.0.0.0/16 s podsítěmi 10.0.0.0/24 a 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Přiřaďte proměnnou podsítě pro další kroky.

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Vytvoření služby API Management ve virtuální síti nakonfigurované v interním režimu

Následující příklad ukazuje, jak vytvořit službu API Management ve virtuální síti nakonfigurovanou pouze pro interní přístup.

### <a name="step-1"></a>Krok 1

Vytvořte objekt API Management Virtual Network pomocí podsítě $apimsubnetdata vytvořené výše.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Krok 2

Vytvoří službu API Management v rámci Virtual Network.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Po úspěšném provedení výše uvedeného příkazu se podívejte na [konfiguraci DNS, která je nutná pro přístup k interní službě VNET API Management](api-management-using-with-internal-vnet.md#apim-dns-configuration) pro přístup k ní. Tento krok může trvat déle než půl hodiny.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Nastavení vlastního názvu domény v API Management

> [!IMPORTANT]
> [Nový portál pro vývojáře](api-management-howto-developer-portal.md) také vyžaduje povolení připojení ke koncovému bodu správy API Management kromě následujících kroků.

### <a name="step-1"></a>Krok 1

Inicializujte následující proměnné s podrobnostmi o certifikátech a soukromých klíčích pro domény. V tomto příkladu použijeme `api.contoso.net` a `portal.contoso.net` .  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Krok 2

Vytvořte a nastavte konfigurační objekty hostname pro proxy server a portál.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Pokud chcete nakonfigurovat starší připojení portálu pro vývojáře, musíte ho `-HostnameType DeveloperPortal` nahradit `-HostnameType Portal` .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Vytvoření veřejné IP adresy pro front-end konfiguraci

Ve skupině prostředků vytvořte **publicIP01** prostředku veřejné IP adresy.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

IP adresa je ke službě Application Gateway přiřazena při spuštění služby.

## <a name="create-application-gateway-configuration"></a>Vytvořit konfiguraci aplikační brány

Před vytvořením služby Application Gateway musí být nastaveny všechny položky konfigurace. Následující kroky slouží k vytvoření položek konfigurace potřebné pro prostředek služby Application Gateway.

### <a name="step-1"></a>Krok 1

Vytvořte konfiguraci protokolu IP služby Application Gateway s názvem **gatewayIP01**. Při spuštění služby Application Gateway se předá IP adresa z nakonfigurované podsítě a síťový provoz se bude směrovat na IP adresy ve fondu back-end IP adres. Uvědomte si, že každá instance vyžaduje jednu IP adresu.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Krok 2

Nakonfigurujte port front-end IP adresy pro koncový bod veřejné IP adresy. Tento port je port, ke kterému se koncoví uživatelé připojují.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Krok 3

Nakonfigurujte IP adresu front-endu s koncovým bodem s veřejnou IP adresou.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Krok 4

Nakonfigurujte certifikáty pro Application Gateway, které se použijí k dešifrování a opětovnému šifrování provozu, který prochází.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Krok 5

Vytvořte naslouchací proces HTTP pro Application Gateway. Přiřaďte k nim certifikáty konfigurace front-end IP adresy, portu a TLS/SSL.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Krok 6

Vytvořte vlastní testy pro `ContosoApi` koncový bod domény proxy serveru služby API Management. Tato cesta `/status-0123456789abcdef` je výchozím koncovým bodem stavu hostovaným na všech službách API Management. Nastavte `api.contoso.net` jako vlastní název hostitele testu, abyste ho zabezpečili pomocí certifikátu TLS/SSL.

> [!NOTE]
> Název hostitele `contosoapi.azure-api.net` je výchozím názvem proxy hostitele nakonfigurovaným při vytvoření služby s názvem `contosoapi` ve veřejném Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/internal-status-0123456789abcdef" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Nahrajte certifikát, který se má použít u prostředků back-endu s povoleným protokolem TLS. Jedná se o stejný certifikát, který jste zadali v kroku 4 výše.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Krok 8

Nakonfigurujte nastavení back-endu protokolu HTTP pro Application Gateway. To zahrnuje nastavení časového limitu pro požadavek na back-end, po kterém se zruší. Tato hodnota se liší od časového limitu testu.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Nakonfigurujte fond back-end IP adres s názvem **apimbackend**  s interní virtuální IP adresou služby API Management vytvořené výše.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Krok 10

Vytvořte pravidla pro Application Gateway pro použití základního směrování.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Změňte-RuleType a směrování, abyste omezili přístup k určitým stránkám portálu pro vývojáře.

### <a name="step-11"></a>Krok 11

Nakonfigurujte počet instancí a velikost Application Gateway. V tomto příkladu používáme [WAF SKU](../web-application-firewall/ag/ag-overview.md) pro zvýšené zabezpečení prostředku API Management.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Krok 12

Nakonfigurujte WAF tak, aby byl v režimu prevence.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Vytvořit Application Gateway

Vytvořte Application Gateway se všemi objekty konfigurace z předchozích kroků.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME API Management název hostitele proxy serveru k veřejnému názvu DNS prostředku Application Gateway

Po vytvoření brány je dalším krokem konfigurace front-endu pro komunikaci. Při použití veřejné IP adresy Application Gateway vyžaduje dynamicky přiřazený název DNS, který možná nebude možné snadno použít.

Název DNS Application Gateway by měl být použit k vytvoření záznamu CNAME, který odkazuje na název hostitele proxy serveru APIM (např. `api.contoso.net` v předchozích příkladech) na tento název DNS. Pokud chcete nakonfigurovat záznam CNAME protokolu IP, načtěte podrobnosti o Application Gateway a jeho přidruženém názvu IP/DNS pomocí elementu PublicIPAddress. Použití záznamů A se nedoporučuje, protože VIP se může při restartování brány změnit.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"></a> Souhrn
Azure API Management nakonfigurovaný ve virtuální síti poskytuje rozhraní jediné brány pro všechna nakonfigurovaná rozhraní API, ať už jsou hostovaná místně nebo v cloudu. Integrace Application Gateway s API Management poskytuje flexibilitu selektivního povolení konkrétních rozhraní API k přístupu na internetu a poskytování firewallu webových aplikací jako front-endu vaší instance API Management.

## <a name="next-steps"></a><a name="next-steps"></a> Další kroky
* Další informace o Azure Application Gateway
  * [Přehled Application Gateway](../application-gateway/overview.md)
  * [Application Gateway Firewall webových aplikací](../web-application-firewall/ag/ag-overview.md)
  * [Application Gateway pomocí směrování založeného na cestách](../application-gateway/tutorial-url-route-powershell.md)
* Další informace o API Management a virtuální sítě
  * [Použití API Management k dispozici pouze v rámci virtuální sítě](api-management-using-with-internal-vnet.md)
  * [Použití API Management ve virtuální síti](api-management-using-with-vnet.md)
