---
title: Použití s interními Load Balancer – Azure Application Gateway
description: Tahle stránka poskytuje pokyny pro vytvoření, konfiguraci, spuštění a odstranění služby Azure application gateway s interním nástrojem pro vyrovnávání zatížení (ILB) pro nástroj Azure Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 3d663dc4e2bd860ec9494785ecbf6dbf10a4c5b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397752"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Vytvoření aplikační brány s interním nástrojem pro vyrovnávání zatížení (interního nástroje)

Služba Azure Application Gateway se dá nakonfigurovat pomocí virtuální IP adresy s přístupem k Internetu, nebo pomocí interního koncového bodu, který není vystavený v Internetu, známého také jako koncový bod interního nástroje pro vyrovnávání zatížení (ILB). Konfigurace brány pomocí ILB je užitečná pro interní-obchodní aplikace, které nejsou vystaveny v Internetu. Je to také užitečné pro služby a vrstvy v rámci vícevrstvé aplikace, které se nacházejí v hranici zabezpečení, která není dostupná pro Internet, ale stále vyžaduje distribuci zatížení pomocí kruhového dotazování, vytrvalost relace nebo zabezpečení TLS (Transport Layer Security), dříve označované jako SSL (Secure Sockets Layer) (SSL), ukončení.

Tenhle článek vás provede kroky konfigurace aplikační brány s ILB.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Nainstalujte nejnovější verzi modulu Azure PowerShell podle [pokynů k instalaci](/powershell/azure/install-az-ps).
2. Vytvořte virtuální síť a podsíť služby Application Gateway. Ujistěte se, že tuto podsíť nepoužívají žádné virtuální počítače ani cloudová nasazení. Aplikační brána musí být sama o sobě v podsíti virtuální sítě.
3. Servery, které nakonfigurujete pro použití služby Application Gateway, musí existovat nebo mít své koncové body vytvořené buď ve virtuální síti, nebo s přiřazenou veřejnou IP adresou nebo virtuální IP adresou.

## <a name="what-is-required-to-create-an-application-gateway"></a>Co je potřeba k vytvoření služby Application Gateway?

* **Fond back-end serverů:** Seznam IP adres back-end serverů. Uvedené IP adresy by měly buď patřit do virtuální sítě, ale v jiné podsíti pro aplikační bránu, nebo by se mělo jednat o veřejné IP nebo virtuální IP adresy.
* **Nastavení fondu back-end serverů:** Každý fond má nastavení, jako je port, protokol a spřažení na základě souborů cookie. Tato nastavení se vážou na fond a používají se na všechny servery v rámci fondu.
* **Front-end port:** Toto je veřejný port, který se otevírá ve službě Application Gateway. Když datový přenos dorazí na tento port, přesměruje se na některý back-end server.
* **Naslouchací proces:** Naslouchací proces má front-end port, protokol (Http nebo Https, s rozlišením malých a velkých písmen) a název certifikátu SSL (pokud se konfiguruje přesměrování zpracování SSL).
* **Pravidlo:** Pravidlo váže naslouchací proces a fond back-end serverů a definuje, ke kterému fondu back-end serverů se má provoz směrovat při volání příslušného naslouchacího procesu. V tuhle chvíli se podporuje jenom *základní* pravidlo. *Základní* pravidlo je distribuce zatížení pomocí kruhového dotazování.

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

Rozdíl mezi použitím nástrojů Azure Classic a Azure Resource Manager je v tom, v jakém pořadí tvoříte službu Application Gateway, a v položkách, které konfigurujete.
S Resource Managerem se všechny položky, které tvoří službu Application Gateway, konfigurují individuálně, potom se spojí dohromady a vytvoří prostředek služby Application Gateway.

Toto jsou kroky, které se musí provést k vytvoření služby Application Gateway:

1. Vytvoření skupiny prostředků pro Resource Manager
2. Vytvoření virtuální sítě a podsítě pro službu Application Gateway
3. Vytvořte objekt konfigurace aplikační brány 
4. Vytvořte prostředek aplikační brány

## <a name="create-a-resource-group-for-resource-manager"></a>Vytvoření skupiny prostředků pro Resource Manager

Ujistěte se, že jste přepnuli režim prostředí PowerShell tak, aby se mohly použít rutiny Azure Resource Manageru. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../azure-resource-manager/management/manage-resources-powershell.md).

### <a name="step-1"></a>Krok 1

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>Krok 2

Zkontrolujte předplatná pro příslušný účet.

```powershell
Get-AzSubscription
```

Zobrazí se výzva k ověření pomocí přihlašovacích údajů.

### <a name="step-3"></a>Krok 3

Zvolte předplatné Azure, které chcete použít.

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Krok 4

Vytvořte novou skupinu prostředků (pokud používáte některou ze stávajících skupin prostředků, můžete tenhle krok přeskočit).

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. To slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Ujistěte se, že všechny příkazy k vytvoření služby Application Gateway používají stejnou skupinu prostředků.

