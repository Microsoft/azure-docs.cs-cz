---
title: Pomocí OMS Log Analytics výstrahy REST API
description: Log Analytics výstrahy REST API umožňuje vytvářet a spravovat výstrahy v analýzy protokolů, která je součástí služby Operations Management Suite (OMS).  Tento článek obsahuje podrobné informace o rozhraní API a několik příkladů pro provádění různých akcí.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e6987900ac2ef535fe31d4d1ecadb1a302a9c0be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Vytvářet a spravovat pravidla výstrah v analýzy protokolů pomocí rozhraní REST API
Log Analytics výstrahy REST API umožňuje vytvářet a spravovat výstrahy v Operations Management Suite (OMS).  Tento článek obsahuje podrobné informace o rozhraní API a několik příkladů pro provádění různých akcí.

Rozhraní REST API Log Analytics Search je dosáhl standardu RESTful a je přístupný prostřednictvím rozhraní REST API Azure Resource Manager. V tomto dokumentu najdete příklady kterých je přístup k rozhraní API z příkazového řádku pomocí prostředí PowerShell [ARMClient](https://github.com/projectkudu/ARMClient), nástroje příkazového řádku open source, který zjednodušuje volání rozhraní API služby Azure Resource Manager. Použití ARMClient a prostředí PowerShell je jedním z mnoha možností pro přístup k rozhraní API pro vyhledávání Analytics protokolu. Pomocí těchto nástrojů můžete využít rozhraní RESTful API Správce prostředků Azure provádět volání do OMS pracovních prostorů a provádět příkazy vyhledávání v nich. Rozhraní API výstup výsledků vyhledávání do je ve formátu JSON, budete moci použít výsledky hledání v mnoha různými způsoby prostřednictvím kódu programu.

## <a name="prerequisites"></a>Požadavky
V současné době mohou výstrahy vytvořeny pouze s uloženého hledání v analýzy protokolů.  Můžete se podívat do [rozhraní API REST vyhledávání protokolu](log-analytics-log-search-api.md) Další informace.

## <a name="schedules"></a>Plány
Uložené hledání může mít jeden nebo více plánů. Plán definuje, jak často hledání je spustit a časový interval, za které se identifikuje kritéria.
Plány mít vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Interval |Jak často je spustit hledání. Měří v minutách. |
| QueryTimeSpan |Časový interval, za které se vyhodnotí kritéria. Musí být rovna nebo větší než Interval. Měří v minutách. |
| Verze |Verze rozhraní API používá.  V současné době to musí být vždy nastavená na hodnotu 1. |

Představte si třeba dotazu událostí s Interval 15 minut a časový interval 30 minut. V takovém případě by se spustí dotaz každých 15 minut a výstraha by spustí, pokud kritéria dál odkazující na hodnotu true přes rozpětí 30 minut.

### <a name="retrieving-schedules"></a>Načítání plány
Umožňuje načíst všechny plány uložených hledání metodu Get.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Pomocí této metody Get s ID plánu načíst konkrétní plán uložených hledání.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Následuje ukázková odpověď pro plán.

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
K vytvoření nového plánu použijte metodu Put s ID plánu jedinečný.  Všimněte si, že dvě plány nemůže mít stejné ID i v případě, že jsou spojeny s jinou uložená hledání.  Při vytváření plánu v konzole OMS, se vytvoří identifikátor GUID pro ID plánu.

> [!NOTE]
> Název pro všechny uložená hledání, plány a akce, které jsou vytvořené pomocí rozhraní API Log Analytics musí být malými písmeny.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Úprava plánu
K úpravě tento plán, použijte metodu Put s existující ID plánu pro stejné uloženého hledání.  Text žádosti musí obsahovat značku etag plánu.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Odstraňování plány
Chcete-li odstranit plán pomocí ID plánu metodu Delete.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Akce
Plán může mít více akcí. Akce může definovat jeden nebo více procesy provést například odesílání e-mailu nebo spuštění sady runbook, nebo se může definovat prahové hodnoty, která určuje, kdy výsledky hledání kritériím některé.  Některé akce obě bude definovat, aby procesy provede, když je splněna prahovou hodnotu.

Všechny akce mít vlastnosti v následující tabulce.  Různé typy výstrah mají různé další vlastnosti, které jsou popsány níže.

| Vlastnost | Popis |
|:--- |:--- |
| Typ |Typ akce.  Možné hodnoty jsou aktuálně upozornění a Webhooku. |
| Název |Zobrazovaný název výstrahy. |
| Verze |Verze rozhraní API používá.  V současné době to musí být vždy nastavená na hodnotu 1. |

### <a name="retrieving-actions"></a>Načítání akce

> [!NOTE]
> Od 14 může 2018, bude automaticky rozšířeno všechny výstrahy v pracovním prostoru do Azure. Uživatel může odpojit iniciovat rozšíření výstrahy do Azure před 14 může 2018. Další informace najdete v tématu [výstrahy rozšířit do Azure z OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, které rozšiřují výstrahy do Azure jsou nyní akce řídí ve službě Azure akce skupiny. Při jeho výstrahy a pracovního prostoru jsou rozšířené a Azure, můžete načíst nebo přidání akcí pomocí [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Umožňuje načíst všechny akce pro plán metodu Get.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Pomocí metody Get s ID akce pro načtení určité akce pro plán.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Vytvořením nebo úpravou akce
Použijte metodu Put s ID akce, které jsou jedinečné pro plán pro vytvoření nové akce.  Když vytvoříte v konzole OMS akce, je identifikátor GUID pro ID akce.

> [!NOTE]
> Název pro všechny uložená hledání, plány a akce, které jsou vytvořené pomocí rozhraní API Log Analytics musí být malými písmeny.

K úpravě tento plán, použijte metodu Put s existující ID akce pro stejné uloženého hledání.  Text žádosti musí obsahovat značku etag plánu.

Formát požadavku pro vytvoření nové akce se liší podle typu akce, takže tyto příklady jsou uvedeny v následujících částech.

### <a name="deleting-actions"></a>Odstranění akcí

> [!NOTE]
> Od 14 může 2018, bude automaticky rozšířeno všechny výstrahy v pracovním prostoru do Azure. Uživatel může odpojit iniciovat rozšíření výstrahy do Azure před 14 může 2018. Další informace najdete v tématu [výstrahy rozšířit do Azure z OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, které rozšiřují výstrahy do Azure jsou nyní akce řídí ve službě Azure akce skupiny. Při jeho výstrahy a pracovního prostoru jsou rozšířené a Azure, můžete načíst nebo přidání akcí pomocí [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Použijte metodu Delete s ID akce k odstranění akce.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Akce výstrah
Plán by měl mít pouze jeden výstrahy akce.  Jeden nebo více oddílů v následující tabulce, které se mají výstrahy akce.  Každý je podrobně popsaná v další níže.

| Sekce | Popis | Využití |
|:--- |:--- |:--- |
| Mezní hodnota |Kritéria pro spuštění akce.| Vyžaduje se pro každou výstrahu před nebo po jejich platnost do Azure. |
| Závažnost |Popisek slouží ke klasifikaci výstrahu při aktivaci.| Vyžaduje se pro každou výstrahu před nebo po jejich platnost do Azure. |
| Skupiny akcí |ID Azure ActionGroup tam, kde je zadán požadované akce, jako například – e-mailů, SMSs, hlasové hovory, Webhooky, runbooků služeb automatizace, ITSM konektory, atd.| Požadované po výstrahy jsou rozšířené a Azure|
| Přizpůsobit akce|Upravit ve standardním výstupu pro akce vyberte z ActionGroup| Volitelné pro každou výstrahu lze po výstrahy jsou rozšířené do Azure. |
| EmailNotification |Odesílat e-maily několika příjemcům. | Není vyžadována, pokud jsou rozšířené výstrahy do Azure|
| Náprava |Spuštění sady runbook ve službě Azure Automation se pokuste odstranit zjištěný problém. |Není vyžadována, pokud jsou rozšířené výstrahy do Azure|
| Akce Webhooku | Nabízet data z výstrahy, k požadované službě jako JSON |Není vyžadována, pokud jsou rozšířené výstrahy do Azure|

> [!NOTE]
> Od 14 může 2018, bude automaticky rozšířeno všechny výstrahy v pracovním prostoru do Azure. Uživatel může odpojit iniciovat rozšíření výstrahy do Azure před 14 může 2018. Další informace najdete v tématu [výstrahy rozšířit do Azure z OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md).

#### <a name="thresholds"></a>Prahové hodnoty
Výstrahy akce by měl mít pouze jednu prahovou hodnotu.  Pokud výsledky uloženého hledání neodpovídají prahovou hodnotu v akci spojené s toto hledání, jsou spuštěny žádné další procesy, které jsou v této akce.  Akce může také obsahovat pouze prahovou hodnotu, aby se může použít s akcemi jiných typů, které neobsahují žádný prahové hodnoty.

Prahové hodnoty mít vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Operátor |Operátor pro porovnání prahovou hodnotu. <br> gt = větší než <br> lt = menší než |
| Hodnota |Hodnota pro mezní hodnotu. |

Představte si třeba dotazu událostí se v intervalu 15 minut, časový interval 30 minut a prahové hodnoty větší než 10. V takovém případě by se spustí dotaz každých 15 minut a výstraha by spustí, pokud je vrácen 10 události, které byly vytvořeny během určitého 30 minut.

Následuje ukázková odpověď pro akce s pouze prahovou hodnotu.  

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

Chcete-li vytvořit novou akci prahovou hodnotu pro harmonogram pomocí akce jedinečné ID metodu Put.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

K úpravě akce prahové hodnoty pro plán, použijte metodu Put s existující ID akce.  Text žádosti musí obsahovat značku etag akce.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Závažnost
Analýzy protokolů umožňuje klasifikovat upozornění do kategorií, umožňující snadnější správu a třídění. Závažnost výstrahy, které jsou definované je: informační, upozornění a kritickou. Tyto jsou namapované na škále normalizovaný závažnost výstrahy Azure jako:

|Úroveň závažnosti analýzy protokolů  |Úroveň závažnosti Azure výstrahy  |
|---------|---------|
|Kritické |Sev 0|
|upozornění |Sev 1|
|Informační | Sev 2|

Následuje ukázková odpověď pro akce s pouze prahovou hodnotu a závažnost. 

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

Chcete-li vytvořit novou akci pro plán se závažností pomocí akce jedinečné ID metodu Put.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

K úpravě závažnost akce pro plán, použijte metodu Put s existující ID akce.  Text žádosti musí obsahovat značku etag akce.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="action-groups"></a>Skupiny akcí
Všechny výstrahy v Azure, použijte akci skupiny jako výchozího mechanismu pro zpracování akce. Akce skupin můžete zadat vaše akce jednou a potom přidružení skupiny akce k více výstrah – v Azure. Bez nutnosti opakovaně opakovaně deklarovat stejné akce. Akce skupiny podporují různé akce – včetně e-mailu, SMS, hlasový hovor, ITSM připojení, sady Automation Runbook, identifikátor URI Webhooku a dalších. 

Pro uživatele, kteří rozšířili výstrahy do Azure – nyní plánu měli mít akce skupiny podrobnosti předán společně s prahovou hodnotou, abyste mohli vytvořit výstrahu. Podrobnosti o e-mailu, adresy URL Webhooku, Runbook automatizace podrobnosti a další akce musí být definován na straně skupinu akcí nejdříve před vytvořením výstrahu; můžete vytvořit jeden [akce skupiny z Azure monitorování](../monitoring-and-diagnostics/monitoring-action-groups.md) v portálu nebo pomocí [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Chcete-li přidat přidružení skupiny akce na výstrahu, zadejte jedinečné ID správce prostředků Azure skupiny akce v definici výstrahy. Obrázek ukázkové najdete níže:

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

Použijte metodu Put s akce jedinečné ID pro přidružení už existující skupiny akce pro plán.  Zde je ukázka obrázku využití.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

K úpravě skupinu akce přidružené pro plán, použijte metodu Put s existující ID akce.  Text žádosti musí obsahovat značku etag akce.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Přizpůsobit akce
Ve výchozím nastavení akce postupujte podle standardní šablona a formát pro oznámení. Ale uživatele můžete přizpůsobit některé akce, i když jsou řízeny skupiny akcí. V současné době je možné předmět e-mailu a datovou část Webhooku přizpůsobení.

##### <a name="customize-e-mail-subject-for-action-group"></a>Přizpůsobení předmět e-mailu pro skupinu akce
Ve výchozím nastavení, je předmět e-mailu pro výstrahy: oznámení výstrah <AlertName> pro <WorkspaceName>. Ale může být přizpůsobený, aby bylo možné určitá slova nebo značky – aby bylo možné snadno využívat pravidla filtru ve vaší doručené poště. Podrobnosti záhlaví přizpůsobit e-mailu je třeba odeslat spolu s ActionGroup podrobnosti, jako následující ukázka.

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
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Použijte metodu Put s ID jedinečná akce přidružení už existující skupiny akce k přizpůsobení pro plán.  Zde je ukázka obrázku využití.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

K úpravě skupinu akce přidružené pro plán, použijte metodu Put s existující ID akce.  Text žádosti musí obsahovat značku etag akce.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Upravit datovou část Webhooku pro skupinu akce
Ve výchozím nastavení webhooku odeslána prostřednictvím akce skupiny pro analýzy protokolů má pevnou struktura. Ale datové části JSON jeden lze přizpůsobit pomocí konkrétní proměnné, které jsou podporované, aby splňoval požadavky webhooku koncového bodu. Další informace najdete v tématu [akce Webhooku pro pravidla výstrah protokolu](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md). 

Podrobnosti webhooku přizpůsobit muset odeslat spolu s ActionGroup podrobnosti a se použijí na všechny Webhooku URI zadat v rámci skupiny akce; stejně jako následující ukázka.

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
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Použijte metodu Put s ID jedinečná akce přidružení už existující skupiny akce k přizpůsobení pro plán.  Zde je ukázka obrázku využití.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

K úpravě skupinu akce přidružené pro plán, použijte metodu Put s existující ID akce.  Text žádosti musí obsahovat značku etag akce.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>E-mailové oznámení
E-mailová oznámení odesílat e-mailu na jeden nebo více příjemců.  Patří mezi ně vlastnosti v následující tabulce.

> [!NOTE]
> Od 14 může 2018, bude automaticky rozšířeno všechny výstrahy v pracovním prostoru do Azure. Uživatel může odpojit iniciovat rozšíření výstrahy do Azure před 14 může 2018. Další informace najdete v tématu [výstrahy rozšířit do Azure z OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, které rozšiřují výstrahy do Azure jsou řízeny v Azure akce skupiny teď akce jako e-mailové oznámení. Při jeho výstrahy a pracovního prostoru jsou rozšířené a Azure, můžete načíst nebo přidání akcí pomocí [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).
   

| Vlastnost | Popis |
|:--- |:--- |
| Příjemci |Seznam adres e-mailu. |
| Předmět |Předmět e-mailu. |
| Příloha |Přílohy nejsou aktuálně podporovány, takže to bude mít vždy hodnotu "Žádný." |

Následuje ukázková odpověď pro akci oznámení e-mailu s prahovou hodnotou.  

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

Chcete-li vytvořit novou e-mailu akci pro plán pomocí akce jedinečné ID metodu Put.  Následující příklad vytvoří e-mailové oznámení s prahovou hodnotou, takže odešle e-mailu v případě, že výsledky uloženého hledání překročit prahovou hodnotu.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

K úpravě akce e-mailu pro plán, použijte metodu Put s existující ID akce.  Text žádosti musí obsahovat značku etag akce.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Nápravné akce
Nápravy spuštění sady runbook ve službě Azure Automation, který se pokouší odstranit problém identifikovaný výstrahy.  Musíte vytvořit webhooku pro sadu runbook použít v akci automatické nápravy a pak zadejte identifikátor URI ve vlastnosti WebhookUri.  Když vytvoříte tuto akci pomocí konzole OMS, se automaticky vytvoří nové webhooku pro sadu runbook.

> [!NOTE]
> Od 14 může 2018, bude automaticky rozšířeno všechny výstrahy v pracovním prostoru do Azure. Uživatel může odpojit iniciovat rozšíření výstrahy do Azure před 14 může 2018. Další informace najdete v tématu [výstrahy rozšířit do Azure z OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, které rozšiřují výstrahy do Azure jsou ve skupinách Azure akce nyní ovládaná akce jako nápravy pomocí sady runbook. Při jeho výstrahy a pracovního prostoru jsou rozšířené a Azure, můžete načíst nebo přidání akcí pomocí [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Nápravami, které zahrnují vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| RunbookName |Název sady runbook. To se musí shodovat publikované sady runbook v účtu automation konfigurované v řešení služby Automation v pracovním prostoru OMS. |
| WebhookUri |Identifikátor URI služby webhooku. |
| Vypršení platnosti |Datum vypršení platnosti a čas webhooku.  Pokud webhooku nemá vypršení platnosti, může to být žádné platné budoucí datum. |

Následuje ukázková odpověď pro akci automatické nápravy s prahovou hodnotou.

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

Chcete-li vytvořit novou akci nápravy pro plán pomocí akce jedinečné ID metodu Put.  Následující příklad vytvoří nápravy s prahovou hodnotou, takže spuštění runbooku při výsledky uloženého hledání překročit prahovou hodnotu.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

K úpravě akci automatické nápravy pro plán, použijte metodu Put s existující ID akce.  Text žádosti musí obsahovat značku etag akce.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Příklad:
Toto je kompletní příklad k vytvoření nové e-mailové výstrahy.  Tím se vytvoří nový plán spolu s akce obsahující prahovou hodnotu a e-mailu.

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
Akce Webhooku spuštění procesu voláním adresu URL a volitelně poskytuje datové části k odeslání.  Jsou podobná nápravné akce s výjimkou jsou určené pro webhooků, který může vyvolat procesy než Azure Automation runbook.  Obsahují taky další možnost poskytnout datové části který bude doručen do vzdálený proces.

> [!NOTE]
> Od 14 může 2018, bude automaticky rozšířeno všechny výstrahy v pracovním prostoru do Azure. Uživatel může odpojit iniciovat rozšíření výstrahy do Azure před 14 může 2018. Další informace najdete v tématu [výstrahy rozšířit do Azure z OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, které rozšiřují výstrahy do Azure jsou nyní akce jako Webhooku řídí ve službě Azure akce skupiny. Při jeho výstrahy a pracovního prostoru jsou rozšířené a Azure, můžete načíst nebo přidání akcí pomocí [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups).


Akce Webhooku nemáte prahovou hodnotu, ale místo toho musí být přidaní do plánu, který má výstrahy akce s prahovou hodnotou.  

Následuje ukázková odpověď pro akce webhooku a přidružené akce výstrah s prahovou hodnotou.

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
K vytvoření nové akce webhooku pro plán použijte metodu Put s ID jedinečný akce.  Následující příklad vytvoří akce Webhooku a výstrah akce s prahovou hodnotu, aby webhooku se aktivuje, když výsledky uloženého hledání překročit prahovou hodnotu.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

K úpravě akce webhooku pro plán, použijte metodu Put s existující ID akce.  Text žádosti musí obsahovat značku etag akce.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Další postup
* Použití [rozhraní API REST k vyhledávání protokolu](log-analytics-log-search-api.md) v analýzy protokolů.
* Další informace o [protokolu výstrahy ve výstrahách azure](../monitoring-and-diagnostics/monitor-alerts-unified-log.md)

