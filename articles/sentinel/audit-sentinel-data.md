---
title: Auditovat dotazy a aktivity Sentinel v Azure | Microsoft Docs
description: Tento článek popisuje, jak auditovat dotazy a aktivity provedené v Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a8ea32d84da521c8a1af926c6cb5e26bc2738de2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054583"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>Auditovat dotazy a aktivity Sentinel Azure

Tento článek popisuje, jak můžete zobrazit data auditu pro dotazy spuštěné a aktivity provedené v pracovním prostoru Azure Sentinel, například pro požadavky na interní a externí dodržování předpisů v pracovním prostoru zabezpečení (SOC).

Azure Sentinel poskytuje přístup k:

- Tabulka **AzureActivity** , která poskytuje podrobné informace o všech akcích provedených v Azure Sentinel, jako je například úprava pravidel upozornění. Tabulka **AzureActivity** neprotokoluje konkrétní data dotazu. Další informace najdete v tématu [auditování pomocí protokolů aktivit Azure](#auditing-with-azure-activity-logs).

- Tabulka **LAQueryLogs** , která poskytuje podrobné informace o dotazech spouštěných v Log Analytics, včetně dotazů, které jsou spouštěny z Azure Sentinel. Další informace najdete v tématu [auditování pomocí LAQueryLogs](#auditing-with-laquerylogs).

> [!TIP]
> Kromě ručních dotazů popsaných v tomto článku poskytuje Azure Sentinel integrovaný sešit, který vám může pomáhat při auditování aktivit ve vašem prostředí SOC.
>
> V oblasti **sešity** Sentinel Azure vyhledejte sešit **auditu pracovního prostoru** .



## <a name="auditing-with-azure-activity-logs"></a>Auditování pomocí protokolů aktivit Azure

Protokoly auditu Azure Sentinel se udržují v [protokolech aktivit Azure](../azure-monitor/essentials/platform-logs-overview.md), kde tabulka **AzureActivity** zahrnuje všechny akce prováděné v pracovním prostoru Sentinel Azure.

Tabulku **AzureActivity** můžete použít při auditování aktivity v prostředí SOC s ověřováním pomocí Azure Sentinel.

**Dotaz na tabulku AzureActivity**:

1. Připojte zdroj dat [aktivit Azure](connect-azure-activity.md) a spusťte streamování událostí auditu do nové tabulky na obrazovce **logs** s názvem AzureActivity.

1. Pak Dotazujte data pomocí KQL, stejně jako u jakékoli jiné tabulky.

    Tabulka **AzureActivity** zahrnuje data z mnoha služeb, včetně Azure Sentinel. Pokud chcete filtrovat jenom data z Azure Sentinel, spusťte dotaz pomocí následujícího kódu:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Chcete-li například zjistit, kdo byl posledním uživatelem pro úpravu konkrétního analytického pravidla, použijte následující dotaz (nahraďte `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ID pravidla pravidla, které chcete ověřit):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

Přidejte do dotazu další parametry pro další zkoumání tabulky **AzureActivities** v závislosti na tom, co je třeba ohlásit. V následujících oddílech jsou uvedeny další Ukázkové dotazy, které se použijí při auditování s daty tabulky **AzureActivity** . 

Další informace najdete v tématu [data služby Azure Sentinel zahrnutá v protokolech aktivit Azure](#azure-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Najde všechny akce provedené konkrétním uživatelem za posledních 24 hodin.

Následující dotaz **AzureActivity** tabulky vypíše všechny akce provedené konkrétním uživatelem služby Azure AD za posledních 24 hodin.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Najít všechny operace odstranění

Následující dotaz **AzureActivity** tabulky obsahuje všechny operace odstranění provedené v pracovním prostoru Sentinel Azure.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Data služby Azure Sentinel zahrnutá v protokolech aktivit Azure
 
Protokoly auditu Azure Sentinel se udržují v [protokolech aktivit Azure](../azure-monitor/essentials/platform-logs-overview.md)a obsahují tyto typy informací:

|Operace  |Typy informací  |
|---------|---------|
|**Vytvořeno**     |Pravidla upozornění <br> Komentáře k případu <br>Komentáře k incidentu <br>Uložená hledání<br>Watchlists    <br>Workbooks     |
|**Odstraněné**     |Pravidla upozornění <br>Záložky <br>Datové konektory <br>Incidenty <br>Uložená hledání <br>Nastavení <br>Sestavy analýzy hrozeb <br>Watchlists      <br>Workbooks <br>Pracovní postup  |
|**Aktualizuj**     |  Pravidla upozornění<br>Záložky <br> Věcech <br> Datové konektory <br>Incidenty <br>Komentáře k incidentu <br>Sestavy analýzy hrozeb <br> Workbooks <br>Pracovní postup       |
|     |         |

K vyhledání autorizací a licencí uživatelů můžete použít také protokoly aktivit Azure. 

V následující tabulce je například uveden seznam vybraných operací, které byly nalezeny v protokolech aktivit Azure s konkrétním prostředkem, ze kterého se data protokolu mají načíst.

|Název operace|    Typ prostředku|
|----|----|
|Vytvořit nebo aktualizovat sešit  |Microsoft. Insights/sešity|
|Odstranit sešit    |Microsoft. Insights/sešity|
|Nastavit pracovní postup   |Microsoft. Logic/Workflows|
|Odstranit pracovní postup    |Microsoft. Logic/Workflows|
|Vytvořit uložené výsledky hledání    |Microsoft. OperationalInsights/pracovní prostory/savedSearches|
|Odstranit uložené výsledky hledání    |Microsoft. OperationalInsights/pracovní prostory/savedSearches|
|Aktualizovat pravidla upozornění |Microsoft. SecurityInsights/alertRules|
|Odstranit pravidla výstrah |Microsoft. SecurityInsights/alertRules|
|Aktualizace akcí reakce na pravidlo upozornění |Microsoft. SecurityInsights/alertRules/Actions|
|Odstranit akce reakce pravidla výstrahy |Microsoft. SecurityInsights/alertRules/Actions|
|Aktualizovat záložky   |Microsoft. SecurityInsights/záložky|
|Odstranit záložky   |Microsoft. SecurityInsights/záložky|
|Aktualizační případy   |Microsoft. SecurityInsights/případy|
|Aktualizovat případové šetření  |Microsoft. SecurityInsights/případy/vyšetřování|
|Vytvořit komentáře k případu   |Microsoft. SecurityInsights/případy/komentáře|
|Aktualizace datových konektorů |Microsoft. SecurityInsights/dataconnects|
|Odstranit datové konektory |Microsoft. SecurityInsights/dataconnects|
|Aktualizace nastavení    |Microsoft. SecurityInsights/Settings|
| | |

Další informace najdete v tématu [schéma událostí protokolu aktivit Azure](/azure/azure-monitor/essentials/activity-log-schema).


## <a name="auditing-with-laquerylogs"></a>Auditování pomocí LAQueryLogs

Tabulka **LAQueryLogs** poskytuje podrobné informace o dotazech protokolu spuštěných v Log Analytics. Vzhledem k tomu, že se Log Analytics používá jako základní úložiště dat Sentinel v Azure, můžete nakonfigurovat systém tak, aby shromáždil data LAQueryLogs v pracovním prostoru Sentinel Azure.

LAQueryLogs data obsahují informace, jako například:

- Při spuštění dotazů
- Kdo spustil dotazy v Log Analytics
- Který nástroj se použil ke spouštění dotazů v Log Analytics, jako je například Azure Sentinel.
- Texty dotazu samotné
- Údaje o výkonu u každého spuštění dotazu

> [!NOTE]
> - Tabulka **LAQueryLogs** obsahuje jenom dotazy, které se spustily v okně Logs v Azure Sentinel. Nezahrnuje dotazy spouštěné pomocí plánovaných analytických pravidel, a to pomocí **grafu šetření** nebo na stránce pro **loveckí** ověřovacích rozhraní Azure.
> - Mezi okamžikem spuštění dotazu může být krátké zpoždění a data se naplní v tabulce **LAQueryLogs** . Doporučujeme počkat asi 5 minut na dotaz na data auditu v tabulce **LAQueryLogs** .
>


**Dotaz na tabulku LAQueryLogs**:

1. Tabulka **LAQueryLogs** není ve výchozím nastavení v pracovním prostoru Log Analytics povolena. Pokud chcete používat **LAQueryLogs** data při auditování v rámci Azure Sentinel, nejdřív povolte **LAQueryLogs** v oblasti **nastavení diagnostiky** v pracovním prostoru Log Analytics.

    Další informace najdete v tématu [auditování dotazů v protokolech Azure monitor](/azure/azure-monitor/logs/query-audit).


1. Pak Dotazujte data pomocí KQL, stejně jako u jakékoli jiné tabulky.

    Následující dotaz například ukazuje, kolik dotazů bylo spuštěno za poslední týden, podle jednotlivých dnů:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

V následujících částech se dozvíte více ukázkových dotazů, které se mají spustit v tabulce **LAQueryLogs** při aktivitách auditování ve vašem prostředí SOC pomocí služby Azure Sentinel.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>Počet dotazů, které jsou spuštěny v případě, že odpověď nebyla "OK"

Následující dotaz **LAQueryLogs** tabulky znázorňuje počet spuštěných dotazů, kde byla přijata cokoli jiného než odpověď HTTP **200 OK** . Například toto číslo bude obsahovat dotazy, které se nepodařilo spustit.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Zobrazit uživatele pro dotazy náročné na procesor

Následující dotaz **LAQueryLogs** tabulky obsahuje uživatele, kteří spustili nejvíc dotazů náročných na procesor, a to na základě používaného procesoru a délky času dotazu.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Zobrazit uživatele, kteří spustili nejvíc dotazů za minulý týden

Následující dotaz **LAQueryLogs** tabulky obsahuje uživatele, kteří spustili nejvíc dotazů za poslední týden.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>Konfigurace výstrah pro aktivity Sentinel Azure

Můžete chtít použít prostředky auditování Sentinel Azure k vytvoření proaktivní výstrah.

Pokud máte například citlivé tabulky v pracovním prostoru Sentinel Azure, použijte následující dotaz, který vám upozorní při každém dotazování těchto tabulek:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>Další kroky

V Azure Sentinel použijte sešit **auditu pracovního prostoru** k auditování aktivit ve vašem SOC prostředí.

Další informace najdete v tématu [kurz: vizualizace a monitorování dat](tutorial-monitor-your-data.md).
