---
title: Řešení potíží s výstrahami protokolu v Azure Monitor | Microsoft Docs
description: Běžné problémy, chyby a řešení pro pravidla upozornění protokolů v Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668498"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Řešení potíží s výstrahami protokolu v Azure Monitor  

V tomto článku se dozvíte, jak vyřešit běžné problémy, které se mohou vyskytnout, když nastavujete výstrahy protokolu v Azure Monitor. Poskytuje také řešení pro běžné problémy s funkcemi nebo konfigurací výstrah protokolů.

Termín *výstrahy protokolu* popisují pravidla, která se aktivují na základě dotazu protokolu v [pracovním prostoru Azure Log Analytics](../learn/tutorial-viewdata.md) nebo v [Azure Application Insights](../../azure-monitor/app/analytics.md). Přečtěte si další informace o funkcích, terminologii a typech v [protokolových výstrahách v Azure monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Tento článek nebere v úvahu případy, kdy Azure Portal zobrazuje aktivované pravidlo výstrahy a přidružená skupina akcí neprovádí oznámení. V takových případech si přečtěte podrobnosti v tématu [Vytvoření a Správa skupin akcí v Azure Portal](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Výstraha protokolu se neaktivuje.

Zde jsou některé běžné důvody, proč stav pro nakonfigurované [pravidlo výstrahy protokolu v Azure monitor](../platform/alerts-log.md) není zobrazený [, když je očekáván ](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Doba přijímání dat pro protokoly

Výstraha protokolu pravidelně spouští dotaz na základě [Log Analytics](../learn/tutorial-viewdata.md) nebo [Application Insights](../../azure-monitor/app/analytics.md). Vzhledem k tomu, že Azure Monitor zpracovává spoustu terabajtů dat od různých zdrojů od různých uživatelů po celém světě, je tato služba náchylná k různým časovým zpožděním. Další informace najdete v tématu [Doba přijímání dat v protokolech Azure monitor](../platform/data-ingestion-time.md).

Chcete-li zmírnit prodlevy, systém počká a znovu pokusí dotaz výstrahy několikrát, pokud najde potřebná data, která se ještě ingestují. Systém má exponenciální zvýšení nastavené čekací doby. Výstraha protokolu se aktivuje až po tom, co jsou data k dispozici, takže zpoždění může být způsobeno pomalým příjmem dat protokolu.

### <a name="incorrect-time-period-configured"></a>Nesprávné nakonfigurované časové období

Jak je popsáno v článku [terminologie pro výstrahy protokolu](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), časová lhůta uvedená v konfiguraci určuje časový rozsah dotazu. Dotaz vrátí pouze záznamy, které byly vytvořeny v tomto rozsahu.

Časové období omezuje data načtená pro dotaz protokolu, aby zabránila zneužití, a v případě, že v dotazu protokolu využije libovolný časový příkaz (například **před**). Pokud je například časové období nastavené na 60 minut a dotaz se spustí na 1:15 ODP. pro dotaz protokolu se použijí jenom záznamy vytvořené mezi 12:15 PM a 1:15 PM. Pokud dotaz protokolu používá časový příkaz jako **Poslední (1d)** , dotaz stále používá pouze data mezi 12:15 pm a 1:15 PM, protože časové období je nastaveno na tento interval.

Ověřte, zda časové období v konfiguraci odpovídá vašemu dotazu. Pro příklad uvedený výše, pokud dotaz protokolu používá **před (1d)** se zelenou značkou, musí být časové období nastavené na 24 hodin nebo 1 440 minut (označeno červeně). Toto nastavení zajistí, že se dotaz spustí podle zamýšleného.

![Časové období](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Možnost potlačit výstrahy je nastavená.

Jak je popsáno v kroku 8 článku týkajícím se [Vytvoření pravidla upozornění protokolu v Azure Portal](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), výstrahy protokolu poskytují možnost **potlačit výstrahy** pro potlačení akcí aktivace a oznámení po nakonfigurované době. V důsledku toho se může stát, že se výstraha neaktivuje. Ve skutečnosti to vyvolalo, ale bylo potlačeno.  

![Potlačit výstrahy](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Pravidlo upozornění na měření metriky není správné.

*Výstrahy protokolu měření metrik* jsou podtypu výstrah protokolů, které mají zvláštní možnosti a omezenou syntaxi dotazů na výstrahy. Pravidlo pro výstrahu protokolu měření metriky vyžaduje, aby výstup dotazu byl časovou řadou metrik. To znamená, že výstupem je tabulka s jedinečnými časovými obdobími, která mají stejnou velikost spolu s odpovídajícími agregovanými hodnotami.

Můžete zvolit, aby se v tabulce společně **AggregatedValuey**další proměnné. Tyto proměnné lze použít k řazení tabulky.

Předpokládejme například, že pravidlo pro výstrahu protokolu měření metrik bylo nakonfigurováno jako:

- Dotaz na `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Časové období 6 hodin
- Prahová hodnota 50
- Logika výstrah pro tři po sobě jdoucí porušení
- **Agregovat** podle zvolených jako **$Table**

Protože příkaz obsahuje **Souhrn... a poskytuje** dvě proměnné (**časové razítko** a **$Table**), systém zvolí **$Table** pro **agregaci**na. Systém seřadí tabulku výsledků podle pole **$Table** , jak je znázorněno na následujícím snímku obrazovky. Pak se podívejte na více instancí **AggregatedValue** pro každý typ tabulky (jako **availabilityResults**), aby se zjistilo, jestli došlo k třem nebo více po sobě jdoucích porušení.

![Zpracování dotazu měření metrik s více hodnotami](media/alert-log-troubleshoot/LogMMQuery.png)

Vzhledem k tomu, že **agregace** je definována na **$Table**, data jsou seřazená podle **$Table** sloupce (označují se červeně). Potom seskupme a vyhledáme typy **agregačních** polí.

Například pro **$Table**se hodnoty pro **availabilityResults** považují za jeden vykreslení nebo entitu (označeno oranžovým). V tomto grafu nebo entitě vyhledává služba Alert tři po sobě jdoucí porušení (označeno zeleně). Tato porušení aktivují výstrahu pro hodnotu tabulky **availabilityResults**.

Podobně platí, že pokud se tři po sobě jdoucí porušení služby stane u jakékoli jiné hodnoty **$Table**, aktivuje se pro stejné věci další upozornění. Služba Alert Service automaticky seřadí hodnoty v jednom grafu nebo entitě (v oranžově) podle času.

Nyní předpokládejme, že pravidlo pro výstrahu protokolu měření metrik bylo upraveno a dotaz byl `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. Zbytek konfigurace zůstává stejný jako předtím, včetně logiky výstrah pro tři po sobě jdoucí porušení. Možnost **Aggregate** on v tomto případě je ve výchozím nastavení **časové razítko** . V dotazu pro shrnutí je uvedena pouze jedna hodnota. **.. podle** (to znamená **časové razítko**). Podobně jako v předchozím příkladu by byl výstup na konci spuštění, jak je znázorněno níže.

   ![Zpracování dotazu měření metrik s hodnotou v čísle](media/alert-log-troubleshoot/LogMMtimestamp.png)

Vzhledem k tomu, že **agregace** na je definována v **časovém razítku**, data jsou řazena ve sloupci **časového razítka** (označeno červeně). Pak budeme seskupovat podle **časového razítka**. Například hodnoty pro `2018-10-17T06:00:00Z` budou považovány za jeden vykreslení nebo entitu (označeno oranžovým). V tomto grafu hodnoty nebo entitě služba Alert nenajde žádná po sobě jdoucí porušení (protože každá hodnota **časového razítka** obsahuje pouze jednu položku). Výstraha se proto nikdy neaktivuje. V takovém případě musí uživatel buď:

- Přidejte fiktivní proměnnou nebo existující proměnnou (například **$Table**) pro správné řazení pomocí pole **agregace po** .
- Překonfigurujte pravidlo výstrahy tak, aby místo toho používalo logiku výstrahy na základě **celkového porušení** .

## <a name="log-alert-fired-unnecessarily"></a>Výstraha protokolu se vyvolala zbytečně.

Nakonfigurované [pravidlo upozornění protokolu v Azure monitor](../platform/alerts-log.md) může být neočekávaně aktivované při jeho zobrazení v [upozorněních Azure](../platform/alerts-managing-alert-states.md). Následující části popisují některé běžné důvody.

### <a name="alert-triggered-by-partial-data"></a>Výstraha aktivovaná částečnými daty

Log Analytics a Application Insights podléhají zpoždění a zpracování příjmu. Když spustíte dotaz na výstrahu protokolu, možná zjistíte, že nejsou k dispozici žádná data, nebo jsou k dispozici pouze data. Další informace najdete v tématu [čas příjmu dat protokolu v Azure monitor](../platform/data-ingestion-time.md).

V závislosti na tom, jak jste nakonfigurovali pravidlo výstrahy, může dojít k neúspěšnému napálení, pokud v době spuštění výstrahy nejsou v protokolech žádná data ani částečná data. V takových případech doporučujeme změnit dotaz nebo konfiguraci výstrahy.

Pokud třeba nakonfigurujete pravidlo upozornění protokolu, které se aktivuje, když je počet výsledků dotazu analýzy menší než 5, aktivuje se výstraha, když nejsou žádná data (žádný záznam) nebo částečné výsledky (jeden záznam). Ale po zpoždění přijímání dat může stejný dotaz s úplnými daty poskytnout výsledek 10 záznamů.

### <a name="alert-query-output-is-misunderstood"></a>Výstup dotazu výstrahy je nesrozumitelný.

V dotazu Analytics zadáte logiku pro výstrahy protokolu. Analytický dotaz může používat různé velké objemy dat a matematické funkce. Služba Alert spustí dotaz v intervalech zadaných za zadané časové období. Služba Alert provádí v dotazu v závislosti na typu výstrahy drobné změny. Tuto změnu můžete zobrazit v části **dotaz k provedení** na obrazovce **Konfigurovat logiku signálu** :

![Dotaz, který se má provést](media/alert-log-troubleshoot/LogAlertPreview.png)

V poli **dotaz, který se má spustit** , je služba Výstrahy protokolu spuštěná. Pokud chcete pochopit, co výstup dotazu výstrahy může být před vytvořením výstrahy, můžete spustit uvedený dotaz a časové rozpětí prostřednictvím [portálu Analytics](../log-query/portals.md) nebo [rozhraní API pro analýzu](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Výstraha protokolu byla zakázána.

V následujících částech jsou uvedeny některé důvody, proč Azure Monitor může [pravidlo výstrahy protokolu](../platform/alerts-log.md)zakázat.

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Prostředek, ve kterém se výstraha vytvořila, už neexistuje.

Pravidla upozornění protokolů vytvořená v Azure Monitor cílí na konkrétní prostředek, jako je pracovní prostor Azure Log Analytics, aplikace Azure Application Insights a prostředek Azure. Služba Výstrahy protokolu potom spustí analytický dotaz, který je k dispozici v pravidle pro zadaný cíl. Ale po vytvoření pravidla se uživatelé často dostanou k odstranění z Azure, nebo se přesunou do Azure – cíl pravidla výstrahy protokolu. Vzhledem k tomu, že cíl pravidla výstrahy již není platný, spuštění pravidla se nezdařilo.

V takových případech Azure Monitor zakáže upozornění protokolu a zaručí, že nebudete fakturovat zbytečně, pokud pravidlo nemůže běžet nepřetržitě pro dobu proměnlivosti (například týden). Můžete zjistit přesný čas, kdy Azure Monitor deaktivovat výstrahu protokolu pomocí [protokolu aktivit Azure](../../azure-resource-manager/management/view-activity-logs.md). V protokolu aktivit Azure se přidá událost, když Azure Monitor zakáže pravidlo upozornění protokolu.

Následující ukázková událost v protokolu aktivit Azure je určena pro pravidlo upozornění, které bylo zakázané kvůli nepřetržité chybě.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Dotaz použitý v upozornění protokolu není platný.

Každé pravidlo upozornění protokolu vytvořené v Azure Monitor jako součást konfigurace musí určovat dotaz Analytics, který služba Alert Service pravidelně spouští. Dotaz Analytics může mít správnou syntaxi v době vytváření nebo aktualizace pravidla. V některých případech ale v časovém intervalu může dotaz uvedený v pravidle protokolu výstrahy vyvíjet problémy se syntaxí a způsobit selhání spuštění pravidla. Mezi běžné důvody, proč dotaz Analytics, který je uvedený v pravidle výstrahy protokolu, může vyvíjet chyby:

- Dotaz je zapsán ke [spuštění v několika prostředcích](../log-query/cross-workspace-query.md). A jeden nebo více zadaných prostředků už neexistují.
- [Výstraha protokolu typu měření metriky](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) nakonfigurované má dotaz na výstrahu nedodržuje normu syntaxe.
- Pro analytickou platformu nedošlo k žádnému toku dat. [Provedení dotazu způsobí chybu](https://dev.loganalytics.io/documentation/Using-the-API/Errors) , protože pro zadaný dotaz nejsou k dispozici žádná data.
- Změny v [dotazovacím jazyce](https://docs.microsoft.com/azure/kusto/query/) obsahují revidovaný formát pro příkazy a funkce. Proto již není dotaz uvedený dříve v pravidle výstrahy platný.

[Azure Advisor](../../advisor/advisor-overview.md) vás upozorní na toto chování. Přidalo se doporučení pro konkrétní pravidlo upozornění protokolu na Azure Advisor, v kategorii vysoké dostupnosti se středním dopadem a s popisem "opravit pravidlo upozornění protokolu pro zajištění monitorování". Pokud dotaz na výstrahu v pravidle výstrahy protokolu není opravený, když Azure Advisor zadal doporučení po dobu sedmi dnů, Azure Monitor zakáže upozornění protokolu a zajistěte, aby se vám nefakturoval zbytečně, pokud se pravidlo nemůže nepřetržitě spouštět po dobu proměnlivosti ( například týden).

Můžete najít přesný čas, kdy Azure Monitor zakázat pravidlo výstrahy protokolu, a to hledáním události v [protokolu aktivit Azure](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si informace o [upozorněních protokolu v Azure](../platform/alerts-unified-log.md).
- Přečtěte si další informace o [Application Insights](../../azure-monitor/app/analytics.md).
- Přečtěte si další informace o [dotazech protokolu](../log-query/log-query-overview.md).
