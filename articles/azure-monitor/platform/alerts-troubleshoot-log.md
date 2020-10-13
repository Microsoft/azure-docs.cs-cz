---
title: Řešení potíží s výstrahami protokolu v Azure Monitor | Microsoft Docs
description: Běžné problémy, chyby a řešení pro pravidla upozornění protokolů v Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: ec2ffe71a32781a855da258f3621738f1a5f6be4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294287"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Řešení potíží s výstrahami protokolu v Azure Monitor  

V tomto článku se dozvíte, jak vyřešit běžné problémy s výstrahami protokolu v Azure Monitor. Poskytuje také řešení pro běžné problémy s funkcemi a konfigurací výstrah protokolů.

Výstrahy protokolu umožňují uživatelům pomocí [log Analyticsho](../log-query/get-started-portal.md) dotazu vyhodnotit protokoly prostředků každou nastavenou frekvencí a vyvolat výstrahu na základě výsledků. Pravidla mohou aktivovat jednu nebo více akcí pomocí [skupin akcí](./action-groups.md). [Přečtěte si další informace o funkcích a terminologii výstrah protokolu](alerts-unified-log.md).

> [!NOTE]
> Tento článek nebere v úvahu případy, kdy Azure Portal zobrazuje aktivované pravidlo výstrahy a přidružená skupina akcí neprovádí oznámení. V takových případech si přečtěte podrobnosti [o řešení potíží](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

## <a name="log-alert-didnt-fire"></a>Výstraha protokolu se neaktivuje.

### <a name="data-ingestion-time-for-logs"></a>Doba přijímání dat pro protokoly

Azure Monitor zpracovává terabajty protokolů zákazníků z celého světa, což může způsobit [latenci přijímání protokolů](./data-ingestion-time.md).

Protokoly jsou částečně strukturovaná data a jsou v podstatě více latentních, než metriky. Pokud máte prodlevu více než 4 minuty v aktivovaném upozorněních, měli byste zvážit použití [Upozornění na metriky](alerts-metric-overview.md). Data můžete do úložiště metrik odesílat z protokolů pomocí [výstrah metrik pro protokoly](alerts-metric-logs.md).

Systém zopakuje vyhodnocení výstrahy několikrát, aby se zmírnila latence. Jakmile data dorazí, výstraha se aktivuje, což ve většině případů se neshoduje s časem záznamu protokolu.

### <a name="incorrect-query-time-range-configured"></a>Byl nakonfigurován nesprávný časový rozsah dotazu.

V definici podmínky pravidla je nastavený časový rozsah dotazu. Toto pole se nazývá **perioda** pro pracovní prostory a Application Insights a pro všechny ostatní typy prostředků se říká **časový rozsah pro přepis dotazů** . Podobně jako ve službě Log Analytics omezuje časový rozsah data dotazu na zadané období. I v případě, že se v dotazu **používá příkaz,** bude platit časový rozsah. 

Například dotaz vyhledává 60 minut, pokud je časový rozsah 60 minut, a to i v případě, že text obsahuje hodnotu **před (1d)**. Časový rozsah a filtrování času dotazu se musí shodovat. V ukázkovém případě bude změna **Period**  /  **rozsahu času dotazu přepisu** období na jeden den fungovat podle očekávání.

![Časové období](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>Akce jsou ztlumené v pravidle výstrahy.

Výstrahy protokolu poskytují možnost ztlumení akcí aktivované výstrahy na základě nastaveného časového intervalu. Toto pole se nazývá **potlačit výstrahy** v pracovních prostorech a Application Insights. Ve všech ostatních typech prostředků se nazývá **ztlumení akcí**. 

Běžným problémem je, že se domníváte, že výstraha neaktivovala akce kvůli problému se službou. I když je tato konfigurace pravidla ztlumená, je to ještě obtížné.

![Potlačit výstrahy](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>Pravidlo upozornění měření metriky s dělením pomocí starší verze rozhraní Log Analytics API

[Měření metriky](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) je typ výstrahy protokolu, která je založena na souhrnných výsledcích časových řad. Tato pravidla umožňují seskupení podle sloupců, aby se [oddělily výstrahy](alerts-unified-log.md#split-by-alert-dimensions). Pokud používáte starší verze rozhraní Log Analytics API, rozdělení nebude fungovat podle očekávání. Výběr seskupení ve starším rozhraní API se nepodporuje.

Aktuální rozhraní API ScheduledQueryRules umožňuje nastavit **agregaci na základě** pravidel [měření metrik](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) , která budou fungovat podle očekávání. [Přečtěte si další informace o přepnutí na aktuální rozhraní ScheduledQueryRules API](alerts-log-api-switch.md).

## <a name="log-alert-fired-unnecessarily"></a>Výstraha protokolu se vyvolala zbytečně.

Nakonfigurované [pravidlo výstrahy protokolu v Azure monitor](./alerts-log.md) může být neočekávaně aktivované. Následující části popisují některé běžné důvody.

### <a name="alert-triggered-by-partial-data"></a>Výstraha aktivovaná částečnými daty

Azure Monitor zpracovává terabajty protokolů zákazníků z celého světa, což může způsobit [latenci přijímání protokolů](./data-ingestion-time.md).

Protokoly jsou částečně strukturovaná data a jsou v podstatě více latentních, než metriky. Pokud se setkáváte s mnoha chybami v aktivovaném upozorněních, měli byste zvážit použití [Upozornění na metriky](alerts-metric-overview.md). Data můžete do úložiště metrik odesílat z protokolů pomocí [výstrah metrik pro protokoly](alerts-metric-logs.md).

Výstrahy protokolu fungují nejlépe, když se pokusíte detekovat data v protokolech. Funguje méně dobře, když se pokusíte detekovat chybějící data v protokolech. Například upozornění na prezenční signál virtuálního počítače. 

I když existují předdefinované možnosti, které zabrání falešným výstrahám, můžou se pořád vyskytovat na velmi latentních datech (víc než 30 minut) a data s špičkami latence.

### <a name="query-optimization-issues"></a>Problémy optimalizace dotazů

Služba Výstrahy změní váš dotaz na optimalizaci pro nižší zatížení a latenci výstrah. Tok výstrah byl vytvořen pro transformaci výsledků, které upozorňují na problém výstrahy. Například v případě dotazu jako:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Pokud je záměrem uživatele výstraha, když dojde k tomuto typu události, logika výstrahy se připojí `count` k dotazu. Dotaz, který se spustí, bude:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Není nutné přidávat do dotazu logiku pro upozorňování a provádět v nich dokonce problémy. Pokud v příkladu výše zahrnete `count` do dotazu, bude vždy výsledkem hodnota 1, protože služba Výstrahy bude provádět `count` `count` .

Optimalizovaným dotazem je to, co služba Výstrahy protokolu spouští. Upravený dotaz můžete spustit na [portálu](../log-query/log-query-overview.md) Log Analytics nebo v [rozhraní API](/rest/api/loganalytics/).

U pracovních prostorů a Application Insights se říká **dotaz, který se má spustit** v podokně podmínka. Ve všech ostatních typech prostředků vyberte na kartě podmínka možnost **Zobrazit konečný dotaz na výstrahu** .

![Dotaz, který se má provést](media/alert-log-troubleshoot/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>Výstraha protokolu byla zakázána.

V následujících částech jsou uvedeny některé důvody, proč Azure Monitor může zakázat pravidlo upozornění protokolu. Zahrnuli jsme také [příklad protokolu aktivit, který se pošle, když je pravidlo zakázané](#activity-log-example-when-rule-is-disabled).

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>Rozsah výstrahy už neexistuje nebo se přesunul.

V případě, že prostředky oboru pravidla výstrahy již nejsou platné, spuštění pravidla se nezdařilo. V tomto případě se fakturace zastaví také.

Azure Monitor zakáže upozornění protokolu po týdnu, pokud dojde k jeho nepřetržitému spuštění.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>Dotaz použitý v upozornění protokolu není platný.

Při vytvoření pravidla upozornění protokolu se dotaz ověří na správnou syntaxi. V některých případech se může stát, že dotaz uvedený v pravidle výstrahy protokolu selže. Mezi běžné příčiny patří:

- Pravidla byla vytvořena prostřednictvím rozhraní API a uživatel ji přeskočil.
- Dotaz [běží na několika prostředcích](../log-query/cross-workspace-query.md) a jeden nebo víc prostředků se odstranil nebo přesunul.
- [Dotaz se nezdařil](https://dev.loganalytics.io/documentation/Using-the-API/Errors) z těchto důvodů:
    - Řešení protokolování nebylo [nasazeno do pracovního prostoru](../insights/solutions.md#install-a-monitoring-solution), takže tabulky se nevytvoří.
    - Data se přestala přenášet do tabulky v dotazu po dobu více než 30 dnů.
    - [Vlastní tabulky protokolů](data-sources-custom-logs.md) ještě nejsou vytvořené, protože tok dat se nespustil.
- Změny v [dotazovacím jazyce](/azure/kusto/query/) obsahují revidovaný formát pro příkazy a funkce. Proto už dříve zadaný dotaz není platný.

[Azure Advisor](../../advisor/advisor-overview.md) vás upozorní na toto chování. Přidá doporučení týkající se ovlivněného pravidla upozornění protokolu. Použitá kategorie je vysoká dostupnost se středním dopadem a popisem pravidla opravit pravidlo upozornění protokolu, aby se zajistilo monitorování.

## <a name="alert-rule-quota-was-reached"></a>Bylo dosaženo kvóty pravidla upozornění.

Na počet pravidel upozornění prohledávání protokolu na jedno předplatné a prostředek se vztahují maximální kvóty popsané [tady](../service-limits.md).

### <a name="recommended-steps"></a>Doporučené kroky
    
Pokud jste dosáhli limitu kvóty, může vám tento problém vyřešit následující kroky.

1. Zkuste odstranit nebo zakázat pravidla upozornění prohledávání protokolu, která se už nepoužívají.
1. Zkuste použít [rozdělení výstrah podle dimenzí](alerts-unified-log.md#split-by-alert-dimensions) k omezení počtu pravidel. Tato pravidla mohou monitorovat mnoho prostředků a zjistitelných případů.
1. Pokud potřebujete maximální kvótu zvýšit, pokračujte otevřením žádosti o podporu a zadejte následující informace:

    - ID předplatných a ID prostředků, pro které se maximální kvóta musí zvýšit.
    - Důvod zvýšení kvóty
    - Typ prostředku pro zvýšení kvóty: **Log Analytics**, **Application Insights**atd.
    - Požadovaná maximální kvóta.


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Chcete-li kontrolovat aktuální využití nových pravidel upozornění protokolu
    
#### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

1. Otevřete obrazovku *výstrahy* a vyberte *Spravovat pravidla výstrah* .
2. Pomocí ovládacího prvku rozevíracího seznamu *Předplatné* vyfiltrujte příslušné předplatné.
3. Ujistěte se, že nechcete filtrovat konkrétní skupinu prostředků, typ prostředku nebo prostředek.
4. V ovládacím prvku rozevíracího seznamu *Typ signálu* vyberte Prohledávání protokolu.
5. Ujistěte se, že je ovládací prvek rozevíracího seznamu *Stav* nastavený na hodnotu Povoleno.
6. Nad seznamem pravidel se zobrazí celkový počet pravidel upozornění prohledávání protokolu.

#### <a name="from-api"></a>Pomocí rozhraní API

- PowerShell – [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- Rozhraní REST API – [Seznam podle předplatného](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>Příklad protokolu aktivit, když je pravidlo zakázané

Pokud se dotaz na sedm dní neobjeví nepřetržitě, Azure Monitor zakáže upozornění protokolu a zastaví fakturaci pravidla. Můžete zjistit přesný čas, kdy Azure Monitor deaktivovat výstrahu protokolu v [protokolu aktivit Azure](../../azure-resource-manager/management/view-activity-logs.md). Podívejte se na tento příklad:

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

## <a name="next-steps"></a>Další kroky

- Přečtěte si informace o [upozorněních protokolu v Azure](./alerts-unified-log.md).
- Přečtěte si další informace o [konfiguraci výstrah protokolu](../log-query/log-query-overview.md).
- Přečtěte si další informace o [dotazech protokolu](../log-query/log-query-overview.md).
