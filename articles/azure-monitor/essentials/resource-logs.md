---
title: Protokoly prostředků Azure
description: Naučte se streamovat protokoly prostředků Azure do pracovního prostoru Log Analytics v Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.openlocfilehash: 2435e4ed16889d9d4701b6047c0a1f602ee7ae91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558691"
---
# <a name="azure-resource-logs"></a>Protokoly prostředků Azure
Protokoly prostředků Azure jsou [protokoly platforem](../essentials/platform-logs-overview.md) , které poskytují přehled o operacích provedených v rámci prostředku Azure. Obsah protokolů prostředků se liší podle typu prostředku a služby Azure. Protokoly prostředků nejsou ve výchozím nastavení shromažďovány. Musíte vytvořit nastavení diagnostiky pro každý prostředek Azure, abyste odesílali své protokoly prostředků do Log Analyticsho pracovního prostoru pro použití s [protokoly Azure monitor](../logs/data-platform-logs.md), Azure Event Hubs k posílání mimo Azure nebo Azure Storage k archivaci.

V tématu [Vytvoření nastavení diagnostiky můžete odesílat protokoly a metriky platforem do různých cílů](../essentials/diagnostic-settings.md) , kde najdete podrobné informace o vytváření nastavení diagnostiky a [nasazování Azure monitor ve velkém rozsahu pomocí Azure Policy](../deploy-scale.md) podrobnější informace o použití Azure Policy k automatickému vytvoření nastavení diagnostiky pro každý prostředek Azure, který vytvoříte.

## <a name="send-to-log-analytics-workspace"></a>Odeslání do pracovního prostoru služby Log Analytics
 Odešlete protokoly prostředků do pracovního prostoru Log Analytics, abyste povolili funkce [Azure monitor protokolů](../logs/data-platform-logs.md) , které zahrnují následující:

- Proveďte korelaci dat protokolu prostředků s dalšími daty monitorování shromážděnými pomocí Azure Monitor.
- Konsolidujte položky protokolu z několika prostředků Azure, předplatných a klientů do jednoho umístění pro účely analýzy dohromady.
- Pomocí dotazů protokolu můžete provádět komplexní analýzy a získat podrobné přehledy o datech protokolu.
- Používejte výstrahy protokolu se složitou logikou výstrah.

[Vytvořte nastavení diagnostiky](../essentials/diagnostic-settings.md) pro odesílání protokolů prostředků do pracovního prostoru Log Analytics. Tato data jsou uložena v tabulkách, jak je popsáno v [části struktura protokolů Azure monitor](../logs/data-platform-logs.md). Tabulky používané v protokolech prostředků závisí na typu kolekce, kterou prostředek používá:

- Diagnostika Azure – všechna zapsaná data jsou do tabulky [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) .
- Data specifická pro prostředky jsou zapsána do jednotlivých tabulek pro každou kategorii prostředku.

### <a name="azure-diagnostics-mode"></a>Režim diagnostiky Azure 
V tomto režimu budou všechna data z jakéhokoli nastavení diagnostiky shromažďována v tabulce [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) . Toto je starší metoda, kterou dnes používá většina služeb Azure. Vzhledem k tomu, že více typů prostředků odesílá data do stejné tabulky, je její schéma nadmnožinou schémat všech shromažďovaných datových typů. Podrobnosti o struktuře této tabulky a o tom, jak funguje s tímto potenciálně velkým počtem sloupců, najdete v [referenčních](/azure/azure-monitor/reference/tables/azurediagnostics) informacích k AzureDiagnostics.

Vezměte v úvahu následující příklad, kdy se diagnostické nastavení shromažďují do stejného pracovního prostoru pro následující typy dat:

- Protokoly auditu služby 1 (se schématem, který se skládá ze sloupců A, B a C)  
- Protokoly chyb služby 1 (se schématem, které obsahuje sloupce D, E a F)  
- Protokol auditu služby 2 (se schématem, který se skládá ze sloupců G, H a I)  

Tabulka AzureDiagnostics bude vypadat takto:  

| ResourceProvider    | Kategorie     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | x1 | Y1 | z1 |    |    |    |    |    |    |
| Microsoft. Service1 | Chybná protokolu    |    |    |    | dotazu | W1 | E1 |    |    |    |
| Microsoft. Jazyka2 | AuditLogs    |    |    |    |    |    |    | j1 | K1 | L1 |
| Microsoft. Service1 | Chybná protokolu    |    |    |    | F2 | W2 | E2 |    |    |    |
| Microsoft. Jazyka2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | L3 |
| Microsoft. Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Specifické pro prostředky
V tomto režimu se pro každou kategorii vybranou v nastavení diagnostiky vytvoří jednotlivé tabulky ve vybraném pracovním prostoru. Tato metoda se doporučuje, protože je mnohem jednodušší pracovat s daty v protokolových dotazech, poskytuje lepší zjistitelnost schémat a jejich struktury, zlepšuje výkon v rámci latence příjmu a doby dotazování a možnost udělovat práva na službu Azure RBAC pro konkrétní tabulku. Všechny služby Azure nakonec budou migrovány do režimu Resource-Specific. 

