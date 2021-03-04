---
title: Diagnostikujte incidenty pomocí Poradce metriky.
titleSuffix: Azure Cognitive Services
description: Naučte se diagnostikovat incidenty pomocí Poradce pro metriky a získat podrobná zobrazení anomálií ve vašich datech.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: ecbfb2d9acf6c62f95c264a14e306442db25e483
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703418"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>Postupy: Diagnostika incidentu pomocí Poradce pro metriky

Poradce metrik nabízí několik funkcí pro diagnostiku a poskytuje podrobné zobrazení zjištěných incidentů a poskytuje analýzu původní příčiny. Když je u metriky zjištěna skupina anomálií, Poradce pro metriky bude seskupovat anomálie do hierarchie a analyzovat je nad ní.

> [!NOTE]
> Aktuálně metrika metrik podporuje diagnostiku incidentů pro metriky s alespoň jedním rozměrem a měření s  *číselným* typem. Metrika musí mít agregovanou hodnotu dimenze, jako je součet pro každou dimenzi, která se používá k sestavení diagnostické hierarchie. Poradce metrik nabízí [**Automatické shrnutí nastavení**](onboard-your-data.md#automatic-roll-up-settings) , které vám pomůžou s generováním agregovaných hodnot. 

Kliknutím na **centrum incidentů** v levém navigačním okně zobrazíte všechny incidenty pod danou metrikou. V horní části stránky můžete vybrat různé metriky, abyste viděli jejich konfigurace detekce, výsledky detekce a časový rozsah můžete změnit.

> [!TIP]
> Do **centra incidentů** můžete také získat:
> * Klikněte na datový bod ve vizualizaci metriky a použijte odkazy v dolní části okna **Přidat zpětnou vazbu** , která se zobrazí.
> * Kliknutím na jednu ze anomálií na kartě **incidenty** pro vaši metriku. 

V části **Přehled** najdete výsledky detekce, včetně počtu anomálií a upozornění v rámci vybraného časového rozsahu.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="Centrum incidentů" lightbox="../media/diagnostics/incident-hub-overview.png":::

Zjištěné incidenty v rámci vybrané metriky a časového rozsahu jsou uvedeny v **seznamu incidentů**. Existují možnosti pro filtrování a řazení incidentů. Například podle závažnosti. Kliknutím na jeden z incidentů přejdete na stránku **incidentu** , kde najdete další diagnostiku.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="Seznam incidentů" lightbox="../media/diagnostics/incident-list.png":::

Část **Diagnostika** vám umožní provést podrobnou analýzu incidentu a nástrojů k identifikaci hlavních příčin.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="Diagnostika incidentu" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>Rady k hlavní příčině

Když se v metrikě zjistí skupina anomálií a způsobí incident, pokusí se nástroj pro zjišťování metrik analyzovat hlavní příčinu incidentu. **Pokyny k hlavní příčině** poskytují automatické návrhy na pravděpodobný příčinu incidentu. Tato funkce je k dispozici pouze v případě, že je v dimenzi agregovaná hodnota. Pokud metrika nemá žádnou dimenzi, hlavní příčina bude sama. Hlavní příčiny jsou uvedeny na panelu na pravé straně a v seznamu se může zobrazit několik důvodů. Pokud tabulka neobsahuje žádná data, znamená to, že vaše dimenze nesplňuje požadavky na provedení analýzy.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="Rady k hlavní příčině":::


Pokud je metrika hlavní příčiny k dispozici s konkrétními dimenzemi, můžete kliknutím na **Přejít na metriku** zobrazit další podrobnosti o této metrikě.

## <a name="incident-tree"></a>Strom incidentů

Společně s automatizovanou analýzou pro potenciální hlavní příčiny podporuje Poradce pro metriky ruční analýzu hlavní příčiny, a to pomocí **stromu incidentu**. Na stránce incidentu existují dva druhy incidentů: strom **rychlé diagnostiky** a **interaktivní strom**.

Strom rychlé diagnostiky je pro diagnostiku aktuálního incidentu a kořenový uzel je omezený na aktuální kořenový uzel incidentu. Stromové uzly můžete rozbalit a sbalit kliknutím na ni a její série se zobrazí spolu s aktuální řadou incidentů v grafu nad stromovou strukturou.

Interaktivní stromové struktury vám umožní diagnostikovat aktuální incidenty i starší incidenty, které souvisejí. Při používání interaktivního stromu klikněte pravým tlačítkem na uzel a otevřete nabídku Akce, kde můžete zvolit dimenzi pro procházení kořenových uzlů a dimenzi pro přechod k podrobnostem pro každý uzel. Kliknutím na tlačítko zrušit v seznamu dimenzí v horní části můžete odebrat přechod z této dimenze nahoru nebo dolů. klikněte na uzel, který chcete vybrat, a zobrazte jeho řadu společně s aktuální řadou incidentů v grafu.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="Strom incidentů" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>Přechod k podrobnostem anomálií

Když zobrazujete informace o incidentech, možná budete potřebovat získat podrobnější informace, například pro různé dimenze a časová razítka. Pokud vaše data obsahují jednu nebo více dimenzí, můžete získat podrobnější zobrazení pomocí funkce přejít k podrobnostem. 

Chcete-li použít funkci přejít k podrobnostem, klikněte na kartu **vrtání metriky** v **centru incidentů**. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Vrtné metriky ":::

Nastavení **dimenzí** je seznam dimenzí incidentu, pro každý z nich můžete vybrat jiné dostupné hodnoty dimenzí. Po změně hodnot dimenze. Nastavení **časového razítka** umožňuje zobrazit aktuální incident v různých časových okamžikech v čase.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Vyberte možnosti procházení a zvolte dimenzi.

Existují dva typy možností přechodu k podrobnostem: **Přechod k podrobnostem** a **horizontálnímu porovnání**.

> [!Note]
> 1. Pro přechod k podrobnostem můžete prozkoumat data z různých hodnot dimenzí s výjimkou momentálně vybraných dimenzí. 
> 2. Pro horizontální porovnání můžete prozkoumat data z různých hodnot dimenzí s výjimkou všech dimenzí.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Dimenze přechodu k podrobnostem":::

### <a name="value-comparison-for-different-dimension-values"></a>Porovnání hodnot u různých hodnot dimenzí

Druhá část karty přejít k podrobnostem je tabulka s porovnáním pro různé hodnoty dimenzí. Obsahuje hodnotu, hodnotu základního hodnoty, hodnotu rozdílu a rozdílovou hodnotu a zda se jedná o anomálii.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Porovnání přechodu k podrobnostem" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Porovnání hodnot a očekávaných hodnot pro jinou hodnotu dimenze

Třetí část karty přejít k podrobnostem je histogram s hodnotami a očekávanými hodnotami pro různé hodnoty dimenzí. Histogram je seřazen podle rozdílu mezi hodnotou a očekávanou hodnotou. Neočekávanou hodnotu můžete snadno najít s největším dopadem. Například na výše uvedeném obrázku můžeme najít to, s výjimkou hodnoty vše, **US7** přispívá pro anomálii maximum.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Tabulka přechodu k podrobnostem" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Vizualizace nezpracovaných hodnot
Poslední část karty přejít k podrobnostem je spojnicový graf nezpracovaných hodnot. V tomto grafu nemusíte pro zobrazení podrobností přejít na stránku metriky.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Spojnicový graf pro přechod k podrobnostem" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Zobrazení podobných anomálií pomocí clusteringu časových řad

Při zobrazení incidentu můžete použít kartu s **podobným časovým obdobím –** pro zobrazení různých řad, které jsou k ní přidružené. Řady v jedné skupině jsou shrnuté dohromady. Z výše uvedeného obrázku můžeme zjistit, že existují alespoň dvě skupiny řad. Tato funkce je k dispozici pouze v případě splnění následujících požadavků:

1. Metriky musí mít jednu nebo více dimenzí nebo hodnot dimenzí.
2. Řada v rámci jedné metriky musí mít podobný trend.

Dostupné dimenze jsou uvedeny v horní části karty a můžete vytvořit výběr pro zadání řad.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="Skupina řad":::

## <a name="compare-time-series"></a>Porovnání časové řady

Pokud je v určité časové řadě zjištěna anomálie, je vhodné ji porovnat s více dalšími řadami v jedné vizualizaci. Klikněte na kartu **Porovnat nástroje** a potom klikněte na tlačítko Blue **+ Přidat** . 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Přidat řadu k porovnání" lightbox="../media/diagnostics/add-series.png":::

Vyberte z datového kanálu řadu. Můžete zvolit stejnou členitost nebo jinou. Vyberte cílové dimenze a načtěte trend řady a kliknutím na tlačítko **OK** ji porovnejte s předchozí řadou. Řada se spojí do jedné vizualizace. Můžete pokračovat v přidávání dalších řad pro porovnání a získat další přehledy. V horní části karty **Porovnat nástroje** klikněte na rozevírací nabídku a porovnejte data časových řad v době posunutí.  

> [!Warning]
> Aby bylo možné provést porovnání, analýza dat časové řady může vyžadovat posun v datových bodech, aby bylo možné členitost dat podporovat. Například pokud jsou vaše data týdně a používáte porovnání dne v **den** , nebudete mít žádné výsledky. V tomto příkladu byste místo toho použili porovnání **měsíců za** měsíc.

Po výběru porovnání s časovým posunutím můžete vybrat, zda chcete porovnat hodnoty dat, rozdílové hodnoty nebo procento Delta.

> [!Note]
> * **Hodnota dat** je hodnota nezpracovaných dat.
> * **Rozdílová hodnota** je rozdíl mezi nezpracovanými a porovnanými hodnotami.
> * **Procentuální hodnota rozdílu** je rozdíl mezi nezpracovanými hodnotami a porovnáním hodnoty dělené porovnáním hodnoty.

## <a name="related-incidents-across-metrics"></a>Související incidenty napříč metrikami

Někdy může být nutné kontrolovat incidenty různých metrik ve stejnou dobu nebo související incidenty v jiných metrikách. Seznam souvisejících incidentů najdete v části **analýzy různých metrik** . 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="související incidenty napříč metrikami":::

Než uvidíte související incidenty pro aktuální metriku, je potřeba přidat relaci mezi metrikami. Kliknutím na **nastavení grafu metriky** přidáte relaci. V relaci můžou být jenom metriky se stejnými názvy dimenzí. Použijte následující parametry.

- Aktuální datový kanál & metriku: datový kanál a metrika aktuálního incidentu
- Směr: směr vztahu mezi dvěma metrikami. (neuplatní se teď na seznam souvisejících incidentů)
- Další & metriky datového kanálu: informační kanál a metrika pro připojení s aktuální metrikou


## <a name="next-steps"></a>Další kroky 

- [Úprava detekce anomálií pomocí zpětné vazby](anomaly-feedback.md)
- [Konfigurace metrik a doladění konfigurace zjišťování](configure-metrics.md)
