---
title: Použití protokolů Azure Monitor s využitím Azure Logic Apps a automatizace
description: Naučte se, jak můžete pomocí Azure Logic Apps a automatizace rychle automatizovat opakované procesy pomocí konektoru Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: fdd3886dda794ff9a91e2c2be6a3d810086d0ed2
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526982"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>Konektor protokolů Azure Monitor pro Logic Apps a automatizace
[Azure Logic Apps](../../logic-apps/index.yml) a [Power](https://flow.microsoft.com) Automata umožňují vytvářet automatizované pracovní postupy s využitím stovek akcí pro nejrůznější služby. Konektor protokolů Azure Monitor umožňuje vytvářet pracovní postupy, které načítají data z pracovního prostoru Log Analytics nebo Application Insights aplikace v Azure Monitor. Tento článek popisuje akce, které konektor obsahuje, a poskytuje návod k sestavení pracovního postupu pomocí těchto dat.

Můžete například vytvořit aplikaci logiky, která bude používat data protokolu Azure Monitor v e-mailovém oznámení ze sady Office 365, vytvořit chybu v Azure DevOps nebo odeslat zprávu o časové rezervě.  Pracovní postup můžete aktivovat jednoduchým plánem nebo z nějaké akce v připojené službě, jako je například doručení e-mailu nebo. 

## <a name="connector-limits"></a>Omezení konektoru
Konektor Azure Monitorch protokolů má tato omezení:
* Maximální velikost dat: 16 MB
* Maximální velikost odpovědi na dotaz 100 MB
* Maximální počet záznamů: 500 000
* Maximální časový limit dotazu 110 sekunda.
* Vizualizace grafů mohou být k dispozici na stránce protokoly a v konektoru chybí, protože konektor a stránka protokolů nepoužívají stejné knihovny grafů v současnosti.

V závislosti na velikosti dat a použitém dotazu může konektor dosáhnout svého limitu a selhání. Tyto případy můžete obejít při úpravách opakování triggeru, aby se spouštěly častěji a aby se dotazoval méně dat. Dotazy, které agreguje vaše data, můžete použít k vrácení méně záznamů a sloupců.

## <a name="actions"></a>Akce
Následující tabulka popisuje akce, které jsou součástí konektoru protokolů Azure Monitor. Oba umožňují spustit dotaz protokolu pro Log Analytics pracovní prostor nebo Application Insights aplikaci. Rozdíl je v způsobu, jakým jsou vrácena data.

> [!NOTE]
> Konektor Azure Monitor logs nahrazuje konektor [azure Log Analytics](/connectors/azureloganalytics/) a [konektor Azure Application Insights](/connectors/applicationinsights/). Tento konektor nabízí stejné funkce jako ostatní a je upřednostňovanou metodou pro spuštění dotazu na Log Analytics pracovní prostor nebo aplikaci Application Insights.


| Akce | Popis |
|:---|:---|
| [Spustit dotaz a výsledky seznamu](/connectors/azuremonitorlogs/#run-query-and-list-results) | Vrátí každý řádek jako svůj vlastní objekt. Tuto akci použijte, pokud chcete s každým řádkem pracovat samostatně ve zbývající části pracovního postupu. Tato akce obvykle následuje za [každou aktivitou](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Spuštění dotazu a vizualizace výsledků](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Vrátí všechny řádky v sadě výsledků dotazu jako jeden formátovaný objekt. Tuto akci použijte, pokud chcete použít sadu výsledků ve zbývající části pracovního postupu, například odeslání výsledků do e-mailu.  |

## <a name="walkthroughs"></a>Postupy
Následující kurzy ilustrují použití Azure Monitorch konektorů v Azure Logic Apps. Stejný příklad můžete provést s automatickým automatizací a jediným rozdílem, jak vytvořit počáteční pracovní postup a spustit ho po dokončení. Konfigurace pracovního postupu a akcí je mezi oběma. Začněte tím, že si můžete začít v tématu [Vytvoření toku ze šablony v Power](/power-automate/get-started-logic-template) automatu.


### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

V Azure Portal přejděte na **Logic Apps** a klikněte na **Přidat**. Vyberte **předplatné**, **skupinu prostředků** a **oblast** , do které chcete uložit novou aplikaci logiky, a pak jí udělte jedinečný název. Můžete zapnout **Log Analytics** nastavení a shromažďovat informace o běhových datech a událostech, jak je popsáno v tématu [nastavení protokolů Azure monitor a shromažďování diagnostických dat pro Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md). Toto nastavení není vyžadováno pro použití konektoru Azure Monitorch protokolů.

![Vytvoření aplikace logiky](media/logicapp-flow-connector/create-logic-app.png)


Klikněte na tlačítko **zkontrolovat + vytvořit** a pak **vytvořit**. Po dokončení nasazení klikněte na **Přejít k prostředku** a otevřete **Návrháře Logic Apps**.

### <a name="create-a-trigger-for-the-logic-app"></a>Vytvoření triggeru pro aplikaci logiky
V části **začít se společným triggerem** vyberte **opakování**. Tím se vytvoří aplikace logiky, která se automaticky spustí v pravidelných intervalech. V poli **frekvence** akce vyberte **den** a v poli **interval** zadejte **1** , pokud chcete pracovní postup spustit jednou za den.

![Akce opakování](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Návod: vizuální výsledky v e-mailu
V následujícím kurzu se dozvíte, jak vytvořit aplikaci logiky, která odesílá výsledky Azure Monitorho dotazu protokolu e-mailem. 

### <a name="add-azure-monitor-logs-action"></a>Akce přidání protokolů Azure Monitor
Kliknutím na **+ Nový krok** přidáte akci, která se spustí po akci opakování. V části **Zvolte akci** zadejte **Azure monitor** a pak vyberte **Azure monitor protokoly**.

![Akce protokolů Azure Monitor](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Klikněte na **Azure Log Analytics – spusťte dotaz a vizualizujte výsledky**.

![Snímek obrazovky s novou akcí, která se přidává do kroku v návrháři aplikace logiky Protokoly Azure Monitor jsou v části zvolit akci zvýrazněné.](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Akce přidání protokolů Azure Monitor

Vyberte **předplatné** a **skupinu prostředků** pro pracovní prostor Log Analytics. Jako **typ prostředku** vyberte *Log Analytics pracovní prostor* a potom v části **název prostředku** vyberte název pracovního prostoru.

Do okna **dotazu** přidejte následující dotaz protokolu.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Jako **časový rozsah** a **tabulku HTML** pro **typ grafu** vyberte *nastavit v dotazu* .
   
![Snímek obrazovky s nastavením pro novou Azure Monitor protokoluje akci s názvem spustit dotaz a vizualizovat výsledky.](media/logicapp-flow-connector/run-query-visualize-action.png)

E-mail odešle účet přidružený k aktuálnímu připojení. Kliknutím na **změnit připojení** můžete zadat jiný účet.

### <a name="add-email-action"></a>Akce přidání e-mailu

Klikněte na **+ Nový krok** a potom klikněte na **+ přidat akci**. V části **Zvolte akci** zadejte **Outlook** a pak vyberte **Office 365 Outlook**.

![Vybrat konektor Outlooku](media/logicapp-flow-connector/select-outlook-connector.png)

Vyberte **Odeslat e-mail (v2)**.

![Okno pro výběr aplikace Office 365 Outlook](media/logicapp-flow-connector/select-mail-action.png)

Kliknutím kamkoli do pole **text** otevřete okno **dynamického obsahu** , ve kterém se otevře okno s hodnotami z předchozích akcí v aplikaci logiky. Vyberte **Zobrazit více** a potom **text** , který je výsledkem dotazu v akci Log Analytics.

![Vybrat tělo](media/logicapp-flow-connector/select-body.png)

Zadejte e-mailovou adresu příjemce v okně **Komu** a předmět e-mailu v **předmětu**. 

![Akce pošty](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Uložení a otestování aplikace logiky
Klikněte na **Uložit** a pak na **Spustit** , aby se spustil test aplikace logiky.

![Uložit a spustit](media/logicapp-flow-connector/save-run.png)


Po dokončení aplikace logiky Ověřte e-mailovou adresu příjemce, kterého jste zadali.  Měli byste obdržet e-mail s textem podobným následujícímu:

![Ukázkový e-mail](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [dotazech protokolu v Azure monitor](../log-query/log-query-overview.md).
- Další informace o [Logic Apps](../../logic-apps/index.yml)
- Přečtěte si další informace o [Power automatizuje](https://flow.microsoft.com).
