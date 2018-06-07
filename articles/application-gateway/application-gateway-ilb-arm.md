---
title: Azure Application Gateway pomocí vyrovnávání zatížení interní - prostředí PowerShell | Microsoft Docs
description: Tahle stránka poskytuje pokyny pro vytvoření, konfiguraci, spuštění a odstranění služby Azure application gateway s interním nástrojem pro vyrovnávání zatížení (ILB) pro nástroj Azure Resource Manager
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2018
ms.author: victorh
ms.openlocfilehash: b9bdc3f4a0f7eb20b1c0cbc33fb257577da08c26
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598483"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Vytvoření služby application gateway s nástrojem pro vyrovnávání interní zatížení (ILB)

Služba Azure Application Gateway se dá nakonfigurovat pomocí virtuální IP adresy s přístupem k Internetu, nebo pomocí interního koncového bodu, který není vystavený v Internetu, známého také jako koncový bod interního nástroje pro vyrovnávání zatížení (ILB). Konfigurace brány pomocí ILB je užitečná pro interní-obchodní aplikace, které nejsou vystaveny v Internetu. To se hodí i pro služby a vrstvy v rámci vícevrstvé aplikace, které se nacházejí v rámci hranice zabezpečení nevystavené k Internetu, ale stále vyžadují distribuci zatížení modelu kruhového dotazování, dlouhodobost relace nebo ukončení SSL (Secure Sockets Layer).

Tenhle článek vás provede kroky konfigurace aplikační brány s ILB.

## <a name="before-you-begin"></a>Než začnete

