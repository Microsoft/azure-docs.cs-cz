---
title: Co je Azure FarmBeats
description: Poskytuje přehled Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b067b18985905b226287f9dd10ad4b937fab6df1
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767970"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Přehled služby Azure FarmBeats (Preview)

Azure FarmBeats je nabídka pro firmy, která je dostupná v Azure Marketplace. Umožňuje agregaci zemědělských datových sad mezi poskytovateli. Azure FarmBeats umožňuje vytvářet modely umělé Intelligence (AI) nebo strojové učení (ML) založené na prostředcích s pojistnou datovou sadu. S využitím Azure FarmBeats se podniky v oblasti zemědělství můžou soustředit na základní hodnoty – přidává se místo nerozlišené těžkého zvedání datových techniků.

> [!NOTE]
> Služba Azure FarmBeats je aktuálně ve verzi Public Preview. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats se poskytuje bez smlouvy o úrovni služeb. Pro podporu použijte [Fórum Azure FarmBeats](https://aka.ms/FarmBeatsMSDN ) .

![Beats farmy projektu](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Ve verzi Preview služby Azure FarmBeats můžete:

- Vyhodnotit stav farmy pomocí vegetativního indexu a vodního indexu na základě satelitních snímků.
- Získejte doporučení, kolik senzorů vlhkosti v půdě použít a kam je umístit.
- Sledujte podmínky farmy tím, že vizualizujete pozemní data shromážděná senzory od různých dodavatelů.
- Získejte mapu vlhkosti půdy založenou na Fusion satelitních a senzorových dat.
- Sestavování modelů AI/ML nad agregovanou datovou sadu vám umožní získat užitečné poznatky.
- Vytvořte nebo Rozšiřte své řešení pro digitální zemědělství poskytováním poradců pro stav farmy.

## <a name="datahub"></a>Datahub

Azure FarmBeats DataHub je vrstva rozhraní API, která umožňuje agregaci, normalizaci a kontextování různých datových sad v zemědělství napříč poskytovateli. Pomocí Azure FarmBeats můžete získat:
- **Data ze senzorů** od dvou poskytovatelů senzorů [Davisové](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl nástrojů](https://metos.at/)
- **Satelitních snímků** z Evropské kosmické služby [Sentinel-2 –](https://sentinel.esa.int/web/sentinel/home) družicová mise
- **Pomocí dronůí snímků** ze tří pomocí dronůových poskytovatelů snímků [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)

DataHub je navržená jako rozšiřitelná platforma rozhraní API. Spolupracujeme s mnoha dalšími poskytovateli pro integraci s Azure FarmBeats, takže při sestavování řešení máte větší možnosti.

## <a name="accelerator"></a>Accelerator

Akcelerátor Azure FarmBeats je Ukázková webová aplikace, která je postavená nad DataHub. Odkaz akcelerátoru – spustí vaše uživatelské rozhraní a vývoj modelu. Akcelerátor Azure FarmBeats používá rozhraní API Azure FarmBeats. Vizualizuje ingestovaná data senzorů jako grafy a výstupy modelu jako mapy. Můžete například použít akcelerátor k rychlému vytvoření farmy a získat mapu pro vegetativní rozvržení nebo mapu umístění senzorů pro tuto farmu snadno.

## <a name="resources"></a>Materiály

Azure FarmBeats se nabízí bez dalších poplatků a platíte jenom za prostředky Azure, které používáte. Pomocí níže uvedených prostředků můžete získat další informace o této nabídce:

- Seznamte se s nejnovějšími zprávami služby Azure FarmBeats, které najdete na [blogu o Azure FarmBeats](https://aka.ms/farmbeatsblog).
- Vyhledejte pomoc publikováním otázky na našem [fóru podpory Azure FarmBeats](https://aka.ms/farmbeatssupport).
- Sdělte nám svůj názor prostřednictvím publikování nebo hlasování o funkcích na našem [fóru pro názory na Azure FarmBeats](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Instalace Azure FarmBeats](install-azure-farmbeats.md)
