---
title: Monitorování zpráv B2B pomocí Azure Monitoru
description: Poradce při potížích se zprávami AS2, X12 a EDIFACT nastavením protokolů Azure Monitoru a shromažďováním diagnostických dat pro aplikace Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907978"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Nastavení protokolů Azure Monitoru a shromažďování diagnostických dat pro b2B zprávy v Aplikacích Azure Logic Apps

Po nastavení b2B komunikace mezi obchodními partnery ve vašem integračním účtu si tito partneři mohou vyměňovat zprávy pomocí protokolů, jako jsou AS2, X12 a EDIFACT. Chcete-li zkontrolovat, zda tato komunikace funguje tak, jak očekáváte, můžete nastavit [protokoly Azure Monitor](../azure-monitor/platform/data-platform-logs.md) pro váš účet integrace. [Azure Monitor](../azure-monitor/overview.md) vám pomůže monitorovat cloudová a místní prostředí, takže můžete snadněji udržovat jejich dostupnost a výkon. Pomocí protokolů Azure Monitor můžete zaznamenávat a ukládat data o datech a událostech za běhu, jako jsou události aktivační události, události spuštění a události akcí v [pracovním prostoru Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). U zpráv protokolování také shromažďuje informace, jako jsou:

* Počet a stav zprávy
* Stav potvrzení
* Korelace mezi zprávami a potvrzeními
* Podrobné popisy chyb pro chyby

Azure Monitor umožňuje vytvářet [dotazy protokolu,](../azure-monitor/log-query/log-query-overview.md) které vám pomohou najít a zkontrolovat tyto informace. Tato diagnostická data můžete [taky použít s jinými službami Azure](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data), jako je Azure Storage a Azure Event Hubs.

