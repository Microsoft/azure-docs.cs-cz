---
title: Použití rozhraní REST API výstrahy analýzy protokolů
description: Rozhraní REST API výstrahy analýzy protokolů umožňuje vytvářet a spravovat výstrahy v Log Analytics, která je součástí analýzy protokolů.  Tento článek obsahuje podrobnosti o rozhraní API a několik příkladů pro provádění různých operací.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: a85dad2ba638505233e5df769e55fa5bd7b8dafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664996"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Vytváření a správa pravidel výstrah v Log Analytics pomocí rozhraní REST API 

Rozhraní REST API výstrahy analýzy protokolů umožňuje vytvářet a spravovat výstrahy v Log Analytics.  Tento článek obsahuje podrobnosti o rozhraní API a několik příkladů pro provádění různých operací.

> [!IMPORTANT]
> Jak [bylo oznámeno dříve](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), pracovní prostory analýzy protokolů vytvořené po *1.* **only** [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) [Azure Resource Mananger Template](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) [PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell) Zákazníci mohou snadno [přepnout své upřednostňované prostředky správy pravidel výstrah](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) pro starší pracovní prostory, aby využili pravidla ScheduledQueryRules Azure Monitor jako výchozí a získali mnoho nových [výhod,](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) jako je možnost používat nativní rutiny Prostředí PowerShell, delší doba zpětného pohledu v pravidlech, vytváření pravidel v samostatné skupině prostředků nebo předplatnéa a mnoho dalšího.

