---
title: Přesun okruhů ExpressRoute z modelu nasazení Classic do Resource Manager | Dokumentace Microsoftu
description: Tato stránka obsahuje přehled toho, co potřebujete vědět o přemostění modelů nasazení Classic a Resource Manager.
documentationcenter: na
services: expressroute
author: ganesr
manager: carmonm
editor: ''
ms.assetid: bdf01217-1a98-4ec0-a08e-d84fd37f78af
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: ganesr
ms.openlocfilehash: 7f8386b518ada850fc03e23c5cae3b159b3b213e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "22710061"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Přesun okruhů ExpressRoute z modelu nasazení Classic do Resource Manager
Tento článek obsahuje přehled toho, co znamená přesun okruhu Azure ExpressRoute z modelu nasazení Classic do Azure Resource Manager.

Jeden okruh ExpressRoute můžete použít pro připojení k virtuálním sítím, které jsou nasazené jak v modelu nasazení Classic, tak i Resource Manager. Okruh ExpressRoute, bez ohledu na to, jak je vytvořený, se nyní může propojit k virtuálním sítím v obou modelech nasazení.

![Okruh ExpressRoute propojující se k virtuálním sítím v obou modelech nasazení](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Okruhy ExpressRoute vytvořené v modelu nasazení Classic
Nejprve je nutné přesunout do modelu nasazení Resource Manager okruhy ExpressRoute, které jsou vytvořené v modelu nasazení Classic, aby byla možné připojení k oběma modelům nasazení, Classic i Resource Manager. Během přesunu připojení nedojde ke ztrátě nebo přerušení připojení. Všechna propojení okruhu do virtuální sítě v modelu nasazení Classic (v rámci stejného předplatného i mezi předplatnými) se zachovají.

Po úspěšném dokončení přesunu okruh ExpressRoute vypadá a pracuje úplně stejně jako okruh ExpressRoute, který byl vytvořený v modelu nasazení Resource Manager. Nyní můžete vytvořit připojení k virtuálním sítím v modelu nasazení Resource Manager.

Po přesunutí okruhu ExpressRoute do modelu nasazení Resource Manager můžete spravovat životní cyklus okruhu ExpressRoute jenom pomocí modelu nasazení Resource Manager. To znamená, že můžete provádět operace, jako je přidání, aktualizace nebo odstranění partnerských vztahů, aktualizace vlastností okruhu (například šířky pásma, SKU a typu fakturace) a odstranění okruhů, pouze v modelu nasazení Resource Manager. V dál uvedených částech o okruzích, které jsou vytvořené v modelu nasazení Resource Manager, najdete další podrobnosti o tom, jak můžete spravovat přístup k oběma modelům nasazení.

Provedení přesunu se váš poskytovatel připojení nemusí účastnit.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Okruhy ExpressRoute vytvořené v modelu nasazení Resource Manager
Můžete povolit, aby okruhy ExpressRoute, které jsou vytvořené v modelu nasazení Resource Manager, byly přístupné z obou modelů nasazení. Přístup z obou modelů nasazení jde povolit pro jakýkoli okruh ExpressRoute v rámci vašeho předplatného.

* Okruhy ExpressRoute, které jsou vytvořené v modelu nasazení Resource Manager, nemají standardně přístup k modelu nasazení Classic.
* Okruhy ExpressRoute, které byly přesunuté z modelu nasazení Classic do modelu nasazení Resource Manager, jsou standardně přístupné z obou modelů nasazení.
* Okruh ExpressRoute má vždy přístup k modelu nasazení Resource Manager, bez ohledu na to, jestli byl vytvořený ve modelu nasazení Classic nebo Resource Manager. To znamená, že můžete vytvořit připojení k virtuálním sítím vytvořeným v modelu nasazení Resource Manager podle pokynů v tématu [Způsob propojení virtuálních sítí](expressroute-howto-linkvnet-arm.md).
* Přístup k modelu nasazení Classic řídí parametr **allowClassicOperations** v okruhu ExpressRoute.

> [!IMPORTANT]
> Platí všechny kvóty, které jsou popsané na stránce [Omezení služby](../azure-subscription-service-limits.md). Například standardní okruh může mít maximálně 10 připojení virtuální sítě přes modely nasazení Classic i Resource Manager.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Řízení přístupu k modelu nasazení Classic
Můžete povolit jeden okruh ExpressRoute k propojení virtuálních sítí v obou modelech nasazení nastavením parametru **allowClassicOperations** okruhu ExpressRoute.

Nastavení parametru **allowClassicOperations** na hodnotu TRUE vám umožní propojit virtuální sítě z obou modelů nasazení k okruhu ExpressRoute. Můžete se propojit k virtuálním sítím v modelu nasazení Classic podle pokynů v tématu [Způsob propojení virtuálních sítí v modelu nasazení Classic](expressroute-howto-linkvnet-classic.md). Můžete se propojit k virtuálním sítím v modelu nasazení Resource Manager podle pokynů v tématu [Způsob propojení virtuálních sítí v modelu nasazení Resource Manager](expressroute-howto-linkvnet-arm.md).

Nastavení parametru **allowClassicOperations** na hodnotu FALSE zablokuje přístup k obvodu z modelu nasazení Classic. Nicméně všechna propojení virtuálních sítí v modelu nasazení Classic se zachovají. V takovém případě není okruh ExpressRoute v modelu nasazení Classic viditelný.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Podporované operace v modelu nasazení Classic
Podporovány jsou následující klasické operace pro okruh ExpressRoute, pokud je parametr **allowClassicOperations** nastavený na hodnotu TRUE:

* Získání informací o okruhu ExpressRoute
* Vytvoření, aktualizace, získání nebo odstranění propojení virtuální sítě ke klasickým virtuálním sítím
* Vytvoření, aktualizace, získání nebo odstranění autorizací propojení virtuální sítě pro připojení mezi předplatnými

Nejde provádět následující klasické operace při nastavení parametru **allowClassicOperations** na hodnotu TRUE:

* Vytvoření, aktualizace, získání nebo odstranění partnerských vztahů protokolu BGP pro soukromé partnerské vztahy Azure, veřejné partnerské vztahy Azure a partnerské vztahy Microsoftu
* Odstranění okruhů ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Komunikace mezi modely nasazení Classic a Resource Manager
Okruh ExpressRoute pracuje jako most mezi modelem nasazení Classic a modelem nasazení Resource Manager. Přenos dat mezi virtuálními počítači ve virtuálních sítích v modelu nasazení Classic a těmi ve virtuálních sítích v modelu nasazení Resource Manager prochází službou ExpressRoute, pokud jsou obě virtuální sítě propojené ke stejnému okruhu ExpressRoute.

Agregační propustnost je omezená kapacitou propustnosti brány virtuální sítě. Přenos dat v takových případech nevstupuje do sítí poskytovatele připojení nebo do vašich sítí. Přenos mezi virtuálními sítěmi je plně obsažen v rámci sítě Microsoftu.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Přístup k prostředkům veřejných partnerských vztahů Azure a partnerských vztahů Microsoftu
Můžete nadále přístup k prostředkům, které jsou obvykle přístupné prostřednictvím veřejných partnerských vztahů Azure a partnerských vztahů Microsoftu, bez přerušení.  

## <a name="whats-supported"></a>Co je podporováno
Tato část popisuje, co je podporováno pro okruhy ExpressRoute:

* Jeden okruh ExpressRoute můžete použít pro přístup k virtuálním sítím, které jsou nasazené v modelu nasazení Classic i Resource Manager.
* Můžete přesunout okruh ExpressRoute z modelu nasazení Classic do Resource Manager. Po přesunu okruh ExpressRoute vypadá a pracuje stejně jako kterýkoli jiný okruh ExpressRoute vytvořený v modelu nasazení Resource Manager.
* Je možné přesunout jenom okruh ExpressRoute. Pomocí této operace nejde přesunout propojení okruhu, virtuální sítě a brány VPN.
* Po přesunutí okruhu ExpressRoute do modelu nasazení Resource Manager můžete spravovat životní cyklus okruhu ExpressRoute jenom pomocí modelu nasazení Resource Manager. To znamená, že můžete provádět operace, jako je přidání, aktualizace nebo odstranění partnerských vztahů, aktualizace vlastností okruhu (například šířky pásma, SKU a typu fakturace) a odstranění okruhů, pouze v modelu nasazení Resource Manager.
* Okruh ExpressRoute pracuje jako most mezi modelem nasazení Classic a modelem nasazení Resource Manager. Přenos dat mezi virtuálními počítači ve virtuálních sítích v modelu nasazení Classic a těmi ve virtuálních sítích v modelu nasazení Resource Manager prochází službou ExpressRoute, pokud jsou obě virtuální sítě propojené ke stejnému okruhu ExpressRoute.
* Připojení mezi předplatnými je podporováno v modelu nasazení Classic i Resource Manager.
* Po přesunutí okruhu ExpressRoute z modelu Classic na model Azure Resource Manager můžete [migrovat virtuální sítě propojené s okruhem ExpressRoute](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Co není podporováno
Tato část popisuje, co není podporováno pro okruhy ExpressRoute:

* Správa životního cyklu okruhu ExpressRoute z modelu nasazení Classic.
* Podpora řízení přístupu na základě role (RBAC) pro model nasazení Classic. Nejde provádět řízení RBAC na okruh v modelu nasazení Classic. Libovolný správce nebo spolusprávce předplatného může propojit virtuální sítě k okruhu nebo toto propojení zrušit.

## <a name="configuration"></a>Konfigurace
Postupujte podle pokynů uvedených v tématu [Přesun okruhu ExpressRoute z modelu nasazení Classic do Resource Manager](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Další kroky
* [Migrace virtuálních sítí propojených s okruhem ExpressRoute z modelu Classic na model Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Informace o pracovním postupu najdete v tématu [Pracovní postupy zřizování okruhů ExpressRoute a stavy okruhu](expressroute-workflows.md).
* Konfigurace připojení ExpressRoute:
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md)

