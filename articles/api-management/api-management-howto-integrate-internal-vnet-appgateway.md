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
ms.date: 06/26/2018
ms.author: sasolank
ms.openlocfilehash: 6356d930b5bf909f1b209272e7367f5e2dcd5a13
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444611"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integraci služby API Management v interní virtuální síti pomocí služby Application Gateway

## <a name="overview"> </a> Přehled

Služba API Management je možné nakonfigurovat ve virtuální síti v interní režim, který je přístupný pouze z v rámci virtuální sítě. Azure Application Gateway je služba PAAS, která poskytuje nástroje pro vyrovnávání zatížení vrstvy 7. Funguje jako služba reverzních proxy serverů a poskytuje mezi jeho nabídky Firewall webových aplikací (WAF).

Kombinování zřizována jako interní virtuální síť s front-endový aplikační brány API Management umožňuje následující scénáře:

* Použijte stejný prostředek API Management k využití v příjemci interní a externí uživatele.
* Použijte jeden prostředek API Management a mají podmnožinu rozhraní API definované ve službě API Management je dostupná pro externí uživatele.
* Zadejte klíč způsob, jak zapnout a vypnout přepnout přístup do API managementu z veřejného Internetu.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků popsaných v tomto článku, budete potřebovat:

* Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certifikáty – pfx a cer pro název hostitele rozhraní API a pfx pro název hostitele portálu pro vývojáře.

## <a name="scenario"> </a> Scénář

Tento článek popisuje způsob použití jediné služby API Management pro interní a externí uživatele a fungují jako jeden front-endu pro obě místní a cloudové rozhraní API. Zobrazí se také, jak vystavit pouze podmnožinu vašich rozhraní API (v příkladu, které jsou zvýrazněny zeleně) pro externí spotřebu pomocí směrování funkce je dostupná ve službě Application Gateway.

V prvním příkladu nastavení všechna svoje rozhraní API spravují pouze v rámci vaší virtuální sítě. Interní příjemci (zvýrazněná v oranžová) můžete přistupovat všechna vaše interní a externí rozhraní API. Provoz nikdy nedostane mimo vysoký výkon se doručí Internet prostřednictvím okruhů Expressroute.

![Adresa URL trasy](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Než začnete

* Ujistěte se, že používáte nejnovější verzi prostředí Azure PowerShell. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager).

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co je potřeba k vytvoření integrace mezi službami API Management a služba Application Gateway?

* **Fond back-end serverů:** jde o interní virtuální IP adresu služby API Management.
* **Nastavení fondu back-end serverů:** Každý fond má nastavení, jako je port, protokol a spřažení na základě souborů cookie. Tato nastavení se použijí na všechny servery v rámci fondu.
* **Front-end port:** Toto je veřejný port, který se otevírá ve službě application gateway. Přenosů kontaktujících ho přesměruje na jeden back-end serverů.
* **Naslouchací proces:** Naslouchací proces má front-end port, protokol (Http nebo Https, u těchto hodnot se rozlišují malá a velká písmena) a název certifikátu SSL (pokud se konfiguruje přesměrování zpracování SSL).
* **Pravidlo:** pravidlo váže naslouchací proces pro fond back endového serveru.
* **Vlastní sondy stavu:** Application Gateway, ve výchozím nastavení, použije IP adres na základě sondy zjistit, které servery v BackendAddressPool jsou aktivní. API Management, kterou služba reagovala jenom na žádosti se hlavička hostitele správná, proto testy výchozí nezdaří. Sonda stavu vlastní musí definovat tak, aby pomůže zjistit, zda služba je aktivní a předávat požadavky službě application gateway.
* **Vlastní domény certifikáty:** z Internetu přístup k rozhraní API Management, je potřeba vytvořit mapování CNAME z jeho názvu hostitele front-endu název DNS Application Gateway. Tím se zajistí, že záhlaví názvu hostitele a certifikát odesílat Application Gateway, která je předána API Management je ten, který APIM dokáže rozpoznat jako platný. V tomto příkladu budeme používat dva certifikáty - pro back-endu a portálu pro vývojáře.  

## <a name="overview-steps"> </a> Kroků potřebných pro integraci služby API Management a služba Application Gateway

