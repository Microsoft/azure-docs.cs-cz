---
title: Jak používat správu rozhraní API ve virtuální síti s aplikační bránou
titleSuffix: Azure API Management
description: Zjistěte, jak nastavit a nakonfigurovat Azure API Management v interní virtuální síti s aplikační bránou (WAF) jako FrontEnd
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
ms.openlocfilehash: 2b8cf66afa1d8aa592d5755ebab70cd6ad2e75fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298049"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrace správy rozhraní API v interní virtuální síti s aplikační bránou

## <a name="overview"></a><a name="overview"> </a> Přehled

Službu API Management lze nakonfigurovat ve virtuální síti v interním režimu, což ji zpřístupňuje pouze z virtuální sítě. Azure Application Gateway je služba PAAS, která poskytuje vyrovnávání zatížení vrstvy 7. Funguje jako reverzní proxy služba a poskytuje mimo jiné webovou aplikační bránu firewall (WAF).

Kombinace správy rozhraní API zřízené v interní virtuální síti s front-endem aplikační brány umožňuje následující scénáře:

* Použijte stejný prostředek správy rozhraní API pro spotřebu interními i externími příjemci.
* Použijte jeden prostředek správy rozhraní API a mít podmnožinu rozhraní API definované ve správě rozhraní API k dispozici pro externí spotřebitele.
* Poskytněte způsob, jak přepnout přístup ke správě rozhraní API ze veřejného Internetu ze veřejného Internetu.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li postupovat podle kroků popsaných v tomto článku, musíte mít:

* Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certifikáty - pfx a cer pro název hostitele rozhraní API a pfx pro název hostitele vývojářského portálu.

## <a name="scenario"></a><a name="scenario"> </a> Scénář

Tento článek popisuje, jak používat jednu službu správy rozhraní API pro interní i externí spotřebitele a zjidřete ji jako jeden front-end pro místní i cloudová rozhraní API. Zobrazí se také způsob, jak vystavit pouze podmnožinu rozhraní API (v příkladu, který jsou zvýrazněny zeleně) pro externí spotřebu pomocí funkce směrování k dispozici v bráně aplikace.

V prvním příkladu nastavení jsou všechna vaše api spravována pouze z virtuální sítě. Interní spotřebitelé (zvýrazněné oranžově) mají přístup ke všem interním i externím finančním udávaní. Provoz nikdy nevychází na internet. Vysoce výkonná konektivita je dodávána prostřednictvím obvodů Express Route.