V předchozím příkladu jsme vytvořili skupinu prostředků s názvem "appgw-RG" a umístěním "Západní USA".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Vytvoření virtuální sítě a podsítě pro službu Application Gateway

Následující příklad ukazuje, jak vytvořit virtuální síť pomocí Resource Managera:

### <a name="step-1"></a>Krok 1

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Tento krok přiřadí proměnné podsítě rozsah adres 10.0.0.0/24, který se použije k vytvoření virtuální sítě.

### <a name="step-2"></a>Krok 2

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Tento krok vytvoří virtuální síť s názvem "appgwvnet" ve skupině prostředků "appgw-RG" pro oblast Západní USA s použitím předpony 10.0.0.0/16 s podsítí 10.0.0.0/24.

### <a name="step-3"></a>Krok 3

```powershell
$subnet = $vnet.subnets[0]
```

Tento krok přiřadí objekt podsítě k proměnné $subnet pro další kroky.

## <a name="create-an-application-gateway-configuration-object"></a>Vytvořte objekt konfigurace aplikační brány 

### <a name="step-1"></a>Krok 1

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Tento krok vytvoří konfiguraci protokolu IP aplikační brány s názvem "gatewayIP01". Při spuštění služby Application Gateway se předá IP adresa z nakonfigurované podsítě a síťový provoz se bude směrovat na IP adresy ve fondu back-end IP adres. Uvědomte si, že každá instance vyžaduje jednu IP adresu.

### <a name="step-2"></a>Krok 2

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Tento krok nakonfiguruje fond back-end IP adres s názvem "pool01" s IP adresami "10.1.1.8, 10.1.1.9, 10.1.1.10". Jsou to IP adresy, které přijímají síťový provoz, který přichází z koncového bodu front-end IP adresy. Předchozí IP adresy nahradíte vlastními aplikačními koncovými body IP adresy.

### <a name="step-3"></a>Krok 3

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Tento krok nakonfiguruje nastavení aplikační brány "poolsetting01" pro síťový provoz s vyrovnáváním zatížení ve fondu back-end.

### <a name="step-4"></a>Krok 4

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Tento krok nakonfiguruje port front-end IP adresy s názvem "frontendport01" pro interního nástroje.

### <a name="step-5"></a>Krok 5

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Tento krok vytvoří konfiguraci front-end IP adresy s názvem "fipconfig01" a přidruží ji k privátní IP adrese z aktuální podsítě virtuální sítě.

### <a name="step-6"></a>Krok 6

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Tento krok vytvoří naslouchací proces nazvaný "listener01" a přiřadí front-end port ke konfiguraci front-endové IP adresy.

### <a name="step-7"></a>Krok 7

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Tento krok vytvoří pravidlo směrování nástroje pro vyrovnávání zatížení s názvem "rule01", které konfiguruje chování nástroje pro vyrovnávání zatížení.

### <a name="step-8"></a>Krok 8

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Tento krok nakonfiguruje velikost instance aplikační brány.

> [!NOTE]
> Výchozí hodnota kapacity je 2. Pro název SKU můžete vybrat mezi Standard_Small, Standard_Medium a Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Vytvořte aplikační bránu pomocí New-AzureApplicationGateway

Vytvoří Aplikační bránu se všemi položkami konfigurace z předchozích kroků. V tomto příkladu má služba Application Gateway název „appgwtest“.

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Tento krok vytvoří Aplikační bránu se všemi položkami konfigurace z předchozích kroků. V příkladu se aplikační brána nazývá „appgwtest“.

## <a name="delete-an-application-gateway"></a>Odstranění služby Application Gateway

Chcete-li odstranit Aplikační bránu, je třeba provést následující kroky v uvedeném pořadí:

1. Pomocí rutiny `Stop-AzApplicationGateway` zastavte bránu.
2. Pomocí rutiny `Remove-AzApplicationGateway` bránu odeberte.
3. Zkontrolujte odstranění brány pomocí rutiny `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Krok 1

Získejte objekt služby Application Gateway a přidružte ho k proměnné „$getgw“.

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Krok 2

Pomocí rutiny `Stop-AzApplicationGateway` zastavte službu Application Gateway. Tato ukázka znázorňuje `Stop-AzApplicationGateway` rutinu na prvním řádku, následovanou výstupem.

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Jakmile je služba Application Gateway v zastaveném stavu, pomocí rutiny `Remove-AzApplicationGateway` službu odstraňte.

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> K potlačení zprávy s potvrzením o odstranění se dá použít přepínač **-force**.

Pokud chcete zkontrolovat, že se služba odstranila, můžete použít rutinu `Get-AzApplicationGateway`. Tenhle krok není povinný.

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Další kroky

Pokud chcete konfigurovat přesměrování zpracování SSL, přejděte do části [Konfigurace aplikační brány pro přesměrování zpracování SSL](./tutorial-ssl-powershell.md).

Pokud chcete další informace o obecných možnostech vyrovnávání zatížení, přečtěte si část:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)