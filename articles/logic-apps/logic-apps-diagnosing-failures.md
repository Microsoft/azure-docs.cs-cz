---
title: Řešení potíží s chybami pracovního postupu a jejich diagnostika
description: Naučte se řešit a diagnostikovat problémy, chyby a chyby v pracovních postupech v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995042"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Řešení potíží s chybami pracovního postupu a jejich diagnostika v Azure Logic Apps

Vaše aplikace logiky generuje informace, které vám pomohou diagnostikovat a ladit problémy ve vaší aplikaci. Aplikaci logiky můžete diagnostikovat tak, že zkontrolujete každý krok pracovního postupu prostřednictvím Azure Portal. Nebo můžete do pracovního postupu přidat některé kroky pro ladění za běhu.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Kontrolovat historii aktivačních událostí

Každá aplikace logiky se spustí s pokusy o aktivaci, takže pokud se Trigger neaktivuje, postupujte podle těchto kroků:

1. Ověřte stav triggeru [kontrolou historie aktivačních událostí](../logic-apps/monitor-logic-apps.md#review-trigger-history). Chcete-li zobrazit další informace o pokusu o aktivační událost, vyberte tuto aktivační událost, například:

   ![Zobrazit stav triggeru a historii](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Zkontrolujte vstupy triggeru a potvrďte, že se zobrazují podle očekávání. V části **odkaz na vstupy** vyberte odkaz, který zobrazuje podokno **vstupy** .

   Vstupy triggerů zahrnují data, která Trigger očekává, a vyžaduje spuštění pracovního postupu. Tyto vstupy vám pomohou určit, zda jsou vstupy triggeru správné a zda byla podmínka splněna, aby mohl pracovní postup pokračovat.

   `feedUrl`Vlastnost tady má například nesprávnou hodnotu informačního kanálu RSS:

   ![Zkontrolovat vstupy triggeru pro chyby](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Zkontrolujte výstupy triggerů, pokud existují, a potvrďte tak, že se zobrazují podle očekávání. V části **odkaz na výstupy** vyberte odkaz, který zobrazuje podokno **výstupy** .

   Výstupy triggerů zahrnují data, která aktivační událost projde k dalšímu kroku pracovního postupu. Tyto výstupy vám pomůžou určit, jestli se do dalšího kroku pracovního postupu předali správné nebo očekávané hodnoty, například:

   ![Kontrola výstupů triggeru pro chyby](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Pokud najdete nějaký obsah, který neznáte, přečtěte si další informace o [různých typech obsahu](../logic-apps/logic-apps-content-type.md) v Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Historie spuštění kontroly

Pokaždé, když se Trigger aktivuje pro položku nebo událost, vytvoří modul Logic Apps a spustí samostatnou instanci pracovního postupu pro každou položku nebo událost. Pokud se spuštění nepovede, postupujte podle těchto kroků a Projděte si informace o tom, co se stalo během tohoto spuštění, včetně stavu každého kroku v pracovním postupu a vstupy a výstupy pro jednotlivé kroky.

1. [Kontrolou historie spuštění](../logic-apps/monitor-logic-apps.md#review-runs-history)zkontroluje stav spuštění pracovního postupu. Pokud chcete zobrazit další informace o neúspěšném spuštění, včetně všech kroků v běhu ve svém stavu, vyberte neúspěšné spuštění.

   ![Zobrazit historii spuštění a vybrat neúspěšné spuštění](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Po zobrazení všech kroků v běhu rozbalte první neúspěšný krok.

   ![Rozbalit první neúspěšný krok](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Zkontrolujte vstupy neúspěšných kroků a ověřte, jestli se zobrazují podle očekávání.

1. Projděte si podrobnosti o jednotlivých krocích konkrétního spuštění. V části **historie spuštění** vyberte běh, který chcete prošetřit.

   ![Kontrola historie spuštění](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Zobrazení podrobností o spuštění aplikace logiky](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Chcete-li prostudovat vstupy, výstupy a jakékoli chybové zprávy pro určitý krok, vyberte tento krok, aby se tvar rozbalí a zobrazí podrobnosti. Například:

   ![Zobrazení podrobností o kroku](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Ladění za běhu

Pro usnadnění ladění můžete přidat diagnostické kroky do pracovního postupu aplikace logiky spolu s kontrolou triggeru a spuštění historie. Můžete například přidat kroky, které používají službu [Tester Webhooku](https://webhook.site/) , abyste mohli kontrolovat požadavky HTTP a určit jejich přesnou velikost, tvar a formát.

1. Přejdete na web [Tester Webhooku](https://webhook.site/) a zkopírujete vygenerovanou jedinečnou adresu URL.

1. V aplikaci logiky přidejte akci HTTP POST a obsah textu, který chcete otestovat, například výraz nebo výstup jiného kroku.

1. Vložte adresu URL z testera Webhooku do akce HTTP POST.

1. Chcete-li zjistit, jak je požadavek vytvořen při generování z modulu Logic Apps, spusťte aplikaci logiky a přečtěte si web Webhook Tester, kde najdete další podrobnosti.

## <a name="next-steps"></a>Další kroky

* [Monitorování aplikace logiky](../logic-apps/monitor-logic-apps.md)
