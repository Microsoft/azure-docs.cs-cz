---
title: Informace o Azure ExpressRoute Direct
description: Seznamte se s klíčovými funkcemi Azure ExpressRoute Direct a informací, které jsou potřeba k tomu, aby ExpressRoute přímé, jako jsou dostupné SKU a technické požadavky.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: duau
ms.openlocfilehash: 56d6a76991c4386be45b2c18f4edb3d363e58fa5
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027138"
---
# <a name="about-expressroute-direct"></a>O službě ExpressRoute Direct

ExpressRoute Direct nabízí možnost připojit se přímo k globální síti Microsoftu při partnerských umístěních, která jsou strategicky distribuována po celém světě. ExpressRoute Direct poskytuje připojení Dual 100 GB/s nebo 10 GB, které podporuje škálování aktivní/aktivní. U ER Direct můžete pracovat s jakýmkoli poskytovatelem služeb.

K dispozici jsou klíčové funkce, které ExpressRoute Direct poskytuje, ale nejsou omezené na:

* Masivní příjem dat do služeb jako Storage a Cosmos DB
* Fyzická izolace pro obory, které jsou regulováné a vyžadují vyhrazené a izolované připojení, jako je například bankovnictví, státní správa a maloobchodní prodej.
* Podrobné řízení distribuce okruhů podle organizační jednotky

## <a name="onboard-to-expressroute-direct"></a>Připojit k ExpressRoute Direct

Než začnete používat ExpressRoute Direct, musíte nejdřív zaregistrovat své předplatné. Pokud se chcete zaregistrovat, spusťte následující příkazy pomocí Azure PowerShell:

1.  Přihlaste se k Azure a vyberte předplatné, které chcete zaregistrovat.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Zaregistrujte své předplatné pro Public Preview pomocí následujícího příkazu:
1. 
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Po zaregistrování ověřte, že je ve vašem předplatném zaregistrovaný poskytovatel prostředků **Microsoft. Network** . Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků.

1. Přístup k nastavením předplatného, jak je popsáno v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).

1. V rámci vašeho předplatného pro **poskytovatele prostředků** ověřte, že poskytovatel **Microsoft. Network** zobrazuje **registrovaný** stav. Pokud poskytovatel prostředků Microsoft. Network není uveden v seznamu registrovaných zprostředkovatelů, přidejte ho.

Pokud začnete používat ExpressRoute Direct a všimnete si, že ve zvoleném umístění partnerského vztahu nejsou dostupné žádné porty, ExpressRouteDirect@microsoft.com můžete požádat o další inventarizaci e-mailem.

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute pomocí poskytovatele služeb a ExpressRoute Direct

| **ExpressRoute pomocí poskytovatele služeb** | **ExpressRoute Direct** | 
| --- | --- |
| Pomocí poskytovatelů služeb umožňuje rychlou registraci a připojení do stávající infrastruktury. | Vyžaduje infrastrukturu s 100 GB/s/10 GB/s a úplnou správu všech vrstev.
| Se integruje se stovkami poskytovatelů, včetně ethernetových a MPLS | Přímá a vyhrazená kapacita pro regulované odvětví a obrovské přijímání dat |
| SKU okruhů od 50 do 10 GB/s | Zákazník může vybrat kombinaci následujících SKU okruhu na 100 – ExpressRoute s přímým přístupem: <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> Zákazník může vybrat kombinaci následujících SKU okruhu pro ExpressRoute Direct na 10 GB/s.<ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>
| Optimalizováno pro jednoho tenanta | Optimalizováno pro jednoho tenanta s více obchodními jednotkami a několika pracovními prostředími

## <a name="expressroute-direct-circuits"></a>Přímé okruhy ExpressRoute

Microsoft Azure ExpressRoute umožňuje rozšiřování místní sítě do cloudu Microsoftu přes soukromé připojení, které usnadňuje poskytovatel připojení. Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako jsou Microsoft Azure a Microsoft 365.

