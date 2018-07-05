---
title: Řešení potíží a Diagnostika selhání – Azure Logic Apps | Dokumentace Microsoftu
description: Vysvětlení, jak a proč selhání aplikací logiky
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 10/15/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: b0bf6cd747860d938f80787d9bef6634a6a22d09
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441528"
---
# <a name="troubleshoot-and-diagnose-logic-app-failures"></a>Řešení potíží a Diagnostika selhání aplikací logiky

Aplikace logiky generuje informace, které vám může pomoct diagnostikovat a ladit problémy ve vaší aplikaci. Aplikace logiky můžete diagnostikovat kontrolou každý krok v pracovním postupu na webu Azure portal. Nebo některé kroky můžete přidat do pracovního postupu pro ladění za běhu.

## <a name="review-trigger-history"></a>Kontrola historie triggerů.

Každá aplikace logiky začíná triggerem. Pokud se aktivační událost se neaktivuje, nejprve zkontrolujte historie aktivačních událostí. Tato historie jsou všechny aktivační události pokusů o vaší aplikaci logiky a podrobnosti o vstupy a výstupy pro jednotlivé pokusy o aktivační události.

1. Chcete-li zkontrolovat, jestli trigger spustí v nabídce aplikace logiky, zvolte **přehled**. V části **historie aktivačních událostí**, zkontrolujte stav triggeru.

   > [!TIP]
   > Pokud se nabídka aplikace logiky nezobrazí, zkuste se vrátit na řídicí panel Azure a otevřít aplikaci logiky znovu.

   ![Kontrola historie triggerů.](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Pokud nenajdete očekávaná data, zkuste vybrat **aktualizovat** na panelu nástrojů.
   > * Pokud seznam obsahuje mnoho pokusů o aktivaci a nemůžete najít položku, kterou chcete, zkuste pomocí filtru v seznamu.

   Tady jsou možné stavy pro pokus o aktivační události:

   | Status | Popis | 
   | ------ | ----------- | 
   | **Bylo úspěšně dokončeno** | Trigger zkontroluje koncový bod a najít dostupná data. Obvykle "Fired" stav se zobrazí také společně s tímto stavem. Pokud ne, definice aktivační události může obsahovat podmínku, nebo `SplitOn` příkaz, který nebyl splněn. <p>Tento stav můžete použít ruční aktivační události, trigger opakování nebo cyklického dotazování aktivační události. Můžete úspěšně spustit aktivační událost, ale samotný běh může stále selhat, když akce, které generují neošetřené chyby. | 
   | **Vynecháno** | Aktivační událost zaregistrované na koncový bod, ale nenašel žádná data. | 
   | **Se nezdařilo** | Došlo k chybě. Zkontrolujte vygenerovaný chybové zprávy neúspěšných aktivační události, vyberte tento trigger pokus a zvolte **výstupy**. Můžete například zjistit vstupy, které nejsou platné. | 
   ||| 

   Můžete mít více položek aktivační událost se stejným datum a čas, který se stane, když vaše aplikace logiky najde více položek. 
   Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění pracovního postupu. Ve výchozím nastavení se každá instance spouští paralelně, tak, aby žádný pracovní postup má čekat před zahájením spuštění.

   > [!TIP]
   > Znovu se aktivační událost můžete zkontrolovat bez čekání na další opakování. Na panelu nástrojů themeroller přehled **spustit trigger**a vyberte trigger, který vynutí kontrolu. Nebo vyberte **spustit** na panelu nástrojů návrháře pro Logic Apps.

3. V části prozkoumat podrobnosti o požadavku na aktivační událost **historie aktivačních událostí**, vyberte tento trigger pokus. 

   ![Vyberte pokus o aktivační události](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Zkontrolujte vstupy a výstupy, které vygeneruje aktivační událost. Výstupy triggerů zobrazit data, která pochází z triggeru. Tyto výstupy můžete zjistit, jestli všechny vlastnosti vrácené podle očekávání.

   > [!NOTE]
   > Pokud najdete veškerý obsah, který nevíte, zjistěte, jak Azure Logic Apps [zpracovává různých typů obsahu](../logic-apps/logic-apps-content-type.md).

   ![Výstupy triggerů](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Kontrola historie spuštění

Každý spouštěný trigger spustí běhu pracovního postupu. Můžete zkontrolovat, co se stalo při spuštění, včetně stavu pro každý krok v pracovním postupu, a navíc vstupy a výstupy pro jednotlivé kroky.

1. V nabídce aplikace logiky zvolte **Přehled**. V části **historie běhů**, zkontrolujte běh pro spouštěný trigger.

   > [!TIP]
   > Pokud se nabídka aplikace logiky nezobrazí, zkuste se vrátit na řídicí panel Azure a otevřít aplikaci logiky znovu.

   ![Kontrola historie spuštění](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Pokud nenajdete očekávaná data, zkuste vybrat **aktualizovat** na panelu nástrojů.
   > * Pokud v seznamu se zobrazí počet spuštění a nemůžete najít požadované položky, zkuste pomocí filtru v seznamu.

   Tady jsou možné stavy pro spuštění:

   | Status | Popis | 
   | ------ | ----------- | 
   | **Bylo úspěšně dokončeno** | Všechny akce proběhla úspěšně. <p>Pokud v rámci konkrétní akce došlo k chybám, následující akce v pracovním postupu zpracována tohoto selhání. | 
   | **Se nezdařilo** | Nejméně jedna akce nebyla úspěšná, a pro zpracování chyby byly nastaveny žádné pozdějších akcích v pracovním postupu. | 
   | **Zrušeno** | Pracovní postup byl spuštěn, ale přijala žádost o zrušení. | 
   | **Spuštění** | Pracovní postup momentálně běží. <p>Tento stav může dojít, omezené pracovních postupů, nebo z důvodu aktuálního cenového plánu. Další informace najdete v tématu [akce omezení na stránce s cenami](https://azure.microsoft.com/pricing/details/logic-apps/). Pokud jste nastavili [protokolování diagnostiky](../logic-apps/logic-apps-monitor-your-logic-apps.md), můžete také získat informace o všech omezení událostí, ke kterým dochází. | 
   ||| 

2. Zkontrolujte podrobnosti pro jednotlivé kroky v konkrétního spuštění. V části **historie běhů**, vyberte spuštění, které chcete prověřit.

   ![Kontrola historie spuštění](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Určuje, zda spuštění samotný úspěšné nebo neúspěšné, zobrazit podrobnosti o spuštění zobrazí každý krok a určuje, zda úspěšné nebo neúspěšné.

   ![Zobrazení podrobností o spuštění aplikace logiky](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Prozkoumat vstupy, výstupy a chybové zprávy pro konkrétní krok, zvolte tak, aby tvaru rozbalí a zobrazí podrobnosti tohoto kroku. Příklad:

   ![Zobrazení podrobností o kroku](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Proveďte ladění za běhu

Chcete-li pomoci s laděním, můžete přidat diagnostiky kroky do pracovního postupu, spolu s revizí aktivační událost a historie spuštění. Například můžete přidat kroky, které používají [Webhooku Tester](https://webhook.site/) služby tak, že můžete zkontrolovat požadavky HTTP a určit jejich přesnou velikost, tvar a formát.

1. Navštivte [Webhooku Tester](https://webhook.site/) a zkopírujte jedinečnou adresu URL, které jsou vytvořeny

2. V aplikaci logiky přidejte akce HTTP POST s obsah textu, který chcete otestovat, například, výraz nebo jiný výstup krok.

3. Vložte adresu URL pro testování vaší Webhooku na akci HTTP POST.

4. Ke kontrole, jak je vytvořen požadavek při generování z modul Logic Apps, spustili aplikaci logiky a testování Webhooku podrobnosti naleznete v tématu.

## <a name="next-steps"></a>Další postup

[Monitorování aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)
