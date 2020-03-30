---
title: Použití protokolů monitorování Azure s aplikacemi Logiky Azure a mocenštými automaty
description: Zjistěte, jak pomocí aplikací Azure Logic Apps a Power Automate rychle automatizovat opakovatelné procesy pomocí konektoru Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480007"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>Konektor protokolů azure monitoru pro logické aplikace a tok
[Aplikace Logiky Azure](/azure/logic-apps/) a [power automaty](https://ms.flow.microsoft.com) umožňují vytvářet automatizované pracovní postupy pomocí stovek akcí pro různé služby. Konektor protokolů monitorování Azure umožňuje vytvářet pracovní postupy, které načítají data z pracovního prostoru Analýzy protokolů nebo z aplikace Application Insights v Azure Monitoru. Tento článek popisuje akce, které jsou součástí konektoru a poskytuje návod k vytvoření pracovního postupu pomocí těchto dat.

Můžete například vytvořit aplikaci logiky pro použití dat protokolu Azure Monitor v e-mailovém oznámení z Office 365, vytvořit chybu v Azure DevOps nebo zveřejnit zprávu Slack.  Pracovní postup můžete spustit jednoduchým plánem nebo nějakou akcí v připojené službě, například při přijetí pošty nebo tweetu. 

## <a name="actions"></a>Akce
Následující tabulka popisuje akce, které jsou součástí konektoru protokolů monitorování Azure. Obě umožňují spustit dotaz protokolu v pracovním prostoru Log Analytics nebo aplikace Application Insights. Rozdíl je ve způsobu, jakým jsou data vrácena.

> [!NOTE]
> Konektor protokolů monitorování Azure nahrazuje [konektor Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalytics/) a [konektor Azure Application Insights](https://docs.microsoft.com/connectors/applicationinsights/). Tento konektor poskytuje stejné funkce jako ostatní a je upřednostňovanou metodou pro spuštění dotazu v pracovním prostoru Log Analytics nebo v aplikaci Application Insights.


| Akce | Popis |
|:---|:---|
| [Spuštění dotazu a a seznam výsledků](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | Vrátí každý řádek jako svůj vlastní objekt. Tuto akci použijte, pokud chcete pracovat s každým řádkem samostatně ve zbytku pracovního postupu. Akce je obvykle následuje [Pro každou aktivitu](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Spuštění dotazu a vizualizace výsledků](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Vrátí všechny řádky v sadě výsledků jako jeden formátovaný objekt. Tuto akci použijte, pokud chcete použít sadu výsledků společně ve zbytku pracovního postupu, například odeslání výsledků poštou.  |

## <a name="walkthroughs"></a>Postupy
Následující kurzy ilustrují použití konektorů Azure Monitor u Azure Logic Apps. Můžete provést stejný příklad s Power Automate, jediný rozdíl je, jak vytvořit počáteční pracovní postup a spustit jej po dokončení. Konfigurace pracovního postupu a akcí je mezi oběma stejná. Další informace o tom, jak začít, [najdete v tématu Vytvoření toku ze šablony v Power Automate.](https://docs.microsoft.com/power-automate/get-started-logic-template)


### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Přejděte na webu **Logic Apps** na webu Azure portal a klikněte na **Přidat**. Vyberte **předplatné**, **skupinu prostředků**a **oblast** pro uložení nové aplikace logiky a pak jí přiřazujte jedinečný název. Můžete zapnout **nastavení Log Analytics** shromažďovat informace o datech a událostech za běhu, jak je popsáno v [nastavení protokolů Azure Monitor a shromažďovat diagnostická data pro Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md). Toto nastavení není nutné pro použití konektoru protokolů monitorování Azure.

![Vytvoření aplikace logiky](media/logicapp-flow-connector/create-logic-app.png)


Klikněte na **Zkontrolovat + Vytvořit** a potom **vytvořit**. Po dokončení nasazení kliknutím na **Přejít na prostředek** otevřete **Návrhář aplikací logiky**.

### <a name="create-a-trigger-for-the-logic-app"></a>Vytvoření aktivační události pro aplikaci logiky
V části **Začít s běžnou aktivační událostí**vyberte **Opakování**. Tím se vytvoří aplikace logiky, která se automaticky spustí v pravidelných intervalech. V poli **Frekvence** akce vyberte **Den** a v poli **Interval** zadejte **hodnotu 1,** chcete-li pracovní postup spustit jednou denně.

![Akce opakování](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Návod: Vizualizované výsledky pošty
Následující kurz ukazuje, jak vytvořit aplikaci logiky, která odešle výsledky dotazu protokolu Azure Monitor e-mailem. 

### <a name="add-azure-monitor-logs-action"></a>Přidat akci Protokoly monitorování Azure
Kliknutím na **+ Nový krok** přidáte akci, která se spustí po akci opakování. V **části Zvolit akci**zadejte Azure **Monitor** a pak vyberte **Protokoly monitorování Azure**.

![Akce Protokoly monitorování Azure](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Klikněte na **Azure Log Analytics – spusťte dotaz a vizualizovat výsledky**.

![Spuštění dotazu a vizualizace výsledků](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Přidat akci Protokoly monitorování Azure

Vyberte pracovní prostor **Odběr** a **Skupina prostředků** pro váš pracovní prostor Analýzy protokolů. Vyberte *pracovní prostor Analýzy protokolů* pro **typ prostředku** a potom vyberte název pracovního prostoru v části **Název prostředku**.

Přidejte následující dotaz protokolu do okna **Dotaz.**  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

V *dotazu* pro **časový rozsah** a tabulku **HTML** pro typ grafu vyberte **možnost**Nastavit .
   
![Spuštění dotazu a vizualizace výsledků](media/logicapp-flow-connector/run-query-visualize-action.png)

E-mail bude odeslán účtem přidruženým k aktuálnímu připojení. Jiný účet můžete zadat kliknutím na **změnit připojení**.

### <a name="add-email-action"></a>Přidat akci e-mailu

Klikněte na **+ Nový krok**a potom klikněte na + Přidat **akci**. V části **Zvolit akci**zadejte **Outlook** a vyberte **Office 365 Outlook**.

![Vybrat konektor Outlooku](media/logicapp-flow-connector/select-outlook-connector.png)

Vyberte **Odeslat e-mail (V2)**.

![Okno výběru Office 365 Outlooku](media/logicapp-flow-connector/select-mail-action.png)

Kliknutím na libovolné místo v poli **Tělo** otevřete okno **dynamický obsah** s hodnotami z předchozích akcí v aplikaci logiky. Vyberte **Zobrazit další** a potom **Body,** což jsou výsledky dotazu v akci Log Analytics.

![Vybrat tělo](media/logicapp-flow-connector/select-body.png)

Zadejte e-mailovou adresu příjemce v okně **Do** a předmět e-mailu v **předmětu**. 

![Akce pošty](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Uložení a testování aplikace logiky
Klikněte na **Uložit** a potom **spustit** provést testovací běh aplikace logiky.

![Uložit a spustit](media/logicapp-flow-connector/save-run.png)


Po dokončení aplikace logiky zkontrolujte poštu příjemce, který jste zadali.  Měli jste obdržet e-mail s tělem podobným následujícímu:

![Ukázkový e-mail](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Další kroky

- Další informace o [dotazech protokolu v Azure Monitoru](../log-query/log-query-overview.md).
- Další informace o [logic aplikacích](/azure/logic-apps/)
- Další informace o [Microsoft Flow](https://ms.flow.microsoft.com).

