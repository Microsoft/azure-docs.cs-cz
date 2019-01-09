---
title: Jak ladit UDF v digitální dvojče Azure | Dokumentace Microsoftu
description: Pokyny o tom, jak ladit UDF v digitální dvojče Azure.
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: stefanmsft
ms.custom: seodec18
ms.openlocfilehash: ebeed6d2a52937a6e80dfe28574ad854643fa7f2
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119205"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Jak ladit uživatelsky definovaných funkcí v Azure digitální dvojče

Tento článek shrnuje, jak diagnostikovat a ladit uživatelem definované funkce. Potom obsahuje některé z nejběžnějších scénářů při ladění je nalezena.

>[!TIP]
> Čtení [jak nakonfigurovat monitorování a protokolování](./how-to-configure-monitoring.md) Další informace o nastavení nástroje pro ladění v Azure digitální dvojče pomocí protokolů aktivit, diagnostické protokoly a Azure Monitor.

## <a name="debug-issues"></a>Ladění problémů

Vědět, jak diagnostikovat problémy, které vznikají v rámci vaší instanci Azure digitální dvojče pomáhá efektivně identifikovat problém, příčinu problému a řešení.

### <a name="enable-log-analytics-for-your-instance"></a>Povolení log analytics pro vaši instanci

Protokoly a metriky pro vaši instanci Azure digitální dvojče se zobrazí ve službě Azure Monitor. Tato dokumentace předpokládá, že jste vytvořili [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md) pracovní prostor prostřednictvím [webu Azure Portal](../azure-monitor/learn/quick-create-workspace.md), pomocí [rozhraní příkazového řádku Azure](../azure-monitor/learn/quick-create-workspace-cli.md), nebo prostřednictvím [ Prostředí PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

> [!NOTE]
> Při odesílání událostí do služby Azure Log Analytics pro první, může dojít ke zpoždění 5 minut.

Ke konfiguraci, monitorování a protokolování pro prostředky Azure digitální dvojče, přečtěte si [jak nakonfigurovat monitorování a protokolování](./how-to-configure-monitoring.md).

Přečtěte si článek [shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](../azure-monitor/platform/diagnostic-logs-overview.md) ke konfiguraci nastavení diagnostického protokolu v digitální dvojče Azure prostřednictvím webu Azure Portal, Azure CLI nebo Powershellu.

>[!IMPORTANT]
> Ujistěte se, že chcete-li vybrat všechny kategorie protokolů, metrik a pracovního prostoru Azure Log Analytics.

### <a name="trace-sensor-telemetry"></a>Telemetrická data ze senzorů trasování

Trasování telemetrická data ze senzorů, ujistěte se, že jsou povolené nastavení diagnostiky pro vaši instanci digitální dvojče Azure. Zkontrolujte, že všechny požadované jsou vybrané kategorie protokolu. Nakonec se přesvědčte, že se požadované protokoly odesílají do služby Azure Log Analytics.

Tak, aby odpovídaly zprávu telemetrická data ze senzorů do jeho příslušného protokolů, můžete zadat ID korelace odesílání dat události. Chcete-li to provést, nastavte `x-ms-client-request-id` vlastnost na identifikátor GUID.

Po odeslání telemetrie, otevřete Azure Log Analytics k dotazování pomocí sady protokolů ID korelace:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Hodnota dotazu | Nahradit hodnotou |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | ID korelace, který byl zadán na data události |

Pokud povolíte protokolování pro uživatelem definované funkce, těchto protokolů se objeví ve vaší instanci Azure Log Analytics s kategorií `UserDefinedFunction`. Pokud chcete je načíst, zadejte následující podmínka dotazu ve službě Azure Log Analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Další informace o operacích výkonný dotazovací najdete [Začínáme s dotazy](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identifikace běžných problémů

Jak Diagnostika a identifikace běžných problémů jsou důležité při řešení potíží s vašeho řešení. Několik problémů, které se běžně vyskytují při vývoji uživatelsky definovaných funkcí jsou shrnuté v následujících oddílech.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Zkontrolujte, zda byla vytvořena přiřazení role

Bez přiřazení role vytvořené v rámci rozhraní API pro správu uživatelem definované funkce nemá přístup k provádět všechny akce, jako je odeslání oznámení, načítání metadat, a nastavení vypočítané hodnoty v rámci topologie.

Zkontrolujte, jestli přiřazení role existuje uživatelem definované funkce prostřednictvím rozhraní API pro správu:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Hodnota parametru | Nahradit hodnotou |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ID uživatelem definované funkce, který se má načíst přiřazení role|

Přečtěte si [vytvoření přiřazení role uživatelem definované funkce](./how-to-user-defined-functions.md), pokud neexistuje žádná přiřazení role.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Zkontrolujte, pokud předávaný pracuje pro telemetrii snímače

Pomocí následující volání rozhraní API správy služby vaše instance Azure digitální dvojče budete moci určit, zda daný předávaný platí pro daný senzoru.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | ID předávaný, kterou chcete vyhodnotit |
| *YOUR_SENSOR_IDENTIFIER* | ID senzor, kterou chcete vyhodnotit |

Odpověď:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Zkontrolujte, co spustí senzoru

Pomocí následujícího volání proti API Správce Azure digitální dvojče budete moci určit identifikátory vaše uživatelem definované funkce aktivované dané senzor příchozích telemetrických dat:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | ID senzor k odesílání telemetrických dat |

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

### <a name="issue-with-receiving-notifications"></a>Problém s příjem oznámení

Když nejsou příjem oznámení z aktivované uživatelem definovanou funkci, potvrďte, že parametr typu objektu vaší topologie odpovídá typu používaného identifikátor, který se používá.

**Nesprávný** příkladu:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Tento scénář nastane, protože používá identifikátor odkazuje na senzoru při zadaný typ objektu topologie je `Space`.

**Správné** příkladu:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Nejjednodušší způsob, jak spustit do tohoto problému je použít `Notify` metodu na objekt metadat.

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

Pokud povolíte nastavení diagnostiky, můžete se setkat tyto běžné výjimky:

1. **Omezení šířky pásma**: Pokud vaše uživatelská funkce překračuje rychlost provádění omezení uvedených v [omezení služby](./concepts-service-limits.md) článku, budou omezeny. Žádné další operace se úspěšně spustili, dokud nevyprší platnost omezení.

1. **Data nebyla nalezena**: Pokud vaše uživatelem definovanou funkci pokusí získat přístup k metadata, která neexistuje, operace se nezdaří.

1. **Nemáte autorizaci**: Pokud už vaše uživatelem definovanou funkci přiřazení rolí, nastavení nebo nemá dostatečná oprávnění pro přístup k určitým metadat od topologie, operace se nezdaří.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak povolit [sledování a protokolů](../azure-monitor/platform/activity-logs-overview.md) v digitální dvojče Azure.