![trasa url](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"> </a> Než začnete

* Ujistěte se, že používáte nejnovější verzi prostředí Azure PowerShell. Podívejte se na pokyny k instalaci na [webu Instalace Prostředí Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co je potřeba k vytvoření integrace mezi správou rozhraní API a aplikační bránou?

* **Fond serverů back-end:** Toto je interní virtuální IP adresa služby API Management.
* **Nastavení fondu serverů back-end:** Každý fond má nastavení, jako je port, protokol a spřažení založené na souborech cookie. Tato nastavení jsou použita pro všechny servery ve fondu.
* **Přední port:** Toto je veřejný port, který se otevře v bráně aplikace. Provoz bít to dostane přesměrován na jeden z back-end serverů.
* **Posluchač:** Naslouchací proces má front-end port, protokol (Http nebo Https, tyto hodnoty jsou malá a velká písmena) a název certifikátu SSL (pokud konfigurace ssl offload).
* **Pravidlo:** Pravidlo váže naslouchací proces do fondu serveru back-end.
* **Vlastní sonda stavu:** Aplikační brána ve výchozím nastavení používá sondy založené na IP adresách k zjištění, které servery v backendaddresspoolu jsou aktivní. Služba api management reaguje pouze na požadavky se správnou hlavičkou hostitele, proto se nezdaří výchozí sondy. Vlastní sonda stavu musí být definována pomoci aplikační brány určit, že služba je aktivní a měla by předávat požadavky.
* **Vlastní certifikáty domény:** Chcete-li získat přístup ke správě rozhraní API z Internetu, musíte vytvořit mapování CNAME jeho názvu hostitele na název DNS front-endu aplikační brány. Tím je zajištěno, že hlavička názvu hostitele a certifikát odeslaný do aplikační brány, která je předána správě rozhraní API, je jeden rozhraní APIM, které lze rozpoznat jako platné. V tomto příkladu použijeme dva certifikáty – pro back-end a pro portál pro vývojáře.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"> </a> Kroky potřebné pro integraci správy rozhraní API a aplikační brány

1. Vytvoření skupiny prostředků pro Resource Manager
2. Vytvořte virtuální síť, podsíť a veřejnou IP adresu pro aplikační bránu. Vytvořte další podsíť pro správu rozhraní API.
3. Vytvořte službu správy rozhraní API uvnitř výše vytvořené podsítě virtuální sítě a ujistěte se, že používáte vnitřní režim.
4. Nastavte vlastní název domény ve službě Api Management.
5. Vytvořte konfigurační objekt aplikační brány.
6. Vytvořte prostředek aplikační brány.
7. Vytvořte CNAME z veřejného názvu DNS aplikační brány na název hostitele proxy služby API Management.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Externí vystavení vývojářského portálu prostřednictvím aplikační brány

V této příručce také zpřístupníme **portál pro vývojáře** externím cílovým skupinám prostřednictvím aplikační brány. Vyžaduje další kroky k vytvoření naslouchací proces, sonda, nastavení a pravidla portálu pro vývojáře. Všechny podrobnosti jsou uvedeny v příslušných krocích.

> [!WARNING]
> Pokud používáte Azure AD nebo ověřování třetích stran, povolte funkci [spřažení relací založených na souborech cookie](../application-gateway/features.md#session-affinity) v application gateway.

> [!WARNING]
> Chcete-li zabránit tomu, aby služba WAF aplikace gateway přerušila stahování specifikace `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`OpenAPI na portálu pro vývojáře, je třeba zakázat pravidlo brány firewall .

## <a name="create-a-resource-group-for-resource-manager"></a>Vytvoření skupiny prostředků pro Resource Manager

### <a name="step-1"></a>Krok 1

Přihlaste se k Azure.

```powershell
Connect-AzAccount
```

Ověřte pomocí svých přihlašovacích údajů.

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

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. To slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Ujistěte se, že všechny příkazy k vytvoření aplikační brány používají stejnou skupinu prostředků.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Vytvoření virtuální sítě a podsítě pro aplikační bránu

Následující příklad ukazuje, jak vytvořit virtuální síť pomocí Správce prostředků.

### <a name="step-1"></a>Krok 1

Přiřaďte rozsah adres 10.0.0.0/24 proměnné podsítě, která má být použita pro aplikační bránu při vytváření virtuální sítě.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Krok 2

Přiřaďte rozsah adres 10.0.1.0/24 proměnné podsítě, která má být použita pro správu rozhraní API při vytváření virtuální sítě.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Krok 3

Vytvořte virtuální síť s názvem **appgwvnet** v apim skupiny **prostředků-appGw-RG** pro oblast Západní USA. Použijte předponu 10.0.0.0/16 s podsítěmi 10.0.0.0/24 a 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Přiřazení proměnné podsítě pro další kroky

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Vytvoření služby správy rozhraní API uvnitř virtuální sítě nakonfigurované v interním režimu

Následující příklad ukazuje, jak vytvořit službu správy rozhraní API ve virtuální síti nakonfigurované pouze pro interní přístup.

### <a name="step-1"></a>Krok 1

Vytvořte objekt virtuální sítě pro správu rozhraní API pomocí výše vytvořené podsítě $apimsubnetdata.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Krok 2

Vytvořte službu správy rozhraní API uvnitř virtuální sítě.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Po úspěšném provedení výše uvedeného příkazu se podívejte na [konfiguraci DNS potřebnou pro přístup k interní službě Správy rozhraní API virtuální sítě](api-management-using-with-internal-vnet.md#apim-dns-configuration) pro přístup k ní. Tento krok může trvat déle než půl hodiny.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Nastavení vlastního názvu domény ve správě rozhraní API

> [!IMPORTANT]
> [Nový portál pro vývojáře](api-management-howto-developer-portal.md) také vyžaduje povolení připojení ke koncovému bodu správy správy rozhraní API kromě níže uvedených kroků.

### <a name="step-1"></a>Krok 1

Inicializovat následující proměnné s podrobnostmi o certifikáty s privátní klíče pro domény. V tomto příkladu `api.contoso.net` použijeme a `portal.contoso.net`.  

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

Vytvořte a nastavte objekty konfigurace názvu hostitele pro proxy server a pro portál.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Chcete-li nakonfigurovat starší připojení `-HostnameType DeveloperPortal` portálu pro vývojáře, je třeba jej nahradit aplikací `-HostnameType Portal`.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Vytvoření veřejné IP adresy pro front-end konfiguraci

Vytvořte veřejný prostředek IP **publicIP01** ve skupině prostředků.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

IP adresa je ke službě Application Gateway přiřazena při spuštění služby.

## <a name="create-application-gateway-configuration"></a>Vytvoření konfigurace aplikační brány

Před vytvořením služby Application Gateway musí být nastaveny všechny položky konfigurace. Následující kroky slouží k vytvoření položek konfigurace potřebné pro prostředek služby Application Gateway.

### <a name="step-1"></a>Krok 1

Vytvořte konfiguraci IP adresy aplikační brány s názvem **gatewayIP01**. Při spuštění služby Application Gateway se předá IP adresa z nakonfigurované podsítě a síťový provoz se bude směrovat na IP adresy ve fondu back-end IP adres. Uvědomte si, že každá instance vyžaduje jednu IP adresu.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Krok 2

Nakonfigurujte port IP front-end pro veřejný koncový bod IP. Tento port je port, ke kterému se koncoví uživatelé připojují.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Krok 3

Nakonfigurujte IP adresu front-endu s koncovým bodem s veřejnou IP adresou.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Krok 4

Nakonfigurujte certifikáty pro aplikační bránu, které budou použity k dešifrování a opětovnému šifrování procházejícího provozu.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Krok 5

Vytvořte http posluchače pro aplikační bránu. Přiřaďte jim certifikáty front-end IP, port a ssl.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Krok 6

Vytvořte vlastní sondy `ContosoApi` do koncového bodu proxy domény služby API Management. Cesta `/status-0123456789abcdef` je výchozí koncový bod stavu hostovaný ve všech službách správy rozhraní API. Nastavte `api.contoso.net` jako vlastní název hostitele sondy, abyste jej zabezpečili pomocí certifikátu SSL.

> [!NOTE]
> Název `contosoapi.azure-api.net` hostitele je výchozí název hostitele proxy `contosoapi` nakonfigurovaný při vytvoření služby s názvem ve veřejném Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Nahrajte certifikát, který se má použít na prostředky back-endového fondu s podporou ssl. Jedná se o stejný certifikát, který jste zadali v kroku 4 výše.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Krok 8

Nakonfigurujte nastavení back-endu HTTP pro aplikační bránu. To zahrnuje nastavení časového limitu pro požadavek back-endu, po kterém jsou zrušeny. Tato hodnota se liší od časového rozlišení sondy.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Nakonfigurujte fond back-endových IP adres s názvem **apimbackend** s interní virtuální IP adresou výše vytvořené služby API Management.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Krok 10

Vytvořte pravidla pro aplikační bránu pro použití základního směrování.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Změňte -RuleType a směrování, chcete-li omezit přístup k určitým stránkám portálu pro vývojáře.

### <a name="step-11"></a>Krok 11

Nakonfigurujte počet instancí a velikost aplikační brány. V tomto příkladu používáme [skladovou položku WAF](../application-gateway/application-gateway-webapplicationfirewall-overview.md) pro zvýšení zabezpečení prostředku správy rozhraní API.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Krok 12

Nakonfigurujte waf tak, aby byl v režimu Prevence.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Vytvořit aplikační bránu

Vytvořte aplikační bránu se všemi konfiguračními objekty z předchozích kroků.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Název hostitele serveru Proxy pro správu rozhraní API pro veřejný název DNS prostředku aplikační brány

Po vytvoření brány je dalším krokem konfigurace front-endu pro komunikaci. Při použití veřejné IP, Application Gateway vyžaduje dynamicky přiřazený název DNS, který nemusí být snadné použití.

Název DNS aplikační brány by měl být použit k vytvoření záznamu CNAME, který odkazuje `api.contoso.net` na název proxy serveru APIM (např. ve výše uvedených příkladech) na tento název DNS. Chcete-li nakonfigurovat front-endový záznam CNAME protokolu IP, načtěte podrobnosti o bráně aplikace a jejím přidruženém názvu IP/DNS pomocí elementu PublicIPAddress. Použití A-záznamů se nedoporučuje, protože VIP může změnit při restartování brány.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"> </a> Shrnutí
Azure API Management nakonfigurovaný ve Virtuální síti poskytuje rozhraní s jednou bránou pro všechna nakonfigurovaná rozhraní API, ať už jsou hostovaná místně nebo v cloudu. Integrace brány aplikací se správou rozhraní API poskytuje flexibilitu selektivního povolení přístupu konkrétních rozhraní API k Internetu a také poskytování brány firewall webových aplikací jako front-end u instance správy rozhraní API.

## <a name="next-steps"></a><a name="next-steps"> </a> Další kroky
* Další informace o Azure Application Gateway
  * [Přehled aplikační brány](../application-gateway/application-gateway-introduction.md)
  * [Brána firewall webové aplikace brány aplikace](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Aplikační brána používající směrování založené na cestě](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Další informace o správě rozhraní API a virtuálních bankách
  * [Použití správy rozhraní API dostupné pouze v rámci virtuální sítě](api-management-using-with-internal-vnet.md)
  * [Použití správy rozhraní API ve virtuální netu](api-management-using-with-vnet.md)