1. Nainstalujte nejnovější verzi rutin prostředí Azure PowerShell pomocí instalační služby webové platformy. Nejnovější verzi můžete stáhnout a nainstalovat v části **Windows PowerShell** na stránce [Položky ke stažení](https://azure.microsoft.com/downloads/).
2. Vytvořte virtuální síť a podsíť služby Application Gateway. Ujistěte se, že žádné virtuální počítače nebo cloudová nasazení nepoužívají podsíť. Aplikační brána musí být sama o sobě v podsíti virtuální sítě.
3. Servery, které nakonfigurujete pro použití služby Application Gateway, musí existovat nebo mít své koncové body vytvořené buď ve virtuální síti, nebo s přiřazenou veřejnou IP adresou nebo virtuální IP adresou.

## <a name="what-is-required-to-create-an-application-gateway"></a>Co je potřeba k vytvoření služby Application Gateway?

* **Fond back-end serverů:** seznam IP adres back-end serverů. Uvedené IP adresy by měly buď patřit do virtuální sítě, ale v jiné podsíti pro aplikační bránu, nebo by se mělo jednat o veřejné IP nebo virtuální IP adresy.
* **Nastavení fondu back-end serverů:** každý fond má nastavení, jako je port, protokol a spřažení na základě souborů cookie. Tato nastavení se vážou na fond a používají se na všechny servery v rámci fondu.
* **Front-end port:** Toto je veřejný port, který se otevírá ve službě Application Gateway. Když datový přenos dorazí na tento port, přesměruje se na některý back-end server.
* **Naslouchací proces:** Naslouchací proces má front-end port, protokol (Http nebo Https, s rozlišením malých a velkých písmen) a název certifikátu SSL (pokud se konfiguruje přesměrování zpracování SSL).
* **Pravidlo:** Pravidlo váže naslouchací proces a fond back-end serverů a definuje, ke kterému fondu back-end serverů se má provoz směrovat při volání příslušného naslouchacího procesu. V tuhle chvíli se podporuje jenom *základní* pravidlo. *Základní* pravidlo je distribuce zatížení pomocí kruhového dotazování.

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Rozdíl mezi použitím nástrojů Azure Classic a Azure Resource Manager je v tom, v jakém pořadí tvoříte službu Application Gateway, a v položkách, které konfigurujete.
S Resource Managerem se všechny položky, které tvoří službu Application Gateway, konfigurují individuálně, potom se spojí dohromady a vytvoří prostředek služby Application Gateway.

Toto jsou kroky, které se musí provést k vytvoření služby Application Gateway:

1. Vytvořte skupinu prostředků pro Resource Manager
2. Vytvoření virtuální sítě a podsítě pro službu Application Gateway
3. Vytvoření objektu konfigurace služby Application Gateway
4. Vytvořte prostředek aplikační brány

## <a name="create-a-resource-group-for-resource-manager"></a>Vytvoření skupiny prostředků pro Resource Manager

Ujistěte se, že jste přepnuli režim prostředí PowerShell tak, aby se mohly použít rutiny Azure Resource Manageru. Další informace jsou k dispozici v části [Použití prostředí Windows PowerShell s Resource Managerem](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Krok 1

```powershell
Connect-AzureRmAccount
```

### <a name="step-2"></a>Krok 2

Zkontrolujte předplatná pro příslušný účet.

```powershell
Get-AzureRmSubscription
```

Zobrazí se výzva k ověření pomocí přihlašovacích údajů.

### <a name="step-3"></a>Krok 3

Zvolte předplatné Azure, které chcete použít.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Krok 4

Vytvořte novou skupinu prostředků (pokud používáte některou ze stávajících skupin prostředků, můžete tenhle krok přeskočit).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -location "West US"
```

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. To slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Ujistěte se, že všechny příkazy k vytvoření služby Application Gateway používají stejnou skupinu prostředků.

V předchozím příkladu jsme vytvořili skupinu prostředků s názvem "appgw-rg" a umístěním "Západní USA".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Vytvořte virtuální síť a podsíť pro aplikační bránu

Následující příklad ukazuje, jak vytvořit virtuální síť pomocí Resource Managera:

### <a name="step-1"></a>Krok 1

```powershell
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Tento krok rozsah adres 10.0.0.0/24 přiřadí proměnné podsítě, který se má použít k vytvoření virtuální sítě.

### <a name="step-2"></a>Krok 2

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Tento krok vytvoří virtuální síť s názvem "appgwvnet" v prostředku skupiny "appgw-rg" pro oblast západní USA pomocí předpony 10.0.0.0/16 s podsítí 10.0.0.0/24.

### <a name="step-3"></a>Krok 3

```powershell
$subnet = $vnet.subnets[0]
```

Tento krok se přiřadí objekt podsítě k proměnné $subnet pro další kroky.

## <a name="create-an-application-gateway-configuration-object"></a>Vytvoření objektu konfigurace služby Application Gateway

### <a name="step-1"></a>Krok 1

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Tento krok vytvoří konfigurace IP aplikační brány s názvem "gatewayIP01". Při spuštění služby Application Gateway se předá IP adresa z nakonfigurované podsítě a síťový provoz se bude směrovat na IP adresy ve fondu back-end IP adres. Uvědomte si, že každá instance vyžaduje jednu IP adresu.

### <a name="step-2"></a>Krok 2

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Tento krok se konfiguruje fond back-end IP adresy s názvem "pool01" s IP adresami "10.1.1.8, 10.1.1.9, 10.1.1.10". Jsou to IP adresy, které přijímají síťový provoz, který přichází z koncového bodu front-end IP adresy. Předchozí IP adresy nahradíte vlastními aplikačními koncovými body IP adresy.

### <a name="step-3"></a>Krok 3

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Tento krok nakonfiguruje aplikaci brány nastavení "poolsetting01" pro zatížení vyrovnáváním síťového provozu ve fondu back-end.

### <a name="step-4"></a>Krok 4

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Tento krok nakonfiguruje port front-end IP adresy s názvem "frontendport01" pro ILB.

### <a name="step-5"></a>Krok 5

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Tento krok vytvoří konfiguraci front-end IP adresy, nazvanou "fipconfig01" a přidruží ji s privátní IP Adresou z aktuální podsítě virtuální sítě.

### <a name="step-6"></a>Krok 6

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Tento krok vytvoří naslouchací proces nazvaný "listener01" a přiřadí front-end port ke konfiguraci front-end IP adresy.

### <a name="step-7"></a>Krok 7

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Tento krok vytvoří pravidlo směrování pro vyrovnávání zatížení názvem "rule01", které konfiguruje chování nástroje pro vyrovnávání zatížení.

### <a name="step-8"></a>Krok 8

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Tento krok se nakonfiguruje velikost instance aplikační brány.

> [!NOTE]
> Výchozí hodnota *InstanceCount* je 2, přičemž maximální hodnota je 10. Výchozí hodnota *GatewaySize* je Medium (Střední). Můžete vybrat mezi Standard_Small, Standard_Medium a Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Vytvořte aplikační bránu pomocí New-AzureApplicationGateway

Vytvoří aplikační brána se všemi položkami konfigurace z předchozích kroků. V tomto příkladu má služba Application Gateway název „appgwtest“.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Tento krok vytvoří aplikační brána se všemi položkami konfigurace z předchozích kroků. V příkladu se aplikační brána nazývá „appgwtest“.

## <a name="delete-an-application-gateway"></a>Odstranění služby Application Gateway

Pokud chcete odstranit aplikační bránu, musíte udělat následující kroky v pořadí:

1. Pomocí rutiny `Stop-AzureRmApplicationGateway` zastavte bránu.
2. Pomocí rutiny `Remove-AzureRmApplicationGateway` bránu odeberte.
3. Zkontrolujte odstranění brány pomocí rutiny `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Krok 1

Získejte objekt služby Application Gateway a přidružte ho k proměnné „$getgw“.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Krok 2

Pomocí rutiny `Stop-AzureRmApplicationGateway` zastavte službu Application Gateway. Tento příklad ukazuje `Stop-AzureRmApplicationGateway` rutiny na prvním řádku, následovanou výstupem.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Jakmile je služba Application Gateway v zastaveném stavu, pomocí rutiny `Remove-AzureRmApplicationGateway` službu odstraňte.

```powershell
Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
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

Pokud chcete zkontrolovat, že se služba odstranila, můžete použít rutinu `Get-AzureRmApplicationGateway`. Tenhle krok není povinný.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Další postup

Když chcete konfigurovat přesměrování zpracování SSL, přejděte do části [Konfigurace aplikační brány pro přesměrování zpracování SSL](application-gateway-ssl.md).

Když chcete provést konfiguraci aplikační brány pro použití s ILB, přečtěte si část [Vytvoření aplikační brány s interním nástrojem pro vyrovnávání zatížení (ILB)](application-gateway-ilb.md).

Pokud chcete další informace o obecných možnostech vyrovnávání zatížení, přečtěte si část:

* [Nástroj pro vyrovnávání zatížení Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

