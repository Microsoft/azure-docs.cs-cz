---
title: Vytvoření upozornění s dynamickými prahovými hodnotami ve službě Azure Monitor
description: Vytvoření upozornění s dynamickými prahovými hodnotami využívajících machine learning
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: Yaniv.Lavi
ms.reviewer: mbullwin
ms.openlocfilehash: b9b56145c13cb83b1686004ce215b1960caced14
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182991"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-private-preview"></a>Upozornění s dynamickými prahovými hodnotami ve službě Azure Monitor (omezená privátní verze Preview)

Upozornění s dynamickými prahovými hodnotami jsou rozšíření Azure Alerts metriky ve službě Azure Monitor, které využívat pokročilé funkce Machine Learning (ML) další metriky historických chování automaticky vypočítat směrné plány a použít je jako prahových hodnot výstrah.

Mezi výhody používání dynamické prahové hodnoty jsou:

- Uložte museli řešit problémy spojené s nastavením hranici předdefinované od rigidních automaticky učí historie výkonu metriky a použije algoritmy k určení prahových hodnot výstrah monitorování.
- Můžou určit sezónní chování a upozornění jen na odchylky od sezónní očekávaná. Pokud vaše služba se pravidelně nečinnosti o víkendech a potom špičky každé pondělí nebudou aktivovat upozornění na metriku s dynamickými prahovými hodnotami. V tuto chvíli nepodporuje: hodinové, denní nebo týdenní sezónnosti.
- Průběžně učí metriky výkonu a je adaptivní metriky změny.

Dynamická prahová hodnota výstrahy jsou k dispozici pro monitorování všech Azure na základě metrik zdrojích uvedených v tomto [článku](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Zaregistrovat se pro přístup k verzi preview

Abyste mohli tuto funkci pro otáčení, [zaregistrovat verzi preview](https://aka.ms/DynamicThresholdMetricAlerts). Jako vždy, rádi bychom znali váš názor, uchovávejte přicházející v [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Postup konfigurace výstrah s dynamickými prahovými hodnotami

Upozornění s dynamickými prahovými hodnotami, které je možné nakonfigurovat pomocí výstrah ve službě Azure Monitor

![Oznámení verze preview](media/alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Vytváří se pravidlo upozornění s dynamickými prahovými hodnotami

1. V podokně výstrahy v části monitorování zvolte **nové pravidlo upozornění** pro vytvoření nového upozornění v Azure.

   ![Nové pravidlo upozornění](media/alerts-dynamic-thresholds/002.png)

2. V části Vytvoření pravidla se zobrazí s tři části, který se skládá z: _Definujte podmínku upozornění_, _definujte podrobnosti o upozornění_, a _definujte skupinu akcí_. Nejprve začínat _Definujte podmínku upozornění_ části používají **vyberte cíl** odkaz zadejte cíl, tak, že vyberete prostředku. Jakmile je vybrána odpovídající prostředek, klikněte na tlačítko Hotovo.

   ![Výběr cíle](media/alerts-dynamic-thresholds/0003.png)

3. Potom pomocí **přidat kritéria** tlačítko Zobrazit seznam možností signál k dispozici pro prostředek a v seznamu signál zvolte odpovídající **metrika** možnost. (Např. využití CPU.)

   ![Přidat kritéria](media/alerts-dynamic-thresholds/004.png)

4. Na obrazovce konfigurace logiky signál v části Alert logic máte možnost přepnout podmínky typu dynamická, která bude automaticky generovat dynamické prahové hodnoty (červené čáry) společně s metriku (modrá čára).

   ![Dynamická](media/alerts-dynamic-thresholds/005.png)

5. Prahové hodnoty v grafu se počítají na základě v posledních sedmi dnů historických dat, jakmile se vytvoří výstrahu, dynamickými prahovými hodnotami získá další historická data, která je k dispozici a průběžně se dozvíte na základě nových dat, aby prahové hodnoty přesnější.

6. Alert logic další nastavení:
   - Podmínka – můžete nastavit výstrahu, kterou chcete aktivovat na jednu z těchto tří podmínek:
       - Větší než horní prahová hodnota nebo nižší než nižší prahová hodnota (výchozí)
       - Větší než horní prahová hodnota
       - Nižší než nižší prahová hodnota.
   - Časová agregace: Průměr (výchozí), sum, min, max.
   - Citlivost výstrah:
       - Vysoká – více výstrah, jak se aktivuje upozornění na nejnižší odchylka.
       - Med – méně citlivé na než vysoká, méně výstrah, než se Vysoká citlivost (výchozí)
       - Nízká – nejnižší citlivostí prahovou hodnotu.

    ![Nastavení logika upozornění](media/alerts-dynamic-thresholds/00007.png)

7. Vyhodnotí na základě:
    -  Jakou dobu trvání výstraha by měl vypadat pro zadanou podmínku výběrem ze **období**.

    ![Vyhodnoceno na základě](media/alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Podporované hodnoty období: 5 minut, 10 minut, 30 minut a 1 hodina.

   Aby výstrahy nepůsobily generovaných přechodné špičky, doporučujeme použít nastavení "Počet narušení pro aktivaci upozornění". Tato funkce umožňuje výstrahy jenom v případě, že porušení prahové hodnoty X po sobě jdoucích nebo časy Y z posledních Z období. Příklad:

    Chcete-li aktivovat upozornění, když tento problém je souvislý 15 minut, 3 po sobě jdoucích v daném časovém intervalu 5 minut, použijte následující nastavení:

   ![Vyhodnoceno na základě](media/alerts-dynamic-thresholds/0008.png)

    Chcete-li aktivovat upozornění, když došlo k narušení z dynamická prahová hodnota za 15 minut z posledních 30 minut s 5 minut, použijte následující nastavení:

   ![Vyhodnoceno na základě](media/alerts-dynamic-thresholds/0009.png)

8. Uživatelé nyní mohou mít výstrahy s dynamická prahová hodnota kritéria jako jediné kritérium.

   ![Vytvořit pravidlo](media/alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Dotazy a odpovědi

- DOTAZ: Pokud metrika pomalu mění v průběhu času, aktivuje toto upozornění s dynamickými prahovými hodnotami?

- ODPOVĚĎ: Pravděpodobně ne. Jsou vhodné pro zjištění významné odchylky, spíše než pomalu se vyvíjejí problémy s dynamickými prahovými hodnotami.

- DOTAZ: Můžete nakonfigurovat dynamickými prahovými hodnotami pomocí rozhraní API?

- ODPOVĚĎ: Pracujeme na něj.
