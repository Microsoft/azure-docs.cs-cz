---
title: 'ExpressRoute: přesunutí okruhů z klasického na Azure Resource Manager'
description: Přečtěte si o tom, co se stane, když přesunete okruh Azure ExpressRoute z modelu nasazení Classic do modelu nasazení Azure Resource Manager.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807937"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Přesun okruhů ExpressRoute z modelu nasazení Classic do Resource Manager
Tento článek poskytuje přehled toho, co se stane, když přesunete okruh Azure ExpressRoute z modelu nasazení Classic do modelu nasazení Azure Resource Manager.

Jeden okruh ExpressRoute můžete použít k propojení virtuálních sítí, které jsou nasazeny v modelu nasazení Classic i Správce prostředků.

![Okruh ExpressRoute propojující se k virtuálním sítím v obou modelech nasazení](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Okruhy ExpressRoute vytvořené v modelu nasazení Classic
Okruhy ExpressRoute vytvořené v modelu nasazení Classic musí nejdřív migrovat na model nasazení Správce prostředků. Můžete povolit jenom připojení k modelům nasazení Classic i Správce prostředků. Při přesunu připojení nedojde ke ztrátě nebo přerušení připojení. Všechna propojení mezi okruhy a virtuální sítě v modelu nasazení Classic v rámci stejného předplatného a mezi předplatnými jsou zachována.

Po úspěšném dokončení přesunu se okruh ExpressRoute chová stejně jako ExpressRoute okruh, který byl vytvořen v modelu nasazení Správce prostředků. Nyní můžete vytvořit připojení k virtuálním sítím v modelu nasazení Resource Manager.

Po přesunutí okruhu ExpressRoute do modelu nasazení Resource Manager ho můžete spravovat jenom v modelu nasazení Správce prostředků. Operace správy partnerských vztahů, aktualizace vlastností okruhu a odstranění okruhů budou dostupné jenom prostřednictvím modelu nasazení Správce prostředků. V následující části najdete další podrobnosti o tom, jak můžete spravovat přístup k oběma modelům nasazení.

Nemusíte zahrnovat svého poskytovatele připojení, aby bylo možné přesunout okruh do modelu nasazení Správce prostředků.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Okruhy ExpressRoute vytvořené v modelu nasazení Resource Manager
Můžete povolit, aby okruhy ExpressRoute, které jsou vytvořené v modelu nasazení Resource Manager, byly přístupné z obou modelů nasazení. Jakýkoli okruh ExpressRoute ve vašem předplatném je možné nakonfigurovat tak, aby měl přístup z obou modelů nasazení.

* Okruhy ExpressRoute vytvořené v modelu nasazení Správce prostředků nemají ve výchozím nastavení přístup k modelu nasazení Classic.
* Okruhy ExpressRoute, které byly přesunuty z modelu nasazení Classic do modelu nasazení Správce prostředků, jsou ve výchozím nastavení přístupné z obou modelů nasazení.
* Okruh ExpressRoute má vždycky přístup k modelu nasazení Správce prostředků bez ohledu na to, jestli byl vytvořený v modelu nasazení Správce prostředků nebo Classic. Připojení k virtuálním sítím můžete vytvořit podle pokynů, [Jak propojit virtuální sítě](expressroute-howto-linkvnet-arm.md).
* Přístup k modelu nasazení Classic řídí parametr **allowClassicOperations** v okruhu ExpressRoute.

> [!IMPORTANT]
> Platí všechny kvóty, které jsou popsané na stránce [Omezení služby](../azure-resource-manager/management/azure-subscription-service-limits.md). Například standardní okruh může mít maximálně 10 připojení virtuální sítě přes modely nasazení Classic i Resource Manager.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Řízení přístupu k modelu nasazení Classic
Okruh ExpressRoute můžete povolit připojení k virtuálním sítím v obou modelech nasazení. Provedete to tak, že v okruhu ExpressRoute nastavíte parametr **allowClassicOperations** .

Nastavení parametru **allowClassicOperations** na hodnotu TRUE vám umožní propojit virtuální sítě z obou modelů nasazení k okruhu ExpressRoute. 
* Chcete-li propojit virtuální sítě v modelu nasazení Classic, přečtěte si téma [Jak propojit virtuální sítě pro model nasazení Classic](expressroute-howto-linkvnet-classic.md).
* Chcete-li propojit virtuální sítě v modelu nasazení Správce prostředků, přečtěte si téma [Postup propojení virtuálních sítí v modelu nasazení Správce prostředků](expressroute-howto-linkvnet-arm.md).

Nastavení parametru **allowClassicOperations** na hodnotu FALSE zablokuje přístup k obvodu z modelu nasazení Classic. Všechny virtuální sítě propojené v modelu nasazení Classic jsou ale pořád zachované. Okruh ExpressRoute není viditelný v modelu nasazení Classic.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Podporované operace v modelu nasazení Classic
Podporovány jsou následující klasické operace pro okruh ExpressRoute, pokud je parametr **allowClassicOperations** nastavený na hodnotu TRUE:

* Získání informací o okruhu ExpressRoute
* Vytvoření, aktualizace, získání nebo odstranění propojení virtuální sítě ke klasickým virtuálním sítím
* Vytvoření, aktualizace, získání nebo odstranění autorizací propojení virtuální sítě pro připojení mezi předplatnými

Pokud je však **allowClassicOperations** nastaveno na hodnotu true, nemůžete spustit následující klasické operace:

* Vytvoření, aktualizace, získání nebo odstranění partnerských vztahů protokolu BGP pro soukromé partnerské vztahy Azure, veřejné partnerské vztahy Azure a partnerské vztahy Microsoftu
* Odstranění okruhů ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Komunikace mezi modely nasazení Classic a Resource Manager
Okruh ExpressRoute pracuje jako most mezi modelem nasazení Classic a modelem nasazení Resource Manager. Provoz mezi virtuálními sítěmi pro oba modely nasazení může projít okruhem ExpressRoute, pokud jsou obě virtuální sítě propojené se stejným okruhem.

Agregační propustnost je omezená kapacitou propustnosti brány virtuální sítě. Provoz nevstoupí v takových případech do sítí poskytovatele připojení ani do vaší sítě. Přenos mezi virtuálními sítěmi je plně obsažen v rámci sítě Microsoftu.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Přístup k prostředkům veřejných partnerských vztahů Azure a partnerských vztahů Microsoftu
Můžete nadále přístup k prostředkům, které jsou obvykle přístupné prostřednictvím veřejných partnerských vztahů Azure a partnerských vztahů Microsoftu, bez přerušení.  

## <a name="whats-supported"></a>Co je podporováno
Tato část popisuje, co je podporováno pro okruhy ExpressRoute:

* Jeden okruh ExpressRoute můžete použít pro přístup k virtuálním sítím, které jsou nasazené v modelu nasazení Classic i Resource Manager.
* Můžete přesunout okruh ExpressRoute z modelu nasazení Classic do Resource Manager. Po přesunu bude okruh ExpressRoute dál fungovat jako jakýkoliv jiný okruh ExpressRoute, který je vytvořený v modelu nasazení Správce prostředků.
* Je možné přesunout jenom okruh ExpressRoute. Pomocí této operace nejde přesunout propojení okruhu, virtuální sítě a brány VPN.
* Po přesunutí okruhu ExpressRoute do modelu nasazení Resource Manager můžete spravovat životní cyklus okruhu ExpressRoute jenom pomocí modelu nasazení Resource Manager. To znamená, že můžete spouštět operace, jako je přidání, aktualizace nebo odstranění partnerských vztahů, aktualizace vlastností okruhu (například šířka pásma, SKU a typ fakturace) a odstranění okruhů pouze v modelu nasazení Správce prostředků.
* Okruh ExpressRoute pracuje jako most mezi modelem nasazení Classic a modelem nasazení Resource Manager. Provoz mezi virtuálními počítači v klasických virtuálních sítích a virtuálních počítačích ve Správce prostředků virtuálních sítích může komunikovat prostřednictvím ExpressRoute, pokud jsou obě virtuální sítě propojené se stejným okruhem ExpressRoute.
* Připojení mezi předplatnými je podporováno v modelu nasazení Classic i Resource Manager.
* Po přesunutí okruhu ExpressRoute z modelu Classic na model Azure Resource Manager můžete [migrovat virtuální sítě propojené s okruhem ExpressRoute](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Co není podporováno
Tato část popisuje, co není podporováno pro okruhy ExpressRoute:

* Správa životního cyklu okruhu ExpressRoute z modelu nasazení Classic.
* Podpora řízení přístupu na základě role Azure (Azure RBAC) pro model nasazení Classic. Pro okruh v modelu nasazení Classic nemůžete spouštět ovládací prvky služby řízení přístupu k Azure. Libovolný správce nebo spolusprávce předplatného může propojit virtuální sítě k okruhu nebo toto propojení zrušit.

## <a name="configuration"></a>Konfigurace
Postupujte podle pokynů uvedených v tématu [Přesun okruhu ExpressRoute z modelu nasazení Classic do Resource Manager](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Další kroky
* [Migrace virtuálních sítí propojených s okruhem ExpressRoute z modelu Classic na model Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Informace o pracovním postupu najdete v tématu [Pracovní postupy zřizování okruhů ExpressRoute a stavy okruhu](expressroute-workflows.md).
* Konfigurace připojení ExpressRoute:
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md)

