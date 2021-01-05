---
title: Pokročilé funkce Průzkumníku metrik Azure
description: Seznamte se s pokročilými funkcemi Azure Monitor Průzkumník metrik
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: a80eaecc02fa3c8c6618341c02e22241f0dc7faf
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845046"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Pokročilé funkce Průzkumníku metrik Azure

> [!NOTE]
> V tomto článku se předpokládá, že máte zkušenosti se základními funkcemi Průzkumník metrik. Pokud jste nový uživatel a chcete zjistit, jak vytvořit první graf metriky, přečtěte si téma [Začínáme s Azure Průzkumník metrik](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Metriky v Azure

[Metrikami v Azure monitor](data-platform-metrics.md) jsou řady měřených hodnot a počty, které se shromažďují a ukládají v průběhu času. Existují metriky Standard (nebo "platforma") a vlastní metriky. Standardní metriky vám nabízí samotná platforma Azure. Standardní metriky odráží údaje o stavu a využití vašich prostředků Azure. Vzhledem k tomu, že své aplikace odesílají vlastní metriky do Azure pomocí [rozhraní Application Insights API pro vlastní události a metriky](../app/api-custom-events-metrics.md),  [rozšíření Windows Azure Diagnostics (WAD)](./diagnostics-extension-overview.md)nebo [Azure monitor REST API](./metrics-store-custom-rest-api.md).

## <a name="resource-scope-picker"></a>Výběr oboru prostředků
Výběr oboru prostředků umožňuje zobrazit metriky v rámci jednoho a několika prostředků. Níže najdete pokyny k použití výběru oboru prostředků. 

### <a name="selecting-a-single-resource"></a>Výběr jednoho prostředku
V nabídce **Azure monitor** vyberte **metriky** nebo v části **monitorování** v nabídce prostředku. Kliknutím na tlačítko Vybrat obor otevřete výběr oboru, který vám umožní vybrat prostředky, pro které chcete zobrazit metriky. Tato hodnota by již měla být naplněna, pokud jste spustili průzkumníka metrik z nabídky prostředku. 

![Snímek obrazovky pro výběr oboru prostředků](./media/metrics-charts/scope-picker.png)

U určitých prostředků můžete najednou zobrazit jenom metriky s jedním prostředkem. Tyto prostředky jsou uvedené v části všechny typy prostředků v rozevíracím seznamu typy prostředků.

![Snímek obrazovky s jedním prostředkem](./media/metrics-charts/single-resource-scope.png)

Po kliknutí na požadovaný prostředek se zobrazí všechna předplatná a skupiny prostředků, které tento prostředek obsahují.

![Snímek dostupných prostředků](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Pokud chcete zobrazit metriky více zdrojů najednou nebo metriky v rámci předplatného nebo skupiny prostředků, klikněte na tlačítko pro odkazování.

Až budete s výběrem spokojeni, klikněte na použít.

### <a name="viewing-metrics-across-multiple-resources"></a>Zobrazení metrik v několika prostředcích
Některé typy prostředků mají povolenou možnost dotazování na metriky přes více prostředků, pokud jsou ve stejném předplatném a umístění. Tyto typy prostředků se dají najít v horní části rozevíracího seznamu "typy prostředků". Další informace o tom, jak zobrazit metriky v několika prostředcích, najdete v [tomto dokumentu](metrics-dynamic-scope.md#selecting-multiple-resources).

![Snímek obrazovky s typy různých prostředků](./media/metrics-charts/multi-resource-scope.png)

Pro typy kompatibilní s více prostředky můžete také zadat dotaz na metriky v rámci předplatného nebo několika skupin prostředků. Další informace o tom, jak to provést, najdete v [tomto článku](metrics-dynamic-scope.md#selecting-a-resource-group-or-subscription) .


## <a name="create-views-with-multiple-metrics-and-charts"></a>Vytváření zobrazení s více metrikami a grafy

Můžete vytvářet grafy, které vykreslují více řádků metrik nebo zobrazují více grafů metrik najednou. Tato funkce umožňuje:

- korelujte související metriky ve stejném grafu a podívejte se, jak jedna hodnota souvisí s jinou hodnotou.
- Zobrazit metriky s různými jednotkami měření v těsné blízkosti
- vizuální agregace a porovnání metrik z několika prostředků

Pokud máte například 5 účtů úložiště a chcete zjistit, kolik místa je mezi nimi využito, můžete vytvořit (skládaný) plošný graf, který zobrazuje jednotlivec a součet všech hodnot v určitých bodech v čase.

### <a name="multiple-metrics-on-the-same-chart"></a>Víc metrik na stejném grafu

Nejprve [vytvořte nový graf](metrics-getting-started.md#create-your-first-metric-chart). Klikněte na **Přidat metriku** a zopakováním kroků přidejte další metriku do stejného grafu.

   > [!NOTE]
   > Obvykle nechcete, aby byly metriky s různými jednotkami měření (tj. "milisekundy" a "kilobajty"), nebo s výrazně odlišným měřítkem v jednom grafu. Místo toho zvažte použití více grafů. Kliknutím na tlačítko přidat graf vytvoříte v Průzkumníkovi metrik více grafů.

### <a name="multiple-charts"></a>Více grafů

Klikněte na **přidat graf** a vytvořte další graf s jinou metrikou.

### <a name="order-or-delete-multiple-charts"></a>Objednat nebo odstranit více grafů

Chcete-li seřadit nebo odstranit více grafů, klikněte na symbol tří teček ( **...** ) a otevřete nabídku graf a vyberte odpovídající položku v nabídce **Přesunout nahoru**, **Přesunout dolů** nebo **Odstranit**.

## <a name="changing-aggregation"></a>Změna agregace

Když přidáte metriku do grafu, Průzkumník metrik automaticky vybere jeho výchozí agregaci. Ve výchozím nastavení je to ve scénářích Basic, ale k získání dalších přehledů o této metrikě můžete použít jinou agregaci. Zobrazení různých agregací v grafu vyžaduje, abyste porozuměli tomu, jak je Průzkumník metrik zpracovává. 

Metriky jsou série měření (neboli hodnoty metrik) zachycené za časové období. Při vykreslení grafu se hodnoty vybrané metriky samostatně agreguje v *časovém intervalu*. Velikost časového intervalu můžete vybrat [pomocí panelu pro výběr Průzkumník metrikho času](metrics-getting-started.md#select-a-time-range). Pokud neprovedete explicitní výběr časového intervalu, časové rozlišení se automaticky vybere v závislosti na aktuálně vybraném časovém rozsahu. Po určení časového intervalu jsou hodnoty metrik, které byly zachyceny během každého intervalu intervalu, agregovány a umístěny do grafu – jedna hodnota DataPoint za časový interval.

Předpokládejme například, že se v grafu zobrazuje metrika **doby odezvy serveru** pomocí **průměrné** agregace za **posledních 24 hodin** časového intervalu:

- Pokud je časová členitost nastavená na 30 minut, graf se vykreslí z 48 agregovaných DataPoint (například Spojnicový graf připojuje 48 teček v oblasti vykreslení grafu). To znamená 24 hodin × 2 datapoints za hodinu. Každý DataPoint představuje *průměr* všech zaznamenaných dob odezvy pro žádosti serveru, ke kterým došlo během každé z příslušných 30 minut časových období.
- Pokud časové rozlišení přepnete na 15 minut, dostanete 96 agregovaných datapoints.  To znamená 24 hodin × 4 datapoints za hodinu.

V Průzkumníkovi metriky je dostupných pět základních výpočetních výpočtů: **Sum**, **Count**, **min**, **Max** a **Average**. Agregace **součtu** je někdy označována jako **Celková** agregace. U mnoha metrik Průzkumník metrik skryje agregace, které jsou zcela nepodstatné a nelze je použít.

**Sum** – součet všech hodnot zachycených v intervalu agregace

![Snímek obrazovky součtu požadavku](./media/metrics-charts/request-sum.png)

**Count** – počet měření zachycených v intervalu agregace. Počítejte s tím, že v případě, že je metrika vždycky zachycena s hodnotou 1, se **počet** bude rovnat **součtu** . To je běžné v případě, že metrika sleduje počet různých událostí a každé měření představuje jednu událost (tj. kód se při každém výskytu nové žádosti aktivuje na záznam metriky).

![Snímek obrazovky počtu požadavků](./media/metrics-charts/request-count.png)

**Average** – průměr hodnot metriky zachycených v intervalu agregace

![Snímek obrazovky s průměrným požadavkem](./media/metrics-charts/request-avg.png)

**Min** – nejmenší hodnota zachycená v intervalu agregace

![Snímek obrazovky s minimální žádostí](./media/metrics-charts/request-min.png)

**Max** – největší hodnota zachycená v intervalu agregace

![Snímek obrazovky s maximálním počtem žádostí](./media/metrics-charts/request-max.png)

## <a name="apply-filters-to-charts"></a>Použití filtrů u grafů

Můžete použít filtry na grafy, které zobrazují metriky s rozměry. Pokud například metrika "Count" obsahuje dimenzi, "typ odezvy", která označuje, zda byla odpověď z transakcí úspěšná nebo neúspěšná, pak filtrování v této dimenzi vykreslí řádek grafu pouze úspěšných (nebo pouze neúspěšných) transakcí. 

### <a name="to-add-a-filter"></a>Přidání filtru

1. Vyberte **Přidat filtr** nad grafem.

2. Vyberte dimenzi (vlastnost), kterou chcete filtrovat.

   ![Snímek obrazovky zobrazující dimenze (vlastnosti), které můžete filtrovat.](./media/metrics-charts/028.png)

3. Vyberte, které hodnoty dimenze chcete zahrnout při vykreslování grafu (Tento příklad ukazuje odfiltrování úspěšných transakcí úložiště):

   ![Snímek obrazovky zobrazující filtrování z úspěšných transakcí úložiště](./media/metrics-charts/029.png)

4. Po výběru hodnot filtru klikněte na pryč v selektoru filtru a zavřete ho. Graf teď zobrazuje, kolik transakcí úložiště selhalo:

   ![Snímek obrazovky, který ukazuje, kolik transakcí úložiště selhalo](./media/metrics-charts/030.png)

5. Opakováním kroků 1-4 můžete použít více filtrů pro stejné grafy.



## <a name="apply-splitting-to-a-chart"></a>Použití rozdělení na graf

Metriku můžete rozdělit podle dimenzí, abyste vizualizují, jak různé segmenty metriky vzájemně porovnávají a identifikují neležící segmenty dimenze.

### <a name="apply-splitting"></a>Použít rozdělení

1. V grafu klikněte na **rozdělení použít** .
 
   > [!NOTE]
   > Rozdělení nelze použít u grafů s více metrikami. Můžete mít také více filtrů, ale v jednom grafu je použita pouze jedna rozdělená dimenze.

2. Vyberte dimenzi, na které chcete graf rozdělit:

   ![Snímek obrazovky zobrazující vybranou dimenzi, na které se má váš graf rozdělit](./media/metrics-charts/031.png)

   Nyní graf nyní zobrazuje více řádků, jeden pro každý segment dimenze:

   ![Snímek obrazovky, který zobrazuje více řádků, jeden pro každý segment dimenze.](./media/metrics-charts/032.png)

3. Kliknutím na tlačítko mimo **Výběr seskupení** jej zavřete.

   > [!NOTE]
   > Použijte filtrování i rozdělení na stejnou dimenzi, abyste skryli segmenty, které jsou pro váš scénář nerelevantní a usnadňují čtení grafů.

## <a name="lock-boundaries-of-chart-y-axis"></a>Zamknout hranice osy y grafu

Uzamykání rozsahu osy y je důležité, pokud se v grafu zobrazí menší kolísání větších hodnot. 

Například pokud se objem úspěšných žádostí sníží z 99,99% na 99,5%, může představovat významné snížení kvality služby. Všímáte malých numerických hodnot ale může být obtížné nebo dokonce nemožné z výchozího nastavení grafu. V takovém případě můžete uzamknout nejnižší hranici grafu až 99%, což by vedlo k lepšímu vynechání tohoto malého vypuštění. 

Dalším příkladem je kolísání dostupné paměti, kde hodnota nebude technicky nikdy dostupná 0. Když opravíte rozsah na vyšší hodnotu, může to být tím, že se v paměti uvolní. 

Chcete-li řídit rozsah osy y, použijte znak "...". Nabídka graf a vyberte **nastavení grafu** pro přístup k rozšířeným nastavením grafu.

![Snímek obrazovky, který zvýrazní možnost nastavení grafu](./media/metrics-charts/033.png)

 Upravte hodnoty v části Rozsah osy Y nebo použijte tlačítko **auto** a vraťte se k výchozímu nastavení.
 
 ![Snímek obrazovky, který zvýrazní část rozsahu osy Y.](./media/metrics-charts/034.png)

> [!WARNING]
> Uzamykání ohraničení osy y pro grafy, které sledují různé počty nebo součty v určitém časovém období (a proto používají počty, součty, minimum nebo maximum agregace) obvykle vyžadují zadání členitosti času, ale nespoléhání se na automatické výchozí hodnoty. To je nezbytné, protože hodnoty v grafech se mění, když je časová hustota automaticky upravována uživatelem v prohlížeči nebo při přechodu z jednoho rozlišení obrazovky na jiný. Výsledná změna v časovém intervalu ovlivňuje vzhled grafu, neověřuje aktuální výběr rozsahu osy y.

## <a name="change-colors-of-chart-lines"></a>Změna barev čar grafu

Po nakonfigurování grafů jsou čáry grafu automaticky přiřazeny barevně z výchozí palety. Tyto barvy můžete změnit.

Chcete-li změnit barvu čáry grafu, klikněte na barevný pruh v legendě, která odpovídá grafu. Otevře se dialogové okno pro výběr barvy. Použijte výběr barvy ke konfiguraci barvy čáry.

![Snímek obrazovky, který ukazuje, jak změnit barvu](./media/metrics-charts/035.png)

Až budou barvy grafu nakonfigurované, zůstanou při připnutí grafu na řídicí panel. V následující části se dozvíte, jak připnout graf.

## <a name="pin-charts-to-dashboards"></a>Připnutí grafů na řídicí panely

Po nakonfigurování grafů je můžete chtít přidat na řídicí panely, abyste ji mohli znovu zobrazit, případně v kontextu jiné telemetrie monitorování nebo sdílet s týmem.

Připnutí nakonfigurovaného grafu na řídicí panel:

Po konfiguraci grafu klikněte na **Připnout na řídicí panel** v pravém horním rohu grafu.

![Snímek obrazovky, který ukazuje, jak připnout na graf](./media/metrics-charts/036.png)

## <a name="create-alert-rules"></a>Vytváření pravidel upozornění

Můžete použít kritéria, která jste nastavili k vizualizaci metrik, jako základ pro pravidlo upozornění na základě metrik. Nové pravidlo upozorňování bude obsahovat cílový prostředek, metriku, rozdělení a filtrování dimenzí v grafu. Tato nastavení budete moct později změnit v podokně vytvoření pravidla výstrahy.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Chcete-li vytvořit nové pravidlo výstrahy, klikněte na **nové pravidlo výstrahy** .

![Tlačítko nové pravidlo upozornění zvýrazněné červeně](./media/metrics-charts/042.png)

Převezmete podokno pro vytvoření pravidla výstrahy se základními rozměry metriky z vašeho grafu, aby bylo snazší generovat vlastní pravidla upozornění.

![Vytvořit pravidlo výstrahy](./media/metrics-charts/041.png)

Další informace o nastavení výstrah metrik najdete v tomto [článku](alerts-metric.md) .

## <a name="troubleshooting"></a>Řešení potíží

*V grafu nevidím žádná data.*

* Filtry se použijí na všechny grafy v podokně. Ujistěte se, že při zaměření na jeden graf jste nastavili filtr, který vyloučí všechna data na jiném.

* Pokud chcete nastavit jiné filtry v různých grafech, vytvořte je v různých oknech a uložte je jako samostatné oblíbené položky. Pokud chcete, můžete je připnout na řídicí panel, abyste je viděli společně.

* Pokud graf vytvoříte tak, že vlastnost, která není definovaná u metriky, nebude v grafu žádná. Zkuste vymazat segmentaci (rozdělení) nebo zvolit jinou vlastnost.

## <a name="next-steps"></a>Další kroky

  Další informace o osvědčených postupech pro vytváření řídicích panelů s možnými metrikami najdete v [tématu vytváření vlastních řídicích panelů klíčových ukazatelů výkonu](../learn/tutorial-app-dashboards.md) .