1. Vytvoření skupiny prostředků pro Resource Manager
2. Vytvořte virtuální síť, podsíť a veřejnou IP adresu pro službu Application Gateway. Vytvoření další podsítě pro službu API Management.
3. Vytvoření služby API Management v podsíti virtuální sítě vytvořené výše a ujistěte se, že používáte interní režimu.
4. Nastavení vlastního názvu domény ve službě API Management.
5. Vytvoření objektu konfigurace aplikační brány.
6. Vytvoří prostředek služby Application Gateway.
7. Vytvořte záznam CNAME z veřejného názvu DNS služby Application Gateway na název hostitele proxy API Management.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Vystavení portálu pro vývojáře externě přes Application Gateway

V této příručce se také zveřejňujeme **portál pro vývojáře** externí cílovým skupinám na místě přes Application Gateway. Vyžaduje další kroky k vytvoření naslouchacího procesu portál pro vývojáře, test, nastavení a pravidla. Všechny podrobnosti jsou uvedeny v příslušných kroků.

> [!WARNING]
> V nastavení popsané v portálu pro vývojáře se přistupuje prostřednictvím Application Gateway se můžete setkat s problémy s ověřováním AAD nebo od jiného výrobce.

## <a name="create-a-resource-group-for-resource-manager"></a>Vytvoření skupiny prostředků pro Resource Manager

### <a name="step-1"></a>Krok 1

Přihlášení k Azure

```powershell
Login-AzureRmAccount
```

Ověření pomocí přihlašovacích údajů.

### <a name="step-2"></a>Krok 2

Vyberte požadované předplatné.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzureRmSubscription -Subscriptionid $subscriptionId | Select-AzureRmSubscription
```

### <a name="step-3"></a>Krok 3

Vytvořte skupinu prostředků (pokud používáte některou ze stávajících skupin prostředků, můžete tenhle krok přeskočit).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzureRmResourceGroup -Name $resGroupName -Location $location
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

Vytvoření virtuální sítě s názvem **appgwvnet** ve skupině prostředků **apim-appGw-RG** pro oblast západní USA. Pomocí předpony 10.0.0.0/16 s podsítí 10.0.0.0/24 a 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Přiřaďte proměnnou podsítě pro další kroky

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Vytvoření služby API Management v síti VNET nakonfigurovaný v interní režimu

Následující příklad ukazuje, jak vytvořit služby API Management ve virtuální síti nakonfigurovat interní pouze pro přístup.

### <a name="step-1"></a>Krok 1

Vytvořte virtuální síť pro správu rozhraní API objektu pomocí podsítě $apimsubnetdata vytvořili výše.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location $location -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Krok 2

Vytvoření služby API Management ve virtuální síti.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzureRmApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Po úspěšném výše uvedený příkaz najdete [konfiguraci DNS vyžadovaných pro přístup k vnitřní chybě služby VNET API Management](api-management-using-with-internal-vnet.md#apim-dns-configuration) k němu přistupovat. Tento krok může trvat více než půl hodiny.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Nastavení vlastního názvu domény ve službě API Management

### <a name="step-1"></a>Krok 1

Nahrajte certifikáty pomocí privátního klíče pro domény. V tomto příkladu budeme používat `api.contoso.net` a `portal.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $gatewayCertPfxPath -PfxPassword $gatewayCertPfxPassword -PassThru
$certPortalUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $portalCertPfxPath -PfxPassword $portalCertPfxPassword -PassThru
```

### <a name="step-2"></a>Krok 2

Po nahrání certifikátů se vytvořte název hostitele objektů konfigurace proxy serveru a na portálu.  

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname $gatewayHostname
$portalHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certPortalUploadResult.Thumbprint -Hostname $portalHostname
$result = Set-AzureRmApiManagementHostnames -Name $apimServiceName -ResourceGroupName $resGroupName –PortalHostnameConfiguration $portalHostnameConfig -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Vytvoření veřejné IP adresy pro front-end konfiguraci

Vytvořte prostředek veřejné IP adresy **adresy publicIP01** ve skupině prostředků.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
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

Konfigurace certifikátů pro službu Application Gateway, která se použije k dešifrování a znovu šifrovat provoz procházející.

```powershell
$certPwd = ConvertTo-SecureString $gatewayCertPfxPassword -AsPlainText -Force
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortalPwd = ConvertTo-SecureString $portalCertPfxPassword -AsPlainText -Force
$certPortal = New-AzureRmApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Krok 5

