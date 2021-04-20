---
title: Přesuňte prostředky Azure mezi skupiny prostředků, předplatná nebo oblasti.
description: Přehled typů prostředků Azure, které se dají přesouvat mezi skupinami prostředků, předplatnými nebo oblastmi.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730427"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>Přesunutí prostředků Azure napříč skupinami prostředků, předplatnými nebo oblastmi

Prostředky Azure je možné přesunout do nové skupiny prostředků nebo předplatného nebo do různých oblastí.

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>Přesunutí prostředků mezi skupinami prostředků nebo předplatnými

Prostředky Azure můžete přesunout do jiného předplatného Azure nebo do jiné skupiny prostředků v rámci stejného předplatného. K přesunu prostředků můžete použít Azure Portal, Azure PowerShell, Azure CLI nebo rozhraní REST API. Další informace najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

### <a name="upgrade-a-subscription"></a>Upgrade předplatného

Pokud ve skutečnosti chcete upgradovat předplatné Azure (jako je například přepínání z bezplatného na průběžné platby), budete muset převést své předplatné.

- Pokud chcete upgradovat bezplatnou zkušební verzi, přečtěte si téma [upgrade bezplatné zkušební verze nebo Microsoft Imagine předplatné Azure na průběžné platby](../../cost-management-billing/manage/upgrade-azure-subscription.md).
- Pokud chcete změnit účet s průběžnými platbami, přečtěte si téma [Změna předplatného s průběžnými platbami Azure na jinou nabídku](../../cost-management-billing/manage/switch-azure-offer.md).

Pokud předplatné nepřevedete, [vytvořte žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Jako typ problému vyberte **Správa předplatného** .

## <a name="move-resources-across-regions"></a>Přesunutí prostředků mezi oblasti

Geografické oblasti Azure, oblasti a zóny dostupnosti tvoří základ globální infrastruktury Azure. Geografické [grafy](https://azure.microsoft.com/global-infrastructure/geographies/) Azure obvykle obsahují dvě nebo víc [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). Oblast je oblast v rámci geografické oblasti, která obsahuje Zóny dostupnosti a více datových center.

Po nasazení prostředků v konkrétní oblasti Azure existuje mnoho důvodů, proč je vhodné přesunout prostředky do jiné oblasti.

- **Zarovnat ke konkrétnímu spuštění oblasti**: přesun svých prostředků do nově zavedené oblasti Azure, která nebyla dřív dostupná.
- **Zarovnání pro služby/funkce**: přesunout prostředky, abyste mohli využívat služby nebo funkce, které jsou k dispozici v konkrétní oblasti.
- **Reakce na obchodní vývoj**: Přesunutí prostředků do oblasti v reakci na změny v podniku, jako je fúze nebo akvizice.
- **Zarovnání k blízkosti**: Přesunutí prostředků do místní oblasti do vaší firmy.
- **Splnění požadavků na data**: Přesunutí prostředků pro zajištění souladu s požadavky na data na místě nebo podle potřeby klasifikace dat. [Další informace](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Reakce na požadavky na nasazení**: přesunout prostředky, které byly nasazeny chybně, nebo je přesunout v reakci na kapacitu kapacity.
- **Reakce na vyřazení z provozu**: Přesunutí prostředků z důvodu vyřazených oblastí.

### <a name="move-resources-with-resource-mover"></a>Přesunutí prostředků pomocí Resource stěhovací

Prostředky můžete přesunout do jiné oblasti pomocí programu [Azure Resource stěhovací](../../resource-mover/overview.md). Resource stěhovací poskytuje:

- Jediné centrum pro přesouvání prostředků napříč oblastmi.
- Zkrácená doba přesunutí a složitost. Všechno, co potřebujete, je na jednom místě.
- Jednoduché a konzistentní prostředí pro přesun různých typů prostředků Azure.
- Snadný způsob, jak identifikovat závislosti mezi prostředky, které chcete přesunout. Tato identifikace vám pomůže přesunout související prostředky dohromady, aby vše v cílové oblasti fungovalo podle očekávání, a to po přesunutí.
- Automatické vyčištění prostředků ve zdrojové oblasti, pokud je chcete po přesunutí odstranit.
- Testování. Můžete si vyzkoušet přesunutí a pak ho zahodit, pokud nechcete provést úplné přesunutí.

Prostředky můžete přesunout do jiné oblasti pomocí několika různých metod:

- **Zahájení přesouvání prostředků ze skupiny prostředků**: s touto metodou jste v rámci skupiny prostředků aktivovali přesunutí oblasti. Po výběru prostředků, které chcete přesunout, proces pokračuje v centru zdrojů pro kontrolu závislostí prostředků a orchestruje proces přesunutí. [Další informace](../../resource-mover/move-region-within-resource-group.md).
- **Začněte přesouvat prostředky přímo z centra pro** podávání prostředků: s touto metodou se zahájí proces přesunu oblasti přímo do centra. [Další informace](../../resource-mover/tutorial-move-region-virtual-machines.md).

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit, jestli je typ prostředku podporovaný, přečtěte si téma [Podpora operací přesunutí pro prostředky](move-support-resources.md).
- Další informace o procesu přesunutí oblasti najdete v tématu [o procesu přesunutí](../../resource-mover/about-move-process.md).
