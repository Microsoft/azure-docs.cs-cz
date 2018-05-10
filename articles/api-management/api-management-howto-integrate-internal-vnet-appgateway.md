---
title: Jak používat Azure API Management ve virtuální síti s aplikační brány | Microsoft Docs
description: Zjistěte, jak nainstalovat a nakonfigurovat Azure API Management v interní virtuální síť s aplikací brány (firewall webových aplikací) jako front-endu
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: 595abcaafdea5cde3f868567bac7fb9cf0ee424b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrovat správu rozhraní API v interní virtuální síť s aplikační brány 

##<a name="overview"> </a> Přehled
 
Služba API Management můžete nakonfigurovat ve virtuální síti v interní režimu, který je dostupný jenom z virtuální sítě. Služba Azure Application Gateway je služba PAAS, které poskytuje nástroj pro vyrovnávání zatížení vrstvy 7. To funguje jako služba reverznímu proxy serveru a poskytuje mezi jeho nabídky webové aplikace brány Firewall (firewall webových aplikací).

Kombinování API Management zřízené v interní virtuální síti VNET s front-endu Application Gateway umožňuje následující scénáře:

* Použijte stejné rozhraní API správy prostředků pro spotřeba příjemci interní i externí spotřebitelů.
* Použít jeden zdroj API Management a mít podmnožinu rozhraní API definované ve službě API Management, které jsou k dispozici pro externí příjemci.
* Zadejte klíč způsob, jak zapnout a vypnout přepnout přístup k rozhraní API správy z veřejného Internetu. 

## <a name="prerequisites"></a>Požadavky

K provedení kroků popsaných v tomto článku, musíte mít:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM instance. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

##<a name="scenario"> </a> Scénář
Tento článek obsahuje informace o používání jedné služby API Management pro interní i externí uživatelé a nastavit jej jako jeden front-end pro obě místní a cloudové rozhraní API. Zobrazí se taky, jak vystavit pouze podmnožina vašich rozhraní API (v příkladu, které jsou vyznačené na zelená) pro externí spotřebu pomocí funkce PathBasedRouting v aplikační brány.

V prvním příkladu instalace všechna rozhraní API spravují pouze v rámci virtuální sítě. Interní příjemci (zvýraznit v oranžová) mají přístup všechny vaše interní a externí rozhraní API. Provoz nikdy nedostane mimo vysoký výkon doručuje Internet prostřednictvím okruhy Expressroute.

