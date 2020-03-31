---
title: Diskmetriky vyřazení na webu Portál Azure | Dokumenty společnosti Microsoft
description: Zjistěte, které metriky disku byly zastaralé a jak aktualizovat upozornění na metriky tak, aby používaly nové metriky.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299800"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Diskové metriky vyřazení na webu Azure Portal

Zastaralé metriky související s diskem se brzy odeberou z webu Azure Portal. K dispozici je nová verze každé zastaralé metriky. V tomto článku se zobrazí, které metriky jsou nové a jak aktualizovat upozornění na metriky tak, aby je používala.

## <a name="list-of-new-metrics"></a>Seznam nových metrik

Tato tabulka mapuje každou zastaralou metriku na odpovídající novou metriku. 

|Kritická metrika|Nová (náhradní) metrika|
|----|----|
|Datový disk QD (zastaralé)|Hloubka fronty datového disku (náhled)|
|Čtení bajtů datového disku/s (zastaralé)|Čtení bajtů datového disku/s (náhled)|
|Operace čtení datového disku/s (zastaralé)|Operace čtení datového disku/s (náhled)|
|Bajty zápisu datového disku/s (zastaralé)|Bajty za zápis datového disku/s (náhled)|
|Operace zápisu datového disku/s (zastaralé)|Operace zápisu datového disku/s (náhled)|
|OS QD (zastaralé)|Hloubka fronty operačního režimu (náhled)|
|Čtení bajtů operačního samu/s (zastaralé)|Čtení bajtů operačního režimu/s (náhled)|
|Operace čtení operačního spoje/s (zastaralé)|Operace čtení operačního režimu/s (náhled)|
|Bajty zápisu v osu/s (zastaralé)|Bajty zápisu operačního režimu/s (náhled)|
|Operace zápisu operačního ého/s (zastaralé)|Operace zápisu operačního režimu/s (náhled)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Migrace metrik v upozorněních na metriky

Aktualizujte upozornění na metriky tak, aby používala nové metriky.

1. Na webu Azure Portal vyhledejte **výstrahy**. Potom v části **Služby** zvolte **Výstrahy**.

   > [!div class="mx-imgBorder"]
   > ![Popis obrázku](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Na stránce **Výstrahy** zvolte tlačítko **Spravovat pravidla výstrah.** 

   > [!div class="mx-imgBorder"]
   > ![Popis obrázku](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. V rozevíracím seznamu **Skupina prostředků** zaškrtněte políčko **Virtuální počítače** a v rozevíracím seznamu Typ **signálu** zaškrtněte políčko **Metriky.** 

   > [!div class="mx-imgBorder"]
   > ![Popis obrázku](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. V seznamu metrik identifikujte podmínky, které se vztahují k diskům. Klikněte na název pravidla. 

   Název se zobrazí jako hypertextový odkaz ve sloupci **Název** tabulky.

   > [!div class="mx-imgBorder"]
   > ![Popis obrázku](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. V části **Podmínky** na stránce **Správa pravidel** klikněte na podmínku výstrahy. 

   Podmínka se zobrazí jako hypertextový odkaz.  

   > [!div class="mx-imgBorder"]
   > ![Popis obrázku](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Zobrazí se stránka **Logika signálu Konfigurace** a nastavení podmínky se zobrazí v části **Logika výstrahy** na této stránce.

6. Chcete-li tato nastavení zaznamenat, protože zmizí, jakmile odeberete zastaralou metriku.

   > [!div class="mx-imgBorder"]
   > ![Popis obrázku](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Zvažte zachycení těchto nastavení na snímku obrazovky nebo v textovém souboru. 

7. Kliknutím na odkaz **Pro výběr signálu** klepněte na tlačítko Zpět.

   > [!div class="mx-imgBorder"]
   > ![Popis obrázku](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Na stránce **Konfigurovat logiku signálu** zvolte příslušnou metriku nahrazení (nová metrika). Pomocí [tabulky,](#update-metrics) která se zobrazí dříve v tomto článku k identitě názvu nové metriky.

   > [!TIP] 
   > Začněte psát do vyhledávacího panelu a zúžíte seznam názvů metrik. 

   > [!div class="mx-imgBorder"]
   > ![Popis obrázku](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Zvolte tlačítko **Hotovo.** 

   > [!div class="mx-imgBorder"]
   > ![Popis obrázku](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Změny potvrďte výběrem tlačítka **Uložit.** 

    > [!div class="mx-imgBorder"]
    > ![Popis obrázku](./media/portal-disk-metrics-deprecation/save-new-metric.png)