Rozhraní REST ROZHRANÍ API hledání služby Log Analytics je restful a lze k němu přistupovat prostřednictvím rozhraní REST Azure Resource Manager. V tomto dokumentu najdete příklady, kde rozhraní API je přístupné z příkazového řádku PowerShell pomocí [ARMClient](https://github.com/projectkudu/ARMClient), open source příkazový řádek nástroj, který zjednodušuje vyvolání rozhraní API Správce prostředků Azure. Použití ARMClient a PowerShell je jednou z mnoha možností pro přístup k rozhraní API vyhledávání Analýzy protokolů. Pomocí těchto nástrojů můžete využít rozhraní API Azure Resource Manager restful k volání do pracovních prostorů Log Analytics a k provádění příkazů vyhledávání v nich. Rozhraní API vám bude výstup výsledků hledání ve formátu JSON, což vám umožní používat výsledky hledání mnoha různými způsoby programově.

## <a name="prerequisites"></a>Požadavky
V současné době lze výstrahy vytvářet pouze pomocí uloženého vyhledávání v log analytics.  Další informace naleznete v [rozhraní REST API pro vyhledávání protokolů.](../../azure-monitor/log-query/log-query-overview.md)

## <a name="schedules"></a>Plány
Uložené hledání může mít jeden nebo více plánů. Plán definuje, jak často je hledání spouštěno a časový interval, ve kterém jsou kritéria identifikována.
Plány mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Interval |Jak často je hledání spuštěno. Měřeno v minutách. |
| QueryTimeSpan |Časový interval, ve kterém jsou kritéria vyhodnocována. Musí být rovno nebo větší než Interval. Měřeno v minutách. |
| Version |Používá se verze rozhraní API.  V současné době by měla být vždy nastavena na 1. |

Zvažte například dotaz na událost s intervalem 15 minut a časovým intervalem 30 minut. V takovém případě by byl dotaz spuštěn každých 15 minut a výstraha by se spustila, pokud by kritéria nadále byla vymizena na hodnotu true v průběhu 30 minut.

### <a name="retrieving-schedules"></a>Načítání plánů
Pomocí metody Get načtěte všechny plány pro uložené hledání.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Pomocí metody Get s ID plánu načtěte konkrétní plán pro uložené hledání.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Následuje ukázková odpověď pro plán.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15,
            "Enabled": true,
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Vytvoření plánu
K vytvoření nového plánu použijte metodu Put s jedinečným ID plánu.  Dva plány nemohou mít stejné ID, i když jsou přidruženy k různým uloženým vyhledáváním.  Při vytváření plánu v konzole Analýzy protokolů se vytvoří identifikátor GUID pro ID plánu.

> [!NOTE]
> Název pro všechna uložená hledání, plány a akce vytvořené pomocí rozhraní API analýzy protokolů musí být malá písmena.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Úprava plánu
Použijte metodu Put s existujícím ID plánu pro stejné uložené hledání k úpravě tohoto plánu; v příkladu pod plánem je zakázán. Tělo žádosti musí obsahovat *etag* plánu.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Odstranění plánů
K odstranění plánu použijte metodu Delete s ID plánu.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Akce
Plán může mít více akcí. Akce může definovat jeden nebo více procesů k provedení, jako je například odeslání pošty nebo spuštění runbooku, nebo může definovat prahovou hodnotu, která určuje, kdy výsledky hledání odpovídají některým kritériím.  Některé akce budou definovat tak, aby procesy jsou prováděny při splnění prahové hodnoty.

Všechny akce mají vlastnosti v následující tabulce.  Různé typy výstrah mají různé další vlastnosti, které jsou popsány níže.

| Vlastnost | Popis |
|:--- |:--- |
| `Type` |Typ akce.  V současné době možné hodnoty jsou Alert a Webhook. |
| `Name` |Zobrazovaný název výstrahy. |
| `Version` |Používá se verze rozhraní API.  V současné době by měla být vždy nastavena na 1. |

### <a name="retrieving-actions"></a>Načítání akcí

Pomocí metody Get načtěte všechny akce pro plán.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Pomocí metody Get s ID akce načtěte určitou akci pro plán.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Vytváření nebo úpravy akcí
Použijte Metodu Put s ID akce, která je jedinečná pro plán, k vytvoření nové akce.  Když vytvoříte akci v konzole Log Analytics, identifikátor GUID je pro ID akce.

> [!NOTE]
> Název pro všechna uložená hledání, plány a akce vytvořené pomocí rozhraní API analýzy protokolů musí být malá písmena.

Použijte metodu Put s existujícím ID akce pro stejné uložené hledání k úpravě tohoto plánu.  Tělo žádosti musí obsahovat etag plánu.

Formát požadavku pro vytvoření nové akce se liší podle typu akce, takže tyto příklady jsou uvedeny v následujících částech.

### <a name="deleting-actions"></a>Odstranění akcí

K odstranění akce použijte metodu Delete s ID akce.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Akce výstrah
Plán by měl mít jednu a pouze jednu akci výstrahy.  Akce výstrah mají jeden nebo více oddílů v následující tabulce.  Každý z nich je popsán podrobněji níže.

| Sekce | Popis | Využití |
|:--- |:--- |:--- |
| Prahová hodnota |Kritéria pro spuštění akce.| Povinné pro každou výstrahu před nebo po jejich rozšíření do Azure. |
| Severity |Popisek používaný ke klasifikaci výstrahy při aktivaci.| Povinné pro každou výstrahu před nebo po jejich rozšíření do Azure. |
| Potlačit |Možnost zastavit oznámení z výstrahy. | Volitelné pro každou výstrahu před nebo po jejich rozšíření do Azure. |
| Skupiny akcí |ID skupiny akcí Azure, kde jsou zadané akce, jako jsou e-maily, sms, hlasová volání, webhooky, automatizační sady Runbook, konektory ITSM atd.| Povinné po rozšíření výstrah do Azure|
| Přizpůsobení akcí|Změna standardního výstupu pro vybrané akce z akční skupiny| Volitelné pro každou výstrahu, lze použít po výstrahy jsou rozšířeny na Azure. |

### <a name="thresholds"></a>Prahové hodnoty
Akce výstrahy by měla mít jednu a pouze jednu prahovou hodnotu.  Pokud výsledky uloženého hledání odpovídají prahové hodnotě v akci přidružené k tomuto hledání, spustí se všechny ostatní procesy v této akci.  Akce může také obsahovat pouze prahovou hodnotu, aby ji bylo možné použít s akcemi jiných typů, které neobsahují prahové hodnoty.

Prahové hodnoty mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| `Operator` |Operátor pro porovnání prahových hodnot. <br> gt = větší než <br> lt = méně než |
| `Value` |Hodnota prahové hodnoty. |

Zvažte například dotaz na událost s intervalem 15 minut, časovým intervalem 30 minut a prahovou hodnotou větší než 10. V takovém případě by byl dotaz spuštěn každých 15 minut a výstraha by se spustila, pokud by vrátila 10 událostí, které byly vytvořeny v průběhu 30 minut.

Následuje ukázková odpověď pro akci s pouze prahovou hodnotou.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Pomocí metody Put s jedinečným ID akce vytvořte novou akci prahové hodnoty pro plán.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Pomocí metody Put s existujícím ID akce upravte akci prahové hodnoty pro plán.  Text žádosti musí obsahovat etag akce.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Log Analytics umožňuje klasifikovat výstrahy do kategorií, aby bylo možné snadněji řídit a třídit. Definovaná závažnost výstrahy je: informační, upozornění a kritická. Ty jsou mapovány na normalizované škálování závažnosti výstrah Azure jako:

|Úroveň závažnosti analýzy protokolů  |Úroveň závažnosti výstrah Azure  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Závažnost 2|

Následuje ukázková odpověď pro akci s pouze prahovou hodnotou a závažností. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Použijte Put metoda s jedinečným ID akce k vytvoření nové akce pro plán se závažností.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Pomocí metody Put s existujícím ID akce můžete upravit akci závažnosti plánu.  Text žádosti musí obsahovat etag akce.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Potlačit
Výstrahy dotazů založené na log Analytics se spustí pokaždé, když je splněna nebo překročena prahová hodnota. Na základě logiky implikované v dotazu to může mít za následek výstrahy získání aktivována pro řadu intervalů a proto oznámení také odesílány neustále. Chcete-li zabránit takovému scénáři, uživatel může nastavit možnost Potlačit pokyn Log Analytics čekat na stanovenou dobu, než je oznámení aktivována podruhé pro pravidlo výstrahy. Takže pokud potlačit je nastavena na 30 minut; pak výstraha se spálí poprvé a odeslat oznámení nakonfigurován. Ale pak počkejte 30 minut, než se znovu použije oznámení o pravidle výstrahy. V mezidobí pravidlo výstrahy bude nadále spuštěno – pouze oznámení je potlačeno službou Log Analytics po určitou dobu, bez ohledu na to, kolikrát je pravidlo výstrahy aktivováno v tomto období.

Vlastnost Potlačit pravidla výstrahy Analýzy protokolů je určena pomocí hodnoty *Omezení* a období potlačení pomocí hodnoty *DurationInMinutes.*

Následuje ukázková odpověď pro akci s pouze prahovou hodnotou, závažností a potlačovací vlastností.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

Použijte Put metoda s jedinečným ID akce k vytvoření nové akce pro plán se závažností.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Pomocí metody Put s existujícím ID akce můžete upravit akci závažnosti plánu.  Text žádosti musí obsahovat etag akce.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Skupiny akcí
Všechny výstrahy v Azure, použijte skupinu akcí jako výchozí mechanismus pro zpracování akcí. Pomocí skupiny akcí můžete zadat své akce jednou a pak přidružit skupinu akcí k více výstrahám – v rámci Azure. Bez nutnosti, opakovaně deklarovat stejné akce znovu a znovu. Skupiny akcí podporují více akcí – včetně e-mailu, SMS, hlasového volání, připojení ITSM, runbooku automation, identifikátoru URI webhooku a dalších. 

Pro uživatele, kteří rozšířili své výstrahy do Azure – plán by měl mít nyní podrobnosti skupiny akcí předány spolu s prahovou hodnotou, aby bylo možné vytvořit výstrahu. Podrobnosti o e-mailu, adresy URL webhooku, podrobnosti o automatizaci sady Runbook a další akce musí být před vytvořením výstrahy definovány na straně skupiny akcí. jeden můžete vytvořit [skupinu akcí z Azure Monitor](../../azure-monitor/platform/action-groups.md) na portálu nebo použít rozhraní API [skupiny akcí](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Chcete-li do výstrahy přidat přidružení skupiny akcí, zadejte jedinečné ID Správce prostředků Azure skupiny akcí v definici výstrahy. Ukázkový obrázek je uveden níže:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Metodu Put s jedinečným ID akce přidružte k přidružení již existující skupiny akcí k plánu.  Následuje ukázka ilustrace použití.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Pomocí metody Put s existujícím ID akce upravte skupinu akcí přidruženou k plánu.  Text žádosti musí obsahovat etag akce.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Přizpůsobení akcí
Ve výchozím nastavení akce postupujte podle standardní šablony a formátu pro oznámení. Ale uživatel může přizpůsobit některé akce, i když jsou řízeny skupinami akcí. V současné době je možné přizpůsobení pro předmět e-mailu a datovou část Webhooku.

##### <a name="customize-e-mail-subject-for-action-group"></a>Přizpůsobit předmět e-mailu pro skupinu akcí
Ve výchozím nastavení je předmětem e-mailu pro výstrahy: Upozornění `<AlertName>` pro `<WorkspaceName>`. Ale to to může být přizpůsoben, takže můžete určitá slova nebo značky - aby vám umožní snadno používat pravidla filtru ve složce Doručená pošta. Podrobnosti záhlaví e-mailu je třeba odeslat spolu s podrobnostmi skupiny ActionGroup, jako v následující ukázce.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Pomocí metody Put s jedinečným ID akce přidružte již existující skupinu akcí k přizpůsobení plánu.  Následuje ukázka ilustrace použití.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Pomocí metody Put s existujícím ID akce upravte skupinu akcí přidruženou k plánu.  Text žádosti musí obsahovat etag akce.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Přizpůsobit datovou část webhooku pro skupinu akcí
Ve výchozím nastavení webhooku odeslanéprostřednictvím skupiny akcí pro analýzu protokolů má pevnou strukturu. Ale jeden můžete přizpůsobit datovou část JSON pomocí specifických podporovaných proměnných, aby splňovaly požadavky koncového bodu webhooku. Další informace naleznete v [tématu Akce Webhook u pravidel upozornění protokolu](../../azure-monitor/platform/alerts-log-webhook.md). 

Podrobnosti vlastního háčku je třeba odeslat spolu s podrobnostmi skupiny ActionGroup a budou použity na všechny identifikátory URI zadaných v rámci skupiny akcí. jako ve vzorku níže.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Pomocí metody Put s jedinečným ID akce přidružte již existující skupinu akcí k přizpůsobení plánu.  Následuje ukázka ilustrace použití.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Pomocí metody Put s existujícím ID akce upravte skupinu akcí přidruženou k plánu.  Text žádosti musí obsahovat etag akce.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Další kroky

* Pomocí [rozhraní REST API můžete provádět vyhledávání protokolů](../../azure-monitor/log-query/log-query-overview.md) v Log Analytics.
* Informace o [výstrahách protokolů v Azure monitoru](../../azure-monitor/platform/alerts-unified-log.md)
* Jak [vytvořit, upravit nebo spravovat pravidla upozornění protokolu v Azure monitoru](../../azure-monitor/platform/alerts-log.md)

