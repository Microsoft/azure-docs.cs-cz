---
title: Jak ladit UDF v Azure Digital provlákna | Microsoft Docs
description: Základní informace o tom, jak ladit UDF v digitálních prozdvojeních Azure.
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: stegaw
ms.custom: seodec18
ms.openlocfilehash: 577467a6322b7f6d3cd7f199d80963f2f1a98ed6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849330"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Jak ladit uživatelsky definované funkce v digitálních prostředníkech Azure

Tento článek shrnuje, jak diagnostikovat a ladit uživatelsky definované funkce v digitálních Vlákenách Azure. Pak identifikuje některé z nejběžnějších scénářů, které byly nalezeny při jejich ladění.

>[!TIP]
> Přečtěte si, [jak nakonfigurovat monitorování a protokolování](./how-to-configure-monitoring.md) pro další informace o nastavení ladicích nástrojů v digitálních aktivitách Azure pomocí protokolů aktivit, diagnostických protokolů a Azure monitor.

## <a name="debug-issues"></a>Problémy ladění

Znalost toho, jak diagnostikovat problémy v rámci digitálních vláken Azure, vám umožní efektivně analyzovat problémy, identifikovat příčiny problémů a poskytovat pro ně vhodná řešení.

K tomuto účelu se poskytuje celá řada nástrojů pro protokolování, analýzu a diagnostiku.

### <a name="enable-logging-for-your-instance"></a>Povolit protokolování pro vaši instanci

Digitální vlákna Azure podporuje robustní protokolování, monitorování a analýzu. Vývojáři řešení můžou používat protokoly Azure Monitor, diagnostické protokoly, protokoly aktivit a další služby pro podporu složitých potřeb monitorování aplikace IoT. Možnosti protokolování lze kombinovat pro dotazování nebo zobrazení záznamů napříč několika službami a pro zajištění podrobného pokrytí protokolování pro mnoho služeb.

* Pro konfiguraci protokolování specificky pro digitální vlákna Azure si přečtěte, [jak nakonfigurovat monitorování a protokolování](./how-to-configure-monitoring.md).
* V přehledu [Azure monitor](../azure-monitor/overview.md) najdete informace o výkonném nastavení protokolu povoleném prostřednictvím Azure monitor.
* Přečtěte si článek [shromáždění a využití dat protokolu z vašich prostředků Azure](../azure-monitor/platform/diagnostic-logs-overview.md) ke konfiguraci nastavení diagnostického protokolu v digitálních prostředcích Azure pomocí webu Azure Portal, Azure CLI nebo PowerShellu.

Po nakonfigurování budete moct vybrat všechny kategorie protokolů, metriky a používat Azure Monitor výkonné pracovní prostory Log Analytics pro podporu vašeho úsilí o ladění.

### <a name="trace-sensor-telemetry"></a>Telemetrie snímačů trasování

Chcete-li trasovat telemetrii senzorů, ověřte, zda jsou pro instanci digitálního vlákna Azure povoleny nastavení diagnostiky. Pak se ujistěte, že jsou vybrané všechny požadované kategorie protokolu. Nakonec potvrďte, že jsou odesílány požadované protokoly do protokolů Azure Monitor.

Pokud chcete, aby se zpráva telemetrie senzorů shodovala s příslušnými protokoly, můžete pro odesílaná data události zadat ID korelace. Provedete to tak, `x-ms-client-request-id` že nastavíte vlastnost na identifikátor GUID.

Po odeslání telemetrie otevřete Log Analytics pro dotaz na protokoly pomocí nastavení ID korelace:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Hodnota dotazu | Nahradit hodnotou |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | ID korelace, které bylo zadáno pro data události |

Pokud povolíte protokolování pro uživatelsky definovanou funkci, zobrazí se tyto protokoly v instanci Log Analytics s kategorií `UserDefinedFunction`. Pokud je chcete načíst, zadejte do Log Analytics následující podmínku dotazu:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Další informace o výkonných operacích dotazů najdete v článku [Začínáme s dotazy](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identifikace běžných problémů

Při řešení potíží s řešením jsou důležité jak diagnostikovat, tak i identifikovat běžné problémy. Několik problémů, které se běžně vyskytují při vývoji uživatelsky definovaných funkcí, jsou shrnuté v následujících pododdílech.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Zkontroluje, jestli se vytvořilo přiřazení role.

Bez přiřazení role vytvořeného v rozhraní API pro správu nemá uživatelem definovaná funkce přístup k provádění jakýchkoli akcí, jako je odesílání oznámení, načítání metadat a nastavení počítaných hodnot v rámci topologie.

Ověřte, jestli pro vaši uživatelsky definovanou funkci prostřednictvím rozhraní API pro správu existuje přiřazení role:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Hodnota parametru | Nahradit hodnotou |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ID uživatelsky definované funkce, pro kterou se mají načíst přiřazení rolí|

Zjistěte, [jak vytvořit přiřazení role pro uživatelsky definovanou funkci](./how-to-user-defined-functions.md), pokud neexistují žádné přiřazení rolí.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Zkontroluje, jestli se pro telemetrii senzoru funguje.

S následujícím voláním rozhraní API pro správu instancí digitálních vláken Azure je možné určit, jestli se daný typ shody pro daný senzor vztahuje.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | ID shody, kterou chcete vyhodnotit |
| *YOUR_SENSOR_IDENTIFIER* | ID senzoru, který chcete vyhodnotit |

Odpověď:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Zjistit, co senzor spouští

S následujícím voláním rozhraní API pro správu digitálních vláken Azure je možné určit identifikátory uživatelem definovaných funkcí aktivovaných příchozí telemetrie daného senzoru:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | ID snímače, který má být odeslán telemetrie |

Odpověď:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Problém s přijímáním oznámení

Pokud nepřijímáte oznámení z aktivované uživatelsky definované funkce, potvrďte, že parametr typu objektu topologie odpovídá typu používaného identifikátoru.

**Nesprávné** Případě

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

K tomuto scénáři dojde, protože použitý identifikátor odkazuje na senzor, zatímco je `Space`zadaný typ objektu topologie.

**Správné** Případě

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Nejjednodušší způsob, jak tento problém vyřešit, je použití `Notify` metody v objektu metadat.

Příklad:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Běžné výjimky diagnostiky

Pokud povolíte nastavení diagnostiky, můžete se setkat s těmito častými výjimkami:

1. **Omezování**: Pokud vaše uživatelsky definovaná funkce překročí omezení přenosové rychlosti uvedené v článku [omezení služby](./concepts-service-limits.md) , bude omezena. Žádné další operace se úspěšně provedly až do vypršení limitu omezení.

1. **Data**nenalezena: Pokud se vaše uživatelsky definovaná funkce pokusí získat přístup k metadatům, které neexistují, operace se nezdařila.

1. Neautorizováno: Pokud vaše uživatelsky definovaná funkce nemá nastavené přiřazení role nebo nemá dostatečná oprávnění pro přístup k určitým metadatům z topologie, operace se nezdařila.

## <a name="next-steps"></a>Další postup

- Naučte se, jak povolit [monitorování a protokoly](./how-to-configure-monitoring.md) v digitálních vlákenách Azure.

- Další možnosti protokolování Azure najdete v článku [Přehled protokolu aktivit Azure](../azure-monitor/platform/activity-logs-overview.md) .
