---
title: Jak používat Azure API Management ve virtuální síti pomocí služby Application Gateway | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a nakonfigurovat Azure API Management v interní virtuální síti s Application Gateway (WAF) jako front-endu
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: c7d4351a9691c9787c42107306220e075f8648a0
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435119"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integraci služby API Management v interní virtuální síti pomocí služby Application Gateway

##<a name="overview"> </a> Přehled

Služba API Management je možné nakonfigurovat ve virtuální síti v interní režim, který je přístupný pouze z v rámci virtuální sítě. Azure Application Gateway je služba PAAS, které poskytuje nástroje pro vyrovnávání zatížení vrstvy 7. Funguje jako služba reverzních proxy serverů a poskytuje mezi jeho nabídky Firewall webových aplikací (WAF).

Kombinování zřizována jako interní virtuální síť s front-endový aplikační brány API Management umožňuje následující scénáře:

* Použijte stejný prostředek API Management k využití v příjemci interní a externí uživatele.
* Použijte jeden prostředek API Management a mají podmnožinu rozhraní API definované ve službě API Management je dostupná pro externí uživatele.
* Zadejte klíč způsob, jak zapnout a vypnout přepnout přístup do API managementu z veřejného Internetu.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést postup popsaný v tomto článku, budete potřebovat:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Instanci služby APIM. Další informace najdete v tématu [vytvoření instance Azure API Management](get-started-create-service-instance.md).

##<a name="scenario"> </a> Scénář
Tento článek popisuje způsob použití jediné služby API Management pro interní a externí uživatele a fungují jako jeden front-endu pro obě místní a cloudové rozhraní API. Zobrazí se také, jak vystavit pouze podmnožinu vašich rozhraní API (v příkladu, které jsou zvýrazněny zeleně) pro externí spotřebu pomocí PathBasedRouting funkce, která je dostupná ve službě Application Gateway.

V prvním příkladu nastavení všechna svoje rozhraní API spravují pouze v rámci vaší virtuální sítě. Interní příjemci (zvýrazněná v oranžová) můžete přistupovat všechna vaše interní a externí rozhraní API. Provoz nikdy nedostane mimo vysoký výkon se doručí Internet prostřednictvím okruhů Expressroute.

