---
title: Použití výstrahy Log Analytics REST API
description: REST API výstrahy Log Analytics umožňuje vytvářet a spravovat výstrahy v Log Analytics, které jsou součástí Log Analytics.  Tento článek poskytuje podrobné informace o rozhraní API a několika příkladech pro provádění různých operací.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: dce340db90c1528c46c1be0bc172751a04feaf31
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294071"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Vytváření a Správa pravidel výstrah v Log Analytics s využitím REST API 

> [!IMPORTANT]
> Jak bylo [oznámeno](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), pracovní prostory Log Analytics vytvořené od *1. června 2019* spravují pravidla výstrah pomocí aktuálního [rozhraní API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules/). Zákazníkům se doporučuje [Přejít na aktuální rozhraní API](./alerts-log-api-switch.md) ve starších pracovních prostorech a využít [výhody](./alerts-log-api-switch.md#benefits)Azure monitor scheduledQueryRules. Tento článek popisuje správu pravidel upozornění využívajících starší verze rozhraní API.

REST API výstrahy Log Analytics umožňuje vytvářet a spravovat výstrahy v Log Analytics.  Tento článek poskytuje podrobné informace o rozhraní API a několika příkladech pro provádění různých operací.

Log Analytics vyhledávání REST API je RESTful a lze k němu přistupovat prostřednictvím REST API Azure Resource Manager. V tomto dokumentu najdete příklady, ve kterých je k rozhraní API přistup z příkazového řádku PowerShellu pomocí  [ARMClient](https://github.com/projectkudu/ARMClient), open source nástroje příkazového řádku, který zjednodušuje vyvolání rozhraní Azure Resource Manager API. Použití ARMClient a PowerShellu je jedním z mnoha možností pro přístup k rozhraní API pro hledání Log Analytics. Pomocí těchto nástrojů můžete využít rozhraní RESTful Azure Resource Manager API k volání Log Analytics pracovních prostorů a provádění příkazů hledání v nich. Rozhraní API bude výstupem výsledků hledání ve formátu JSON, což vám umožní používat výsledky hledání mnoha různými způsoby prostřednictvím kódu programu.

## <a name="prerequisites"></a>Požadavky
V současné době je možné výstrahy vytvořit pouze s uloženým hledáním v Log Analytics.  Další informace najdete v [REST API prohledávání protokolu](../log-query/log-query-overview.md) .

## <a name="schedules"></a>Plány
Uložené hledání může mít jeden nebo více plánů. Plán definuje, jak často se spouští hledání, a časový interval, po kterém jsou kritéria identifikována.
Plány mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Interval |Jak často se spouští hledání. Měří se v minutách. |
| QueryTimeSpan |Časový interval, po kterém jsou kritéria vyhodnocena. Musí být rovno nebo větší než interval. Měří se v minutách. |
| Verze |Použitá verze rozhraní API.  V současné době by měl být vždy nastaven na hodnotu 1. |

Můžete například zvážit dotaz na událost s intervalem 15 minut a časovým intervalem 30 minut. V takovém případě se dotaz spustí každých 15 minut a výstraha se aktivuje, pokud se kritéria pokračuje v překladu na true v rozsahu 30 minut.

### <a name="retrieving-schedules"></a>Načítají se plány
K načtení všech plánů uloženého hledání použijte metodu get.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20
```

Použijte metodu Get s ID plánu k načtení konkrétního plánu pro uložené hledání.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

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
Pomocí metody PUT s jedinečným ID plánu vytvořte nový plán.  Dva plány nemohou mít stejné ID i v případě, že jsou přidruženy k různým uloženým hledáním.  Při vytváření plánu v konzole Log Analytics se vytvoří identifikátor GUID pro ID plánu.

> [!NOTE]
> Název všech uložených hledání, plánů a akcí vytvořených pomocí rozhraní Log Analytics API musí být malými písmeny.

```powershell
$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="editing-a-schedule"></a>Úprava plánu
Pro úpravu tohoto plánu použijte metodu PUT s existujícím ID plánu pro stejné uložené hledání. v následujícím příkladu je plán zakázán. Tělo žádosti musí obsahovat *značku ETag* plánu.

```powershell
$scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="deleting-schedules"></a>Odstraňují se plány
K odstranění plánu použijte metodu DELETE s ID plánu.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

## <a name="actions"></a>Akce
Plán může mít několik akcí. Akce může definovat jeden nebo více procesů, které mají být provedeny jako odeslání e-mailu nebo spuštění Runbooku, nebo může definovat prahovou hodnotu, která určuje, kdy výsledky hledání odpovídají určitým kritériím.  Některé akce budou definovat obojí, aby byly procesy provedeny při splnění prahové hodnoty.

Všechny akce mají vlastnosti v následující tabulce.  Různé typy výstrah mají další vlastnosti, které jsou popsány níže.

| Vlastnost | Popis |
|:--- |:--- |
| `Type` |Typ akce.  V současné době jsou možné hodnoty Alert a Webhook. |
| `Name` |Zobrazovaný název výstrahy |
| `Version` |Použitá verze rozhraní API.  V současné době by měl být vždy nastaven na hodnotu 1. |

### <a name="retrieving-actions"></a>Načítání akcí

Použijte metodu Get k načtení všech akcí pro plán.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20
```

Použijte metodu Get s ID akce k načtení konkrétní akce pro plán.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20
```

### <a name="creating-or-editing-actions"></a>Vytvoření nebo úprava akcí
Použijte metodu PUT s ID akce, které je jedinečné pro plán pro vytvoření nové akce.  Při vytváření akce v konzole Log Analytics je identifikátor GUID pro ID akce.

> [!NOTE]
> Název všech uložených hledání, plánů a akcí vytvořených pomocí rozhraní Log Analytics API musí být malými písmeny.

Pro úpravu tohoto plánu použijte metodu PUT s existujícím ID akce pro stejné uložené hledání.  Tělo žádosti musí obsahovat značku ETag plánu.

Formát žádosti o vytvoření nové akce se liší podle typu akce, takže tyto příklady jsou k dispozici v následujících oddílech.

### <a name="deleting-actions"></a>Odstraňování akcí

Akci odstraňte pomocí metody Delete s ID akce.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20
```

### <a name="alert-actions"></a>Akce výstrah
Plán by měl mít jednu a jenom jednu akci výstrahy.  Akce výstrahy mají jednu nebo více částí v následující tabulce.  Každá je podrobněji popsána níže.

| Sekce | Popis | Využití |
|:--- |:--- |:--- |
| Prahová hodnota |Kritéria pro spuštění akce.| Vyžaduje se pro každé upozornění, před nebo po rozšíření na Azure. |
| Závažnost |Popisek, který se používá k klasifikaci výstrahy, když se aktivuje| Vyžaduje se pro každé upozornění, před nebo po rozšíření na Azure. |
| Potlačit |Možnost zastavení oznámení z výstrahy. | Volitelné pro každou výstrahu před nebo po rozšíření na Azure. |
| Skupiny akcí |ID služby Azure Action, kde jsou zadány požadované akce, jako jsou e-maily, SMSs, hlasové hovory, Webhooky, Runbooky automatizace, konektory ITSM atd.| Vyžadované po rozšíření upozornění na Azure|
| Přizpůsobení akcí|Úprava standardního výstupu pro vybrané akce ze služby Action| Volitelné pro každou výstrahu můžete použít po rozšíření upozornění na Azure. |

### <a name="thresholds"></a>Prahové hodnoty
Akce výstrahy by měla mít jednu a jenom jednu prahovou hodnotu.  Když výsledky uloženého hledání odpovídají prahové hodnotě v akci přidružené k tomuto hledání, spustí se všechny ostatní procesy v této akci.  Akce může také obsahovat pouze prahovou hodnotu, aby ji bylo možné použít s akcemi jiných typů, které neobsahují prahové hodnoty.

Mezní hodnoty mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| `Operator` |Operátor pro porovnání prahové hodnoty. <br> gt = větší než <br> lt = menší než |
| `Value` |Hodnota prahové hodnoty. |

Můžete například zvážit dotaz na událost v intervalu 15 minut, rozpětí 30 minut a prahovou hodnotu větší než 10. V takovém případě se dotaz spustí každých 15 minut a spustí se výstraha, pokud se vrátí 10 událostí, které byly vytvořeny během 30 minut.

Následuje ukázková odpověď pro akci s pouze prahovou hodnotou.  

```json
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
```

Pro vytvoření nové prahové hodnoty pro plán použijte metodu PUT s jedinečným ID akce.  

```powershell
$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

Pro úpravu prahové hodnoty pro plán použijte metodu PUT s existujícím ID akce.  Tělo žádosti musí obsahovat značku ETag akce.

```powershell
$thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

#### <a name="severity"></a>Závažnost
Log Analytics umožňuje klasifikovat výstrahy do kategorií a umožnit tak snazší správu a třídění. Definovaná Závažnost výstrahy je: informativní, varovná a kritická. Ty jsou namapovány na normalizované měřítko závažnosti výstrah Azure jako:

|Úroveň závažnosti Log Analytics  |Úroveň závažnosti výstrah Azure  |
|---------|---------|
|`critical` |Závažnost 0|
|`warning` |Závažnost 1|
|`informational` | Závažnost 2|

Následuje ukázková odpověď pro akci s pouze prahovou hodnotou a závažností. 

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Severity": "critical",
   "Version": 1
}
```

Pomocí metody PUT s jedinečným ID akce vytvořte novou akci pro plán se závažností.  

```powershell
$thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

Pro úpravu akce závažnosti pro plán použijte metodu PUT s existujícím ID akce.  Tělo žádosti musí obsahovat značku ETag akce.

```powershell
$thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

#### <a name="suppress"></a>Potlačit
Výstrahy dotazování založené na Log Analytics se spustí při každém splnění nebo překročení prahové hodnoty. V závislosti na logice odvozené v dotazu to může vést k tomu, že se aktivuje výstraha pro řadu intervalů, takže oznámení se taky odesílají trvale. Aby se takovému scénáři zabránilo, může uživatel nastavit možnost potlačit Log Analytics, aby čekala na stanovenou dobu, než se na základě pravidla výstrahy spustí oznámení za sekundu. Takže pokud je potlačení nastaveno na 30 minut; pak se výstraha aktivuje poprvé a pošle se nakonfigurovaná oznámení. Ale potom počkejte 30 minut, než se znovu použije oznámení pro pravidlo upozornění. V průběžném období bude pravidlo výstrahy i nadále používat oznámení, že Log Analytics po zadanou dobu, a to bez ohledu na to, kolikrát se pravidlo výstrahy vyvolalo v tomto období.

Vlastnost potlačit Log Analytics pravidlo výstrahy je určena pomocí hodnoty *omezení* a periody potlačení pomocí hodnoty *DurationInMinutes* .

Následuje ukázková odpověď pro akci, která má jenom vlastnost Threshold, závažnost a potlačení.

```json
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
   "Version": 1
}
```

Pomocí metody PUT s jedinečným ID akce vytvořte novou akci pro plán se závažností.  

```powershell
$AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

