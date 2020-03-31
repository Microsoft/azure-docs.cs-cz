---
title: Poradce při potížích s výstrahami protokolu v Azure Monitoru | Dokumenty společnosti Microsoft
description: Běžné problémy, chyby a řešení pravidel upozornění protokolu v Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249032"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Poradce při potížích s výstrahami protokolu v Azure Monitoru  

Tento článek ukazuje, jak vyřešit běžné problémy, které se mohou stát při nastavování výstrah protokolu v Azure Monitoru. Poskytuje také řešení běžných problémů s funkcemi nebo konfigurací výstrah protokolu.

*Výstrahy protokolu termínů* popisují pravidla, která se spalují na základě dotazu protokolu v [pracovním prostoru Azure Log Analytics](../learn/tutorial-viewdata.md) nebo v Azure Application [Insights](../../azure-monitor/app/analytics.md). Další informace o funkcích, terminologii a typech ve [výstrahách protokolů v Azure Monitoru](../platform/alerts-unified-log.md).

> [!NOTE]
> Tento článek nebere v úvahu případy, kdy portál Azure zobrazuje pravidlo výstrahy aktivované a oznámení není provedeno přidružené skupiny akcí. V takových případech najdete podrobnosti v tématu [Vytvoření a správa skupin akcí na webu Azure Portal](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Výstraha protokolu se nevystřelila

Tady jsou některé běžné důvody, proč se stav nakonfigurovaného [pravidla výstrahy protokolu v Azure Monitoru](../platform/alerts-log.md) nezobrazuje [jako *aktivováno,* když se očekává](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Doba požití dat pro protokoly

Výstraha protokolu pravidelně spouští dotaz na základě [analýzy protokolů](../learn/tutorial-viewdata.md) nebo [přehledů aplikací](../../azure-monitor/app/analytics.md). Vzhledem k tomu, že Azure Monitor zpracovává mnoho terabajtů dat od tisíců zákazníků z různých zdrojů po celém světě, je služba náchylná k různým časovým zpožděním. Další informace najdete [v tématu doba přihlašování dat v protokolech Azure Monitor .](../platform/data-ingestion-time.md)

Chcete-li zmírnit zpoždění, systém čeká a opakuje dotaz výstrahy vícekrát, pokud zjistí, že potřebná data ještě není ingestována. Systém má exponenciálně rostoucí čekací doby nastavit. Výstraha protokolu se aktivuje až po data je k dispozici, takže zpoždění může být způsobeno pomalé přihlašování dat protokolu.

### <a name="incorrect-time-period-configured"></a>Nakonfigurováno nesprávné časové období

Jak je popsáno v článku o [terminologii pro výstrahy protokolu](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), časové období uvedené v konfiguraci určuje časový rozsah pro dotaz. Dotaz vrátí pouze záznamy, které byly vytvořeny v rámci této oblasti.

Časové období omezuje data načtená pro dotaz protokolu, aby se zabránilo zneužití, a obchází jakýkoli příkaz time (jako **před)** použitý v dotazu protokolu. Například Pokud je časové období nastaveno na 60 minut a dotaz je spuštěn v 1:15 PM, pro dotaz protokolu se používají pouze záznamy vytvořené mezi 12:15 a 1:15 PM. Pokud dotaz protokolu používá příkaz time jako **před (1d),** dotaz stále používá pouze data mezi 12:15 pm a 1:15 PM, protože časové období je nastaveno na tento interval.

Zkontrolujte, zda časové období v konfiguraci odpovídá vašemu dotazu. Pro příklad zobrazený dříve, pokud dotaz protokolu používá **před (1d)** se zelenou značkou, časové období by měla být nastavena na 24 hodin nebo 1 440 minut (označeny červeně). Toto nastavení zajišťuje, že dotaz běží podle určení.

![Časové období](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Je nastavena možnost Potlačit výstrahy.

Jak je popsáno v kroku 8 článku o [vytvoření pravidla výstrahprotokolu](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)na webu Azure Portal , výstrahy protokolu poskytují **možnost Potlačit výstrahy** potlačit aktivační události a akce oznámení na konfigurované množství času. V důsledku toho si můžete myslet, že výstraha nevystřelila. Ve skutečnosti to střílelo, ale bylo potlačeno.  

![Potlačit výstrahy](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Pravidlo upozornění na měření metriky je nesprávné

*Upozornění protokolu metriky měření* jsou podtypem výstrah protokolu, které mají speciální možnosti a syntaxi dotazu s omezeným přístupem. Pravidlo pro upozornění protokolu měření metriky vyžaduje, aby výstup dotazu byl časovou řadou metriky. To znamená, že výstup je tabulka s odlišnými, stejně velkými časovými obdobími spolu s odpovídajícími agregovanými hodnotami.

Můžete zvolit další proměnné v tabulce vedle **AggregatedValue**. Tyto proměnné lze použít k řazení tabulky.

Předpokládejme například, že pravidlo pro výstrahu protokolu měření metriky bylo nakonfigurováno jako:

- Dotaz na`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Časové období 6 hodin
- Práh 50
- Logika výstrah y tří po sobě jdoucích porušení
- **Agregovat Na** zvoleném jako **$table**

Vzhledem k tomu, že příkaz obsahuje **shrnout ... a** poskytuje dvě proměnné (**časové razítko** a **$table**), systém zvolí **$table** pro **Aggregate Upon**. Systém seřadí tabulku výsledků podle **pole $table,** jak je znázorněno na následujícím snímku obrazovky. Pak se podívá na více **AggregatedValue** instance pro každý typ tabulky (jako **availabilityResults)** chcete-li zjistit, zda byly tři nebo více po sobě jdoucích porušení.

![Provádění dotazu na měření metriky s více hodnotami](media/alert-log-troubleshoot/LogMMQuery.png)

Vzhledem k **tomu, že agregace po** je definována na **$table**, jsou data seřazena podle **sloupce $table** (označeného červeně). Pak seskupíme a podíváme se na typy pole **Agregace.**

Například pro **$table**budou hodnoty pro **availabilityResults** považovány za jeden obrázek/entitu (označeno oranžově). V tomto obrázku/entitě hodnoty služba výstrahkontroluje tři po sobě jdoucí porušení (označená zeleně). Porušení spustit výstrahu pro dostupnost hodnoty **tabulkyResults**.

Podobně pokud dojde k třem po sobě jdoucím porušením pro jakoukoli jinou hodnotu **$table**, spustí se další upozornění pro stejnou věc. Služba výstrahy automaticky seřadí hodnoty v jednom grafu nebo entitě (označené oranžově) podle času.

Nyní předpokládejme, že pravidlo pro výstrahu protokolu `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`měření metriky bylo změněno a dotaz byl . Zbytek konfigurace zůstal stejný jako předtím, včetně logiky výstrahy pro tři po sobě jdoucí porušení. Možnost **Agregovat v** tomto případě je ve výchozím nastavení **časové razítko.** V dotazu pro souhrn je uvedena pouze jedna hodnota **... (to** znamená **časové razítko).** Stejně jako v předchozím příkladu výstup na konci spuštění by být znázorněno takto.

   ![Provádění dotazu na měření metriky s jedinečnou hodnotou](media/alert-log-troubleshoot/LogMMtimestamp.png)

Vzhledem k **tomu, že agregační po** je definována na **časové razítko**, data jsou seřazeny na sloupec **časové razítko** (označeny červeně). Pak jsme seskupit podle **časového razítka**. Například hodnoty `2018-10-17T06:00:00Z` pro budou považovány za jeden obrázek/entitu (označeny oranžově). V této hodnotě plot/entity služba výstrahy nenajde žádné po sobě jdoucí porušení (protože každá hodnota **časového razítka** má pouze jednu položku). Takže výstraha není nikdy spuštěna. V takovém případě musí uživatel buď:

- Přidejte fiktivní proměnnou nebo existující proměnnou (například **$table),** chcete-li správně řadit pomocí pole **Agregovat po.**
- Překonfigurujte pravidlo výstrahy tak, aby místo toho používalo logiku výstrah na základě **úplného porušení zabezpečení.**

## <a name="log-alert-fired-unnecessarily"></a>Výstraha protokolu, která byla aktivována zbytečně

Nakonfigurované [pravidlo výstrahprotokolu v Azure Monitoru](../platform/alerts-log.md) se může neočekávaně aktivovat při jeho zobrazení v [Azure Alerts](../platform/alerts-managing-alert-states.md). Následující části popisují některé běžné důvody.

### <a name="alert-triggered-by-partial-data"></a>Výstraha spuštěná částečnými daty

Log Analytics a Application Insights podléhají zpoždění majetek a zpracování. Při spuštění dotazu upozornění protokolu můžete zjistit, že nejsou k dispozici žádná data nebo jsou k dispozici pouze některá data. Další informace najdete [v tématu Protokolování doby přihlašování dat v Azure Monitoru](../platform/data-ingestion-time.md).

V závislosti na tom, jak jste nakonfigurovali pravidlo výstrahy, může dojít k chybnému spuštění, pokud v době spuštění výstrahy nejsou v protokolech žádná data nebo částečná data. V takových případech doporučujeme změnit výstražný dotaz nebo konfiguraci.

Pokud například nakonfigurujete pravidlo výstrahprotokolu tak, aby se aktivovalo, když je počet výsledků analytického dotazu menší než 5, výstraha se aktivuje, když nejsou k dispozici žádná data (nulový záznam) nebo částečné výsledky (jeden záznam). Ale po zpoždění požití dat, stejný dotaz s úplnými daty může poskytnout výsledek 10 záznamů.

### <a name="alert-query-output-is-misunderstood"></a>Výstup výstražného dotazu je nepochopen.

Zadáte logiku pro výstrahy protokolu v analytickém dotazu. Analytický dotaz může používat různé velké objemy dat a matematické funkce. Služba výstrah spustí dotaz v intervalech určených daty za zadané časové období. Služba výstrahy provede drobné změny v dotazu na základě typu výstrahy. Tuto změnu můžete zobrazit v části **Dotaz, který má být proveden,** na obrazovce **Logika signálu Konfigurace:**

![Dotaz, který má být proveden](media/alert-log-troubleshoot/LogAlertPreview.png)

**Pole Dotaz, který má být proveden,** je to, co spustí služba výstrahprotokolu. Pokud chcete pochopit, jaký může být výstup dotazu na výstrahu před vytvořením [výstrahy,](https://docs.microsoft.com/rest/api/loganalytics/)můžete uvedený dotaz a časový rozsah spustit prostřednictvím [portálu Analytics](../log-query/portals.md) nebo rozhraní API Analytics .

## <a name="log-alert-was-disabled"></a>Výstraha protokolu byla zakázána.

V následujících částech jsou uvedeny některé důvody, proč azure monitor může zakázat [pravidlo výstrahprotokolu](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Zdroj, ve kterém byla výstraha vytvořena, již neexistuje.

Protokolovat pravidla výstrah vytvořených v Azure Monitoru cílí na konkrétní prostředek, jako je pracovní prostor Azure Log Analytics, aplikace Azure Application Insights a prostředek Azure. Služba výstrahprotokolu pak spustí analytický dotaz uvedený v pravidle pro zadaný cíl. Ale po vytvoření pravidla uživatelé často přejít k odstranění z Azure – nebo přesunout uvnitř Azure -- cíl pravidla výstrahy protokolu. Vzhledem k tomu, že cíl pravidla výstrahy již není platný, provádění pravidla se nezdaří.

V takových případech Azure Monitor zakáže výstrahu protokolu a zajistí, že se vám zbytečně neúčtují, když pravidlo nemůže běžet nepřetržitě po značnou dobu (například týden). Můžete zjistit přesný čas, kdy Azure Monitor zakázal výstrahu protokolu prostřednictvím [protokolu aktivit Azure](../../azure-resource-manager/management/view-activity-logs.md). V protokolu aktivit Azure se přidá událost, když Azure Monitor zakáže pravidlo výstrahprotokolu.

Následující ukázková událost v protokolu aktivit Azure je pro pravidlo výstrahy, která byla zakázána z důvodu neustálého selhání.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Dotaz použitý ve výstraze protokolu není platný.

Každé pravidlo výstrahprotokolu vytvořené v Azure Monitoru jako součást jeho konfigurace musí určit analytický dotaz, který bude služba výstrah y pravidelně spouštět. Analytický dotaz může mít správnou syntaxi v době vytvoření nebo aktualizace pravidla. Ale někdy v průběhu času dotaz udaného v pravidle výstrahy protokolu může vyvinout problémy se syntaxí a způsobit selhání spuštění pravidla. Některé běžné důvody, proč analytický dotaz uvedený v pravidle výstrahy protokolu může vyvíjet chyby, jsou:

- Dotaz je zapsán [ke spuštění napříč více prostředky](../log-query/cross-workspace-query.md). A jeden nebo více zadaných prostředků již neexistuje.
- [Metrika měření typ protokolu upozornění](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) nakonfigurován o výstražný dotaz není v souladu s normami syntaxe
- Na analytickou platformu nebyl žádný tok dat. [Spuštění dotazu poskytuje chybu,](https://dev.loganalytics.io/documentation/Using-the-API/Errors) protože pro zadaný dotaz neexistují žádná data.
- Změny v [jazyce dotazů](https://docs.microsoft.com/azure/kusto/query/) zahrnují revidovaný formát příkazů a funkcí. Dotaz poskytnutý dříve v pravidle výstrahy tedy již není platný.

[Azure Advisor](../../advisor/advisor-overview.md) vás před tímto chováním upozorní. Přidá se doporučení pro konkrétní pravidlo upozornění protokolu na Azure Advisor, v rámci kategorie vysoká dostupnost se středním dopadem a popis "Opravit pravidlo upozornění protokolu k zajištění monitorování." Pokud se dotaz na výstrahu výstrahy v protokolu nenapraví poté, co Azure Advisor poskytl doporučení po dobu sedmi dnů, Azure Monitor zakáže výstrahu protokolu a zajistí, že se vám nebude účtovat zbytečně, když pravidlo nemůže běžet nepřetržitě po značnou dobu ( jako týden).

Přesný čas, kdy Azure Monitor zakázal pravidlo výstrahprotokolu, můžete najít tak, že vyhledáte událost v [protokolu aktivit Azure](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Další kroky

- Další informace o [výstrahách protokolů v Azure](../platform/alerts-unified-log.md).
- Další informace o [application insights](../../azure-monitor/app/analytics.md).
- Další informace o [dotazech protokolu](../log-query/log-query-overview.md).