Každé umístění partnerského vztahu má přístup ke globální síti Microsoftu a má ve výchozím nastavení přístup k libovolné oblasti v geopolitické zóně. Přístup ke všem globálním oblastem získáte pomocí okruhu Premium.  

Funkce ve většině scénářů je ekvivalentní okruhům, které používají poskytovatele služby ExpressRoute k provozu. Aby bylo možné podporovat další členitost a nové funkce, které jsou nabízeny pomocí ExpressRoute Direct, existují některé klíčové funkce, které existují na ExpressRoute přímých Okruhech.

## <a name="circuit-skus"></a>SKU okruhů

ExpressRoute Direct podporuje rozsáhlé scénáře přijímání dat do služby Azure Storage a dalších služeb pro velké objemy dat. Okruhy ExpressRoute na 100-GB ExpressRoute Direct teď také podporují **40 GB/** s a * 100 * SKU okruhu GB/s. Fyzické páry portů jsou **100 GB/s nebo 10 GB/** s a můžou mít několik virtuálních okruhů. Velikosti okruhů:

| **100-GB ExpressRoute Direct** | **ExpressRoute přímý přenos s rychlostí 10 GB/s** | 
| --- | --- |
| **Předplacená šířka pásma**: 200 GB/s | **Přihlášená šířka pásma**: 20 GB/s |
| <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> | <ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>

## <a name="technical-requirements"></a>Technické požadavky

* Rozhraní MSEE (Enterprise Edge router) společnosti Microsoft:
    * Duální 10 gigabitových nebo 100 – portů gigabitového Ethernetu v páru směrovačů
    * Připojení k LR vláknu s jedním režimem
    * IPv4 a IPv6
    * IP jednotka MTU 1500 bajtů

* Konektivita/vrstva směrovače 2/+ vrstvy tři:
    * Musí podporovat 1 802.1 Q (Dot1Q) nebo dvě značky 802.1 Q (QinQ) – zapouzdření značky.
    * EtherType = 0x8100
    * Je nutné přidat vnější značku VLAN (STAG) založenou na ID sítě VLAN, které je určeno pro Microsoft, *pouze v QinQ* .
    * Musí podporovat víc relací protokolu BGP (VLAN) na port a zařízení.
    * Připojení IPv4 a IPv6. *Pro protokol IPv6 se vytvoří žádné další podrozhraní. Adresa IPv6 bude přidána do stávajícího podrozhraní*. 
    * Volitelné: podpora [detekce obousměrného předávání (BFD)](./expressroute-bfd.md) , která je nakonfigurovaná ve výchozím nastavení u všech privátních partnerských vztahů v okruhech ExpressRoute.

## <a name="vlan-tagging"></a>Označování značek VLAN

ExpressRoute Direct podporuje označování značek VLAN QinQ a Dot1Q.

* **Označování QINQ VLAN** umožňuje doménám izolované směrování na jednotlivých okruhech ExpressRoute. Azure při vytváření okruhů dynamicky poskytuje značku S-tag a nedá se změnit. Každý partnerský vztah na okruhu (Private a Microsoft) bude jako síť VLAN používat jedinečnou značku C. Značka C se nevyžaduje, aby byla v okruhech na portech Direct ExpressRoute jedinečná.

* **Označení sítě VLAN Dot1Q** umožňuje jednu OZNAČENOU síť VLAN pro každou dvojici přímých portů ExpressRoute. Označení jazyka C používaného pro partnerský vztah musí být jedinečné ve všech okruhech a partnerských vztahů na páru portů ExpressRoute Direct.

## <a name="workflow"></a>Pracovní postup

[![pracovního postupu](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct poskytuje stejnou smlouvu SLA na podnikové úrovni s aktivními nebo aktivními redundantními připojeními do globální sítě Microsoft. Infrastruktura ExpressRoute je redundantní a konektivita do globální sítě Microsoft je redundantní a různorodá a správně škáluje s požadavky zákazníků. 

## <a name="next-steps"></a>Další kroky

[Konfigurace ExpressRoute Direct](expressroute-howto-erdirect.md)
