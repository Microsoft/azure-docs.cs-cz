---
title: 'Konfigurace globální dosah – ExpressRoute: Azure | Dokumentace Microsoftu'
description: Tento článek vám pomůže propojit okruhy ExpressRoute tvořit privátní sítě mezi místními sítěmi a povolit globální dosah.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: ab1098ca65ad92cffdbe1dfb24fd43fcc8f10eae
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431668"
---
# <a name="configure-expressroute-global-reach-preview"></a>Konfigurace ExpressRoute globální dosah (preview)
Tento článek vám pomůže nakonfigurovat ExpressRoute globální dosah pomocí Powershellu. Další informace najdete v tématu [ExpressRouteRoute globální dosah](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Před zahájením
> [!IMPORTANT]
> Tato veřejná Předběžná verze je k dispozici bez smlouvu o úrovni služeb a nemělo používat pro produkční úlohy. Některé funkce nemusí být podporované, mohou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Před zahájením konfigurace, zkontrolujte následující body:

* Že jste nainstalovali nejnovější verzi Azure Powershellu. Další informace najdete v článku [Instalace a konfigurace Azure PowerShellu](/powershell/azure/azurerm/install-azurerm-ps).
* Vysvětlení zřizování okruhů ExpressRoute [pracovních postupů](expressroute-workflows.md).
* Aby vaše okruhy ExpressRoute jsou ve stavu zřízeno.
* Tento soukromý partnerský vztah Azure je nakonfigurovaná na okruhů ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure
Spustit konfiguraci, přihlaste se ke svému účtu Azure. 

Otevřete konzolu Powershellu se zvýšenými oprávněními a připojte se ke svému účtu. Příkaz vás vyzve k zadání pověření přihlásit ke svému účtu Azure.  

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
Můžete povolit globální dosah ExpressRoute mezi jakékoli dva okruhy ExpressRoute nacházejí v podporované země a byly vytvořeny v různých umístění partnerského vztahu. Pokud vaše předplatné vlastní obou okruzích, můžete buď okruh a spustit konfiguraci v následujících částech. 

Pokud dva okruhy jsou v různých předplatných Azure, je nutné autorizace z jednoho předplatného Azure. Pak můžete předat autorizační klíč při spuštění příkazu konfigurace v rámci předplatného Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Povolit připojení mezi místními sítěmi

Použijte následující příkazy k získání okruh 1 a 2 okruhu. Dva okruhy jsou ve stejném předplatném.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Spusťte následující příkaz okruh 1 a předat ID privátní partnerský vztah okruhu 2. Při spuštění příkazu, vezměte na vědomí následující:

* ID privátní partnerský vztah vypadá podobně jako v následujícím příkladu: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix* musí být IPv4 je/29 podsítě, například "10.0.0.0/29". K navázání připojení mezi dva okruhy ExpressRoute používáme IP adres v této podsíti. Adresy byste neměli používat v této podsíti ve virtuálním sítím Azure nebo v místní síti.

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

Uložte konfiguraci v okruhu 1 následujícím způsobem:
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po dokončení předchozí operace a pak byste měli mít připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Okruhy ExpressRoute v různých předplatných Azure

Pokud dva okruhy nejsou ve stejném předplatném Azure, musíte autorizaci. V následující konfiguraci autorizace je vygenerována v rámci předplatného k okruhu 2 a autorizační klíč je předán okruh 1.

Generovat autorizačního klíče. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
ID privátní partnerský vztah okruhu 2, stejně jako autorizační klíč si poznamenejte.

Spusťte následující příkaz okruh 1. Předejte ID privátní partnerský vztah okruhu 2 a autorizačního klíče.
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Uložte konfiguraci v okruhu 1.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po dokončení předchozí operace a pak byste měli mít připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Získání a ověřit konfiguraci

Použijte následující příkaz k ověření konfigurace na okruh, kde byla provedena konfigurace (například 1 okruh v předchozím příkladu).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Pokud spustíte jednoduše *$ckt1* v prostředí PowerShell, uvidíte *CircuitConnectionStatus* ve výstupu. Zjistíte, jestli je připojení vytvořeno, "Připojeno" nebo "Odpojené". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Zakázat připojení mezi místními sítěmi

Zakázat připojení, spusťte příkazy proti okruh, kde byla provedena konfigurace (například 1 okruh v předchozím příkladu).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Můžete spustit operaci Get, abyste ověřili stav. 

Po dokončení předchozí operace už nebude mít připojení mezi vaší místní síti prostřednictvím okruhů ExpressRoute. 


## <a name="next-steps"></a>Další postup
1. [Další informace o ExpressRoute globální dosah](expressroute-global-reach.md)
2. [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Propojení okruhu ExpressRoute ke službě Azure virtual network](expressroute-howto-linkvnet-arm.md)


