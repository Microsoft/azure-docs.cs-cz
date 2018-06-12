---
title: Vytvoření výstrahy s dynamické mezní hodnoty v Azure monitorování
description: Vytvoření výstrahy s prahové hodnoty dynamického využívajících machine learning
author: antonfrMSFT
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: mbullwin
ms.reviewer: antonfr
ms.component: alerts
ms.openlocfilehash: 01f924e0b3a2976a3f537cb5acac842eeeaccb4b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263309"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-public-preview"></a>Výstrahy s dynamické mezní hodnoty v Azure monitorování (omezený Public Preview)

Výstrahy s dynamické prahové hodnoty jsou rozšíření Azure metrika výstrah v monitorování Azure, který využít rozšířené možnosti Machine Learning (ML) další metriky se historických chování automaticky vypočítat směrné plány a použít je jako prahových hodnot výstrah.

Výhody použití dynamické prahové hodnoty jsou:

- Uložte jednoduchý přidružené nastavení předdefinované pevné hranice, protože monitor automaticky zjišťuje historických výkonu metriky a použije ML algoritmy k určení prahových hodnot výstrah.
- Můžete identifikují sezónní chování a výstrahu pouze u odchylky od sezónní očekávané chování. Metriky výstrahy s dynamické prahové hodnoty nebudou aktivovat, pokud vaše služba je pravidelně nečinnosti na víkendy a pak špičky každé pondělí. Aktuálně podporované: hodinové, denní a týdenní sezónnosti.
- Nepřetržitě zjišťuje metriky výkonu a je adaptivní metriky změny.

Dynamické výstrahy na základě prahové hodnoty jsou k dispozici pro všechny Azure monitorování na základě metriky zdrojích uvedených v tomto [článku](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Zaregistrovat k verzi preview

Chcete-li provést tuto možnost u typu číselník, [zaregistrujte si verzi preview](http://aka.ms/DynamicThresholdMetricAlerts). Jako vždy, budeme rádi uslyšíme vaše názory, Udržovat přicházející na [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Jak konfigurovat výstrahy s dynamické prahovými hodnotami

Výstrahy s dynamické prahové hodnoty můžete nakonfigurovat přes výstrah v monitorování Azure

![Výstrahy preview](./media/monitoring-alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Vytvoření pravidla výstrahy s dynamické prahovými hodnotami

1. V podokně výstrahy v části monitorování, vyberte **nové pravidlo výstrahy** tlačítko Vytvořit nové oznámení v Azure.

   ![Nové pravidlo upozornění](./media/monitoring-alerts-dynamic-thresholds/002.png)

2. V části Vytvoření pravidla se zobrazí se tří částí, který se skládá z: _definovat výstrahy podmínku_, _definovat podrobnosti výstrahy_, a _definovat akce skupiny_. Nejprve začínat _definovat výstrahy podmínku_ oddílu používají **vyberte cíl** odkaz na zadejte cíl, výběrem prostředku. Jakmile je zvoleno odpovídající prostředek, klikněte na tlačítko Hotovo.

   ![Vyberte cíl](./media/monitoring-alerts-dynamic-thresholds/0003.png)

3. Potom použít **přidat kritéria** tlačítko Zobrazit seznam možností signál k dispozici pro daný prostředek a ze seznamu signál zvolte odpovídající **metrika** možnost. (Například procento procesoru.)

   ![Přidat kritéria](./media/monitoring-alerts-dynamic-thresholds/004.png)

4. Na obrazovce konfigurace signál logiku v části výstrahy logiku máte možnost přepnout podmínku typ dynamického, který automaticky vytvoří dynamické prahové hodnoty (červené čáry) vedle metrika (modré řádku).

   ![Dynamická](./media/monitoring-alerts-dynamic-thresholds/005.png)

5. Prahové hodnoty, které jsou uvedeny v grafu se počítá na základě v posledních sedmi dnů historická data, jakmile je vytvořena výstraha, dynamické prahové hodnoty využijí další historická data, která je k dispozici a bude nepřetržitě informace na základě nová data, aby prahové hodnoty přesnější.

6. Nastavení další logiku navíc výstrah:
   - Podmínku – můžete výstrahu, kterou chcete spustit na jednom z následujících tří podmínek:
       - Větší než prahová hodnota vyšší nebo nižší než nižší prahová hodnota (výchozí)
       - Větší než vyšší prahová hodnota
       - Nižší než nižší prahová hodnota.
   - Čas agregace: průměr (výchozí), sum, min, max.
   - Výstrahy velkých a malých písmen:
       - Vysoká – další výstrahy, jako upozornění se spustí na nejmenší odchylku.
       - Med – méně velká a malá písmena než horní, méně výstrahy než s vysoká citlivost (výchozí)
       - Nízká – nejnižší citlivostí prahovou hodnotu.

    ![Nastavení výstrah logiky](./media/monitoring-alerts-dynamic-thresholds/00007.png)

7. Vyhodnocení na základě:
    -  Jaké doby trvání výstrahy by měl vypadat pro zadanou podmínku tak, že zvolíte **období**.

    ![Vyhodnoceno na základě](./media/monitoring-alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Podporované hodnoty doby: 5 minut, 10 minut, 30 minut a 1 hodina.

   Abyste snížili výstrahy nepůsobily generované přechodný špičky, doporučujeme použít nastavení "Počet porušení pro aktivování upozornění". Tato funkce umožňuje získat výstrahu pouze v případě, že porušení prahové hodnoty X po sobě nebo časy Y mimo poslední Z období. Příklad:

    Spustí výstrahu, pokud tento problém je souvislý 15 minut, po sobě jdoucích 3krát v daném časovém intervalu 5 minut, použijte následující nastavení:

   ![Vyhodnoceno na základě](./media/monitoring-alerts-dynamic-thresholds/0008.png)

    Ke spuštění výstrahy, když došlo k narušení z dynamické prahová hodnota za 15 minut mimo posledních 30 minut s dobou 5 minut, použijte následující nastavení:

   ![Vyhodnoceno na základě](./media/monitoring-alerts-dynamic-thresholds/0009.png)

8. Aktuálně uživatelům používat výstrahy s dynamické prahová hodnota kritéria jako jeden kritéria.

   ![Vytvořit pravidlo](./media/monitoring-alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Dotazy a odpovědi

- Otázka: když metrika pomalu změny v čase, aktivuje Tato výstraha s dynamické prahové hodnoty?

- Odpověď: pravděpodobně ne. Dynamické prahové hodnoty jsou vhodné pro zjišťování významné odchylky než pomalu vyvíjející se problémy.

- Otázka: je možné nakonfigurovat dynamické prahové hodnoty prostřednictvím rozhraní API?

- Odpověď: pracujeme na něm.
