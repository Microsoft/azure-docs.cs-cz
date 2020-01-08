---
title: Řešení potíží a Diagnostika selhání pracovního postupu
description: Naučte se řešit a diagnostikovat problémy, chyby a chyby v pracovních postupech v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 79cc9d1bf7aa9e8848197525646b0a3646a558d2
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666801"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Řešení potíží a Diagnostika selhání pracovního postupu v Azure Logic Apps

Vaše aplikace logiky generuje informace, které vám pomohou diagnostikovat a ladit problémy ve vaší aplikaci. Aplikaci logiky můžete diagnostikovat tak, že zkontrolujete každý krok pracovního postupu prostřednictvím Azure Portal. Případně můžete do pracovního postupu přidat některé kroky pro ladění za běhu.

## <a name="review-trigger-history"></a>Kontrola historie aktivačních událostí

Každá aplikace logiky se spustí s triggerem. Pokud se Trigger neaktivuje, nejdřív ověřte historii triggeru. Tato historie obsahuje seznam všech pokusů o aktivační události, které vaše aplikace logiky vytvořila, a podrobnosti o vstupech a výstupech pro každý pokus o aktivaci.

1. Pokud chcete zjistit, jestli Trigger vyvolal, klikněte v nabídce aplikace logiky na **Přehled**. V části **Historie aktivační události**zkontrolujte stav triggeru.

   > [!TIP]
   > Pokud se nabídka aplikace logiky nezobrazí, zkuste se vrátit na řídicí panel Azure a otevřít aplikaci logiky znovu.

   ![Kontrola historie aktivačních událostí](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Pokud nenajdete očekávaná data, zkuste na panelu nástrojů vybrat možnost **aktualizovat** .
   > * Pokud se v seznamu zobrazí mnoho pokusů o aktivační události a nemůžete najít požadovanou položku, zkuste vyfiltrovat seznam.

   Tady jsou možné stavy pro pokus o aktivační událost:

   | Stav | Popis | 
   | ------ | ----------- | 
   | **Úspěchu** | Aktivační událost kontrolovala koncový bod a našla dostupná data. Stav "aktivováno" se obvykle zobrazuje současně s tímto stavem. V takovém případě může mít definice triggeru podmínku nebo příkaz `SplitOn`, který nebyl splněn. <p>Tento stav se může vztahovat na manuální aktivační událost, Trigger opakování nebo aktivační událost cyklického dotazování. Aktivační událost může být úspěšně spuštěna, ale samotný běh může selhat i v případě, že akce generují neošetřené chyby. | 
   | **Vynecháno** | Aktivační událost kontrolovala koncový bod, ale nenašla žádná data. | 
   | **Se nezdařilo** | Došlo k chybě. Pokud chcete zkontrolovat všechny generované chybové zprávy pro aktivační událost, vyberte tuto aktivační událost a zvolte **výstup**. Například můžete najít vstupy, které nejsou platné. | 
   ||| 

   Můžete mít více položek triggeru se stejným datem a časem, k němuž dojde, když aplikace logiky nalezne více položek. 
   Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky pro spuštění vašeho pracovního postupu. Ve výchozím nastavení jsou jednotlivé instance spouštěny paralelně, takže žádný pracovní postup nesmí čekat před spuštěním spuštění.

   > [!TIP]
   > Aktivační událost se dá znovu ověřit bez čekání na další opakování. Na panelu nástrojů přehled zvolte možnost **Spustit Trigger**a vyberte aktivační událost, která vynucuje kontrolu. Případně vyberte možnost **Spustit** na panelu nástrojů návrháře Logic Apps.

3. Pokud si chcete prohlédnout podrobnosti o pokusu o aktivační událost, vyberte v části **Historie aktivační**události tento pokus o aktivaci. 

   ![Vybrat pokus o aktivaci](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Zkontrolujte vstupy a výstupy, které aktivační událost vygenerovala. Výstupy aktivačních událostí zobrazují data, která byla získána z triggeru. Tyto výstupy vám pomohou určit, zda všechny vlastnosti vráceny podle očekávání.

   > [!NOTE]
   > Pokud najdete nějaký obsah, který nerozumíte, přečtěte si, jak Azure Logic Apps [zpracovává různé typy obsahu](../logic-apps/logic-apps-content-type.md).

   ![Výstupy triggeru](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Kontrola historie spuštění

Každá aktivovaná aktivační událost spustí spuštění pracovního postupu. Můžete se podívat, co se stalo během tohoto spuštění, včetně stavu každého kroku v pracovním postupu, a také vstupy a výstupy pro jednotlivé kroky.

1. V nabídce aplikace logiky zvolte **Přehled**. V části **historie spuštění**zkontrolujte spuštění triggeru aktivovaného.

   > [!TIP]
   > Pokud se nabídka aplikace logiky nezobrazí, zkuste se vrátit na řídicí panel Azure a otevřít aplikaci logiky znovu.

   ![Historie spuštění kontroly](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Pokud nenajdete očekávaná data, zkuste na panelu nástrojů vybrat možnost **aktualizovat** .
   > * Pokud se v seznamu zobrazují spousty spuštění a nemůžete najít požadovanou položku, zkuste vyfiltrovat seznam.

   Tady jsou možné stavy pro spuštění:

   | Stav | Popis | 
   | ------ | ----------- | 
   | **Úspěchu** | Všechny akce byly úspěšné. <p>Pokud v určité akci došlo k nějakým chybám, tato akce v pracovním postupu tuto chybu zpracovala. | 
   | **Se nezdařilo** | Nejméně jedna akce se nezdařila a žádné pozdější akce v pracovním postupu nebyly nastaveny pro zpracování tohoto selhání. | 
   | **Stornován** | Pracovní postup byl spuštěn, ale přijal žádost o zrušení. | 
   | **Instalovanou** | Pracovní postup je aktuálně spuštěný. <p>Tento stav může nastat pro omezené pracovní postupy nebo z důvodu aktuálního cenového plánu. Další informace najdete v tématu [omezení akcí na stránce s cenami](https://azure.microsoft.com/pricing/details/logic-apps/). Pokud nastavíte [diagnostické protokolování](../logic-apps/logic-apps-monitor-your-logic-apps.md), můžete také získat informace o všech událostech omezení, ke kterým dochází. | 
   ||| 

2. Projděte si podrobnosti o jednotlivých krocích konkrétního spuštění. V části **historie spuštění**vyberte běh, který chcete prošetřit.

   ![Historie spuštění kontroly](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Bez ohledu na to, zda se spuštění zdařilo nebo nebylo úspěšné, zobrazí se v zobrazení Podrobnosti o spuštění každý krok a zda byly úspěšné nebo neúspěšné.

   ![Zobrazení podrobností o spuštění aplikace logiky](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Chcete-li prostudovat vstupy, výstupy a jakékoli chybové zprávy pro určitý krok, vyberte tento krok, aby se tvar rozbalí a zobrazí podrobnosti. Příklad:

   ![Zobrazení podrobností o kroku](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Provést ladění za běhu

Pro usnadnění ladění můžete do pracovního postupu přidat diagnostické kroky a zkontrolovat historii triggeru a spuštění. Můžete například přidat kroky, které používají službu [Tester Webhooku](https://webhook.site/) , abyste mohli kontrolovat požadavky HTTP a určit jejich přesnou velikost, tvar a formát.

1. Navštivte [Webhook Tester](https://webhook.site/) a zkopírujte jedinečnou VYTVOŘENOU adresu URL.

2. V aplikaci logiky přidejte akci HTTP POST s obsahem textu, který chcete testovat, například výraz nebo výstup jiného kroku.

3. Vložte adresu URL pro vašeho testera Webhooku do akce HTTP POST.

4. Chcete-li zjistit, jak je požadavek vytvořen při generování z modulu Logic Apps, spusťte aplikaci logiky a zobrazte podrobnosti v části Tester Webhooku.

## <a name="next-steps"></a>Další kroky

[Monitorování aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)