Pro úpravu akce závažnosti pro plán použijte metodu PUT s existujícím ID akce.  Tělo žádosti musí obsahovat značku ETag akce.

```powershell
$AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

#### <a name="action-groups"></a>Skupiny akcí
Všechny výstrahy v Azure používají jako výchozí mechanismus pro zpracování akcí skupinu akcí. Pomocí skupiny akcí můžete zadat akce jednou a potom přidružit skupinu akcí k několika výstrahám – napříč Azure. Bez nutnosti opakovaně deklarovat stejné akce před a znovu. Skupiny akcí podporují více akcí – včetně e-mailu, SMS, hlasového hovoru, připojení ITSM, sady Automation Runbook, identifikátoru URI Webhooku a dalších. 

Pro uživatele, kteří rozšířili své výstrahy do Azure – by měl nyní mít plán k dispozici podrobnosti skupiny akcí, které mají za úkol vytvořit výstrahu. Podrobnosti e-mailu, adresy URL Webhooku, podrobnosti automatizace sady Runbook a další akce musí být před vytvořením výstrahy nejprve definovány na straně skupiny akcí; jedna z nich může vytvořit [skupinu akcí z Azure monitor](./action-groups.md) na portálu nebo pomocí [rozhraní API pro skupiny akcí](/rest/api/monitor/actiongroups).

Chcete-li přidat přidružení skupiny akcí k výstraze, zadejte jedinečné ID Azure Resource Manager skupiny akcí v definici výstrahy. Ukázka ilustrace je k dispozici níže:

```json
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
}
```

K přidružení již existující skupiny akcí pro plán použijte metodu PUT s jedinečným ID akce.  Následuje ukázka ilustrace použití.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Použijte metodu PUT s existujícím ID akce a upravte skupinu akcí přidruženou k plánu.  Tělo žádosti musí obsahovat značku ETag akce.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

#### <a name="customize-actions"></a>Přizpůsobení akcí
Ve výchozím nastavení postupuje pro oznámení standardní šablonu a formát. Uživatel ale může přizpůsobit některé akce, i když je řídí skupiny akcí. V současné době je přizpůsobení možné pro předmět e-mailu a datovou část Webhooku.

##### <a name="customize-e-mail-subject-for-action-group"></a>Přizpůsobení předmětu e-mailu pro skupinu akcí
Ve výchozím nastavení je předmět e-mailu pro výstrahy: upozornění výstrahy `<AlertName>` pro `<WorkspaceName>` . To ale můžete přizpůsobit, abyste mohli určit určitá slova nebo značky – abyste mohli pravidla filtru snadno využívat ve své doručené poště. Podrobnosti hlavičky přizpůsobení e-mailu se musí posílat společně s podrobnostmi o členovi akce, jak je uvedeno níže v ukázce.

```json
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
}
```

K přidružení již existující skupiny akcí s přizpůsobením plánu použijte metodu PUT s jedinečným ID akce.  Následuje ukázka ilustrace použití.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Použijte metodu PUT s existujícím ID akce a upravte skupinu akcí přidruženou k plánu.  Tělo žádosti musí obsahovat značku ETag akce.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

##### <a name="customize-webhook-payload-for-action-group"></a>Přizpůsobení datové části Webhooku pro skupinu akcí
Ve výchozím nastavení má Webhook odeslaný přes skupinu akcí pro Log Analytics pevnou strukturu. Ale jedna může přizpůsobit datovou část JSON pomocí konkrétních podporovaných proměnných, aby splňovala požadavky koncového bodu Webhooku. Další informace najdete v tématu [Akce Webhooku pro pravidla upozornění protokolu](./alerts-log-webhook.md). 

Přizpůsobování podrobností Webhooku se musí posílat společně s podrobnostmi o skupině akcí a budou se používat pro všechny identifikátory URI Webhooku zadané uvnitř skupiny akcí. jako v ukázce níže.

```json
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
```

K přidružení již existující skupiny akcí s přizpůsobením plánu použijte metodu PUT s jedinečným ID akce.  Následuje ukázka ilustrace použití.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Použijte metodu PUT s existujícím ID akce a upravte skupinu akcí přidruženou k plánu.  Tělo žádosti musí obsahovat značku ETag akce.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

## <a name="next-steps"></a>Další kroky

* Pomocí [REST API provádět prohledávání protokolu](../log-query/log-query-overview.md) v Log Analytics.
* Informace o [upozorněních protokolu ve službě Azure monitor](./alerts-unified-log.md)
* Jak [vytvářet, upravovat a spravovat pravidla upozornění protokolů ve službě Azure monitor](./alerts-log.md)

