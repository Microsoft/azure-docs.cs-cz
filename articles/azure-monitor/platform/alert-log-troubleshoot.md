---
title: Řešení potíží s využitím upozornění log ve službě Azure Monitor | Dokumentace Microsoftu
description: Běžné problémy, chyby a řešení pro pravidla upozornění protokolů v Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c7712fc2ce55a3d22995bb119a9ee485a064903
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683398"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Řešení potíží s využitím upozornění log ve službě Azure Monitor  

V tomto článku se dozvíte, jak řešit běžné potíže, které může dojít, pokud nastavujete upozornění protokolů ve službě Azure Monitor. Poskytuje také řešení běžných problémů s Konfigurace upozornění protokolů nebo funkce. 

Termín *upozornění protokolů* popisuje výstrahy, fire založené na dotazu protokolu v [pracovního prostoru Azure Log Analytics](../learn/tutorial-viewdata.md) nebo v [Azure Application Insights](../../azure-monitor/app/analytics.md). Další informace o funkci, terminologie a typy v [upozornění protokolů ve službě Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Tento článek nebere v úvahu případech, kdy na webu Azure portal zobrazuje aktivovat pravidlo upozornění a oznámení neprovádí se skupinou souvisejících akcí. Takové případy, najdete v podrobnostech v [vytvořit a spravovat skupiny akcí na webu Azure Portal](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Neměli aktivovat upozornění protokolu

Tady je několik běžných příčin, proč stav nakonfigurovaného [pravidel upozornění protokolů ve službě Azure Monitor](../platform/alerts-log.md) nezobrazí [jako *aktivuje* očekával](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Doba příjem dat protokolů

Upozornění protokolu pravidelně spouští dotaz na základě [Log Analytics](../learn/tutorial-viewdata.md) nebo [Application Insights](../../azure-monitor/app/analytics.md). Protože Azure Monitor zpracovává mnoho terabajtů dat z tisíce zákazníků z různých zdrojů po celém světě, tato služba je náchylný k různým zpoždění. Další informace najdete v tématu [doba příjem dat v protokolech Azure Monitor](../platform/data-ingestion-time.md).

Ke zmírnění zpoždění, systému vyčká a pokusí znovu výstraha dotazu více než jednou pokud zjistí, že zatím není přijatých potřebná data. Systém má exponenciálně rostoucím čekací doba nastavena. Až po data jsou k dispozici, takže zpoždění může být způsobeno pomalé příjem dat protokolu, se aktivuje upozornění protokolu. 

### <a name="incorrect-time-period-configured"></a>Nakonfigurované správné časové období

Jak je popsáno v článku na [terminologie pro výstrahy protokolu](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), určuje časové období, uvádí v konfiguraci časový rozsah dotazu. Dotaz vrátí pouze záznamy, které byly vytvořeny v rámci tohoto rozsahu. 

Časové období omezuje data načtena protokolu dotazu, aby se zabránilo zneužití a obchází jakýkoli příkaz čas (jako je **před**) používaných v dotazu protokolu. Například toto časové období je nastavený na 60 minut a spuštění dotazu v 13:15, se používají pouze záznamy vytvořené mezi 12:15 PM a 1:15 PM pro dotaz protokolu. Pokud dotaz protokolu používá čas příkaz podobný **před (1d)**, dotaz stále pouze používá data mezi 12:15 PM a 1:15 PM, protože toto časové období je nastavena na tento interval.

Zkontrolujte, že časové období v konfiguraci odpovídá vašemu dotazu. Například je uvedeno výše pokud používá dotaz protokolu **před (1d)** s zelené značky, je třeba nastavit časové období na 24 hodin nebo 1 440 minut (označená červenou barvou). Toto nastavení zajistí, že spuštění dotazu tak, jak má.

![Časové období](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Potlačit výstrahy nastavit možnost

Jak je popsáno v kroku 8 tohoto článku na [vytváření pravidel upozornění protokolů na webu Azure Portal](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), poskytují upozornění protokolů **potlačit výstrahy** možnost potlačit akce aktivuje a oznámení pro nakonfigurované Time. Díky tomu si možná myslíte, že nebyl vyvolat výstrahu. Ve skutečnosti aktivují, ale došlo k potlačení.  

![Potlačit výstrahy](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Pravidlo upozornění metriky měření je nesprávný

*Upozornění protokolů měření metriky* jsou podtypem typu upozornění protokolů, které mají speciální funkce a syntaxi s omezeným přístupem dotaz na upozornění. Pravidlo upozornění protokolu metriky měření vyžaduje, aby dotaz výstup bude metrik časové řady. To znamená výstupem je tabulka s distinct, stejně velké časová období společně s odpovídající agregované hodnoty. 

Můžete mít další proměnné v tabulce spolu s **AggregatedValue**. Tyto proměnné můžete použít k seřazení tabulky. 

Předpokládejme například, že pravidlo upozornění protokolu metriky měření bylo nakonfigurováno jako:

- Dotaz `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- časové období 6 hodin
- Prahová hodnota 50
- alert logic tří po sobě jdoucí porušení
- **Agregace na** vybrána jako **$table**

Vzhledem k tomu příkaz zahrnuje **shrnutí... podle** a poskytuje dvě proměnné (**časové razítko** a **$table**), systém zvolí **$table** pro **agregované po** . Systém řadí tabulku výsledků podle **$table** pole, jak je znázorněno na následujícím snímku obrazovky. Pak dohlíží na násobek **AggregatedValue** instance pro každý typ tabulky (jako je **availabilityResults**) Chcete-li zobrazit, pokud se třemi nebo více po sobě jdoucí porušení.

![Metriky měření provádění dotazu s více hodnotami](media/alert-log-troubleshoot/LogMMQuery.png)

Protože **agregované po** je definován na **$table**, data je seřazená podle **$table** sloupec (označená červenou barvou). Potom jsme skupině a hledat typy **agregované po** pole. 

Například pro **$table**, hodnoty **availabilityResults** se považuje za jednu vykreslení na entitu (označená zvýrazněných oranžovou barvou). V tuto zobrazovanou hodnotu na entitu služba upozornění kontroluje tří po sobě jdoucí porušení (označené zeleně). Aktivovat upozornění pro hodnota tabulky, porušení **availabilityResults**. 

Podobně pokud tří po sobě jdoucí porušení provede pro jakoukoli jinou hodnotu parametru **$table**, jiné upozornění se aktivuje pro stejnou věc. Služba upozornění automaticky seřadí hodnoty v jedné vykreslení na entitu (označená zvýrazněných oranžovou barvou) podle času.

Nyní předpokládejme, že byla změněna pravidla upozornění protokolu měření metriky a dotaz byl `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. Zbývající část konfigurace zůstala stejná jako před zahrnutím logika upozornění pro tři po sobě jdoucí porušení. **Agregované po** v tomto případě je možnost **časové razítko** ve výchozím nastavení. Zadat pouze jednu hodnotu v dotazu pro **shrnutí... podle** (to znamená **časové razítko**). Stejně jako předchozím příkladu se výstupu na konci spuštění by, jak je znázorněno v následujícím způsobem.

   ![Provádění dotazu metriky měření singulární hodnotou](media/alert-log-troubleshoot/LogMMtimestamp.png)

Protože **agregované po** je definován na **časové razítko**, data je seřazená podle **časové razítko** sloupec (označená červenou barvou). Potom jsme Seskupit podle **časové razítko**. Například hodnoty `2018-10-17T06:00:00Z` se považuje za jednu vykreslení na entitu (označená zvýrazněných oranžovou barvou). V tuto zobrazovanou hodnotu na entitu, služba upozornění najdete žádné po sobě jdoucí porušení (protože každý **časové razítko** hodnota obsahuje jenom jednu položku). Takže výstraha je neaktivní. V takovém případě musí uživatel buď:

- Přidejte fiktivní proměnnou nebo existující proměnné (jako je **$table**) správně řazení pomocí **agregované po** pole.
- Znovu nakonfigurujte pravidlo upozornění na použití logika upozornění na základě **celkový počet porušení** místo.

## <a name="log-alert-fired-unnecessarily"></a>Zbytečně aktivováno upozornění protokolu

Nakonfigurovaného [pravidel upozornění protokolů ve službě Azure Monitor](../platform/alerts-log.md) může aktivovat neočekávaně při zobrazení v [Azure Alerts](../platform/alerts-managing-alert-states.md). Následující části popisují některé z důvodů.

### <a name="alert-triggered-by-partial-data"></a>Výstraha se aktivuje částečná data

Log Analytics a Application Insights se vztahují zpoždění ingestování a zpracování. Když spustíte dotaz na upozornění protokolu, je možné, že je k dispozici žádná data, nebo jenom některá data jsou k dispozici. Další informace najdete v tématu [protokolu Doba příjmu dat ve službě Azure Monitor](../platform/data-ingestion-time.md).

V závislosti na konfiguraci pravidla upozornění misfiring může dojít, pokud je v době spuštění výstrahy žádná data nebo částečná data v protokolech. V takovém případě doporučujeme vám změnit dotaz na upozornění nebo konfigurace. 

Například pokud nakonfigurujete pravidlo upozornění protokolu aktivuje, když počet výsledků z dotazu analytics je menší než 5, je výstraha, pokud neexistuje žádná data (žádný záznam) nebo částečné výsledky (jeden záznam). Ale po nastavené prodlevě příjmu dat, může poskytnout stejný dotaz s úplnou výsledek 10 záznamů.

### <a name="alert-query-output-is-misunderstood"></a>Dotaz na upozornění výstupu se nesprávně pochopeny

Poskytuje logiku pro výstrahy protokolu v dotazu analytics. Analytický dotaz můžete použít různé velké objemy dat a matematických funkcí. Služba upozornění spouští dotaz v zadaných s daty za zadané časové období. Služba upozornění provede drobné změny dotaz založený na typu výstrahy. Můžete zobrazit tato změna **dotaz, který se spustí** části na **konfigurovat logiku signálů** obrazovky:

![Provedení dotazu](media/alert-log-troubleshoot/LogAlertPreview.png)

**Dotaz, který se spustí** pole je, cokoli běží služba upozornění protokolů. Pokud chcete pochopit, co výstraha dotazu výstup, může být předtím, než vytvoříte upozornění, můžete spustit stanovených dotazu a časový interval prostřednictvím [portál Analytics](../log-query/portals.md) nebo [rozhraní API pro analýzu](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Upozornění protokolu byla zakázána.

Následující části uvádějí některé důvody, proč může být Azure Monitor zakažte [pravidel upozornění protokolů](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Prostředek, ve kterém byla výstraha vytvořena už existuje

Pravidla upozornění protokolů vytvořená ve službě Azure Monitor cílit na konkrétní prostředek, jako je pracovní prostor služby Azure Log Analytics, Azure Application Insights aplikaci a prostředek Azure. Služba upozornění protokolu pak spustí dotazu analytics zadaná v pravidle pro zadaný cíl. Ale po vytvoření pravidla, uživatelé často znamená přechod na z Azure – odstranit nebo přesunout v Azure – cíl pravidla upozornění protokolů. Vzhledem k tomu, že již není platný cíl pravidla upozornění, pravidlo se nezdaří.

V takových případech Azure Monitor zakáže upozornění protokolu a zajistí, že vám nebudeme nic účtovat zbytečně při pravidlo nelze spustit průběžně dobu proměnlivou velikostí (např. týdně). Můžete zjistit přesný čas, když Azure Monitor zakázáno upozornění protokolu prostřednictvím [protokolu aktivit Azure](../../azure-resource-manager/resource-group-audit.md). V protokolu aktivit Azure přidá se při monitorování Azure zakáže pravidlo upozornění protokolu události.

Následující ukázkové události v protokolu aktivit Azure je pro pravidlo výstrahy bylo zakázáno z důvodu selhání neustále.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Dotaz použitý v upozornění protokolu není platná

Každé pravidlo upozornění protokolu vytvoří ve službě Azure Monitor jako součást konfigurace musíte zadat dotazu analytics, která bude pravidelně spouštět služba upozornění. Analytický dotaz může mít správnou syntaxi v době vytvoření pravidla nebo aktualizace. Ale v některých případech po určitou dobu dotazu podle pravidel upozornění protokolů můžete vyvíjet problémů a způsobit, že je spuštění pravidla selhání. Některé běžné důvody, proč dotazu analytics podle pravidel upozornění protokolů můžete vyvíjet chyby jsou:

- Dotaz je zapsán do [spouštět napříč několika prostředcích](../log-query/cross-workspace-query.md). A jeden nebo více zadané prostředky už neexistuje.
- Žádný tok dat pro analytické platformy došlo. [Provádění dotazu vrátí chybu](https://dev.loganalytics.io/documentation/Using-the-API/Errors) vzhledem k tomu, že neexistuje žádná data pro zadaný dotaz.
- Změny v [dotazovací jazyk](https://docs.microsoft.com/azure/kusto/query/) obsahovat upravená formát pro příkazy a funkce. Proto dotaz dříve součástí pravidla upozornění už nejsou platné.

[Azure Advisor](../../advisor/advisor-overview.md) upozorňuje na toto chování. Přidá se doporučení pro konkrétní pravidlo upozornění v Azure Advisoru v rámci kategorie vysokou dostupnost s možnostmi střední dopad a popis "Opravy vašich pravidel upozornění protokolů k zajištění monitorování." Pokud výstrahy dotazu v pravidel upozornění protokolů není napravit po Azure Advisor poskytuje doporučení pro sedm dní, bude Azure Monitor zakažte upozornění protokolu a ujistěte se, že vám nebudeme nic účtovat zbytečně Pokud pravidlo nelze neustále spuštěný proměnlivou velikostí období ( například týdně).

Můžete vyhledat přesný čas při monitorování Azure zakázáno pravidel upozornění protokolů tím, že hledají událost v [protokolu aktivit Azure](../../azure-resource-manager/resource-group-audit.md).

## <a name="next-steps"></a>Další postup

- Další informace o [upozornění protokolů ve službě Azure](../platform/alerts-unified-log.md).
- Další informace o [Application Insights](../../azure-monitor/app/analytics.md).
- Další informace o [protokolu dotazy](../log-query/log-query-overview.md).
