---
title: Řešení potíží s chybami pracovního postupu a jejich diagnostika
description: Zjistěte, jak řešit a diagnostikovat problémy, chyby a chyby ve vašich pracovních postupech v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905104"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Řešení potíží s chybami pracovního postupu a jejich diagnostika v Azure Logic Apps

Vaše aplikace logiky generuje informace, které vám pomůžou diagnostikovat a ladit problémy ve vaší aplikaci. Aplikaci logiky můžete diagnostikovat tak, že si projdete jednotlivé kroky pracovního postupu prostřednictvím portálu Azure. Nebo můžete přidat některé kroky do pracovního postupu pro ladění za běhu.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Zkontrolovat historii aktivačních událostí

Spuštění každé aplikace logiky začíná pokusem o aktivační událost, takže pokud se aktivační událost neaktivuje, postupujte takto:

1. Zkontrolujte stav aktivační události [kontrolou historie aktivační události](../logic-apps/monitor-logic-apps.md#review-trigger-history). Chcete-li zobrazit další informace o pokusu o aktivační událost, vyberte tuto událost aktivační události, například:

   ![Zobrazit stav aktivační události a historii](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Zkontrolujte vstupy aktivační události a potvrďte, že se zobrazí podle očekávání. V části **Vstupy**vyberte odkaz, který zobrazuje podokno **Vstupy.**

   Aktivační vstupy zahrnují data, která aktivační událost očekává a vyžaduje spuštění pracovního postupu. Kontrola těchto vstupů vám může pomoci určit, zda jsou vstupy aktivační události správné a zda byla splněna podmínka, aby mohl pracovní postup pokračovat.

   Například `feedUrl` vlastnost zde má nesprávnou hodnotu informačního kanálu RSS:

   ![Kontrola aktivačních vstupů na chyby](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Zkontrolujte výstupy aktivačních událostí, pokud existuje, abyste potvrdili, že se zobrazí podle očekávání. V části **Výstupy vyberte**odkaz, který zobrazuje podokno **Výstupy.**

   Výstupy aktivační ch aktivační události zahrnují data, která aktivační událost předává dalšímu kroku pracovního postupu. Kontrola těchto výstupů vám může pomoci určit, zda správné nebo očekávané hodnoty předané dalšímu kroku pracovního postupu, například:

   ![Kontrola spouštěcích výstupů chyb](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Pokud najdete jakýkoli obsah, který nepoznáváte, přečtěte si další informace o [různých typech obsahu](../logic-apps/logic-apps-content-type.md) v Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Kontrola historie spuštění

Pokaždé, když aktivační událost spustí pro položku nebo událost, modul Logic Apps vytvoří a spustí samostatnou instanci pracovního postupu pro každou položku nebo událost. Pokud se spuštění nezdaří, postupujte podle těchto kroků a zkontrolujte, co se stalo během tohoto spuštění, včetně stavu pro každý krok v pracovním postupu plus vstupy a výstupy pro každý krok.

1. Zkontrolujte stav spuštění pracovního postupu [kontrolou historie spuštění](../logic-apps/monitor-logic-apps.md#review-runs-history). Chcete-li zobrazit další informace o neúspěšném spuštění, včetně všech kroků v tomto spuštění v jejich stavu, vyberte neúspěšné spuštění.

   ![Zobrazit historii spuštění a vybrat neúspěšné spuštění](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Po zobrazení všech kroků v běhu rozbalte první neúspěšný krok.

   ![Rozbalit první neúspěšný krok](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Zkontrolujte vstupy neúspěšného kroku a ověřte, zda se zobrazí podle očekávání.

1. Zkontrolujte podrobnosti pro každý krok v konkrétním spuštění. V části **Historie spuštění**vyberte spuštění, které chcete prozkoumat.

   ![Recenze spustí historii](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Zobrazení podrobností o spuštění aplikace logiky](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Chcete-li prozkoumat vstupy, výstupy a všechny chybové zprávy pro konkrétní krok, zvolte tento krok tak, aby se obrazec rozbalil a zokáral podrobnosti. Například:

   ![Zobrazení podrobností o kroku](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Ladění za běhu

Chcete-li pomoci s laděním, můžete přidat diagnostické kroky do pracovního postupu aplikace logiky, spolu s kontrolou aktivační události a spustí historii. Můžete například přidat kroky, které používají službu [Webhook Tester,](https://webhook.site/) abyste mohli zkontrolovat požadavky HTTP a určit jejich přesnou velikost, tvar a formát.

1. Přejděte na web [Webhook Tester](https://webhook.site/) a zkopírujte vygenerovanou jedinečnou adresu URL.

1. V aplikaci logiky přidejte akci HTTP POST plus obsah těla, který chcete testovat, například výraz nebo jiný krok výstup.

1. Vložte adresu URL z testeru Webhooku do akce HTTP POST.

1. Chcete-li zkontrolovat, jak je vytvořen požadavek při generování z modulu Logic Apps, spusťte aplikaci logiky a znovu web Webhook Tester pro další podrobnosti.

## <a name="next-steps"></a>Další kroky

* [Sledování aplikace logiky](../logic-apps/monitor-logic-apps.md)