Vytvořte naslouchací procesy HTTP služby Application Gateway. Jim přiřadíte front-end IP konfigurace, portu a protokolu ssl certifikáty.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Krok 6

Vytvoření vlastní sondy ke službě API Management `ContosoApi` koncový bod domény proxy serveru. Cesta `/status-0123456789abcdef` je výchozího stavu pomocí koncových bodů hostované na všechny služby API Management. Nastavte `api.contoso.net` jako název hostitele vlastní test paměti k zabezpečení pomocí certifikátu protokolu SSL.

> [!NOTE]
> Název hostitele `contosoapi.azure-api.net` je výchozí název hostitele proxy server nakonfigurovaný s názvem služby `contosoapi` se vytvoří v Azure veřejné.
>

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzureRmApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Nahrajte certifikát, který chcete použít u prostředků fondu back-end s podporou protokolu SSL. Toto je stejný certifikát, který jste zadali v kroku 4 výše.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Krok 8

Konfigurace nastavení HTTP back-endu pro službu Application Gateway. To zahrnuje nastavení časového limitu pro požadavek back-endu, po jejímž uplynutí se zrušilo. Tato hodnota se liší od časový limit testu.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Nakonfigurujte fond back-end IP adres s názvem **apimbackend** interní virtuální IP adresy služby API Management vytvořili výše.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Krok 10

Vytvoření pravidel pro službu Application Gateway používat základní směrování.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Změna typu pravidla – a směrování k omezení přístupu k určitým stránek portálu pro vývojáře.

### <a name="step-11"></a>Krok 11

Nakonfigurujte počet instancí a velikost pro službu Application Gateway. V tomto příkladu používáme [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) pro zvýšení zabezpečení prostředků API Management.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Krok 12

Konfigurace WAF bude v režimu "Ochrany před únikem informací".

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Vytvoření aplikační brány

Vytvoření služby Application Gateway se všemi objekty konfigurace z předchozích kroků.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Název hostitele proxy API Management do prostředku Application Gateway veřejného názvu DNS CNAME

Po vytvoření brány je dalším krokem konfigurace front-endu pro komunikaci. Při použití veřejné IP adresy, služba Application Gateway vyžaduje dynamicky přidělený název DNS, který nemusí být snadné použití.

Application Gateway název DNS by měla sloužit k vytvoření záznamu CNAME, který odkazuje název hostitele proxy serveru služby APIM (třeba `api.contoso.net` ve výše uvedených příkladech) na tento název DNS. Pokud chcete nakonfigurovat záznam IP CNAME front-endu, načtěte podrobnosti o službě Application Gateway a název její přidružené IP adresy nebo DNS pomocí elementu PublicIPAddress. Použití záznamů A se nedoporučuje, protože virtuální IP adresa se může změnit při restartování služby brány.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"> </a> Souhrn
Nakonfigurovaná ve virtuální síti Azure API Management poskytuje rozhraní jednu bránu pro všechny nakonfigurované rozhraní API, ať už jsou hostované v místním prostředí nebo v cloudu. Integrace Application Gateway pomocí služby API Management nabízí flexibilitu selektivně povolení konkrétní rozhraní API bude přístupný na Internetu, a také poskytuje firewallu webových aplikací jako front-endu k vaší instanci API Management.

##<a name="next-steps"> </a> Další kroky
* Další informace o službě Azure Application Gateway
  * [Přehled služby Application Gateway](../application-gateway/application-gateway-introduction.md)
  * [Firewall webových aplikací pomocí Application Gateway](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Application Gateway používající směrování na základě cest](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Další informace o API Management a virtuálními sítěmi
  * [Použití služby API Management k dispozici pouze v rámci virtuální sítě](api-management-using-with-internal-vnet.md)
  * [Použití služby API Management ve virtuální síti](api-management-using-with-vnet.md)
