---
title: Globální dosah Azure ExpressRoute | Dokumentace Microsoftu
description: Tento článek vám pomůže propojit okruhy ExpressRoute tvořit privátní sítě mezi místními sítěmi a povolit globální dosah.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 90f34da10e0193e31a0f70187463799013f52be8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966811"
---
# <a name="link-expressroute-circuits-to-enable-expressroute-global-reach-preview"></a>Okruhy ExpressRoute odkaz umožňující ExpressRoute globální dosah (Preview)

ExpressRoute je privátní a odolná proti selháním způsob, jak připojit místní sítě k Microsoft Cloud. Mnoho cloudových služeb Microsoftu, jako je například Azure, Office 365 a Dynamics 365 můžete přistupovat z vašeho privátního datového centra nebo vaší podnikové síti. Například může mít firemní pobočky v kalifornském San Franciscu okruh ExpressRoute v Silicon Valley a jinou firemní pobočku v Baltimore s okruhem ExpressRoute ve Washingtonu, DC. 

Obou poboček může mít vysokorychlostní připojení k prostředkům Azure v USA – východ a USA – západ. Firemní pobočky však nelze vyměňovat data navzájem přímo. Jinými slovy 10.0.1.0/24 si mohou vyměňovat data s 10.0.3.0/24 a 10.0.4.0/24, ale ne s 10.0.2.0/24.

![Bez][1]

S **ExpressRoute globální dosah**, je možné propojit okruhy ExpressRoute společně k soukromým datům sítě mezi místními sítěmi. V příkladu výše, a uveďte ExpressRoute globální dosah svého pracoviště San Francisku (10.0.1.0/24) přímo vyměňovat data s vaší Baltimore office (10.0.2.0/24) prostřednictvím stávajících okruhů ExpressRoute a pomocí globální síti Microsoftu. 

![s][2]

Globální dosah ExpressRoute je aktuálně podporované v těchto zemích:

* Spojené státy
* Spojené království 
* Japonsko

Okruhů ExpressRoute, musí být vytvořeny na [umístění partnerského vztahu ExpressRoute](expressroute-locations.md) nad zemí. Povolit globální dosah ExpressRoute mezi [různých geopolitických oblastí](expressroute-locations.md), vaší obvody musí být SKU úrovně Premium.


## <a name="before-you-begin"></a>Než začnete

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Před zahájením konfigurace, je potřeba zkontrolovat následující požadavky.

* Nainstalujte nejnovější verzi Azure Powershellu. Zobrazit [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Vysvětlení zřizování okruhů ExpressRoute [pracovních postupů](expressroute-workflows.md).
* Zajistěte, aby že vaše okruhy ExpressRoute jsou ve stavu zřízená.
* Ujistěte se, že soukromý partnerský vztah Azure je nakonfigurovaný na okruhů ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Přihlaste se do svého účtu Azure
Spustit konfiguraci, musíte se přihlásit ke svému účtu Azure. 

Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Příkaz zobrazí výzvu k přihlášení přihlašovací údaje ke svému účtu Azure.  

```powershell
Connect-AzureRmAccount
```

Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

```powershell
Get-AzureRmSubscription
```

Určete předplatné, které chcete použít.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifikujte okruhů ExpressRoute pro konfiguraci
Můžete povolit globální dosah ExpressRoute mezi jakékoli dva okruhy ExpressRoute nacházejí v podporované země a jejich vytvoření při různých umístění partnerského vztahu. Pokud vaše předplatné vlastní obou okruzích, můžete buď okruh a spustit konfiguraci v následujících částech. Pokud dva okruhy jsou v různých předplatných Azure, bude nutné autorizace z jednoho předplatného Azure a předat autorizační klíč při spuštění příkazu konfigurace v rámci předplatného Azure.

## <a name="to-enable-connectivity-between-your-on-premises-networks"></a>Povolit připojení mezi místními sítěmi

Použijte následující příkazy k získání okruh 1 a 2 okruhu. Dva okruhy jsou ve stejném předplatném.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Spusťte následující příkaz okruh 1 a předejte mu ID 2 privátní partnerský vztah okruhu společnosti.

Všimněte si, že privátní partnerský vztah pro Id vypadá jako:

`/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering`


```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

*- AddressPrefix* musí být IPv4 je/29 podsítě, například "10.0.0.0/29". IP adresy v této podsíti použije k navázání připojení mezi dva okruhy ExpressRoute. Adresy byste neměli používat v této podsíti ve virtuálních sítích Azure nebo v místních sítí. 


Uložte konfiguraci v okruhu 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po dokončení předchozí operace a pak byste měli mít připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Okruhy ExpressRoute v různých předplatných Azure

Pokud dva okruhy nejsou ve stejném předplatném Azure, budete potřebovat autorizaci. V následující konfiguraci autorizace je vygenerována v předplatném okruh 2 a autorizační klíč je předána na okruh 1.

Generovat autorizačního klíče. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Poznamenejte si ID 2 privátní partnerský vztah okruhu společnosti, jakož i autorizační klíč.

Spusťte následující příkaz okruh 1. Předejte 2 privátní partnerský vztah okruhu ID a autorizační klíč

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Uložte konfiguraci v okruhu 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po dokončení předchozí operace by měla mít připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

## <a name="to-get-and-verify-the-configuration"></a>K získání a ověřit konfiguraci

Použijte následující příkaz k ověření konfigurace na okruh, kde byla provedena konfigurace. Tento příklad používá okruh 1 z předchozího příkladu.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Pokud spustíte jednoduše *$ckt1* v prostředí PowerShell, zobrazí se vám *CircuitConnectionStatus* ve výstupu. To zjistíte, zda připojení se naváže, "Připojeno", nebo Ne, "Odpojené". 

## <a name="to-disable-connectivity-between-your-on-premises-networks"></a>Chcete-li zakázat připojení mezi místními sítěmi

Pokud chcete zakázat, spusťte příkazy proti okruh, kde byla provedena konfigurace. Tento příklad používá 1 okruh v předchozích příkladech.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Můžete spustit operaci Get, abyste ověřili stav. 

Po dokončení výše uvedené operace se už nebude mít připojení mezi vaší místní síti prostřednictvím okruhů ExpressRoute. 

## <a name="next-steps"></a>Další postup
1. [Další informace o ExpressRoute globální dosah](expressroute-faqs.md#globalreach)
2. [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Propojení okruhu ExpressRoute k virtuální síti Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram bez globálním dosahem"
[2]: ./media/expressroute-global-reach/2.png "diagram s globálním dosahem"