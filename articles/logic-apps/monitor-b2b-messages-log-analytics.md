---
title: Monitorování zpráv B2B pomocí Azure Monitor
description: Řešení potíží se zprávami AS2, X12 a EDIFACT nastavením protokolů Azure Monitor a shromažďování diagnostických dat pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 765c15897bd5d435503d3bef07e76a93b148971c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596860"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Nastavení protokolů služby Azure Monitor a shromažďování diagnostických dat o zprávách B2B v Azure Logic Apps

Po nastavení komunikace B2B mezi obchodními partnery v účtu integrace mohou tito partneři vyměňovat zprávy pomocí protokolů, jako jsou AS2, X12 a EDIFACT. Pokud chcete ověřit, že tato komunikace funguje očekávaným způsobem, můžete nastavit [protokoly Azure monitor](../azure-monitor/logs/data-platform-logs.md) pro svůj účet pro integraci. [Azure monitor](../azure-monitor/overview.md) vám pomůže monitorovat cloudová a místní prostředí, abyste mohli snadněji udržovat jejich dostupnost a výkon. Pomocí protokolů Azure Monitor můžete zaznamenávat a ukládat data o běhových datech a událostech, jako jsou události triggeru, události spuštění a události akcí v [pracovním prostoru Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). V případě zpráv se v protokolování také shromažďují informace, jako například:

* Počet zpráv a stav
* Stav potvrzení
* Korelace mezi zprávami a potvrzeními
* Podrobné popisy chyb pro selhání

Azure Monitor vám umožní vytvořit [dotazy protokolu](../azure-monitor/logs/log-query-overview.md) , které vám pomohou najít a zkontrolovat tyto informace. [Tato diagnostická data můžete také použít s jinými službami Azure](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data), například Azure Storage a Azure Event Hubs.