![Adresa URL trasy](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Než začnete

1. Nainstalujte nejnovější verzi rutin prostředí Azure PowerShell pomocí instalační služby webové platformy. Nejnovější verzi můžete stáhnout a nainstalovat v části **Windows PowerShell** na stránce [Položky ke stažení](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Vytvoření virtuální sítě a vytvořte oddělené podsítě pro API Management a služba Application Gateway.
3. Pokud chcete vytvořit vlastní server DNS pro virtuální síť, učiňte tak před zahájením nasazení. Překontrolujte, které funguje díky zajištění virtuálního počítače vytvořeného v nové podsíti ve virtuální síti může přeložit a přístup k všechny koncové body služeb Azure.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co je potřeba k vytvoření integrace mezi službami API Management a služba Application Gateway?

* **Fond back-end serverů:** jde o interní virtuální IP adresu služby API Management.
* **Nastavení fondu back-end serverů:** Každý fond má nastavení, jako je port, protokol a spřažení na základě souborů cookie. Tato nastavení se použijí na všechny servery v rámci fondu.
* **Front-end port:** Toto je veřejný port, který se otevírá ve službě application gateway. Přenosů kontaktujících ho přesměruje na jeden back-end serverů.
* **Naslouchací proces:** Naslouchací proces má front-end port, protokol (Http nebo Https, u těchto hodnot se rozlišují malá a velká písmena) a název certifikátu SSL (pokud se konfiguruje přesměrování zpracování SSL).
* **Pravidlo:** pravidlo váže naslouchací proces pro fond back endového serveru.
* **Vlastní sondy stavu:** Application Gateway, ve výchozím nastavení, použije IP adres na základě sondy zjistit, které servery v BackendAddressPool jsou aktivní. API Management, kterou služba pouze reaguje na požadavky, které mají hlavičku hostitele správná, proto testy výchozí nezdaří. Sonda stavu vlastní musí definovat tak, aby pomůže zjistit, zda služba je aktivní a předávat požadavky službě application gateway.
* **Certifikát vlastní domény:** z Internetu je potřeba vytvořit mapování CNAME z jeho názvu hostitele front-endu název DNS Application Gateway přístup k rozhraní API Management. Tím se zajistí, že záhlaví názvu hostitele a certifikát odesílat Application Gateway, která je předána API Management je ten, který APIM dokáže rozpoznat jako platný.

## <a name="overview-steps"> </a> Kroků potřebných pro integraci služby API Management a služba Application Gateway

1. Vytvoření skupiny prostředků pro Resource Manager
2. Vytvořte virtuální síť, podsíť a veřejnou IP adresu pro službu Application Gateway. Vytvoření další podsítě pro službu API Management.
3. Vytvoření služby API Management v podsíti virtuální sítě vytvořené výše a ujistěte se, že používáte interní režimu.
4. Nastavení názvu vlastní domény ve službě API Management.
5. Vytvoření objektu konfigurace aplikační brány.
6. Vytvoří prostředek služby Application Gateway.
7. Vytvořte záznam CNAME z veřejného názvu DNS služby Application Gateway na název hostitele proxy API Management.

## <a name="create-a-resource-group-for-resource-manager"></a>Vytvoření skupiny prostředků pro Resource Manager

Ujistěte se, že používáte nejnovější verzi prostředí Azure PowerShell. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager).

### <a name="step-1"></a>Krok 1

Přihlášení k Azure

```powershell
Connect-AzureRmAccount
```

Ověření pomocí přihlašovacích údajů.<BR>

### <a name="step-2"></a>Krok 2

Zkontrolujte předplatná pro daný účet a vyberte ji.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Krok 3

Vytvořte skupinu prostředků (pokud používáte některou ze stávajících skupin prostředků, můžete tenhle krok přeskočit).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. To slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Ujistěte se, že všechny příkazy k vytvoření služby application gateway používají stejnou skupinu prostředků.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Vytvořte virtuální síť a podsíť pro službu application gateway

Následující příklad ukazuje, jak vytvořit virtuální síť pomocí zdroj správce.

### <a name="step-1"></a>Krok 1

Přiřaďte rozsah adres 10.0.0.0/24 proměnné podsítě, která má být použit pro službu Application Gateway při vytváření virtuální sítě.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Krok 2

Přiřaďte 10.0.1.0/24 rozsah adres proměnné podsítě, která má být použit pro službu API Management při vytváření virtuální sítě.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Krok 3

Vytvoření virtuální sítě s názvem **appgwvnet** ve skupině prostředků **apim-appGw-RG** pro oblast západní USA pomocí předpony 10.0.0.0/16 s podsítí 10.0.0.0/24 a 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Přiřaďte proměnnou podsítě pro další kroky

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Vytvoření služby API Management v síti VNET nakonfigurovaný v interní režimu

Následující příklad ukazuje, jak vytvořit služby API Management ve virtuální síti nakonfigurovat interní pouze pro přístup.

### <a name="step-1"></a>Krok 1
Vytvořte virtuální síť pro správu rozhraní API objektu pomocí podsítě $apimsubnetdata vytvořili výše.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Krok 2
Vytvoření služby API Management ve virtuální síti.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
Po úspěšném výše uvedený příkaz najdete [konfiguraci DNS vyžadovaných pro přístup k vnitřní chybě služby VNET API Management](api-management-using-with-internal-vnet.md#apim-dns-configuration) k němu přistupovat.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Nastavení vlastního názvu domény ve službě API Management

### <a name="step-1"></a>Krok 1
Nahrajte certifikát s privátním klíčem pro doménu. V tomto příkladu bude `*.contoso.net`.

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Krok 2
Po nahrání certifikátu vytvořte objekt konfigurace názvu hostitele pro proxy server s názvem hostitele z `api.contoso.net`, protože poskytuje autority pro certifikát příklad `*.contoso.net` domény.

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Vytvoření veřejné IP adresy pro front-end konfiguraci

Vytvořte prostředek veřejné IP adresy **adresy publicIP01** ve skupině prostředků **apim-appGw-RG** pro oblast západní USA.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

IP adresa je ke službě Application Gateway přiřazena při spuštění služby.

## <a name="create-application-gateway-configuration"></a>Vytvoření konfigurace aplikační brány

Před vytvořením služby Application Gateway musí být nastaveny všechny položky konfigurace. Následující kroky slouží k vytvoření položek konfigurace potřebné pro prostředek služby Application Gateway.

### <a name="step-1"></a>Krok 1

Vytvořte konfiguraci IP adresy služby Application Gateway s názvem **gatewayIP01**. Při spuštění služby Application Gateway se předá IP adresa z nakonfigurované podsítě a síťový provoz se bude směrovat na IP adresy ve fondu back-end IP adres. Uvědomte si, že každá instance vyžaduje jednu IP adresu.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Krok 2

Nakonfigurujte port front-end IP pro koncový bod veřejné IP adresy. Tento port je port, který koncoví uživatelé připojit.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>Krok 3

Nakonfigurujte IP adresu front-endu s koncovým bodem s veřejnou IP adresou.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Krok 4

Konfigurace certifikátu pro službu Application Gateway, použít k dešifrování a znovu šifrovat provoz procházející.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Krok 5

Vytvořte naslouchací proces protokolu HTTP služby Application Gateway. Jí přiřadíte front-end IP konfigurace, portu a protokolu ssl certifikát.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Krok 6

Vytvoření vlastního testu paměti pro službu API Management `ContosoApi` koncový bod domény proxy serveru. Cesta `/status-0123456789abcdef` je výchozího stavu pomocí koncových bodů hostované na všechny služby API Management. Nastavte `api.contoso.net` jako název hostitele vlastní test paměti k zabezpečení pomocí certifikátu protokolu SSL.

> [!NOTE]
> Název hostitele `contosoapi.azure-api.net` je výchozí název hostitele proxy server nakonfigurovaný s názvem služby `contosoapi` se vytvoří v Azure veřejné.
>

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Nahrajte certifikát, který chcete použít u prostředků fondu back-end s podporou protokolu SSL. Toto je stejný certifikát, který jste zadali v kroku 4 výše.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Krok 8

Konfigurace nastavení HTTP back-endu pro službu Application Gateway. To zahrnuje nastavení časového limitu pro požadavek back-endu, po jejímž uplynutí se zruší. Tato hodnota se liší od časový limit testu.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Nakonfigurujte fond back-end IP adres s názvem **apimbackend** interní virtuální IP adresy služby API Management vytvořili výše.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Krok 10

Vytvoření nastavení pro fiktivní back-endu (neexistující). Požadavky na rozhraní API cesty, které jsme nechcete vystavit ze služby API Management prostřednictvím Application Gateway se přístupů tento back-end a vrátit kód 404.

Konfigurace nastavení protokolu HTTP pro fiktivní back-endu.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Konfigurace back-end fiktivní **dummyBackendPool**, která odkazuje na adresu plně kvalifikovaného názvu domény **dummybackend.com**. Tuto adresu plně kvalifikovaného názvu domény ve virtuální síti neexistuje.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Vytvořte nastavení pravidla, která ve výchozím nastavení, která odkazuje na neexistující back-end budou používat službu Application Gateway **dummybackend.com** ve virtuální síti.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>Krok 11

Konfigurovat adresy URL pravidlo cesty pro back endové fondy. To umožňuje výběr jenom některé z rozhraní API ze služby API Management můžou zpřístupnit pro veřejnost. Například pokud existují `Echo API` (/ echo /), `Calculator API` (/calc/) atd. Zkontrolujte pouze `Echo API` přístupný z Internetu).

Následující příklad vytvoří jednoduché pravidlo pro "/ echo /" cesty směrování provozu do back endu "apimProxyBackendPool".

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Pokud cesta neodpovídá pravidla cesty chceme, aby ze služby API Management, konfigurace pravidla cesty mapy také nakonfiguruje výchozího fondu adres back-end s názvem **dummyBackendPool**. Například http://api.contoso.net/calc/sum přejde na **dummyBackendPool** definovaný jako výchozí fond pro zrušení odpovídající provoz.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

Výše uvedený krok zajistí, který si vyžádá pouze pro cestu "/ echo" mají povolený průchod přes Application Gateway. Požadavky pro jiná rozhraní API nakonfigurovat ve službě API Management se vyvolat chyby 404 ze služby Application Gateway při přístupu z Internetu.

### <a name="step-12"></a>Krok 12

Vytvoření nastavení pravidla pro Application Gateway pro použití směrování na základě cesty URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>Kroku 13

Nakonfigurujte počet instancí a velikost pro službu Application Gateway. Tady se používá [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) pro zvýšení zabezpečení prostředků API Management.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>Krok 14

Konfigurace WAF bude v režimu "Ochrany před únikem informací".
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Vytvoření aplikační brány

Vytvoření služby Application Gateway se všemi objekty konfigurace z předchozích kroků.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Název hostitele proxy API Management do prostředku Application Gateway veřejného názvu DNS CNAME

Po vytvoření brány je dalším krokem konfigurace front-endu pro komunikaci. Při použití veřejné IP adresy, služba Application Gateway vyžaduje dynamicky přidělený název DNS, který nemusí být snadné použití.

Application Gateway název DNS by měla sloužit k vytvoření záznamu CNAME, který odkazuje název hostitele proxy serveru služby APIM (třeba `api.contoso.net` ve výše uvedených příkladech) na tento název DNS. Pokud chcete nakonfigurovat záznam IP CNAME front-endu, načtěte podrobnosti o službě Application Gateway a název její přidružené IP adresy nebo DNS pomocí elementu PublicIPAddress. Použití záznamů A se nedoporučuje, protože virtuální IP adresa se může změnit při restartování služby brány.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"> </a> Souhrn
Nakonfigurovaná ve virtuální síti Azure API Management poskytuje rozhraní jednu bránu pro všechny nakonfigurované rozhraní API, ať už jsou hostované v místním prostředí nebo v cloudu. Integrace Application Gateway pomocí služby API Management nabízí flexibilitu selektivně povolení konkrétní rozhraní API bude přístupný na Internetu, a také poskytuje firewallu webových aplikací jako front-endu k vaší instanci API Management.

##<a name="next-steps"> </a> Další kroky
* Další informace o službě Azure Application Gateway
  * [Přehled služby Application Gateway](../application-gateway/application-gateway-introduction.md)
  * [Firewall webových aplikací pomocí Application Gateway](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Application Gateway používající směrování na základě cest](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Další informace o API Management a virtuálními sítěmi
  * [Použití služby API Management k dispozici pouze v rámci virtuální sítě](api-management-using-with-internal-vnet.md)
  * [Použití služby API Management ve virtuální síti](api-management-using-with-vnet.md)
