---
title: Odpovědi výstrah v Azure Log Analytics | Microsoft Docs
description: Výstrahy v Log Analytics identifikovat důležité informace v pracovním prostoru Azure a můžete proaktivně upozorňují na problémy nebo vyvolání akce se pokusit o opravte je.  Tento článek popisuje, jak vytvořit pravidlo výstrahy a podrobnosti o různé akce, které jejich zajištění může trvat.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 717adf1b19b9de8542ec507df3a01b187d0df8a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31521060"
---
# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Přidání akce do pravidla výstrah v analýzy protokolů

> [!NOTE]
> Výstrahy v analýzy protokolů jsou [rozšiřovanou do Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md).  Výstrahy v Azure pomocí [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md) k definování svých akcí místo informace v tomto článku.


Když [v analýzy protokolů se vytvoří výstraha](log-analytics-alerts.md), máte možnost [konfigurace pravidlo výstrahy](log-analytics-alerts.md) provést několik akcí.  Tento článek popisuje různé akce, které jsou k dispozici a podrobnosti o konfiguraci jednotlivých typů.

| Akce | Popis |
|:--|:--|
| [E-mail](#email-actions) | Jeden nebo více příjemcům odeslat e-mail s detaily výstrahy. |
| [Webhook](#webhook-actions) | Vyvolání externího procesu prostřednictvím jedné žádosti HTTP POST. |
| [sady runbook](#runbook-actions) | Spuštění sady runbook ve službě Azure Automation. |


## <a name="email-actions"></a>Akce e-mailu
E-mailu akce odeslání e-mailu s detaily výstrahy jednoho nebo více příjemců.  Můžete zadat předmět e-mailu, ale jeho obsah má standardní formát, který vytvoří Log Analytics.  Obsahuje souhrnné informace, jako je název výstrahy kromě podrobností až deset záznamů protokolu nalezené.  Zahrnuje také odkaz k hledání protokolů v analýzy protokolů, který vrací celou sadu záznamů z tohoto dotazu.   Odesílatelem e-mailu je *týmu Microsoft Operations Management Suite &lt; noreply@oms.microsoft.com &gt;* . 

E-mailu akce vyžadují vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Předmět |Subjektu v e-mailu.  Tělo e-mailu se nedá změnit. |
| Příjemci |Adresy všech příjemců e-mailu.  Pokud zadáte více než jednu adresu, oddělte adresy středníkem (;). |


## <a name="webhook-actions"></a>Akce Webhooku

Akce Webhooku umožňují vyvolání externího procesu prostřednictvím jedné žádosti HTTP POST.  Služba volané by měla podporovat webhooky a určit, jak se použijí všechny datové části obdrží.  Může také zavolat REST API, která nepodporuje konkrétně webhooků, pokud je požadavek ve formátu, který funguje s technologií rozhraní API.  Příklady použití webhook, jehož v reakci na výstrahy jsou odesílání zprávy v [Slack](http://slack.com) nebo vytváření incidentu v [PagerDuty](http://pagerduty.com/).  Kompletní a podrobný postup vytvoření pravidla výstrahy s webhooku pro volání Slack je k dispozici na [Webhooky ve výstrahách analýzy protokolů](log-analytics-alerts-webhooks.md).

Akce Webhooku vyžadují vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Adresa URL Webhooku |Adresa URL webhooku. |
| Vlastní datovou část JSON |Vlastní datovou část odeslat spolu s webhooku.  Níže naleznete podrobnosti. |


Webhooky zahrnují adresu URL a datovou část ve formátu JSON, který se data odesílají externí služby.  Ve výchozím nastavení obsahuje datové části hodnoty v následující tabulce.  Můžete nahradit vlastní jeden vlastní tato datová část.  V takovém případě můžete proměnné v tabulce pro jednotlivé parametry mají být zahrnuty jejich hodnota vaše vlastní datovou část.


| Parametr | Proměnná | Popis |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Název pravidla výstrahy. |
| AlertThresholdOperator |#thresholdoperator |Operátor prahová hodnota pro pravidlo výstrahy.  *Větší než* nebo *menší než*. |
| AlertThresholdValue |#thresholdvalue |Prahová hodnota pro pravidlo výstrahy. |
| LinkToSearchResults |#linktosearchresults |Propojit vyhledávání protokolu analýzy protokolů, který vrátí záznamy v dotazu, který vytvořili výstrahu. |
| Element resultcount element |#searchresultcount |Počet záznamů ve výsledcích hledání. |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |Koncový čas pro dotaz ve formátu UTC. |
| SearchIntervalInSeconds |#searchinterval |Časový interval pro pravidlo výstrahy. |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |Počáteční čas pro dotaz ve formátu UTC. |
| SearchQuery |#searchquery |Vyhledávací dotaz protokolu používá pravidlo výstrahy. |
| SearchResults |Viz níže |Záznamů vrácených dotazem ve formátu JSON.  Omezeno na první 5 000 záznamů. |
| ID pracovního prostoru |#workspaceid |ID pracovního prostoru analýzy protokolů. |

Například může určit následující vlastní datovou část, která obsahuje jeden parametr s názvem *text*.  Služby, který volá tento webhook by byla očekávána tento parametr.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Tato datová část příkladu by odkazující na něco jako následující odeslání do webhooku.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Zahrnout vlastní datovou část výsledky hledání, přidejte následující řádek jako vlastnost nejvyšší úrovně v datové části json.  

    "IncludeSearchResults":true

Například pokud chcete vytvořit vlastní datovou část, která obsahuje jenom název výstrahy a výsledky hledání, můžete použít následující. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Si můžete projít kompletní příklad vytvoření pravidla výstrahy s webhooku zahájíte externí služba v [vytvoří akci, výstrah webhooku v analýzy protokolů k odeslání zprávy na Slack](log-analytics-alerts-webhooks.md).


## <a name="runbook-actions"></a>Akce sady Runbook
Runbook akce spuštění sady runbook ve službě Azure Automation.  Chcete-li použít tento typ akce, musíte mít [řešení služby Automation](log-analytics-add-solutions.md) nainstalován a nakonfigurován v pracovní prostor analýzy protokolů.  Můžete vybrat ze sady runbook do účtu automation, který jste nakonfigurovali v řešení služby Automation.

Akce Runbook vyžadují vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:---|
| Runbook | Sady Runbook, který chcete spustit v případě, že se vytvoří výstraha. |
| Spusťte na | Zadejte **Azure** ke spuštění runbooku v cloudu.  Zadejte **hybridní pracovní proces** ke spuštění sady runbook na agenta s [hybridní pracovní proces Runbooku](../automation/automation-hybrid-runbook-worker.md ) nainstalována.  |

Akce Runbook spustit pomocí sady runbook [webhooku](../automation/automation-webhooks.md).  Když vytvoříte pravidlo výstrahy, automaticky vytvoří nové webhooku pro sadu runbook s názvem **OMS výstraha nápravy** následuje identifikátor GUID.  

Nelze přímo naplnit žádné parametry sady runbook, ale [parametru $WebhookData](../automation/automation-webhooks.md) obsahuje podrobnosti o výstraze, včetně výsledky hledání protokolů, která ji vytvořila.  Sada runbook je potřeba určit **$WebhookData** jako parametr pro ho pro přístup k vlastnostem výstrahy.  Data výstrah je k dispozici ve formátu json v jednom vlastnost s názvem **SearchResult** (pro runbook akce a akce webhooku se standardní datovou část) nebo **SearchResults** (webhooku akce s vlastní datová část včetně **IncludeSearchResults ": true**) v **RequestBody** vlastnost **$WebhookData**.  Tato akce nemá s vlastnostmi v následující tabulce.

>[!NOTE]
> Pokud pracovní prostor byl upgradován na verzi [nové analýzy protokolů dotazu jazyka](log-analytics-log-search-upgrade.md), pak došlo ke změně datové sady runbook.  Podrobnosti o formátu najdete v tématu [Rozhraní REST API služby Azure Log Analytics](https://aka.ms/loganalyticsapiresponse).  Můžete zobrazit příklad v [ukázky](#sample-payload) níže.  

| Node | Popis |
|:--- |:--- |
| id |Cesta a identifikátor GUID hledání. |
| __metadata |Informace o výstraze, včetně počet záznamů a stav výsledky hledání. |
| hodnota |Samostatný záznam pro každý záznam ve výsledcích hledání.  Podrobnosti o položce odpovídat vlastnosti a hodnoty záznamu. |

Následující sady runbook by například rozbalte záznamy nalezené protokolu a přiřadit jiné vlastnosti na základě typu každý záznam.  Všimněte si, že sada runbook spustí převedením **RequestBody** z formátu json, které se možné pracovat s jako objekt v prostředí PowerShell.

>[!NOTE]
> Tato sada runbook používá **SearchResult** kterého je vlastnost, která obsahuje výsledky pro runbook akce a akce webhooku se standardní datovou část.  Pokud sada runbook měla volat z webhooku odpověď s použitím vlastní datovou část, potřebujete změnit tuto vlastnost, aby **SearchResults**.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody

    # Get all metadata properties    
    $AlertRuleName = $RequestBody.AlertRuleName
    $AlertThresholdOperator = $RequestBody.AlertThresholdOperator
    $AlertThresholdValue = $RequestBody.AlertThresholdValue
    $AlertDescription = $RequestBody.Description
    $LinktoSearchResults =$RequestBody.LinkToSearchResults
    $ResultCount =$RequestBody.ResultCount
    $Severity = $RequestBody.Severity
    $SearchQuery = $RequestBody.SearchQuery
    $WorkspaceID = $RequestBody.WorkspaceId
    $SearchWindowStartTime = $RequestBody.SearchIntervalStartTimeUtc
    $SearchWindowEndTime = $RequestBody.SearchIntervalEndtimeUtc
    $SearchWindowInterval = $RequestBody.SearchIntervalInSeconds

    # Get detailed search results
    if($RequestBody.SearchResult -ne $null)
    {
        $SearchResultRows    = $RequestBody.SearchResult.tables[0].rows 
        $SearchResultColumns = $RequestBody.SearchResult.tables[0].columns;

        foreach ($SearchResultRow in $SearchResultRows)
        {   
            $Column = 0
            $Record = New-Object –TypeName PSObject 
        
            foreach ($SearchResultColumn in $SearchResultColumns)
            {
                $Name = $SearchResultColumn.name
                $ColumnValue = $SearchResultRow[$Column]
                $Record | Add-Member –MemberType NoteProperty –Name $name –Value $ColumnValue -Force
                        
                $Column++
            }

            # Include code to work with the record. 
            # For example $Record.Computer to get the computer property from the record.
            
        }
    }



## <a name="sample-payload"></a>Datová část ukázky
Tato část uvádí datová část ukázky pro akce webhooku a sady runbook.

### <a name="webhook-actions"></a>Akce Webhooku
Tento příklad používá **SearchResult** kterého je vlastnost, která obsahuje výsledky pro akce webhooku s standardní datovou část.  Pokud webhooku používá vlastní datovou část, která zahrnuje výsledky hledání, tato vlastnost by **SearchResults**.

Následuje ukázka datové části pro akce webhooku.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "workspaceID",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
                "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }


### <a name="runbooks"></a>Runbooky

Následuje ukázka datové části pro akci sady runbook.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "AutomationAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "861bd466-5400-44be-9552-5ba40823c3aa",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
            "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Critical"
    }



## <a name="next-steps"></a>Další postup
- Dokončete průvodce pro [konfigurace webook](log-analytics-alerts-webhooks.md) s pravidlo výstrahy.  
- Další informace o zápisu [sady runbook ve službě Azure Automation](https://azure.microsoft.com/documentation/services/automation) k nápravě problémů identifikovaný výstrahy.