![Adresa URL trasy](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Než začnete

1. Nainstalujte nejnovější verzi rutin prostředí Azure PowerShell pomocí instalační služby webové platformy. Nejnovější verzi můžete stáhnout a nainstalovat v části **Windows PowerShell** na stránce [Položky ke stažení](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Vytvoření virtuální sítě a vytvořte oddělené podsítě pro API Management a aplikační brány. 
3. Pokud máte v úmyslu vytvořit vlastního serveru DNS pro virtuální síť, můžete tak učiňte před zahájením nasazení. Překontrolujte, který funguje zajištěním virtuální počítač vytvořený v novou podsíť ve virtuální síti můžete vyřešit a získat přístup k všechny koncové body služby Azure.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co je potřeba k vytvoření integraci mezi API Management a Application Gateway?

* **Fond back-end serverů:** Toto je interní virtuální IP adresu služby API Management.
* **Nastavení fondu back-end serverů:** Každý fond má nastavení, jako je port, protokol a spřažení na základě souborů cookie. Tato nastavení se použijí na všechny servery v rámci fondu.
* **Front-end port:** Toto je veřejný port, který se otevírá ve službě application gateway. Získá provoz stiskne ho přesměruje na jeden z back-end serverů.
* **Naslouchací proces:** Naslouchací proces má front-end port, protokol (Http nebo Https, u těchto hodnot se rozlišují malá a velká písmena) a název certifikátu SSL (pokud se konfiguruje přesměrování zpracování SSL).
* **Pravidlo:** pravidlo váže naslouchací proces pro fond back-end serverů.
* **Vlastní test stavu:** Application Gateway, standardně používá IP adresu, na základě sondy zjistěte, které servery v BackendAddressPool jsou aktivní. Rozhraní API správy, které služba pouze reaguje na požadavky, které mají hlavičku hostitele správná, proto sondy výchozí nezdaří. Test vlastní stavu musí být definován pomohou určit, že služba není aktivní a předávat požadavky aplikační brány.
* **Certifikát vlastní domény:** pro přístup k rozhraní API správy z Internetu, je potřeba vytvořit mapování CNAME z jeho názvu hostitele k názvu DNS front-end pro aplikační bránu. To zajišťuje, že název hostitele záhlaví a certifikát odeslat aplikační bránu, která se předají do rozhraní API správy jednoho APIM rozpoznat jako platný.

## <a name="overview-steps"> </a> Kroky potřebné k integraci API Management a aplikační brány 

1. Vytvoření skupiny prostředků pro Resource Manager
2. Vytvořte virtuální síť, podsíť a veřejnou IP adresu pro aplikační bránu. Vytvořte další podsítě pro API Management.
3. Vytvoření služby API Management uvnitř vytvořili výše podsíť virtuální sítě a ujistěte se, že používáte interní režim.
4. Nastavte vlastní název domény ve službě API Management.
5. Vytvoření objektu konfigurace služby Application Gateway.
6. Vytvořte prostředek aplikační brány.
7. Vytvořte záznam CNAME z veřejného názvu DNS aplikační brány pro název hostitele proxy API Management.

## <a name="create-a-resource-group-for-resource-manager"></a>Vytvoření skupiny prostředků pro Resource Manager

Ujistěte se, že používáte nejnovější verzi prostředí Azure PowerShell. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager).

### <a name="step-1"></a>Krok 1

Přihlášení k Azure

```powershell
Connect-AzureRmAccount
```

Ověření pomocí svých přihlašovacích údajů.<BR>

### <a name="step-2"></a>Krok 2

Zkontrolujte předplatná pro příslušný účet a vyberte jej.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Krok 3

Vytvořte skupinu prostředků (pokud používáte některou ze stávajících skupin prostředků, můžete tenhle krok přeskočit).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. To slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Ujistěte se, že všechny příkazy k vytvoření služby application gateway používají stejnou skupinu prostředků.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Vytvořte virtuální síť a podsíť pro aplikační bránu

Následující příklad ukazuje, jak vytvořit virtuální síť pomocí prostředek správci.

### <a name="step-1"></a>Krok 1

Rozsah adres 10.0.0.0/24 přiřadí proměnné podsítě, který má být použit pro službu Application Gateway při vytvoření virtuální sítě.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Krok 2

10.0.1.0/24 rozsah adres přiřadí proměnné podsítě, který má být použit pro API Management při vytvoření virtuální sítě.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Krok 3

Vytvořit virtuální síť s názvem **appgwvnet** ve skupině prostředků **apim-appGw-RG** pro oblast západní USA pomocí předpony 10.0.0.0/16 s podsítí 10.0.0.0/24 a 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Přiřaďte proměnnou podsítě pro další kroky

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Vytvoření služby API Management uvnitř nakonfigurován v režimu interní virtuální sítě

Následující příklad ukazuje postup vytvoření služby API Management ve virtuální síti nakonfigurován pro interní přístup jenom.

### <a name="step-1"></a>Krok 1
Vytvořte virtuální síť pro správu rozhraní API objekt, který používá podsíť $apimsubnetdata vytvořili výše.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Krok 2
Vytvoření služby API Management ve virtuální síti.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
Po úspěšném provedení výše uvedeném příkazu odkazovat na [DNS konfigurace požadovaná pro přístup k interní virtuální síť rozhraní API správy služby](api-management-using-with-internal-vnet.md#apim-dns-configuration) k přístupu.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Nastavit vlastní název domény ve službě API Management

### <a name="step-1"></a>Krok 1
Nahrajte certifikát s privátním klíčem pro doménu. V tomto příkladu bude `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Krok 2
Po nahrání certifikátu vytvořit objekt konfigurace název hostitele pro proxy server s název hostitele `api.contoso.net`, jako například certifikát poskytuje autority pro `*.contoso.net` domény. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Vytvoření veřejné IP adresy pro front-end konfiguraci

Vytvořte prostředek veřejné IP **adresy publicIP01** ve skupině prostředků **apim-appGw-RG** pro oblast západní USA.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

IP adresa je ke službě Application Gateway přiřazena při spuštění služby.

## <a name="create-application-gateway-configuration"></a>Vytvoření konfigurace brány aplikace

Před vytvořením služby Application Gateway musí být nastaveny všechny položky konfigurace. Následující kroky slouží k vytvoření položek konfigurace potřebné pro prostředek služby Application Gateway.

### <a name="step-1"></a>Krok 1

Vytvořte konfiguraci IP adresy služby Application Gateway s názvem **gatewayIP01**. Při spuštění služby Application Gateway se předá IP adresa z nakonfigurované podsítě a síťový provoz se bude směrovat na IP adresy ve fondu back-end IP adres. Uvědomte si, že každá instance vyžaduje jednu IP adresu.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Krok 2

Nakonfigurujte port front-end IP pro koncový bod veřejné IP adresy. Tento port je port, který koncoví uživatelé připojit k.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>Krok 3

Nakonfigurujte IP adresu front-endu s koncovým bodem s veřejnou IP adresou.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Krok 4

Nakonfigurujte certifikát pro službu Application Gateway, používá k dešifrování a znovu zašifrovat přenosy procházející.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Krok 5

Vytvořte naslouchací proces protokolu HTTP pro službu Application Gateway. Jí přiřadíte front-end IP certifikát konfigurace, port a protokol ssl.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Krok 6

Vytvořit vlastní test paměti do služby API Management `ContosoApi` koncový bod domény proxy serveru. Cesta `/status-0123456789abcdef` je výchozího koncového bodu stavu hostované na všechny služby API Management. Nastavit `api.contoso.net` jako název hostitele vlastní test paměti k zabezpečení s certifikátem SSL.

> [!NOTE]
> Název hostitele `contosoapi.azure-api.net` je název hostitele proxy výchozí nakonfigurované služby s názvem `contosoapi` je vytvořen v veřejný Azure. 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Nahrajte certifikát, který chcete použít pro protokol SSL povolen back-end fondu prostředků. Toto je stejný certifikát, který jste zadali v kroku 4 výše.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Krok 8

Nakonfigurujte nastavení HTTP back-end pro službu Application Gateway. To zahrnuje nastavení časového limitu pro požadavek back-end, po jejímž uplynutí se zrušil. Tato hodnota se liší od časový limit testu.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Nakonfigurujte fond back-end IP adresy s názvem **apimbackend** s interní virtuální IP adresu služby API Management vytvořili výše.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Krok 10

Vytvořte nastavení pro fiktivní back-end (neexistující). Požadavky na rozhraní API cesty, které jsme nechcete vystavit ze správy rozhraní API prostřednictvím brány aplikace bude dosáhl tento back-end a vrátí 404.

Nakonfigurujte nastavení protokolu HTTP pro fiktivní back-end.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Konfigurace fiktivní back-end **dummyBackendPool**, která odkazuje na adresu plně kvalifikovaný název domény **dummybackend.com**. Tato adresa plně kvalifikovaný název domény ve virtuální síti neexistuje.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Vytvořit pravidlo nastavení, které aplikační brány použije ve výchozím nastavení, která odkazuje na neexistující back-end **dummybackend.com** ve virtuální síti.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>Krok 11

Konfigurovat pravidla cesty adresy URL pro back endové fondy. To umožňuje výběr jenom některé z rozhraní API ze správy rozhraní API pro vystavení veřejnosti. Například, pokud existují `Echo API` (/ echo /), `Calculator API` (/calc/) atd. Zkontrolujte pouze `Echo API` přístupné z Internetu). 

Následující příklad vytvoří jednoduché pravidlo pro "/ echo /" cesty směrování provozu, který back-end "apimProxyBackendPool".

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Pokud cesta neodpovídá pravidla cesty chceme, aby ze správy rozhraní API, konfigurace pravidla cesty mapy nakonfiguruje taky výchozího fondu adres back-end s názvem **dummyBackendPool**. Například http://api.contoso.net/calc/sum přejde na **dummyBackendPool** je definovaný jako výchozí fond pro zrušení odpovídající provoz.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

Předchozí krok zajistí, který pouze požadavky pro cestu "/ odezvu" jsou povoleny prostřednictvím služby Application Gateway. Požadavky pro jiná rozhraní API nakonfigurovaný ve službě API Management vyvolá výjimku chyby 404 ze Application Gateway při přístupu z Internetu. 

### <a name="step-12"></a>Krok 12

Vytvoření pravidla nastavení pro službu Application Gateway používat na základě cestu směrování adres URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>Krok 13

Nakonfigurujte počet instancí a velikost pro službu Application Gateway. Tady se používá [firewall webových aplikací SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) pro zvýšení zabezpečení prostředků API Management.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>Krok 14

Nakonfigurujte firewall webových aplikací v režimu "Prevence".
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Vytvořte aplikační bránu

Vytvoření služby Application Gateway se všemi objekty konfigurace z předchozích kroků.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Název hostitele proxy API Management prostředku aplikační brány jako veřejný název DNS CNAME

Po vytvoření brány je dalším krokem konfigurace front-endu pro komunikaci. Pokud používáte veřejnou IP adresu, aplikační brána vyžaduje dynamicky přiřazené název DNS, který nemusí být nejjednodušší. 

Název DNS služby Application Gateway by měl použít k vytvoření záznam CNAME, který ukazuje název APIM proxy hostitele (například `api.contoso.net` ve výše uvedených příkladech) k tomuto názvu DNS. Pokud chcete nakonfigurovat záznam IP CNAME front-endu, načtení podrobností o aplikační bránu a svému přidruženému názvu IP a DNS pomocí elementu PublicIPAddress. Použití záznamů A se nedoporučuje, protože VIP může změnit při restartu brány.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"> </a> Souhrn
Nakonfigurovat ve virtuální síti Azure API Management poskytuje rozhraní jedna brána pro všechny nakonfigurované rozhraní API, ať už jsou hostované v místní nebo v cloudu. Integrace Application Gateway s API Management nabízí flexibilitu selektivně povolení konkrétní rozhraní API bude přístupný na Internetu, jakož i poskytnutí brány Firewall webových aplikací jako front-end pro vaše instance služby API Management.

##<a name="next-steps"> </a> Další kroky
* Další informace o Azure Application Gateway
  * [Přehled brány aplikace](../application-gateway/application-gateway-introduction.md)
  * [Brány Firewall webových aplikací Application Gateway](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Aplikační bránu pomocí směrování na základě cesty](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Další informace o rozhraní API správy a virtuální sítě
  * [Použití služby API Management, které jsou k dispozici pouze v rámci virtuální sítě](api-management-using-with-internal-vnet.md)
  * [Použití služby API Management ve virtuální síti](api-management-using-with-vnet.md)
