---
title: Přehled služby Azure FarmBeats
description: Poskytuje přehled Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ff28ae7dbff40910ac0431fb47f7f0be3e1569c0
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538132"
---
# <a name="overview-of-azure-farmbeats"></a>Přehled služby Azure FarmBeats

Azure FarmBeats je kolekce služeb a schopností Azure, které jsou navržené tak, aby vám usnadnily rychlé vytváření inteligentních řešení založených na datech v zemědělství. Azure FarmBeats je nabídka Azure Marketplace, která umožňuje získávat, agregovat a zpracovávat data týkající se zemědělství z různých zdrojů, jako jsou senzory, DRONY zachraňují životy, kamery a satelity, a to bez investování do prostředků pro rozsáhlou datovou strojírenství.

> [!NOTE]
> Služba Azure FarmBeats je aktuálně ve verzi Public Preview. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats se poskytuje bez smlouvy o úrovni služeb. Pro podporu použijte [Fórum Azure FarmBeats](https://aka.ms/FarmBeatsMSDN ) .

Pomocí Azure FarmBeats budete moct získat data z různých zdrojů, jako jsou senzory, satelity, DRONY zachraňují životy, a to vše v kontextu farmy (zeměpisné oblasti zájmu).

Budete moci:

- Agregace zemědělských datových sad od různých poskytovatelů
- Rychlé vytváření modelů s umělou logikou a strojového jazyka (AI/ML) odmítnutím různých datových sad

Azure FarmBeats poskytuje robustní a snadný způsob, jak provádět tyto akce:

- Vytvořte mapu pro farmu pomocí souboru. JSON.
- Vyhodnotit stav farmy pomocí vegetativního indexu a vodního indexu na základě satelitních snímků.
- Získejte doporučení, kolik senzorů se má použít a kam je umístit.
- Sledujte stav farmy tím, že vizualizujete pozemní data shromážděná senzory od různých dodavatelů snímačů.
- Získejte mapu vlhkosti půdy založenou na Fusion satelitních a senzorových dat.
- Sestavování modelů AI/ML nad agregovanou datovou sadu vám umožní získat užitečné poznatky.
- Vytvořte nebo Rozšiřte své řešení pro digitální zemědělství poskytováním poradců pro stav farmy.

Komponenty Azure FarmBeats jsou popsány v následujících částech tohoto článku.

## <a name="data-hub"></a>Datové centrum

Datový rozbočovač Azure FarmBeats je vrstva rozhraní API, která umožňuje agregaci, normalizaci a kontextování různých datových sad v zemědělství napříč poskytovateli. V této verzi Preview můžete využít dva poskytovatele senzorů [Davisové nástroje](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), jednoho satelitního ukázkového cloudového satelitního cloudu [– 2](https://sentinel.esa.int/web/sentinel/home)a tři pomocí dronů [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/). Datový rozbočovač je navržený jako platforma rozhraní API a pracujeme s mnoha dalšími poskytovateli pro integraci s Azure FarmBeats, takže při sestavování řešení máte větší možnosti.

## <a name="accelerator"></a>Accelerator

Ukázkové řešení, které je postavené na datovém centru, které Jumpstarts vaše uživatelské rozhraní a vývoj modelů. Tato webová aplikace využívá rozhraní API k předvedení vizualizace dat s příjmovým senzorem jako grafů a vizualizaci výstupu modelu jako map. Akcelerátor můžete použít například k rychlému vytvoření farmy a ke snadnému získání mapy pro vegetaci nebo k mapě umístění senzoru pro danou farmu.

## <a name="resources"></a>Zdroje informací

Další informace najdete na [blogu](https://aka.ms/AzureFarmBeats) o Azure FarmBeats a na [fórech](https://aka.ms/FarmBeatsMSDN) .

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure FarmBeats, přejděte na web [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) a spusťte proces nasazení.