Pokud chcete nastavit protokolování pro účet pro integraci, [nainstalujte Logic Apps B2B řešení](#install-b2b-solution) do Azure Portal. Toto řešení poskytuje agregované informace pro události zpráv B2B. Pokud pak chcete povolit protokolování a vytváření dotazů pro tyto informace, nastavte [protokoly Azure monitor](#set-up-resource-logs).

Tento článek popisuje, jak povolit protokolování Azure Monitor pro účet pro integraci.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Log Analytics. Pokud nemáte pracovní prostor Log Analytics, přečtěte si, [jak vytvořit pracovní prostor Log Analytics](../azure-monitor/logs/quick-create-workspace.md).

* Aplikace logiky, která je nastavená s Azure Monitor protokolování a odesílá tyto informace do pracovního prostoru Log Analytics. Přečtěte si, [jak nastavit protokoly Azure monitor pro vaši aplikaci logiky](../logic-apps/monitor-logic-apps.md).

* Účet pro integraci, který je propojený s vaší aplikací logiky. Přečtěte si, [Jak propojit účet pro integraci s vaší aplikací logiky](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Instalace řešení Logic Apps B2B

Předtím, než protokoly Azure Monitor můžou sledovat zprávy B2B pro vaši aplikaci logiky, přidejte řešení **Logic Apps B2B** do pracovního prostoru Log Analytics.

1. Do vyhledávacího pole [Azure Portal](https://portal.azure.com)zadejte `log analytics workspaces` a potom vyberte **Log Analytics pracovní prostory**.

   ![Vyberte Log Analytics pracovní prostory.](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. V části **Log Analytics pracovní prostory** vyberte svůj pracovní prostor.

   ![Vyberte svůj pracovní prostor Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. V podokně Přehled v části Začínáme **s Log Analytics**  >  **Konfigurace řešení monitorování** vyberte **Zobrazit řešení**.

   ![V podokně s přehledem vyberte možnost Zobrazit řešení.](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. V podokně Přehled vyberte **Přidat**.

   ![V podokně s přehledem přidejte nové řešení.](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Po otevření **Marketplace** do vyhledávacího pole zadejte `logic apps b2b` a vyberte **Logic Apps B2B**.

   ![Z Marketplace vyberte Logic Apps Management.](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. V podokně s popisem řešení vyberte **vytvořit**.

   ![Pokud chcete přidat Logic Apps B2B řešení, vyberte vytvořit.](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Zkontrolujte a potvrďte Log Analytics pracovní prostor, do kterého chcete řešení nainstalovat, a pak vyberte **vytvořit** znovu.

   ![Vyberte "vytvořit" pro "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Jakmile Azure nasadí řešení do skupiny prostředků Azure, která obsahuje váš pracovní prostor Log Analytics, toto řešení se zobrazí v podokně Souhrn v pracovním prostoru. Při zpracování zpráv B2B se počet zpráv v tomto podokně aktualizuje.

   ![Podokno souhrnu pracovního prostoru](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Nastavení protokolů Azure Monitor

Protokolování Azure Monitor můžete povolit přímo z účtu pro integraci.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte účet pro integraci.

   ![Vyhledejte a vyberte účet pro integraci.](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. V nabídce účtu pro integraci vyberte v části **monitorování** možnost **nastavení diagnostiky**. Vyberte **Přidat nastavení diagnostiky**.

   ![V části monitorování vyberte nastavení diagnostiky.](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Nastavení vytvoříte pomocí následujících kroků:

   1. Zadejte název nastavení.

   1. Vyberte **odeslat Log Analytics**.

   1. V poli **předplatné** vyberte předplatné Azure, které je přidružené k vašemu pracovnímu prostoru Log Analytics.

   1. V části **pracovní prostor Log Analytics** vyberte pracovní prostor, který chcete použít.

   1. V části **protokol** vyberte kategorii **IntegrationAccountTrackingEvents** , která určuje kategorii událostí, kterou chcete zaznamenat.

   1. Jakmile budete mít hotovo, vyberte **Uložit**.

   Například: 

   ![Nastavení protokolů Azure Monitor ke shromažďování diagnostických dat](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Zobrazit stav zprávy

Po spuštění aplikace logiky můžete zobrazit stav a data o těchto zprávách v pracovním prostoru Log Analytics.

1. Do vyhledávacího pole [Azure Portal](https://portal.azure.com) vyhledejte a otevřete Log Analytics pracovní prostor.

1. V nabídce pracovního prostoru vyberte možnost **Souhrn Logic Apps B2B pracovního prostoru**  >  .

   ![Podokno souhrnu pracovního prostoru](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Pokud Logic Apps B2B dlaždice po spuštění okamžitě nezobrazuje výsledky, zkuste si vybrat možnost **aktualizovat** nebo počkat na krátkou dobu, než to zkusí znovu.

   Ve výchozím nastavení se na dlaždici **Logic Apps B2B** zobrazuje data na základě jednoho dne. Chcete-li změnit rozsah dat na jiný interval, vyberte v horní části stránky ovládací prvek rozsah:

   ![Interval změny](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Po zobrazení řídicího panelu stavu zprávy můžete zobrazit další podrobnosti pro určitý typ zprávy, který zobrazuje data na základě jednoho dne. Vyberte dlaždici pro **AS2**, **X12** nebo **EDIFACT**.

   ![Zobrazení stavů zpráv](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Zobrazí se seznam zpráv pro vaši zvolenou dlaždici. Tady je příklad, jak může seznam zpráv AS2 vypadat takto:

   ![Stavy a podrobnosti pro zprávy AS2](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Další informace o vlastnostech každého typu zprávy najdete v popisech těchto vlastností zprávy:

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

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../azure-monitor/logs/log-query-overview.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Popisy vlastností a formáty názvů pro zprávy AS2, X12 a EDIFACT

V případě každého typu zprávy zde jsou popisy vlastností a formáty názvů pro stažené soubory zpráv.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Popis vlastnosti zprávy AS2

Tady jsou popisy vlastností každé zprávy AS2.

| Vlastnost | Popis |
|----------|-------------|
| **Odesílatel** | Partner hosta zadaný v **Nastavení příjmu** nebo v hostitelském partnerovi zadaném v **nastavení Odeslat** pro smlouvu AS2 |
| **Příjemce** | Hostitelský partner zadaný v **Nastavení příjmu** nebo partner hosta zadaný v **nastavení Odeslat** pro smlouvu AS2 |
| **Aplikace logiky** | Aplikace logiky, ve které se nastavují akce AS2 |
| **Stav** | Stav zprávy AS2 <br>Úspěch = přijata nebo odeslána platná zpráva AS2. Není nastavený žádný MDN. <br>Úspěch = přijata nebo odeslána platná zpráva AS2. MDN se nastavuje a přijímá nebo se posílá MDN. <br>Neúspěšné = byla přijata neplatná zpráva AS2. Není nastavený žádný MDN. <br>Pending = přijato nebo odeslána platná zpráva AS2. MDN je nastavené a očekává se MDN. |
| **ZTRACEN** | Stav zprávy MDN <br>Přijato = přijato nebo odesláno kladné MDN. <br>Čeká na vyřízení = čekání na přijetí nebo odeslání MDN. <br>Odmítnuto = přijato nebo odesláno záporné MDN. <br>Nepožadováno = MDN se v této smlouvě nenastavuje. |
| **Směr** | Směr zprávy AS2 |
| **ID sledování** | ID, které koreluje všechny triggery a akce v aplikaci logiky |
| **ID zprávy** | ID zprávy AS2 ze záhlaví zpráv AS2 |
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

### <a name="x12-message-property-descriptions"></a>Popis vlastnosti zprávy X12

Tady jsou popisy vlastností každé zprávy X12.

| Vlastnost | Popis |
|----------|-------------|
| **Odesílatel** | Partner hosta zadaný v **Nastavení příjmu** nebo v hostitelském partnerovi zadaném v **nastavení Odeslat** pro smlouvu X12 |
| **Příjemce** | Hostitelský partner zadaný v **Nastavení příjmu** nebo partner hosta zadaný v **nastavení Odeslat** pro smlouvu X12 |
| **Aplikace logiky** | Aplikace logiky, ve které se nastavují akce X12 |
| **Stav** | Stav zprávy X12 <br>Úspěch = přijata nebo odeslána platná zpráva X12. Není nastavené žádné funkční potvrzení. <br>Úspěch = přijata nebo odeslána platná zpráva X12. Je nastaveno a přijato funkční potvrzení, nebo je odesláno funkční potvrzení. <br>Nepodařilo se = přijmout nebo odeslat neplatnou zprávu X12. <br>Pending = přijato nebo odeslána platná zpráva X12. Je nastavené funkční potvrzení a očekává se funkční potvrzení. |
| **ZTRACEN** | Stav funkce ACK (997) <br>Přijato = přijato nebo odesláno kladné funkční potvrzení. <br>Odmítnuto = přijato nebo odesláno záporné funkční potvrzení. <br>Pending = očekává se funkční potvrzení, ale nepřijalo se. <br>Pending = vygenerovalo funkční potvrzení, ale nemůže být odesláno partnerovi. <br>Nepožadováno = funkční potvrzení není nastavené. |
| **Směr** | Směr zprávy X12 |
| **ID sledování** | ID, které koreluje všechny triggery a akce v aplikaci logiky |
| **Typ zprávy** | Typ zprávy X12 EDI |
| **ICN** | Řídicí číslo výměny pro zprávu X12 |
| **TSCN** | Kontrolní číslo sady transakcí pro zprávu X12 |
| **Časové razítko** | Čas, kdy akce X12 zpracovala zprávu |
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

### <a name="edifact-message-property-descriptions"></a>Popis vlastnosti zprávy EDIFACT

Tady jsou popisy vlastností každé zprávy EDIFACT.

| Vlastnost | Popis |
|----------|-------------|
| **Odesílatel** | Partner hosta zadaný v **Nastavení příjmu** nebo v hostitelském partnerovi zadaném v **nastavení Odeslat** pro smlouvu EDIFACT |
| **Příjemce** | Hostitelský partner zadaný v **Nastavení příjmu** nebo partner hosta zadaný v **nastavení Odeslat** pro smlouvu EDIFACT |
| **Aplikace logiky** | Aplikace logiky, ve které se nastavují akce EDIFACT |
| **Stav** | Stav zprávy EDIFACT <br>Úspěch = přijata nebo odeslána platná zpráva EDIFACT. Není nastavené žádné funkční potvrzení. <br>Úspěch = přijata nebo odeslána platná zpráva EDIFACT. Je nastaveno a přijato funkční potvrzení, nebo je odesláno funkční potvrzení. <br>Nepodařilo se = přijmout nebo odeslat neplatnou zprávu EDIFACT <br>Pending = přijato nebo odeslána platná zpráva EDIFACT. Je nastavené funkční potvrzení a očekává se funkční potvrzení. |
| **ZTRACEN** | Stav CONTRL (funkční ACK) <br>Přijato = přijato nebo odesláno kladné funkční potvrzení. <br>Odmítnuto = přijato nebo odesláno záporné funkční potvrzení. <br>Pending = očekává se funkční potvrzení, ale nepřijalo se. <br>Pending = vygenerovalo funkční potvrzení, ale nemůže být odesláno partnerovi. <br>Nepožadováno = funkční potvrzení není nastavené. |
| **Směr** | Směr zprávy EDIFACT |
| **ID sledování** | ID, které koreluje všechny triggery a akce v aplikaci logiky |
| **Typ zprávy** | Typ zprávy EDIFACT |
| **ICN** | Řídicí číslo výměny pro zprávu EDIFACT |
| **TSCN** | Kontrolní číslo sady transakcí pro zprávu EDIFACT |
| **Časové razítko** | Čas, kdy akce EDIFACT zpracovala zprávu |
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
