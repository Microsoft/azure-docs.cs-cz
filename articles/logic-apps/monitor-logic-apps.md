---
title: Sledování stavu, zobrazení historie a nastavení výstrah
description: Poradce při potížích s aplikacemi logiky kontrolou stavu spuštění, kontrolou historie aktivačních událostí a povolením výstrah v Aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907770"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Sledování stavu spuštění, kontrola historie aktivačních událostí a nastavení výstrah pro aplikace Azure Logic Apps

Po [vytvoření a spuštění aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)můžete zkontrolovat stav spuštění této aplikace logiky, [spustí historii](#review-runs-history), [historii aktivačních událostí](#review-trigger-history)a výkon. Chcete-li dostávat oznámení o selháních nebo jiných možných problémech, nastavte [výstrahy](#add-azure-alerts). Můžete například vytvořit výstrahu, která detekuje "při selhání více než pět spuštění za hodinu."

Pro monitorování událostí v reálném čase a bohatší ladění nastavte protokolování diagnostiky pro aplikaci logiky pomocí [protokolů Azure Monitor](../azure-monitor/overview.md). Tato služba Azure vám pomůže monitorovat cloudová a místní prostředí, takže můžete snadněji udržovat jejich dostupnost a výkon. Potom můžete vyhledat a zobrazit události, jako jsou události aktivační události, události spuštění a události akcí. Uložením těchto informací v [protokolech Azure Monitor](../azure-monitor/platform/data-platform-logs.md), můžete vytvořit [dotazy protokolu,](../azure-monitor/log-query/log-query-overview.md) které vám pomohou najít a analyzovat tyto informace. Tato diagnostická data můžete taky použít s jinými službami Azure, jako je Azure Storage a Azure Event Hubs. Další informace najdete v tématu [Sledování aplikací logiky pomocí Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Recenze spustí historii

Pokaždé, když aktivační událost spustí pro položku nebo událost, modul Logic Apps vytvoří a spustí samostatnou instanci pracovního postupu pro každou položku nebo událost. Ve výchozím nastavení se každá instance pracovního postupu spouští paralelně, takže žádný pracovní postup musí čekat před spuštěním spuštění. Můžete zkontrolovat, co se stalo během tohoto spuštění, včetně stavu pro každý krok v pracovním postupu plus vstupy a výstupy pro každý krok.

1. Na [webu Azure Portal](https://portal.azure.com)najděte a otevřete aplikaci logiky v Návrháři aplikací logiky.

   Pokud chcete najít aplikaci logiky , `logic apps`v hlavním vyhledávacím poli Azure zadejte a vyberte **Logic Apps**.

   ![Vyhledání a výběr služby "Logic Apps"](./media/monitor-logic-apps/find-your-logic-app.png)

   Na webu Azure Portal se zobrazují všechny aplikace logiky, které jsou přidružené k vašim předplatným Azure. Tento seznam můžete filtrovat na základě názvu, předplatného, skupiny prostředků, umístění a tak dále.

   ![Zobrazení aplikací logiky přidružených k předplatným](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Vyberte aplikaci logiky a pak vyberte **Přehled**.

   V podokně přehledu se v části **Historie spustí**zobrazí všechny minulé, aktuální a všechny čekající spuštění aplikace logiky. Pokud seznam zobrazuje mnoho spuštění a nemůžete najít požadovanou položku, zkuste seznam filtrovat. Pokud nenajdete očekávaná data, zkuste na panelu nástrojů vybrat **aktualizovat.**

   ![Přehled, spouští historii a další informace o aplikaci logiky](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Tady jsou možné stavy pro spuštění aplikace logiky:

   | Status | Popis |
   |--------|-------------|
   | **Cancelled** | Pracovní postup byl spuštěn, ale obdržel požadavek na zrušení. |
   | **Failed** | Nejméně jedna akce se nezdařila a žádné pozdější akce v pracovním postupu nebyly nastaveny pro zpracování selhání. |
   | **Spuštěno** | Pracovní postup je právě spuštěn. <p>Tento stav se může zobrazit také pro omezené pracovní postupy nebo z důvodu aktuálního cenového plánu. Další informace naleznete v [omezeních akcí na stránce s cenami](https://azure.microsoft.com/pricing/details/logic-apps/). Pokud nastavíte [protokolování diagnostiky](../logic-apps/monitor-logic-apps.md), můžete získat informace o všech událostech omezení, ke kterým dojde. |
   | **Úspěch** | Všechny akce byly úspěšné. <p>**Poznámka:** Pokud došlo k chybám v určité akci, pozdější akce v pracovním postupu tuto chybu zpracovala. |
   | **Čekání** | Pracovní postup nebyl spuštěn nebo je pozastaven, například z důvodu dřívějšího pracovního postupu, který je stále spuštěn. |
   |||

1. Chcete-li zkontrolovat kroky a další informace pro konkrétní spuštění, vyberte v části **Spuštění historie**toto spuštění.

   ![Vyberte konkrétní spuštění ke kontrole.](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   Podokno **spuštění aplikace Logika** zobrazuje každý krok ve vybraném spuštění, stav spuštění každého kroku a čas, který byl pro každý krok spuštěn, například:

   ![Každá akce v konkrétním spuštění](./media/monitor-logic-apps/logic-app-run-pane.png)

   Chcete-li tyto informace zobrazit ve formuláři seznamu, vyberte na panelu nástrojů **spuštění aplikace Logika** **položku Spustit podrobnosti**.

   ![Na panelu nástrojů vyberte "Spustit podrobnosti"](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   Zobrazení Podrobnosti spuštění zobrazuje každý krok, jejich stav a další informace.

   ![Projděte si podrobnosti o jednotlivých krocích v běhu](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Například můžete získat spuštění **id korelace** vlastnost, kterou můžete potřebovat při použití [rozhraní REST API pro logic apps](https://docs.microsoft.com/rest/api/logic).

1. Chcete-li získat další informace o konkrétním kroku, vyberte jednu z možností:

   * V podokně **spuštění aplikace Logika** vyberte krok tak, aby se obrazec rozbalil. Nyní můžete zobrazit informace, jako jsou vstupy, výstupy a všechny chyby, ke kterým došlo v tomto kroku, například:

     ![V podokně spuštění aplikace logiky se nepodařilo zobrazit krok zobrazení](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * V podokně **podrobností spuštění aplikace Logika** vyberte požadovaný krok.

     ![V podokně podrobností spuštění se nezdařil krok zobrazení](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Nyní můžete zobrazit informace, jako jsou vstupy a výstupy pro tento krok, například:

   > [!NOTE]
   > Všechny podrobnosti o běhu a události jsou šifrovány v rámci služby Logic Apps. Jsou dešifrovány pouze v případě, že uživatel požádá o zobrazení dat. Můžete [skrýt vstupy a výstupy v historii spuštění](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) nebo řídit přístup uživatelů k těmto informacím pomocí Řízení [přístupu na základě rolí Azure (RBAC)](../role-based-access-control/overview.md).

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Kontrola historie aktivačních událostí

Každé spuštění aplikace logiky začíná aktivační událostí. Historie aktivační události uvádí všechny pokusy o aktivační událost, které vaše aplikace logiky a informace o vstupy a výstupy pro každý pokus o aktivační událost.

1. Na [webu Azure Portal](https://portal.azure.com)najděte a otevřete aplikaci logiky v Návrháři aplikací logiky.

   Pokud chcete najít aplikaci logiky , `logic apps`v hlavním vyhledávacím poli Azure zadejte a vyberte **Logic Apps**.

   ![Vyhledání a výběr služby "Logic Apps"](./media/monitor-logic-apps/find-your-logic-app.png)

   Na webu Azure Portal se zobrazují všechny aplikace logiky, které jsou přidružené k vašim předplatným Azure. Tento seznam můžete filtrovat na základě názvu, předplatného, skupiny prostředků, umístění a tak dále.

   ![Zobrazení aplikací logiky přidružených k předplatným](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Vyberte aplikaci logiky a pak vyberte **Přehled**.

1. V nabídce aplikace logiky vyberte **Přehled**. V části **Souhrn** vyberte v části **Vyhodnocení** **položku Zobrazit historii aktivačních událostí**.

   ![Zobrazení historie aktivačních událostí pro aplikaci logiky](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   Podokno historie aktivačních událostí zobrazuje všechny pokusy o aktivační událost, které vaše aplikace logiky provedla. Pokaždé, když aktivační událost spustí pro položku nebo událost, modul Logic Apps vytvoří samostatnou instanci aplikace logiky, která spustí pracovní postup. Ve výchozím nastavení každá instance běží paralelně, takže žádný pracovní postup musí čekat před spuštěním spuštění. Takže pokud vaše aplikace logiky aktivuje na více položek současně, aktivační událost položka se stejným datem a časem se zobrazí pro každou položku.

   ![Více pokusů o aktivační událost pro různé položky](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Zde jsou možné stavy pro pokus o aktivační událost:

   | Status | Popis |
   |--------|-------------|
   | **Failed** | Došlo k chybě. Chcete-li zkontrolovat všechny generované chybové zprávy pro neúspěšnou aktivační událost, vyberte tento pokus o aktivační událost a zvolte **Výstupy**. Můžete například najít vstupy, které nejsou platné. |
   | **Přeskočen** | Aktivační událost zkontrolovala koncový bod, ale nenašla žádná data. |
   | **Úspěch** | Aktivační událost zkontrolovala koncový bod a nalezla dostupná data. Obvykle se vedle tohoto stavu zobrazí také stav "Fired". Pokud tomu tak není, definice `SplitOn` aktivační události může mít podmínku nebo příkaz, který nebyl splněn. <p>Tento stav lze použít pro ruční aktivační událost, aktivační událost opakování nebo aktivační událost dotazování. Aktivační událost může být úspěšně spuštěna, ale samotné spuštění může stále selhat, pokud akce generují neošetřené chyby. |
   |||

   > [!TIP]
   > Aktivační událost můžete znovu zkontrolovat bez čekání na další opakování. Na panelu nástrojů přehledu vyberte **Spustit aktivační událost**a vyberte aktivační událost, která vynutí kontrolu. Nebo vyberte **Spustit** na panelu nástrojů Návrháře logických aplikací.

1. Chcete-li zobrazit informace o konkrétním pokusu o aktivační událost, vyberte v podokně aktivační události tuto aktivační událost. Pokud seznam zobrazuje mnoho pokusů o aktivační událost a nemůžete najít požadovanou položku, zkuste seznam filtrovat. Pokud nenajdete očekávaná data, zkuste na panelu nástrojů vybrat **aktualizovat.**

   ![Zobrazit konkrétní pokus o aktivační událost](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Nyní můžete zkontrolovat informace o vybrané aktivační události, například:

   ![Zobrazit konkrétní informace o aktivační události](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Nastavení výstrah monitorování

Chcete-li získat výstrahy na základě konkrétnímetriky nebo překročení prahových hodnot pro aplikaci logiky, nastavte [výstrahy v Azure Monitoru](../azure-monitor/platform/alerts-overview.md). Přečtěte si o [metrikách v Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Pokud chcete nastavit výstrahy bez použití [Azure Monitoru](../log-analytics/log-analytics-overview.md), postupujte takto.

1. V nabídce aplikace logiky vyberte v části **Sledování**možnost **Upozornění** > **nové hospo- pravidlo výstrah**.

   ![Přidání upozornění pro aplikaci logiky](./media/monitor-logic-apps/add-new-alert-rule.png)

1. V podokně **Vytvořit pravidla** vyberte v části **Prostředek**aplikaci logiky, pokud ještě není vybraná. V **části Podmínka**vyberte **Přidat,** abyste mohli definovat podmínku, která spustí výstrahu.

   ![Přidání podmínky pro pravidlo](./media/monitor-logic-apps/add-condition-for-rule.png)

1. V podokně **Konfigurovat logiku signálu** vyhledejte a vyberte signál, pro který chcete získat výstrahu. Můžete použít vyhledávací pole nebo seřadit signály abecedně, vyberte záhlaví sloupce **Název signálu.**

   Chcete-li například odeslat výstrahu v případě selhání aktivační události, postupujte takto:

   1. Ve sloupci **Název signálu** vyhledejte a vyberte signál **Triggers Failed** .

      ![Vybrat signál pro vytvoření výstrahy](./media/monitor-logic-apps/find-and-select-signal.png)

   1. V informačním podokně, které se otevře pro vybraný signál, nastavte v části **Logika výstrahy**stav, například:

   1. V **částce Operátor**vyberte **možnost Větší nebo rovna .**

   1. V **popřípadě vyberte možnost** **Počet**.

   1. Do **prahové** `1`hodnoty zadejte .

   1. V **části Náhled podmínky**zkontrolujte, zda je stav správný.

   1. V části **Vyhodnocení na základě nanastavte**interval a frekvenci pro spuštění pravidla výstrahy. Pro **rozlišovací schopnost agregace (Období)** vyberte období pro seskupení dat. V **části Frekvence hodnocení**vyberte, jak často chcete podmínku kontrolovat.

   1. Až budete připraveni, vyberte **Hotovo**.

   Zde je hotová podmínka:

   ![Nastavit podmínku výstrahy](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   Stránka **Vytvořit pravidlo** nyní zobrazuje podmínku, kterou jste vytvořili, a náklady na spuštění této výstrahy.

   ![Nová výstraha na stránce Vytvořit pravidlo](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Zadejte název, volitelný popis a úroveň závažnosti výstrahy. Ponechte **nastavení Povolit pravidlo při vytváření** zapnuté, nebo ho vypněte, dokud nebudete připraveni pravidlo povolit.

1. Až budete hotovi, vyberte **Vytvořit pravidlo výstrahy**.

> [!TIP]
> Chcete-li spustit aplikaci logiky z výstrahy, můžete do pracovního postupu zahrnout [aktivační událost požadavku,](../connectors/connectors-native-reqres.md) která vám umožní provádět úlohy, jako jsou tyto příklady:
> 
> * [Zaúčtovat do slacku](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Odeslání textu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Přidání zprávy do fronty](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Další kroky

* [Monitorování aplikací logiky pomocí Azure Monitoru](../logic-apps/monitor-logic-apps-log-analytics.md)