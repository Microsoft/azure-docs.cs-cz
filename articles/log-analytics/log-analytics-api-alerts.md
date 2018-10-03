---
title: Pomocí rozhraní API REST upozornění OMS Log Analytics
description: Log Analytics výstrah REST API můžete vytvářet a spravovat upozornění v Log Analytics, která je součástí sady Operations Management Suite (OMS).  Tento článek obsahuje podrobnosti o rozhraní API a několik příkladů k provádění různých operací.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 6aaf9b42677064b31c56be96775692c75812e145
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044616"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Vytvářet a spravovat pravidla výstrah ve službě Log Analytics pomocí rozhraní REST API
Log Analytics výstrah REST API můžete vytvářet a spravovat upozornění v Operations Management Suite (OMS).  Tento článek obsahuje podrobnosti o rozhraní API a několik příkladů k provádění různých operací.

REST API pro hledání Log Analytics je RESTful a je přístupný prostřednictvím rozhraní REST API Azure Resource Manageru. V tomto dokumentu najdete příklady kde je rozhraní API pro přístup z příkazového řádku pomocí prostředí PowerShell [ARMClient](https://github.com/projectkudu/ARMClient), nástroje příkazového řádku open source, který zjednodušuje volání rozhraní API Azure Resource Manageru. Použití ARMClient a prostředí PowerShell je jedním z mnoha možností pro přístup k rozhraní API pro hledání Log Analytics. Pomocí těchto nástrojů můžete využít rozhraní RESTful API Azure Resource Manageru provádět volání do pracovních prostorů OMS a provádět příkazy vyhledávání v nich. Rozhraní API bude vypsání výsledků vyhledávání vám ve formátu JSON, abyste mohli používat výsledky hledání prostřednictvím kódu programu mnoha různými způsoby.

## <a name="prerequisites"></a>Požadavky
V současné době mohou výstrahy vytvořeny pouze se uložené výsledky hledání v Log Analytics.  Můžete se podívat do [protokolu REST API služby Search](log-analytics-log-search-api.md) Další informace.

## <a name="schedules"></a>Plány
Uložené výsledky hledání můžete mít nejmíň jeden plán. Plán definuje, jak často se hledání spuštění a časový interval nad tím, které je identifikován kritéria.
Plány mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Interval |Jak často se spustí vyhledávání. Měří během několika minut. |
| QueryTimeSpan |Časový interval nad tím, které se vyhodnotí kritéria. Musí být roven nebo větší než Interval. Měří během několika minut. |
| Verze |Verze rozhraní API používá.  V současné době to musí být vždy nastavená na hodnotu 1. |

Představte si třeba dotaz události s intervalem 15 minut a časový interval 30 minut. V tomto případě dotazu by spuštění každých 15 minut a by aktivuje upozornění, pokud kritéria nadále přeložit na hodnotu true přes víkend na 30minutové rozpětí.

### <a name="retrieving-schedules"></a>Načítají se plány
Pomocí metody Get pro načtení všech plánů pro uložené výsledky hledání.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Pomocí této metody Get s ID plánu načíst konkrétní plán pro uložené výsledky hledání.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Následuje ukázka odezvy pro plán.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Vytvoření plánu
K vytvoření nového plánu pomocí ID jedinečná plánovací metody Put.  Všimněte si, že dva plány nemůže mít stejné ID i v případě, že jsou přidruženy k jiné uložená hledání.  Při vytváření plánu v konzole OMS se vytvoří identifikátor GUID pro ID plánu.

> [!NOTE]
> Název pro všechny uložené výsledky hledání, plány a akce, které jsou vytvořené pomocí rozhraní API pro analýzu protokolu musí být malými písmeny.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Úprava plánu
Použijte metodu Put se existující ID plánu pro stejný uložené výsledky hledání k úpravě tohoto plánu.  Text požadavku musí obsahovat etag plán.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Odstraňuje se plány
Použijte metodu Delete s ID plánu odstranění plánu.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Akce
Plán může mít více akcí. Akce může definovat jeden nebo více procesy provádět například poslání e-mailu nebo spuštění sady runbook nebo ji může definovat prahové hodnoty, která určuje, kdy výsledky hledání odpovídají kritérií.  Některé akce budou definovat i tak, aby procesy, které jsou prováděny při splnění prahovou hodnotu.

Všechny akce mají vlastnosti v následující tabulce.  Různé typy výstrah mají různé další vlastnosti, které jsou popsané níže.

| Vlastnost | Popis |
|:--- |:--- |
| Typ |Typ akce.  Možné hodnoty jsou teď upozornění a Webhook. |
| Název |Zobrazovaný název výstrahy. |
| Verze |Verze rozhraní API používá.  V současné době to musí být vždy nastavená na hodnotu 1. |

### <a name="retrieving-actions"></a>Načítají se akce

> [!NOTE]
> Od 14. května 2018, se automaticky rozšíří všechna upozornění v instanci pracovního prostoru Log Analytics na veřejném cloudu Azure do Azure. Uživatel můžete odpojit zahájit rozšiřování upozornění do Azure před 14. května 2018. Další informace najdete v tématu [upozornění rozšířit do Azure od OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, kteří rozšíření upozornění do Azure jsou teď akce provádí na skupiny akcí Azure. Jakmile pracovního prostoru a jeho výstrahy se rozšíří do Azure, můžete načíst nebo přidání akcí s použitím [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Pomocí metody Get pro načtení všech akcí pro plán.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Pomocí této metody Get s ID akce načtení konkrétní akci pro plán.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Vytváření nebo úpravu akce
Použijte metodu Put s ID akce, které jsou jedinečné pro plán pro vytvoření nové akce.  Při vytváření akce v konzole OMS je identifikátor GUID pro ID akce.

> [!NOTE]
> Název pro všechny uložené výsledky hledání, plány a akce, které jsou vytvořené pomocí rozhraní API pro analýzu protokolu musí být malými písmeny.

Použijte metodu Put se existující ID akce pro stejný uložené výsledky hledání k úpravě tohoto plánu.  Text požadavku musí obsahovat etag plán.

Formát požadavku pro vytvoření nové akce se liší podle typu akce, takže tyto příklady jsou k dispozici v následujících částech.

### <a name="deleting-actions"></a>Odstraňuje se akce

> [!NOTE]
> Od 14. května 2018, se automaticky rozšíří všechna upozornění v instanci pracovního prostoru Log Analytics na veřejném cloudu Azure do Azure. Uživatel můžete odpojit zahájit rozšiřování upozornění do Azure před 14. května 2018. Další informace najdete v tématu [upozornění rozšířit do Azure od OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, kteří rozšíření upozornění do Azure jsou teď akce provádí na skupiny akcí Azure. Jakmile pracovního prostoru a jeho výstrahy se rozšíří do Azure, můžete načíst nebo přidání akcí s použitím [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Použijte metodu Delete s ID akce pro akci odstranit.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Akce upozornění
Plán by měl mít jeden a pouze jeden akce upozornění.  Akce upozornění mají jednu nebo více oddílů v následující tabulce.  Popsány podrobněji níže.

| Sekce | Popis | Využití |
|:--- |:--- |:--- |
| Prahová hodnota |Kritéria pro při spuštění akce.| Vyžaduje se pro každou výstrahu před nebo po se rozšíří do Azure. |
| Severity |Popisek použitý ke klasifikaci, když se aktivuje upozornění.| Vyžaduje se pro každou výstrahu před nebo po se rozšíří do Azure. |
| Skupiny akcí |ID skupina akcí Azure, ve kterém jsou zadané požadované akce, například - e-mailů, SMSs, hlasových hovorů, Webhooků, Runbooků služby Automation, konektorů ITSM atd.| Vyžaduje po výstrahy se rozšíří do Azure|
| Přizpůsobit akce|Upravit ve standardním výstupu pro akce vyberte z skupina akcí| Volitelné pro každou výstrahu, je možné po výstrahy se rozšíří do Azure. |
| EmailNotification |Odeslání e-mailu více příjemcům. | Není vyžadována, pokud se upozornění rozšíří do Azure|
| Náprava |Spuštění runbooku ve službě Azure Automation se pokusit opravit zjištěné potíže. |Není vyžadována, pokud se upozornění rozšíří do Azure|
| Akce Webhooku | Zápis dat z výstrah, k požadované službě jako JSON. |Není vyžadována, pokud se upozornění rozšíří do Azure|

> [!NOTE]
> Od 14. května 2018, se automaticky rozšíří všechna upozornění v instanci pracovního prostoru Log Analytics na veřejném cloudu Azure do Azure. Uživatel můžete odpojit zahájit rozšiřování upozornění do Azure před 14. května 2018. Další informace najdete v tématu [upozornění rozšířit do Azure od OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md).

#### <a name="thresholds"></a>Mezní hodnoty
Akci upozornění by měl mít jeden a pouze jeden prahovou hodnotu.  Když výsledky uložené výsledky hledání odpovídají prahovou hodnotu v akce přidružené k danému hledání, jsou spuštěny žádné další procesy v této akci.  Akce může také obsahovat pouze mezní hodnotu tak, že je možné s akcemi jiných typů, které neobsahují slovo prahové hodnoty.

Prahové hodnoty mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Operátor |Operátor porovnání prahové hodnoty. <br> gt = větší než <br> lt = menší než |
| Hodnota |Hodnota pro mezní hodnotu. |

Představte si třeba dotaz události s intervalem 15 minut, časový interval 30 minut a prahová hodnota větší než 10. V tomto případě dotazu by spuštění každých 15 minut a by aktivuje upozornění, pokud byl vrácen 10 události, které se vytvořily přes víkend na 30minutové rozpětí.

Tady je ukázková odpověď pro akce s pouze prahovou hodnotu.  

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

Pomocí metody Put s ID jedinečná akce můžete vytvořit novou akci prahové hodnoty pro plán.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Chcete-li změnit prahovou hodnotu akce pro plán pomocí stávající ID akce metodu Put.  Text požadavku musí obsahovat značku etag akce.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Log Analytics umožňuje klasifikovat vaše upozornění do kategorií, umožňující snadnější správu a třídění. Závažnost výstrahy, které jsou definovány je: informační, varovná a kritická. Tyto jsou mapovány na škálování normalizované závažnost výstrah Azure jako:

|Úroveň závažnosti log Analytics  |Úroveň závažnosti upozornění v Azure  |
|---------|---------|
|kritický |Sev 0|
|upozornění |Sev 1|
|Informační | Sev 2|

Následuje ukázka odezvy pro akce s pouze prahovou hodnotu a závažnost. 

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

Použijte metodu Put s ID jedinečná akce pro vytvoření nové akce pro plán se závažností.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Použijte metodu Put se existující ID akce změnit závažnost akce pro plán.  Text požadavku musí obsahovat značku etag akce.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="action-groups"></a>Skupiny akcí
Všechna upozornění v Azure, použijte skupiny akcí jako výchozího mechanismu pro zpracování akce. Pomocí skupiny akcí můžete zadat vaše akce jednou a přidružte skupinu akcí více výstrah – napříč Azure. Bez nutnosti opakovaně opětovně deklarovat stejné akce. Skupiny akcí podporovat více akcí – včetně e-mailu, SMS, hlasovým hovorem, připojení ITSM, Runbook služby Automation, Webhooku URI a dalších. 

Pro uživatele, kteří mají svá upozornění rozšíří do Azure – plánu teď měli mít podrobnosti skupiny akcí předána spolu s prahovou hodnotou, bude moct vytvořit výstrahu. Podrobnosti o e-mailu, adresy URL Webhooku, automatických postupů Runbook. Podrobnosti a další akce, musí být definován na straně nejdříve výstrahu; před vytvořením skupiny akcí můžete vytvořit jednu [skupiny akcí ze služby Azure Monitor](../monitoring-and-diagnostics/monitoring-action-groups.md) portálu nebo pomocí [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Přidat přidružení skupiny akcí na výstrahu, zadejte jedinečné ID Azure Resource Manageru skupiny akcí v definici upozornění. Obrázek ukázky jsou uvedeny níže:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
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

Pomocí metody Put s ID jedinečná akce můžete přidružit již existující skupiny akcí pro plán.  Následuje ukázkový obrázek využití.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Použijte metodu Put se existující ID akce Upravit skupinu akcí přidružené k plánu.  Text požadavku musí obsahovat značku etag akce.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Přizpůsobit akce
Výchozí akcí postupujte podle standardní šablony a formát pro oznámení. Ale může uživatel přizpůsobit některé akce, i když jsou ovládány skupin akcí. V současné době je možné, předmět e-mailu a datová část Webhooku vlastní nastavení.

##### <a name="customize-e-mail-subject-for-action-group"></a>Přizpůsobit předmět e-mailu pro skupinu akcí
Ve výchozím nastavení, je předmět e-mailu pro výstrahy: oznámení výstrah <AlertName> pro <WorkspaceName>. Ale to je možné přizpůsobit, tak, aby bylo možné konkrétní slova nebo značky – aby bylo možné snadno využívat pravidla filtru v doručené poště. Podrobnosti o vlastní e-mailové hlavičky potřeba odeslat spolu s podrobnostmi skupina akcí, jako v následující ukázce.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
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

Pomocí metody Put s ID jedinečná akce můžete přidružit vlastní nastavení pro plán již existující skupiny akcí.  Následuje ukázkový obrázek využití.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Použijte metodu Put se existující ID akce Upravit skupinu akcí přidružené k plánu.  Text požadavku musí obsahovat značku etag akce.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Vlastní datová část Webhooku pro skupinu akcí
Ve výchozím nastavení má webhooku odeslaná pomocí skupiny akcí pro log analytics pevnou strukturu. Ale jeden přizpůsobit datovou část JSON s použitím konkrétní proměnné, které jsou podporované pro splnění požadavků na koncový bod webhooku. Další informace najdete v tématu [akce Webhooku pro pravidla upozornění protokolů](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md). 

Podrobnosti o vlastní webhooku nemusejí odeslat spolu s podrobnostmi skupina akcí a použít na všechny Webhooku URI zadat v rámci skupiny akcí; stejně jako v následující ukázce.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
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

Pomocí metody Put s ID jedinečná akce můžete přidružit vlastní nastavení pro plán již existující skupiny akcí.  Následuje ukázkový obrázek využití.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Použijte metodu Put se existující ID akce Upravit skupinu akcí přidružené k plánu.  Text požadavku musí obsahovat značku etag akce.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>E-mailové oznámení
E-mailová oznámení odeslat poštu na jeden nebo více příjemcům.  Patří mezi ně vlastnosti v následující tabulce.

> [!NOTE]
> Od 14. května 2018, se automaticky rozšíří všechna upozornění v instanci pracovního prostoru Log Analytics na veřejném cloudu Azure do Azure. Uživatel můžete odpojit zahájit rozšiřování upozornění do Azure před 14. května 2018. Další informace najdete v tématu [upozornění rozšířit do Azure od OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, kteří rozšíření upozornění do Azure jsou řízeny v skupiny akcí Azure nyní akce, jako je e-mailové oznámení. Jakmile pracovního prostoru a jeho výstrahy se rozšíří do Azure, můžete načíst nebo přidání akcí s použitím [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).
   

| Vlastnost | Popis |
|:--- |:--- |
| Příjemci |Seznam adres e-mailu. |
| Subjekt |Předmět e-mailu. |
| Příloha |Přílohy se momentálně nepodporují, takže to bude mít vždy hodnotu "None". |

Následuje ukázka odezvy pro akci oznámení e-mailu s prahovou hodnotou.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Použijte metodu Put s ID jedinečná akce pro vytvoření nového e-mailové akce pro plán.  Následující příklad vytvoří e-mailové oznámení s prahovou hodnotou, e-mail se odešle, když výsledky uložené vyhledávání překročí prahovou hodnotu.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Použijte metodu Put se existující ID akce upravit e-mailové akce pro plán.  Text požadavku musí obsahovat značku etag akce.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Nápravné akce
Nápravy spuštění runbooku ve službě Azure Automation, která se pokusí o odstranění problému identifikovaného výstrahou.  Musíte vytvořit webhook pro runbook používá v rámci nápravné akce a potom zadejte identifikátor URI ve vlastnosti WebhookUri.  Při vytváření této akce v konzole OMS se automaticky vytvoří nový webhook pro runbook.

> [!NOTE]
> Od 14. května 2018, se automaticky rozšíří všechna upozornění v instanci pracovního prostoru Log Analytics na veřejném cloudu Azure do Azure. Uživatel můžete odpojit zahájit rozšiřování upozornění do Azure před 14. května 2018. Další informace najdete v tématu [upozornění rozšířit do Azure od OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, kteří rozšíření upozornění do Azure jsou nyní akce, jako je náprava runbooku provádí na skupiny akcí Azure. Jakmile pracovního prostoru a jeho výstrahy se rozšíří do Azure, můžete načíst nebo přidání akcí s použitím [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Nápravy zahrnují vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| RunbookName |Název sady runbook. Musí se shodovat s publikované sady runbook v účtu automation, který je nakonfigurovaný v řešení služby Automation v pracovním prostoru OMS. |
| WebhookUri |Identifikátor URI webhooku. |
| Vypršení platnosti |Datum vypršení platnosti a čas webhooku.  Pokud se webhook nemá vypršení platnosti, to může být libovolný platný budoucí datum. |

Tady je ukázková odpověď pro nápravné akce s prahovou hodnotou.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Pomocí metody Put s ID jedinečná akce můžete vytvořit novou akci nápravy pro plán.  Následující příklad vytvoří nápravě s prahovou hodnotou, spuštění runbooku, když výsledky uložené vyhledávání překročí prahovou hodnotu.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Použijte metodu Put se existující ID akce nápravnou akci pro plán.  Text požadavku musí obsahovat značku etag akce.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Příklad:
Následuje Úplný příklad k vytvoření nového e-mailové upozornění.  Tím se vytvoří nový plán spolu s obsahující prahové hodnoty a e-mailovou akci.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Akce Webhooku
Akce Webhooku spuštění procesu pomocí volání adresy URL a volitelně poskytuje datovou část, která je k odeslání.  Když se podobají nápravné akce, s výjimkou jsou určeny pro webhooky, které mohou vyvolat procesy než runbooky Azure Automation.  Obsahují taky další možnost poskytnout datovou část, která bude doručen do vzdáleného procesu.

> [!NOTE]
> Od 14. května 2018, se automaticky rozšíří všechna upozornění v instanci pracovního prostoru Log Analytics na veřejném cloudu Azure do Azure. Uživatel můžete odpojit zahájit rozšiřování upozornění do Azure před 14. května 2018. Další informace najdete v tématu [upozornění rozšířit do Azure od OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, kteří rozšíření upozornění do Azure jsou akce, jako je Webhook teď provádí na skupiny akcí Azure. Jakmile pracovního prostoru a jeho výstrahy se rozšíří do Azure, můžete načíst nebo přidání akcí s použitím [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).


Akce Webhooku nemáte prahovou hodnotu, ale místo toho měla být přidána do plánu, který má akci upozornění s prahovou hodnotou.  

Tady je ukázková odpověď pro akce webhooku a přidružené akce upozornění s prahovou hodnotou.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>Vytvořte nebo upravte akce webhooku
Pomocí metody Put s ID jedinečná akce můžete vytvořit nové akce webhooku pro plán.  Následující příklad vytvoří výstrahu a akce Webhooku s prahovou hodnotu, aby webhook se aktivuje, když výsledky uložené vyhledávání překročí prahovou hodnotu.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Použijte metodu Put se existující ID akce k úpravě akce webhooku pro plán.  Text požadavku musí obsahovat značku etag akce.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Další postup
* Použití [rozhraní REST API k provedení prohledávání protokolů](log-analytics-log-search-api.md) v Log Analytics.
* Další informace o [upozornění protokolů ve výstrahách azure](../monitoring-and-diagnostics/monitor-alerts-unified-log.md)

