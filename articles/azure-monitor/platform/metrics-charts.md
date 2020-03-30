---
title: Pokročilé funkce Průzkumníku metrik Azure
description: Informace o pokročilých funkcích Průzkumníka metrik Azure Monitoru
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 2df1e0bb7d586edb13dc86e163f0e5728608d2a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371607"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Pokročilé funkce Průzkumníku metrik Azure

> [!NOTE]
> Tento článek předpokládá, že jste obeznámeni se základními funkcemi Průzkumníka metrik. Pokud jste nový uživatel a chcete se dozvědět, jak vytvořit svůj první graf metriky, najdete [v tématu Začínáme s Průzkumníkem metrik Azure](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Metriky v Azure

[Metriky ve službě Azure Monitor](data-platform-metrics.md) jsou řada naměřených hodnot a počtů, které se shromažďují a ukládají v průběhu času. Existují standardní (nebo "platforma") metriky a vlastní metriky. Standardní metriky vám poskytuje samotná platforma Azure. Standardní metriky odrážejí statistiky stavu a využití vašich prostředků Azure. Vzhledem k tomu, že vlastní metriky jsou odesílány do Azure vašimi aplikacemi pomocí [rozhraní API Application Insights pro vlastní události a metriky](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), rozšíření [Diagnostika Windows Azure (WAD)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)nebo [rozhraní AZURE Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Vytváření zobrazení s více metrikami a grafy

Můžete vytvořit grafy, které vykreslují více řádků metrik nebo zobrazují více grafů metrik najednou. Tato funkce umožňuje:

- korelovat související metriky ve stejném grafu, abyste zjistili, jak jedna hodnota souvisí s jinou
- zobrazit metriky s různými měrnými jednotkami v těsné blízkosti
- vizuálně agregovat a porovnávat metriky z více zdrojů

Pokud například máte 5 účtů úložiště a chcete vědět, kolik celkového místa je mezi nimi spotřebováno, můžete vytvořit (skládaný) plošný graf, který zobrazuje jednotlivé a součet všech hodnot v určitých časových bodech.

### <a name="multiple-metrics-on-the-same-chart"></a>Více metrik ve stejném grafu

Nejprve [vytvořte nový graf](metrics-getting-started.md#create-your-first-metric-chart). Klikněte na **Přidat metriku** a opakujte kroky a přidejte další metriku do stejného grafu.

   > [!NOTE]
   > Obvykle nechcete mít metriky s různými měrnými jednotkami (tj. "milisekundy" a "kilobajty") nebo s výrazně odlišným měřítkem v jednom grafu. Místo toho zvažte použití více grafů. Kliknutím na tlačítko Přidat graf vytvoříte v průzkumníku metrik více grafů.

### <a name="multiple-charts"></a>Více grafů

Klikněte na **Přidat graf** a vytvořte jiný graf s jinou metrikou.

### <a name="order-or-delete-multiple-charts"></a>Pořadí nebo odstranění více grafů

Chcete-li objednat nebo odstranit více grafů, kliknutím na symbol elipsy ( **...** ) otevřete nabídku grafu a zvolte příslušnou položku nabídky **Přesunout nahoru**, **Přesunout dolů**nebo **Odstranit**.

## <a name="apply-filters-to-charts"></a>Použití filtrů u grafů

Filtry můžete použít na grafy, které zobrazují metriky s dimenzemi. Například pokud metrika "Počet transakcí" má dimenzi "Typ odpovědi", který označuje, zda odpověď z transakcí proběhla úspěšně nebo se nezdařilo, pak filtrování v této dimenzi by vykreslilo řádek grafu pouze pro úspěšné (nebo pouze neúspěšné) transakce. 

### <a name="to-add-a-filter"></a>Přidání filtru

1. Vybrat **filtr Přidat** nad grafem

2. Vyberte dimenzi (vlastnost), kterou chcete filtrovat.

   ![obrázek metriky](./media/metrics-charts/00006.png)

3. Vyberte, které hodnoty dimenzí chcete zahrnout při vykreslování grafu (tento příklad ukazuje filtrování úspěšných transakcí úložiště):

   ![obrázek metriky](./media/metrics-charts/00007.png)

4. Po výběru hodnot filtru jej zavřete klepnutím mimo volič filtru. Graf teď ukazuje, kolik transakcí úložiště selhalo:

   ![obrázek metriky](./media/metrics-charts/00008.png)

5. Postup 1–4 můžete opakovat a použít více filtrů na stejné grafy.



## <a name="apply-splitting-to-a-chart"></a>Použití rozdělení v grafu

Metriku můžete rozdělit podle dimenze, abyste si mohli představit, jak se různé segmenty metriky vzájemně porovnávají, a identifikovat odlehlé segmenty dimenze.

### <a name="apply-splitting"></a>Použít rozdělení

1. Klikněte na **Použít rozdělení** nad grafem.
 
   > [!NOTE]
   > Rozdělení nelze použít s grafy, které mají více metrik. Můžete také použít více filtrů, ale pouze jednu štípací dimenzi pro libovolný jednotlivý graf.

2. Vyberte dimenzi, ve které chcete graf segmentovat:

   ![obrázek metriky](./media/metrics-charts/00010.png)

   Nyní graf zobrazuje více řádků, jeden pro každý segment dimenze:

   ![obrázek metriky](./media/metrics-charts/00012.png)

3. Kliknutím mimo **seskupovací volič** jej zavřete.

   > [!NOTE]
   > Pomocí filtrování i rozdělení ve stejné dimenzi skryjte segmenty, které jsou pro váš scénář irelevantní, a usnadňují čtení grafů.

## <a name="lock-boundaries-of-chart-y-axis"></a>Zamknout hranice osy y grafu

Uzamčení rozsahu osy y se stane důležitým, když graf zobrazuje menší výkyvy větších hodnot. 

Pokud například objem úspěšných požadavků klesne z 99,99 % na 99,5 %, může to představovat významné snížení kvality služeb. Nicméně, si všiml, malé číselné hodnoty kolísání by bylo obtížné nebo dokonce nemožné z výchozího nastavení grafu. V takovém případě můžete uzamknout nejnižší hranici grafu na 99 %, což by zviditalel tento malý pokles. 

Dalším příkladem je kolísání dostupné paměti, kde hodnota technicky nikdy nedosáhne 0. Upevnění rozsahu na vyšší hodnotu může usnadnit na místě kapky v dostupné paměti. 

Chcete-li ovládat rozsah os y, použijte "..." a vyberte **Upravit graf** pro přístup k upřesňujícím nastavením grafu. Upravte hodnoty v části Rozsah osy Y nebo se pomocí tlačítka **Auto** vraťte k výchozím hodnotám.

![obrázek metriky](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Uzamčení hranic osy y pro grafy, které sledují různá počty nebo součty za určité časové období (a tedy používají počet, součet, minimum nebo maximální agregace), obvykle vyžaduje zadání pevné časové rozlišovací schopnosti, nikoli spoléhání se na automatické výchozí hodnoty. To je nezbytné, protože hodnoty v grafech se mění, když je rozlišovací schopnost automaticky změněna uživatelem, který změní velikost okna prohlížeče nebo přejde z jednoho rozlišení obrazovky do druhého. Výsledná změna rozlišovací schopnosti času ovlivňuje vzhled grafu a zneplatní aktuální výběr rozsahu osy y.

## <a name="change-colors-of-chart-lines"></a>Změna barev čar grafu

Po konfiguraci grafů jsou čáry grafu automaticky přiřazeny k barvě z výchozí palety. Můžete změnit tyto barvy.

Chcete-li změnit barvu čáry grafu, klikněte na barevný pruh v legendě, který odpovídá grafu. Otevře se dialogové okno pro výběr barvy. Pomocí výběru barev nakonfigurujte barvu čáry.

Po konfiguraci barev grafu zůstanou tak, když graf připnete na řídicí panel. Následující část ukazuje, jak připnout graf.

> [!NOTE]
> Vzhledem k omezením našeho plánu vydání a publikování změna barev řádků grafu dočasně vyžaduje předání speciálního [https://portal.azure.com/?feature.colorpicker=true](https://portal.azure.com/?feature.colorpicker=true)parametru **?feature.colorpicker=true** při spuštění portálu Azure . Toto omezení bude brzy odstraněno. 

![obrázek metriky](./media/metrics-charts/018.png)

## <a name="pin-charts-to-dashboards"></a>Připnutí grafů k řídicím panelům

Po konfiguraci grafů můžete chtít přidat do řídicích panelů, abyste je mohli znovu zobrazit, případně v kontextu jiné telemetrie monitorování, nebo sdílet s týmem.

Připnutí nakonfigurovaného grafu na řídicí panel:

Po konfiguraci grafu klikněte na nabídku **Akce grafu** v pravém horním rohu grafu a klikněte **na Připnout na řídicí panel**.

![obrázek metriky](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Vytváření pravidel upozornění

Kritéria, která jste nastavili k vizualizaci metrik, můžete použít jako základ pravidla výstrahy založeného na metrikách. Nové pravidlo upozornění bude zahrnovat cílové dimenze zdroje, metriky, rozdělení a filtrování z grafu. Tato nastavení budete moci později upravit v podokně vytvoření pravidla výstrahy.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Chcete-li vytvořit nové pravidlo výstrahy, klepněte na tlačítko **Nové pravidlo výstrahy.**

![Červené tlačítko Nové pravidlo výstrahy zvýrazněné červeně](./media/metrics-charts/015.png)

Budete převedeni do podokna vytvoření pravidla výstrahy s podkladovými dimenzemi metriky z předem vyplněného grafu, abyste usnadnili generování vlastních pravidel výstrah.

![Vytvořit pravidlo výstrahy](./media/metrics-charts/016.png)

Další informace o nastavení upozornění na metriky najdete v tomto [článku.](alerts-metric.md)

## <a name="troubleshooting"></a>Řešení potíží

*V grafu nejsou žádná data.*

* Filtry platí pro všechny grafy v podokně. Ujistěte se, že při zaměření na jeden graf jste nenastavili filtr, který vylučuje všechna data v jiném grafu.

* Chcete-li nastavit různé filtry v různých grafech, vytvořte je v různých čepelích a uložte je jako samostatné oblíbené položky. Pokud chcete, můžete je připnout k řídicímu panelu, abyste je mohli vidět vedle sebe.

* Pokud segmentujete graf podle vlastnosti, která není definována v metrice, nebude v grafu nic. Zkuste odstranit segmentaci (rozdělení) nebo zvolte jinou vlastnost.

## <a name="next-steps"></a>Další kroky

  Přečtěte [si článek Vytváření vlastních řídicích panelů klíčových ukazatelů výkonu,](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) kde najdete informace o doporučených postupech pro vytváření použitelných řídicích panelů s metrikami.

