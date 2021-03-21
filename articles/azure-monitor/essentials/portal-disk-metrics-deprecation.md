---
title: Nepoužívané metriky disku v Azure Portal | Microsoft Docs
description: Zjistěte, které metriky disků jsou zastaralé a jak aktualizovat výstrahy metrik tak, aby používaly nové metriky.
services: azure-monitor
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: 607ccacbfff86decaecaad0a869c8c0fe7500680
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033397"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Nepoužívané metriky disku v Azure Portal

Zastaralé metriky související s disky budou brzy odebrány z Azure Portal. K dispozici je nová verze každé zastaralé metriky, kterou můžete použít. V tomto článku se dozvíte, které metriky jsou nové a jak aktualizovat výstrahy metriky tak, aby se používaly.

## <a name="list-of-new-metrics"></a>Seznam nových metrik

Tato tabulka mapuje každou zastaralou metriku na odpovídající novou metriku. 

|Nepoužívané metriky|Nová (náhrada) metrika|
|----|----|
|Datový disk hloubka fronty (zastaralé)|Hloubka fronty datových disků (Preview)|
|Bajty přečtené z datového disku za sekundu (zastaralé)|Bajty přečtené z datového disku za sekundu (Preview)|
|Operace čtení z datového disku za sekundu (zastaralé)|Operace čtení z datového disku za sekundu (Preview)|
|Bajty zapsané na datový disk za sekundu (zastaralé)|Bajty zapsané na datový disk za sekundu (Preview)|
|Operace zápisu na datový disk za sekundu (zastaralé)|Operace zápisu na datový disk za sekundu (Preview)|
|OPERAČNÍ hloubka fronty (zastaralé)|Hloubka fronty operačního systému (Preview)|
|Bajty čtení z operačního systému/s (zastaralé)|Počet přečtených vstupně-výstupních OS za sekundu (Preview)|
|Operace čtení operačního systému/s (zastaralé)|Operace čtení operačního systému za sekundu (Preview)|
|Bajty zápisu v operačním systému/s (zastaralé)|Bajty zápisu v operačním systému/s (Preview)|
|Operace zápisu operačního systému za sekundu (zastaralé)|Operace zápisu operačního systému za sekundu (Preview)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Migrace metrik v upozorněních metriky

Aktualizujte výstrahy metriky, aby používaly nové metriky.

1. V Azure Portal vyhledejte **výstrahy**. Pak v části **služby** zvolte **výstrahy**.

   > [!div class="mx-imgBorder"]
   > ![Služba Výstrahy](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Na stránce **výstrahy** klikněte na tlačítko **Spravovat pravidla výstrah** . 

   > [!div class="mx-imgBorder"]
   > ![Správa pravidel výstrah](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. V rozevíracím seznamu **Skupina prostředků** vyberte zaškrtávací políčko **Virtual Machines** a v rozevíracím seznamu **typ signálu** vyberte zaškrtávací políčko **metriky** . 

   > [!div class="mx-imgBorder"]
   > ![Filtrovat výstrahy](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. V seznamu metrik určete podmínky, které se vztahují k diskům. Klikněte na název pravidla. 

   Název se zobrazí jako hypertextový odkaz ve sloupci **název** v tabulce.

   > [!div class="mx-imgBorder"]
   > ![Najít podmínky disku](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. V části **podmínky** na stránce **Správa pravidel** klikněte na podmínku výstrahy. 

   Podmínka se zobrazí jako hypertextový odkaz.  

   > [!div class="mx-imgBorder"]
   > ![Upravit podmínky](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Zobrazí se stránka **Konfigurovat logiku signálu** a v části **logika výstrahy** této stránky se zobrazí nastavení podmínky.

6. Když odeberete vystaralou metriku, vytvoří se záznam těchto nastavení, protože zmizí.

   > [!div class="mx-imgBorder"]
   > ![Pravidla podmínky](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Zvažte zachytávání těchto nastavení na snímku obrazovky nebo v textovém souboru. 

7. Klikněte na odkaz **zpět k výběru signálu** .

   > [!div class="mx-imgBorder"]
   > ![Zpět na výběr signálu](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Na stránce **Konfigurovat logiku signálu** vyberte vhodnou metriku pro nahrazení (nová metrika). Použijte [tabulku](#update-metrics) , která se zobrazí dříve v tomto článku, aby se identifikoval název nové metriky.

   > [!TIP] 
   > Začněte psát na panelu hledání a upřesněte seznam názvů metrik. 

   > [!div class="mx-imgBorder"]
   > ![Výběr nové metriky](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Klikněte na tlačítko **Hotovo** . 

   > [!div class="mx-imgBorder"]
   > ![Nastavení nové metriky](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Potvrďte provedené změny kliknutím na tlačítko **Uložit** . 

    > [!div class="mx-imgBorder"]
    > ![Uložit novou metriku](./media/portal-disk-metrics-deprecation/save-new-metric.png)






