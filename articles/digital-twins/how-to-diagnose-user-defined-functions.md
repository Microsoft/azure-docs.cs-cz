---
title: Jak ladit ufláže – digitální dvojčata Azure | Dokumenty společnosti Microsoft
description: Další informace o doporučených přístupech k ladění uživatelem definovaných funkcí v Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 518383488aa878dab75aec7ad5da664332b62ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511633"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Jak ladit uživatelem definované funkce v Azure Digital Twins

Tento článek shrnuje, jak diagnostikovat a ladit uživatelem definované funkce v Azure Digital Twins. Potom identifikuje některé z nejběžnějších scénářů nalezených při jejich ladění.

>[!TIP]
> Přečtěte si [článek Jak nakonfigurovat monitorování a protokolování,](./how-to-configure-monitoring.md) abyste se dozvěděli další informace o nastavení ladicích nástrojů v digitálních dvojčatech Azure pomocí protokolů aktivit, diagnostických protokolů a Azure Monitoru.

## <a name="debug-issues"></a>Problémy s laděním

Znalost toho, jak diagnostikovat problémy v rámci Azure Digital Twins, vám umožní efektivně analyzovat problémy, identifikovat příčiny problémů a poskytnout pro ně vhodná řešení.

Za tímto účelem jsou k dispozici různé nástroje pro protokolování, analýzy a diagnostiku.

### <a name="enable-logging-for-your-instance"></a>Povolení protokolování pro vaši instanci

Azure Digital Twins podporuje robustní protokolování, monitorování a analýzy. Vývojáři řešení můžou používat protokoly Azure Monitoru, diagnostické protokoly, protokoly aktivit a další služby k podpoře složitých potřeb monitorování aplikace IoT. Možnosti protokolování lze kombinovat pro dotazování nebo zobrazení záznamů v několika službách a poskytovat podrobné pokrytí protokolování pro mnoho služeb.

* Konfigurace protokolování specifická pro Azure Digital Twins načtete [článek Jak nakonfigurovat monitorování a protokolování](./how-to-configure-monitoring.md).
* Informace o výkonných nastaveních protokolů povolených prostřednictvím Azure Monitoru najdete v přehledu [Azure](../azure-monitor/overview.md) Monitoru.
* Přečtěte si článek [Shromažďování a využívání dat protokolu z prostředků Azure](../azure-monitor/platform/platform-logs-overview.md) pro konfiguraci nastavení diagnostického protokolu v Azure Digital Twins prostřednictvím portálu Azure, Azure CLI nebo PowerShellu.

Po konfiguraci budete moct vybrat všechny kategorie protokolů, metriky a používat výkonné pracovní prostory analýzy protokolů Azure Monitor pro podporu vašeho úsilí o ladění.

### <a name="trace-sensor-telemetry"></a>Telemetrie stopového senzoru

Chcete-li sledovat telemetrii senzoru, ověřte, že jsou povolená nastavení diagnostiky pro vaši instanci Azure Digital Twins. Potom se ujistěte, že jsou vybrány všechny požadované kategorie protokolu. Nakonec potvrďte, že požadované protokoly jsou odesílány do protokolů Azure Monitor.

Chcete-li porovnat telemetrickou zprávu senzoru s příslušnými protokoly, můžete zadat ID korelace u odesílaných dat události. Chcete-li tak `x-ms-client-request-id` učinit, nastavte vlastnost na identifikátor GUID.

Po odeslání telemetrie otevřete analýzu protokolů Azure Monitor pro dotazování na protokoly pomocí nastavené id korelace:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Hodnota dotazu | Nahradit hodnotou |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | ID korelace, která byla zadána v datech události |

Chcete-li číst všechny poslední protokoly telemetrie dotaz:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Pokud povolíte protokolování pro uživatelem definovanou funkci, tyto protokoly se `UserDefinedFunction`zobrazí v instanci analýzy protokolů s kategorií . Chcete-li je načíst, zadejte do analýzy protokolu následující podmínku dotazu:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Další informace o výkonných operacích dotazů [načtete Článek Začínáme s dotazy](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identifikace běžných problémů

Při řešení potíží s řešením potíží jsou důležité diagnostikovat i identifikovat běžné problémy. Několik problémů, které se běžně vyskytují při vývoji uživatelem definovaných funkcí jsou shrnuty v následujících podsekcích.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Kontrola, zda bylo vytvořeno přiřazení role

Bez přiřazení role vytvořeného v rozhraní API pro správu nemá uživatelem definovaná funkce přístup k provádění akcí, jako je odesílání oznámení, načítání metadat a nastavení vypočítaných hodnot v rámci topologie.

Zkontrolujte, zda pro vaši uživatelem definovanou funkci existuje přiřazení role prostřednictvím rozhraní API pro správu:

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Hodnota parametru | Nahradit hodnotou |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ID uživatelem definované funkce pro načtení přiřazení rolí pro|

Přečtěte [si, jak vytvořit přiřazení role pro uživatelem definovanou funkci](./how-to-user-defined-functions.md), pokud přiřazení rolí neexistují.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Zkontrolujte, zda matcher pracuje pro telemetrii senzoru

S následující volání proti rozhraní API správy azure digitální dvojče, můžete určit, pokud daný matcher platí pro daný senzor.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | ID matcheru, který chcete vyhodnotit |
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

### <a name="check-what-a-sensor-triggers"></a>Zkontrolujte, co senzor spouští

Pomocí následujícího volání proti rozhraním API pro správu digitálních dvojčat Azure můžete určit identifikátory vašich uživatelem definovaných funkcí spouštěných příchozí telemetrií daného senzoru:

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | ID senzoru pro odesílání telemetrie |

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

Pokud nedostáváte oznámení od aktivované uživatelem definované funkce, zkontrolujte, zda parametr typu objektu topologie odpovídá typu používaného identifikátoru.

**Nesprávné** Příklad:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Tento scénář nastane, protože použitý identifikátor odkazuje na senzor, `Space`zatímco zadaný typ objektu topologie je .

**Opravit** Příklad:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Nejjednodušší způsob, jak nenarazit na tento `Notify` problém, je použít metodu na objektmetadata.

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

## <a name="common-diagnostic-exceptions"></a>Běžné diagnostické výjimky

Pokud povolíte nastavení diagnostiky, může dojít k těmto běžným výjimkám:

1. **Omezení**: Pokud vaše uživatelem definovaná funkce překročí omezení rychlosti provádění popsané v článku [Omezení služeb,](./concepts-service-limits.md) bude omezena. Žádné další operace jsou úspěšně provedeny, dokud omezení omezení vyprší.

1. **Data nebyla nalezena:** Pokud se uživatelem definovaná funkce pokusí o přístup k metadatům, která neexistují, operace se nezdaří.

1. **Není autorizováno:** Pokud vaše uživatelem definovaná funkce nemá nastavenou sadu přiřazení rolí nebo nemá dostatečná oprávnění pro přístup k určitým metadatům z topologie, operace se nezdaří.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak povolit [monitorování a protokoly](./how-to-configure-monitoring.md) v Azure Digital Twins.

- Přečtěte si [článek Přehled protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md) pro další možnosti protokolování Azure.