Pokud chcete nastavit protokolování pro svůj účet integrace, nainstalujte na portál Azure [řešení Logic Apps B2B.](#install-b2b-solution) Toto řešení poskytuje souhrnné informace pro události zpráv B2B. Chcete-li povolit protokolování a vytváření dotazů na tyto informace, nastavte [protokoly Azure Monitor .](#set-up-resource-logs)

Tento článek ukazuje, jak povolit protokolování Azure Monitoru pro váš účet integrace.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Log Analytics. Pokud nemáte pracovní prostor Log Analytics, přečtěte si, [jak vytvořit pracovní prostor Analýzy protokolů](../azure-monitor/learn/quick-create-workspace.md).

* Aplikace logiky, která je nastavena pomocí protokolování Azure Monitora a odesílá tyto informace do pracovního prostoru Analýzy protokolů. Přečtěte [si, jak nastavit protokoly Azure Monitoru pro vaši aplikaci logiky](../logic-apps/monitor-logic-apps.md).

* Účet integrace, který je propojený s vaší aplikací logiky. Přečtěte [si, jak propojit účet integrace s aplikací logiky](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Instalace řešení B2B aplikace Logic Apps

Než budou protokoly Azure Monitoru sledovat zprávy B2B pro vaši aplikaci logiky, přidejte do pracovního prostoru Analýzy protokolů řešení **Logic Apps B2B.**

1. Do vyhledávacího pole [portálu](https://portal.azure.com) `log analytics workspaces`Azure Portal zadejte a vyberte **pracovní prostory Analýzy protokolů**.

   ![Vyberte možnost "Pracovní prostory analýzy protokolů"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. V části **Pracovní prostory Log Analytics**vyberte pracovní prostor.

   ![Vyberte pracovní prostor Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. V podokně Přehled vyberte v části **Začínáme s řešeními** > **monitorování Konfigurace protokolů**vyberte **Zobrazit řešení**.

   ![V podokně Přehled vyberte Možnost Zobrazit řešení.](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. V podokně Přehled vyberte **Přidat**.

   ![V podokně přehledu přidejte nové řešení](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Po otevření **Marketplace** zadejte do `logic apps b2b`vyhledávacího pole a vyberte Logic Apps **B2B**.

   ![Na marketplace vyberte "Správa logických aplikací"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. V podokně popis řešení vyberte **Vytvořit**.

   ![Výběrem možnosti "Vytvořit" přidáte řešení Logic Apps B2B.](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Zkontrolujte a potvrďte pracovní prostor Log Analytics, do kterého chcete nainstalovat řešení, a vyberte **Znovu vytvořit.**

   ![Vyberte "Vytvořit" pro "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Poté, co Azure nasadí řešení do skupiny prostředků Azure, která obsahuje váš pracovní prostor Analýzy protokolů, řešení se zobrazí v podokně souhrnu vašeho pracovního prostoru. Při zpracování zpráv B2B se aktualizuje počet zpráv v tomto podokně.

   ![Podokno souhrnu pracovního prostoru](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Nastavení protokolů Azure Monitoru

Protokolování Azure Monitoru můžete povolit přímo z vašeho účtu integrace.

1. Na [webu Azure Portal](https://portal.azure.com)najděte a vyberte svůj účet integrace.

   ![Vyhledání a výběr účtu integrace](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. V nabídce účtu integrace vyberte v části **Sledování** **položku Diagnostic settings**. Vyberte **Přidat diagnostické nastavení**.

   ![V části "Monitorování" vyberte "Nastavení diagnostiky"](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Chcete-li nastavení vytvořit, postupujte takto:

   1. Zadejte název nastavení.

   1. Vyberte **odeslat do analýzy protokolů**.

   1. V **části Předplatné**vyberte předplatné Azure, které je přidružené k vašemu pracovnímu prostoru Analýzy protokolů.

   1. V **části Pracovní prostor Analýzy protokolů**vyberte pracovní prostor, který chcete použít.

   1. V **části log**vyberte kategorii **IntegrationAccountTrackingEvents,** která určuje kategorii událostí, kterou chcete zaznamenat.

   1. Jakmile budete mít hotovo, vyberte **Uložit**.

   Například: 

   ![Nastavení protokolů Azure Monitor u shromažďovat diagnostická data](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Zobrazit stav zprávy

Po spuštění aplikace logiky můžete zobrazit stav a data o těchto zprávách v pracovním prostoru Log Analytics.

1. V vyhledávacím poli [portálu Azure](https://portal.azure.com) najděte a otevřete pracovní prostor Log Analytics.

1. V nabídce pracovního prostoru vyberte **souhrnná logika** > aplikace pracovního prostoru**B2B**.

   ![Podokno souhrnu pracovního prostoru](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Pokud se na dlaždici B2B aplikace logiky po spuštění okamžitě nezobrazí výsledky, zkuste vybrat **možnost Aktualizovat** nebo chvíli počkat, než to zkusíte znovu.

   Ve výchozím nastavení se na dlaždici **Logic Apps B2B** zobrazují data založená na jednom dni. Chcete-li změnit rozsah dat na jiný interval, vyberte ovládací prvek oboru v horní části stránky:

   ![Interval změny](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Po zobrazení řídicího panelu stavu zprávy můžete zobrazit další podrobnosti o konkrétním typu zprávy, který zobrazuje data založená na jednom dni. Vyberte dlaždici **as2**, **X12**nebo **EDIFACT**.

   ![Zobrazení stavů zpráv](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Pro vybranou dlaždici se zobrazí seznam zpráv. Například, tady je to, co seznam zpráv AS2 může vypadat takto:

   ![Stavy a podrobnosti pro zprávy AS2](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Další informace o vlastnostech jednotlivých typů zpráv najdete v těchto popisech vlastností zprávy:

   * [Vlastnosti zprávy AS2](#as2-message-properties)
   * [Vlastnosti zprávy X12](#x12-message-properties)
   * [Vlastnosti zprávy EDIFACT](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Popisy vlastností a formáty názvů pro zprávy AS2, X12 a EDIFACT

Pro každý typ zprávy jsou zde popisy vlastností a formáty názvů stažených souborů zpráv.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Popisy vlastností zprávy AS2

Zde jsou popisy vlastností pro každou zprávu AS2.

| Vlastnost | Popis |
|----------|-------------|
| **Odesílatel** | Partner hosta zadaný v **nastavení příjmu**nebo hostitelský partner zadaný v části **Nastavení odesílání** pro smlouvu AS2 |
| **Příjemce** | Hostitelský partner zadaný v **nastavení příjmu**nebo partner hosta zadaný v **nastavení odesílání** pro smlouvu AS2 |
| **Aplikace logiky** | Aplikace logiky, kde jsou nastaveny akce AS2 |
| **Stav** | Stav zprávy AS2 <br>Úspěch = Přijato nebo odesláno platné as2 zprávy. Není nastavena žádná mdn. <br>Úspěch = Přijato nebo odesláno platné as2 zprávy. MDN je nastavena a přijata nebo je odeslána mdn. <br>Nezdařilo se = Byla přijata neplatná zpráva AS2. Není nastavena žádná mdn. <br>Čekající = Přijata nebo odeslána platná zpráva AS2. MDN je nastavena a mdn se očekává. |
| **Ack** | Stav zprávy MDN <br>Přijato = Přijato nebo odesláno kladné MDN. <br>Čeká na vyřízení = Čekání na příjem nebo odeslání MDN. <br>Odmítnuto = Přijato nebo odesláno záporné Číslo MDN. <br>Není vyžadováno = MDN není ve smlouvě nastavena. |
| **Směru** | Směr zprávy AS2 |
| **ID sledování** | ID, které koreluje všechny aktivační události a akce v aplikaci logiky |
| **ID zprávy** | ID zprávy AS2 z hlavičky zprávy AS2 |
| **Časové razítko** | Čas, kdy akce AS2 zpracovala zprávu |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Popisy vlastností zprávy X12

Zde jsou popisy vlastností pro každou zprávu X12.

| Vlastnost | Popis |
|----------|-------------|
| **Odesílatel** | Partner hosta zadaný v **nastavení příjmu**nebo hostitelský partner zadaný v části **Nastavení odesílání** pro smlouvu X12. |
| **Příjemce** | Hostitelský partner zadaný v **nastavení příjmu**nebo partner hosta zadaný v **nastavení odesílání** pro smlouvu X12. |
| **Aplikace logiky** | Aplikace logiky, kde jsou nastaveny akce X12 |
| **Stav** | Stav zprávy X12 <br>Úspěch = Přijata nebo odeslána platná zpráva X12. Není nastaven žádný funkční ack. <br>Úspěch = Přijata nebo odeslána platná zpráva X12. Funkční potvrzení je nastaveno a přijato, nebo je odeslán funkční ack. <br>Nepodařilo se = Přijato nebo odesláno neplatné X12 zprávy. <br>Čeká na vyřízení = Přijato nebo odesláno platné X12 zprávy. Funkční ack je nastavena, a funkční ack se očekává. |
| **Ack** | Funkční stav Ack (997) <br>Přijato = Přijato nebo odesláno pozitivní funkční ack. <br>Odmítnuto = Přijato nebo odesláno negativní funkční potvrzení. <br>Čeká na vyřízení = Očekává funkční potvrzení, ale nebylo přijato. <br>Čeká na vyřízení = Vygeneroval funkční potvrzení, ale nelze odeslat partnerovi. <br>Není vyžadováno = Funkční potvrzení není nastaveno. |
| **Směru** | Směr zprávy X12 |
| **ID sledování** | ID, které koreluje všechny aktivační události a akce v aplikaci logiky |
| **Typ Msg** | Typ zprávy EDI X12 |
| **Icn** | Kontrolní číslo výměny pro zprávu X12 |
| **TSCN** | Kontrolní číslo sady transakcí pro zprávu X12 |
| **Časové razítko** | Doba, kdy akce X12 zpracovala zprávu |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Popisy vlastností zpráv EDIFACT

Zde jsou popisy vlastností pro každou zprávu EDIFACT.

| Vlastnost | Popis |
|----------|-------------|
| **Odesílatel** | Partner hosta zadaný v **nastavení příjmu**nebo hostitelský partner zadaný v části **Nastavení odesílání** pro smlouvu EDIFACT |
| **Příjemce** | Hostitelský partner zadaný v **nastavení příjmu**nebo partner hosta zadaný v části **Nastavení odesílání** pro smlouvu EDIFACT |
| **Aplikace logiky** | Aplikace logiky, kde jsou nastaveny akce EDIFACT |
| **Stav** | Stav zprávy EDIFACT <br>Úspěch = Přijato nebo odesláno platné zprávy EDIFACT. Není nastaven žádný funkční ack. <br>Úspěch = Přijato nebo odesláno platné zprávy EDIFACT. Funkční potvrzení je nastaveno a přijato, nebo je odeslán funkční ack. <br>Nepodařilo se = Přijato nebo odesláno neplatné zprávy EDIFACT <br>Čekající = Přijato nebo odesláno platné zprávy EDIFACT. Funkční ack je nastavena, a funkční ack se očekává. |
| **Ack** | Funkční stav ack (CONTRL) <br>Přijato = Přijato nebo odesláno pozitivní funkční ack. <br>Odmítnuto = Přijato nebo odesláno negativní funkční potvrzení. <br>Čeká na vyřízení = Očekává funkční potvrzení, ale nebylo přijato. <br>Čeká na vyřízení = Vygeneroval funkční potvrzení, ale nelze odeslat partnerovi. <br>Není vyžadováno = Funkční potvrzení není nastaveno. |
| **Směru** | Směr zprávy EDIFACT |
| **ID sledování** | ID, které koreluje všechny aktivační události a akce v aplikaci logiky |
| **Typ Msg** | Typ zprávy EDIFACT |
| **Icn** | Kontrolní číslo pro výměnu zprávy EDIFACT |
| **TSCN** | Kontrolní číslo sady transakcí pro zprávu EDIFACT |
| **Časové razítko** | Doba, kdy akce EDIFACT zpracovala zprávu |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Další kroky

* [Vytváření monitorovacích a sledovacích dotazů](../logic-apps/create-monitoring-tracking-queries.md)