V předchozím příkladu by se vytvořily tři tabulky:
 
- Tabulka *Service1AuditLogs* následujícím způsobem:

    | Poskytovatel prostředků | Kategorie | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | Y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- Tabulka *Service1ErrorLogs* následujícím způsobem:  

    | Poskytovatel prostředků | Kategorie | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | Chybná protokolu |  dotazu | W1 | E1 |
    | Service1 | Chybná protokolu |  F2 | W2 | E2 |
    | ... |

- Tabulka *Service2AuditLogs* následujícím způsobem:  

    | Poskytovatel prostředků | Kategorie | G | H | I |
    | -- | -- | -- | -- | -- |
    | Jazyka2 | AuditLogs | j1 | K1 | L1|
    | Jazyka2 | AuditLogs | j3 | k3 | L3|
    | ... |



### <a name="select-the-collection-mode"></a>Výběr režimu kolekce
Většina prostředků Azure zapíše data do pracovního prostoru v režimu **diagnostiky Azure** nebo **specifického prostředku** , a to bez toho, abyste si zvolili. Podrobnosti o tom, který režim používá, najdete v [dokumentaci ke každé službě](./resource-logs-schema.md) . Všechny služby Azure budou nakonec používat režim Resource-Specific. V rámci tohoto přechodu vám některé prostředky umožní vybrat režim v nastavení diagnostiky. Pro všechna nová nastavení diagnostiky zadejte režim specifický pro určitý prostředek, protože to usnadňuje správu dat a může vám pomohou se vyhnout složitým migracím později.
  
   ![Selektor režimu nastavení diagnostiky](media/resource-logs/diagnostic-settings-mode-selector.png)

> [!NOTE]
> Příklad nastavení režimu kolekce pomocí šablony Resource Manageru najdete v tématu [Správce prostředků ukázek šablon pro nastavení diagnostiky v Azure monitor](./resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault).


Existující nastavení diagnostiky můžete upravit do režimu specifického pro prostředky. V tomto případě zůstanou shromážděná data v tabulce _AzureDiagnostics_ , dokud je neodeberete podle nastavení uchování pro daný pracovní prostor. Ve vyhrazené tabulce budou shromažďována nová data. Použijte operátor [Union](/azure/kusto/query/unionoperator) k dotazování dat napříč oběma tabulkami.

Dál Sledujte Blog o [aktualizacích Azure](https://azure.microsoft.com/updates/) , kde najdete oznámení o službách Azure, které podporují režim Resource-Specific.


## <a name="send-to-azure-event-hubs"></a>Odeslat do Azure Event Hubs
Odešlete protokoly prostředků do centra událostí, abyste je mohli poslat mimo Azure, například SIEM třetí strany nebo jiná řešení Log Analytics. Protokoly prostředků z Center událostí se spotřebovávají ve formátu JSON s prvkem, který `records` obsahuje záznamy v každé datové části. Schéma závisí na typu prostředku, jak je popsáno v tématu [běžné a specifické schéma pro protokoly prostředků Azure](resource-logs-schema.md).

Následuje ukázka výstupních dat z Event Hubs pro protokol prostředku:

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Odeslání do Azure Storage
Odešlete protokoly prostředků do služby Azure Storage, abyste je zachovali pro archivaci. Po vytvoření nastavení diagnostiky se kontejner úložiště vytvoří v účtu úložiště, jakmile dojde k události v jedné z kategorií povolených protokolů. Objekty BLOB v kontejneru používají následující konvence vytváření názvů:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Například objekt BLOB pro skupinu zabezpečení sítě může mít název podobný následujícímu:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Každý objekt blob PT1H.json obsahuje objekt blob ve formátu JSON událostí, ke kterým došlo během hodiny zadané v adrese URL objektu blob (například h=12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Hodnota minut (m = 00) je vždy 00, protože události protokolu prostředku jsou v jednotlivých objektech blob za hodinu rozděleny.

V rámci PT1H.jsv souboru je každá událost uložená v následujícím formátu. Použije se společné schéma nejvyšší úrovně, ale pro každou službu Azure je jedinečné, jak je popsáno v tématu [schéma protokolů prostředků](./resource-logs-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Protokoly platforem se zapisují do úložiště objektů BLOB pomocí [řádků JSON](http://jsonlines.org/), kde každá událost je řádek a znak nového řádku indikuje novou událost. Tento formát byl implementován v listopadu 2018. Před tímto datem byly protokoly zapsány do úložiště objektů BLOB jako pole JSON záznamů, jak je popsáno v tématu [Příprava změny formátu na Azure monitor protokoly platformy archivované na účet úložiště](resource-logs-blob-format.md).


## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace o protokolech prostředků](../essentials/platform-logs-overview.md).
* [Vytvořte nastavení diagnostiky pro odesílání protokolů a metrik platforem do různých umístění](../essentials/diagnostic-settings.md).
