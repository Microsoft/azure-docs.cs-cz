---
title: Monitorování stavu, historie zobrazení a nastavení výstrah
description: Řešení potíží s Logic Apps pomocí kontroly stavu spuštění, kontrola historie aktivačních událostí a povolení výstrah v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 66796a819c0ca7e114d82210a988fc7e13003941
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078200"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Monitorovat stav spuštění, zkontrolovat historii triggerů a nastavit výstrahy pro Azure Logic Apps

Po [Vytvoření a spuštění aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)můžete kontrolovat stav spuštění aplikace logiky, [historii spuštění](#review-runs-history), [historii aktivačních událostí](#review-trigger-history)a výkon. Pokud chcete dostávat oznámení o selháních nebo jiných možných problémech, nastavte [výstrahy](#add-azure-alerts). Můžete například vytvořit výstrahu, která detekuje "Pokud více než pět běhů selže za hodinu."

Pro monitorování událostí v reálném čase a bohatší ladění nastavte protokolování diagnostiky pro vaši aplikaci logiky pomocí [protokolů Azure monitor](../azure-monitor/overview.md). Tato služba Azure vám pomůže monitorovat cloudové a místní prostředí, abyste mohli snadněji udržovat jejich dostupnost a výkon. Pak můžete najít a zobrazit události, jako jsou události triggeru, události spuštění a události akcí. Uložením těchto informací v [protokolech Azure monitor](../azure-monitor/platform/data-platform-logs.md)můžete vytvořit [dotazy protokolu](../azure-monitor/log-query/log-query-overview.md) , které vám pomůžou najít a analyzovat tyto informace. Tato diagnostická data můžete také použít s jinými službami Azure, například Azure Storage a Azure Event Hubs. Další informace najdete v tématu [monitorování aplikací logiky pomocí Azure monitor](../logic-apps/monitor-logic-apps-log-analytics.md).

> [!NOTE]
> Pokud vaše aplikace logiky běží v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , které bylo vytvořeno za účelem použití [koncového bodu interního přístupu](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access), můžete zobrazit vstupy a výstupy z historie spuštění aplikace logiky *jenom v rámci vaší virtuální sítě*. Ujistěte se, že máte síťové připojení mezi soukromými koncovými body a počítačem, ze kterého chcete získat přístup k historii spuštění. Například klientský počítač může existovat uvnitř virtuální sítě ISE nebo uvnitř virtuální sítě, která je připojená k virtuální síti ISE, například prostřednictvím partnerského vztahu nebo virtuální privátní sítě. Další informace najdete v tématu [ISE Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Kontrola historie spuštění

Pokaždé, když se Trigger aktivuje pro položku nebo událost, vytvoří modul Logic Apps a spustí samostatnou instanci pracovního postupu pro každou položku nebo událost. Ve výchozím nastavení se každá instance pracovního postupu spouští paralelně, takže žádný pracovní postup nesmí čekat před spuštěním spuštění. Můžete se podívat, co se stalo během tohoto spuštění, včetně stavu každého kroku v pracovním postupu a vstupy a výstupy pro jednotlivé kroky.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete aplikaci logiky v návrháři aplikace logiky.

   Pokud chcete najít aplikaci logiky, zadejte do hlavního pole Azure Search `logic apps` a pak vyberte **Logic Apps**.

   ![Vyhledejte a vyberte službu Logic Apps.](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure Portal zobrazí všechny aplikace logiky, které jsou přidruženy k předplatným Azure. Tento seznam můžete filtrovat podle názvu, předplatného, skupiny prostředků, umístění atd.

   ![Zobrazení aplikací logiky přidružených k předplatným](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Vyberte svou aplikaci logiky a pak vyberte **Přehled**.

   V podokně Přehled se zobrazí v části **historie spuštění**všechny poslední, aktuální a všechny čekající běhy pro vaši aplikaci logiky. Pokud se v seznamu zobrazí mnoho spuštění a nemůžete najít požadovanou položku, zkuste vyfiltrovat seznam. Pokud nenajdete očekávaná data, zkuste na panelu nástrojů vybrat možnost **aktualizovat** .

   ![Přehled, historie spuštění a další informace o aplikaci logiky](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Tady jsou možné stavy pro spuštění aplikace logiky:

   | Status | Popis |
   |--------|-------------|
   | **Stornován** | Pracovní postup byl spuštěn, ale přijal žádost o zrušení. |
   | **Neúspěšný** | Nejméně jedna akce se nezdařila a žádné pozdější akce v pracovním postupu nebyly nastaveny pro zpracování selhání. |
   | **Spuštěno** | Pracovní postup je aktuálně spuštěný. <p>Tento stav se může zobrazit také pro omezené pracovní postupy nebo z důvodu aktuálního cenového plánu. Další informace najdete v tématu [omezení akcí na stránce s cenami](https://azure.microsoft.com/pricing/details/logic-apps/). Pokud nastavíte [diagnostické protokolování](../logic-apps/monitor-logic-apps.md), můžete získat informace o všech událostech omezení, ke kterým dochází. |
   | **Úspěšný** | Všechny akce byly úspěšné. <p>**Poznámka**: Pokud došlo k nějakým chybám v určité akci, bude tato chyba zpracována později v pracovním postupu. |
   | **Čekající** | Pracovní postup nebyl spuštěn nebo byl pozastaven, například kvůli dřívějšímu pracovnímu postupu, který je stále spuštěn. |
   |||

1. Chcete-li zkontrolovat postup a další informace pro konkrétní spuštění, vyberte v části **historie spuštění**možnost spustit.

   ![Vyberte konkrétní spuštění ke kontrole.](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   V podokně **spuštění aplikace logiky** se zobrazí každý krok ve vybraném spuštění, stav spuštění každého kroku a čas potřebný ke spuštění každého kroku, například:

   ![Každá akce v konkrétním běhu](./media/monitor-logic-apps/logic-app-run-pane.png)

   Chcete-li zobrazit tyto informace ve formuláři seznamu, vyberte na panelu nástrojů **spuštění aplikace logiky** možnost **Spustit podrobnosti**.

   ![Na panelu nástrojů vyberte spustit podrobnosti.](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   Zobrazení podrobností o spuštění zobrazuje jednotlivé kroky, jejich stav a další informace.

   ![Zkontrolujte podrobnosti o jednotlivých krocích spuštění.](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Můžete například získat vlastnost **ID korelace** spuštění, kterou můžete potřebovat při použití [REST API pro Logic Apps](/rest/api/logic).

1. Pokud chcete získat další informace o konkrétním kroku, vyberte jednu z možností:

   * V podokně **spuštění aplikace logiky** vyberte krok, aby se obrazec rozbalí. Nyní můžete zobrazit informace, jako jsou vstupy, výstupy a případné chyby, ke kterým došlo v tomto kroku, například:

     ![V podokně spuštění aplikace logiky zobrazit neúspěšný krok](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * V podokně **podrobností o spuštění aplikace logiky** vyberte požadovaný krok.

     ![V podokně podrobností o spuštění se zobrazí neúspěšný krok.](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Nyní můžete zobrazit informace, jako jsou vstupy a výstupy pro daný krok, například:

   > [!NOTE]
   > Všechny podrobnosti a události modulu runtime jsou v rámci Logic Apps služby šifrované. Šifrují se pouze v případě, že uživatel požaduje zobrazení těchto dat. Pomocí [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md)můžete [Skrýt vstupy a výstupy v historii spuštění](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) nebo řídit přístup uživatelů k těmto informacím.

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Kontrola historie aktivačních událostí

Každá aplikace logiky se spustí s triggerem. V okně historie aktivačních událostí se zobrazí všechny pokusy o aktivaci, které vaše aplikace logiky vytvořila, a informace o vstupech a výstupech pro každý pokus o aktivaci

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete aplikaci logiky v návrháři aplikace logiky.

   Pokud chcete najít aplikaci logiky, zadejte do hlavního pole Azure Search `logic apps` a pak vyberte **Logic Apps**.

   ![Vyhledejte a vyberte službu Logic Apps.](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure Portal zobrazí všechny aplikace logiky, které jsou přidruženy k předplatným Azure. Tento seznam můžete filtrovat podle názvu, předplatného, skupiny prostředků, umístění atd.

   ![Zobrazení aplikací logiky přidružených k předplatným](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Vyberte svou aplikaci logiky a pak vyberte **Přehled**.

1. V nabídce aplikace logiky zvolte **Přehled**. V části **Souhrn** vyberte v části **hodnocení**možnost **Zobrazit historii aktivačních událostí**.

   ![Zobrazení historie aktivačních událostí pro vaši aplikaci logiky](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   V podokně historie aktivačních událostí se zobrazí všechny pokusy o aktivační události, které vaše aplikace logiky vytvořila. Pokaždé, když se Trigger aktivuje pro položku nebo událost, vytvoří modul Logic Apps samostatnou instanci aplikace logiky, která spouští pracovní postup. Ve výchozím nastavení jsou jednotlivé instance spouštěny paralelně, takže žádný pracovní postup nesmí čekat před spuštěním spuštění. Takže pokud vaše aplikace logiky aktivuje více položek najednou, zobrazí se pro každou položku aktivační položka se stejným datem a časem.

   ![Více pokusů o spuštění různých položek](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Tady jsou možné stavy pro pokus o aktivační událost:

   | Status | Popis |
   |--------|-------------|
   | **Neúspěšný** | Došlo k chybě. Pokud chcete zkontrolovat všechny generované chybové zprávy pro aktivační událost, vyberte tuto aktivační událost a zvolte **výstup**. Například můžete najít vstupy, které nejsou platné. |
   | **Přeskočeno** | Aktivační událost kontrolovala koncový bod, ale nenašla žádná data. |
   | **Úspěšný** | Aktivační událost kontrolovala koncový bod a našla dostupná data. Stav "aktivováno" se obvykle zobrazuje současně s tímto stavem. V takovém případě může být v definici triggeru podmínka nebo `SplitOn` příkaz, který nebyl splněn. <p>Tento stav se může vztahovat na manuální aktivační událost, Trigger opakování nebo aktivační událost cyklického dotazování. Aktivační událost může být úspěšně spuštěna, ale samotný běh může selhat i v případě, že akce generují neošetřené chyby. |
   |||

   > [!TIP]
   > Aktivační událost se dá znovu ověřit bez čekání na další opakování. Na panelu nástrojů přehled vyberte **Spustit Trigger**a vyberte aktivační událost, která vynucuje kontrolu. Případně vyberte možnost **Spustit** na panelu nástrojů návrháře Logic Apps.

1. Chcete-li zobrazit informace o konkrétním pokusu o aktivační událost, vyberte v podokně Trigger tuto aktivační událost. Pokud se v seznamu zobrazí mnoho pokusů o aktivační události a nemůžete najít požadovanou položku, zkuste vyfiltrovat seznam. Pokud nenajdete očekávaná data, zkuste na panelu nástrojů vybrat možnost **aktualizovat** .

   ![Zobrazit konkrétní pokus o aktivaci](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Nyní můžete zkontrolovat informace o vybrané události triggeru, například:

   ![Zobrazit informace o konkrétním triggeru](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Nastavení výstrah monitorování

Pokud chcete dostávat upozornění na základě konkrétních metrik nebo překročení prahových hodnot pro vaši aplikaci logiky, nastavte [výstrahy v Azure monitor](../azure-monitor/platform/alerts-overview.md). Seznamte [se s metrikami v Azure](../azure-monitor/platform/data-platform.md). Pokud chcete nastavit výstrahy bez použití [Azure monitor](../azure-monitor/log-query/log-query-overview.md), postupujte podle těchto kroků.

1. V nabídce aplikace logiky v části **monitorování**vyberte **výstrahy**  >  **nové pravidlo výstrahy**.

   ![Přidání upozornění pro aplikaci logiky](./media/monitor-logic-apps/add-new-alert-rule.png)

1. V podokně **vytvořit pravidlo** vyberte v části **prostředek**možnost aplikace logiky, pokud ještě není vybraná. V části **Podmínka**vyberte **Přidat** , abyste mohli definovat podmínku, která aktivuje výstrahu.

   ![Přidat podmínku pro pravidlo](./media/monitor-logic-apps/add-condition-for-rule.png)

1. V podokně **Konfigurovat logiku signálu** vyhledejte a vyberte signál, pro který chcete získat výstrahu. Můžete použít vyhledávací pole nebo k řazení signálů abecedně, vyberte záhlaví sloupce **Název signálu** .

   Pokud například chcete odeslat výstrahu, když dojde k chybě triggeru, postupujte podle následujících kroků:

   1. Ve sloupci **Název signálu** vyhledejte a vyberte signál **triggery, které selhaly** .

      ![Vybrat signál pro vytvoření výstrahy](./media/monitor-logic-apps/find-and-select-signal.png)

   1. V podokně s informacemi, které se otevře pro vybraný signál, v části **logika výstrahy**nastavte podmínku, například:

   1. Jako **operátor**vyberte **větší než nebo rovno**.

   1. Jako **typ agregace**vyberte **počet**.

   1. V případě **prahové hodnoty**zadejte `1` .

   1. V části **Preview podmínka**ověřte, že se zobrazuje stav správné.

   1. Pod položkou **vyhodnoceno na základě**nastavte interval a četnost spouštění pravidla výstrahy. Pro **členitost agregace (perioda)** vyberte období pro seskupení dat. V případě **četnosti vyhodnocení**vyberte, jak často chcete podmínku ověřit.

   1. Až budete připraveni, vyberte **Hotovo**.

   Tady je stav dokončení:

   ![Nastavit podmínku pro výstrahu](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   Na stránce **vytvořit pravidlo** se nyní zobrazuje stav, který jste vytvořili, a náklady na spuštění této výstrahy.

   ![Nové upozornění na stránce Vytvořit pravidlo](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Zadejte název, volitelný popis a úroveň závažnosti výstrahy. Buď ponechte **pravidlo Povolit při vytváření** nastavení zapnuté, nebo ho vypněte, dokud nebudete připraveni pravidlo Povolit.

1. Až budete hotovi, vyberte **vytvořit pravidlo výstrahy**.

> [!TIP]
> Pokud chcete z výstrahy spustit aplikaci logiky, můžete do svého pracovního postupu zahrnout [aktivační událost žádosti](../connectors/connectors-native-reqres.md) , která vám umožní provádět následující úlohy, jako jsou tyto příklady:
> 
> * [Odeslat do časové rezervy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Odeslat text](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Přidat zprávu do fronty](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Další kroky

* [Monitorování aplikací logiky pomocí Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)
