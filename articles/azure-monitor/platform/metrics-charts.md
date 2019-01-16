---
title: Průzkumník metrik Azure monitoru
description: Informace o nových funkcích v Průzkumníku metrik Azure monitoru
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: vitalyg
ms.component: metrics
ms.openlocfilehash: 457c7e8904797955854c4c3e16a631cf6537e2b8
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330071"
---
# <a name="azure-monitor-metrics-explorer"></a>Průzkumník metrik Azure monitoru

Průzkumník metrik služby Azure Monitor je součástí portálu Microsoft Azure, která umožňuje vykreslení grafy, vizuálně korelace trendy a prošetřování provozní špičky a poklesy hodnot metrik. Průzkumník metrik je nezbytné výchozí bod pro zkoumání různých výkon a dostupnost problémy s vaší aplikací a infrastruktury hostované v Azure nebo monitorovat pomocí služby Azure Monitor.

## <a name="metrics-in-azure"></a>Metriky v Azure

Metriky ve službě Microsoft Azure jsou řady měřené hodnoty a výčty, které jsou shromážděná a uložená v čase. Existují metriky standard (nebo "platformy") a vlastní metriky. Standardní metriky jsou které jste získali od platformě Azure jako takové. Standardní metriky odrážejí statistické údaje o stavu a využití vašich prostředků Azure. Vzhledem k tomu, vlastní metriky se odesílají do Azure ve svých aplikacích pomocí [API pro Application Insights pro vlastní události](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Vlastní metriky jsou uloženy v prostředky Application Insights spolu s dalšími metrikami konkrétní aplikace.

## <a name="create-a-new-chart"></a>Vytvořit nový graf

1. Otevřete na webu Azure portal
2. Přejděte k novému **monitorování** kartu a potom vyberte **metriky**.

   ![Obrázek metriky](./media/metrics-charts/00001.png)

3. **Metriky selektor** bude automaticky otevřít za vás. Zvolte prostředek ze seznamu zobrazíte jeho související metriky. V seznamu jsou uvedeny pouze prostředky s metrikami.

   ![Obrázek metriky](./media/metrics-charts/00002.png)

   > [!NOTE]
   >Pokud máte více než jedno předplatné Azure, -> si Průzkumníka metrik z prostředků ve všech předplatných, které jsou vybrány v nastavení portálu filtr podle seznamu předplatných. Ho změnit, klikněte na ikonu ozubeného kola nastavení portálu na obrazovku a vyberte předplatné, které chcete použít.

4. Pro některé typy prostředků (účty úložiště a virtuální počítače), před výběrem metriku, je nutné vybrat **Namespace**. Každý obor názvů představuje vlastní sadu metriky, které jsou relevantní pouze tento obor názvů a ne ostatní obory názvů.

   Každé služby Azure Storage má například metriky pro subservices "BLOB", "Files", "Fronty" a "Tabulky", které jsou všechny části účtu úložiště. Metrika "počet zpráv fronty" je však přirozeně použitelné, což "Fronty", ne všechny ostatní subservices účtu úložiště.

   ![Obrázek metriky](./media/metrics-charts/00003.png)

5. Vyberte metriku ze seznamu. Pokud znáte částečný název metriky, které chcete, můžete začít zadávat v filtrovaný seznam dostupných metrik:

   ![Obrázek metriky](./media/metrics-charts/00004.png)

6. Po výběru metriku, bude vykreslení grafu s výchozí agregace pro vybranou metriku. V tomto okamžiku můžete jen kliknout na klávesou **metriky selektor** ho zavřít. Graf můžete také v případě potřeby přepnout na jinou agregaci. Pro některé metriky přepínání agregace umožňuje zvolit hodnotu, která chcete zobrazit v grafu. Například můžete přepínat mezi průměrné, minimální a maximální hodnoty. 

7. Po kliknutí na **přidat metriku** a opakováním kroků 3 až 6, můžete přidat další metriky na stejném grafu.

   > [!NOTE]
   > Obvykle nechcete mít metriky s různými jednotkami měření (například "MS" a "kilobajtů") nebo se výrazně liší škálovací na jednom grafu. Místo toho zvažte použití více grafů. Klikněte na tlačítko přidat graf vytvořit několik grafů v Průzkumníku metrik.

## <a name="apply-filters-to-charts"></a>Nastavte filtry pro grafy

Nastavte filtry pro grafy, které ukazují metriky s dimenzemi. Pokud metrika "Počet transakcí" dimenzi, například by "Typ odpovědi", který označuje, zda odezvu transakce úspěšné nebo neúspěšné následného filtrování v této dimenzi vykreslení čára grafu pro pouze úspěšné (nebo jenom se nezdařilo) transakce. 

### <a name="to-add-a-filter"></a>Přidání filtru

1. Vyberte **přidat filtr** nad grafem

2. Vybrat dimenzi, která (vlastnost), které chcete filtrovat

   ![metriky image](./media/metrics-charts/00006.png)

3. Výběr hodnot dimenzí, které chcete zahrnout při vykreslení grafu (Tento příklad ukazuje, odfiltrováním transakce úspěšné úložiště):

   ![metriky image](./media/metrics-charts/00007.png)

4. Po výběru hodnot filtru, klikněte na tlačítko mimo selektor filtru, abyste jej zavřeli. Graf nyní zobrazuje, kolik transakcí úložiště, které selhaly:

   ![metriky image](./media/metrics-charts/00008.png)

5. Kroky 1 až 4 aplikovat více filtrů stejného grafy můžete opakovat.

## <a name="segment-a-chart"></a>Segment grafu

Můžete rozdělit metriku dimenze k vizualizaci různých segmentů metriky porovnání proti sobě navzájem a identifikovat odlehlé segmenty dimenze. 

### <a name="to-segment-a-chart"></a>Segmentu grafu

1. Klikněte na **použít rozdělení** nad grafem.
 
   > [!NOTE]
   > Může mít několik filtrů ale hodnotu rozdělení/segmentace pouze jeden na jednoho grafu.

2. Vyberte dimenze, na kterém chcete graf segmentovat:

   ![metriky image](./media/metrics-charts/00010.png)

   Graf nyní zobrazuje nyní více řádků, jeden pro každý segment dimenze:

   ![metriky image](./media/metrics-charts/00012.png)

3. Klikněte na tlačítko klávesou **seskupení selektor** ho zavřít.

   > [!NOTE]
   > Skrýt segmenty, které jsou relevantní pro váš scénář a usnadňují grafy pomocí filtrování a rozdělení na stejné dimenzi.

### <a name="new-alert-rule"></a>Nové pravidlo upozornění

Můžete také použít kritéria, která jste nastavili vizualizovat metriky, jako základ pro logiku metriky na základě pravidel upozornění. 

Vyberete-li **oznámení nové pravidlo**

![Tlačítko Nová pravidla upozornění zvýrazněný červenou barvou](./media/metrics-charts/015.png)

Budete přesměrováni do podokna vytváření pravidla upozornění se základní metriky dimenzemi z grafu předem vyplněné, aby bylo snazší generovat vlastní pravidla upozornění.

![Vytvořit pravidlo upozornění](./media/metrics-charts/016.png)

Rezervovat to [článku](alerts-metric.md) Další informace o nastavení upozornění na metriku.

## <a name="lock-boundaries-of-chart-y-axis"></a>Zámek hranice osa y grafu

Uzamčení rozsah osy y je důležitá, pokud graf znázorňuje menší kolísání vyšší hodnoty. 

Například když objem úspěšných požadavků z 99,99 % se rozevře 99,5 %, může představovat k výraznému snížení kvality služeb. Řadí malé číselnou hodnotu kolísání by však bylo obtížné či nemožné i z výchozího nastavení grafu. V tomto případě může zamknout nejnižší hranice grafu tak, aby 99 %, což by mohlo způsobit nepoužitelnost tohoto malý dolní zřetelnější. 

Dalším příkladem je kolísání dostupné paměti, kde hodnota technicky nikdy nenaplní 0. Stanovení rozsahu na hodnotu vyšší může usnadnit drops v paměti k dispozici přímé. 

Pokud chcete nastavit rozsah osy y, použijte "..." Graf nabídku a vyberte **upravit graf** pro přístup k rozšířené nastavení grafu. Upravte hodnoty v oddílu rozsah osy y, nebo použijte **automaticky** tlačítko Obnovit výchozí hodnoty.

![metriky image](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Uzamčení hranice osy y pro grafy, které sledují různé počty nebo sečte po určitou dobu (a tedy počet používaných, sum, minimální nebo maximální agregace) obvykle vyžaduje zadání pevné časové intervaly, spíše než spoléhání na automatické výchozí hodnoty. To je nezbytné, protože změny hodnot u grafů, když uživatel změně velikosti okna prohlížeče nebo bude z jedné obrazovky řešení je automaticky upravit časové intervaly. Výsledná změnit čas členitosti účinky vzhled grafu, zrušení platnosti aktuálního výběru rozsah osy y.

## <a name="pin-charts-to-dashboards"></a>Grafy připnout na řídicí panely

Po dokončení konfigurace grafy, můžete přidat řídicí panely, takže lze znovu zobrazit, pravděpodobně v kontextu jiných monitorování telemetrických dat, nebo sdílet se svým týmem.

Pokud chcete připnout nakonfigurované graf na řídicí panel:

Po dokončení konfigurace diagramu, klikněte na **grafu akce** nabídky v pravém horním rohu grafu top a klikněte na **připnout na řídicí panel**.

![metriky image](./media/metrics-charts/00013.png)

## <a name="next-steps"></a>Další postup

  Čtení [vytváření vlastních řídicích panelů klíčového ukazatele výkonu](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) Další informace o osvědčené postupy pro vytváření užitečných řídicí panely s metrikami.
