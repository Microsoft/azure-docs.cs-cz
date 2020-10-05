---
title: Začínáme s ukázkou Poradce pro metriky
titleSuffix: Azure Cognitive Services
description: Přečtěte si o webovém rozhraní Advisor metriky s využitím ukázky, kterou poskytujeme.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: fbc73e20b8cc2baa1cc5c5a5b2f674fb1b2dde84
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90946972"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>Rychlý Start: Prozkoumejte ukázku poradce metriky s příklady dat

> [!Note]
> Ukázka služby metriky pro metriky je jen pro čtení a nebudete moct data připojit. Pokud chcete službu používat na vašich datech, [vytvořte nejdřív prostředek Advisoru metriky](web-portal.md).

Tento článek vám pomůže rychle začít zkoumat klíčové funkce v poradci metriky. Poskytujeme ukázkový web s ukázkovými daty a přednastavenými konfiguracemi, abyste se seznámili s plně vybaveným webovým portálem.

Kliknutím na [Tento odkaz](https://aka.ms/MetricsAdvisor/Demo) přejdete na ukázkový web.

## <a name="view-the-available-sample-data"></a>Zobrazení dostupných ukázkových dat

Po přihlášení k ukázkové lokalitě se zobrazí stránka **datového kanálu** . datový kanál je logická skupina dat časových řad, která je dotazována ze zdroje dat. Další informace o termínech a konceptech používaných v poradci metrik najdete v [glosáři](../glossary.md). 

V seznamu je několik datových kanálů, které se ingestují z různých typů zdrojů dat, jako je Azure SQL Database nebo tabulka Azure. Každá z nich používá mírně různá nastavení konfigurace pro připojení k přidruženým úložištím dat.

:::image type="content" source="../media/sample-datafeeds.png" alt-text="Seznam ukázkových dat" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>Prozkoumejte konfigurace datového kanálu

Klikněte na datový kanál *Ukázka-náklady/výnosy – město/kategorie* . V informačním kanálu se zobrazí několik částí podrobností:

* Název datového kanálu a stav ingestování.
* Seznam metrik, které byly dotazovány ze zdroje dat. Například *náklady* a *výnosy*. 
* Historie výstrah, kdy datový kanál nebude k dispozici 
* Protokoly, kdy se datový kanál aktualizoval   
* Informace a nastavení datového kanálu.

:::image type="content" source="../media/data-feed-view.png" alt-text="Seznam ukázkových dat" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>Zobrazení vizualizací a konfigurací časových řad

Klikněte na metriku *nákladů* v kanálu dat *Ukázka-náklady/výnosy – město/kategorie* . Zobrazí se související časová řada, která je rozdělená podle dimenzí, s vizualizacemi podle historických dat metriky. Modrý pruh kolem dat metriky představuje rozsah očekávané hodnoty z modelů strojového učení služby Advisor metriky. Body, které spadají mimo tento proužek, budou označeny jako červené tečky, které jsou zjištěny anomálie. 

:::image type="content" source="../media/series-visualization.png" alt-text="Seznam ukázkových dat" lightbox="../media/series-visualization.png":::

Detekce anomálií se dá nakonfigurovat optimalizací **konfigurací zjišťování** na levé straně stránky s podrobnostmi metriky. K dispozici je několik metod detekce anomálií, které je možné kombinovat. Můžete také vyzkoušet různé Sensitivities, zjistit směry a další konfigurace. Odkaz **Upřesnit konfiguraci** v dolní části **detekce konfigurací** umožňuje vytvořit složitější a přizpůsobená nastavení detekce, která se dají použít pro skupiny nebo jednotlivé řady. 

Detekci anomálií můžete také ladit tím, že poskytnete zpětnou vazbu k algoritmu detekce. Klikněte na anomálii a pomocí panelu **Přidat zpětnou vazbu** nakonfigurujte stav anomálie, sezónnost a stav bodu změny. Tato zpětná vazba bude začleněna do zjišťování budoucích bodů.  

V dolní části panelu **Přidat zpětná vazba** je odkaz **na centrum incidentů**, který vás přesměruje na stránku analýzy incidentů a analyzuje hlavní příčinu incidentu.  

:::image type="content" source="../media/incident-link.png" alt-text="Seznam ukázkových dat" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>Prozkoumat výsledky detekce anomálií a provést analýzu původní příčiny

Když kliknete na odkaz **na centrum incidentů** z anomálie, zobrazí se stránka analýzy incidentů, která obsahuje diagnostické poznatky o incidentu, jako je závažnost, počet nezúčastněných anomálií a počáteční/koncový čas. Oddíl **hlavní příčiny** zobrazuje automatizované poradenství analýzou stromu incidentů, přičemž vezme v úvahu odchylku, rozdělení a podíl na nadřízených anomálií, což může představovat hlavní příčinu incidentu.

V části **Diagnostika** se zobrazuje strom incidentu spolu s několika kartami pro diagnostiku incidentu.

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Seznam ukázkových dat" lightbox="../media/incident-diagnostic.png":::

Určením hlavní příčiny incidentu můžete provést akci a zmírnit problém, dokud se situace nestane horší. Další přehledy můžete prozkoumat také tak, že kliknete na další poskytnuté diagnostické funkce. 

## <a name="next-steps"></a>Další kroky

- [Použití webového portálu](web-portal.md)
- [Použití rozhraní REST API](rest-api.md)
- [Zprovoznění datových kanálů](../how-tos/onboard-your-data.md)
    - [Správa datových kanálů](../how-tos/manage-data-feeds.md)
    - [Konfigurace pro různé zdroje dat](../data-feeds-from-different-sources.md)
