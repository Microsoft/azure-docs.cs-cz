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
ms.openlocfilehash: e373e7c3ca83a0200cd1b6b945c5e4cb43b77a51
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974858"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Jak ladit uživatelsky definovaných funkcí v Azure digitální dvojče

Tento článek shrnuje, jak diagnostikovat uživatelem definované funkce. Potom obsahuje některé z nejběžnějších scénářů, které se mohou vyskytnout při práci s nimi.

>[!TIP]
> Čtení [jak nakonfigurovat monitorování a protokolování](./how-to-configure-monitoring.md) Další informace o nastavení nástroje pro ladění v Azure digitální dvojče pomocí protokolů aktivit, diagnostické protokoly a Azure Monitor.

## <a name="debug-issues"></a>Ladění problémů

Vědět, jak diagnostikovat problémy, které vznikají v rámci vaší instanci Azure digitální dvojče vám pomůže efektivně identifikovat problém, příčinu problému a řešení.

### <a name="enable-log-analytics-for-your-instance"></a>Povolení log analytics pro vaši instanci

Protokoly a metriky pro vaši instanci Azure digitální dvojče jsou přístupné prostřednictvím služby Azure Monitor. Následující dokumentace předpokládá, že jste vytvořili [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md) pracovní prostor prostřednictvím [webu Azure Portal](../azure-monitor/learn/quick-create-workspace.md), pomocí [rozhraní příkazového řádku Azure](../azure-monitor/learn/quick-create-workspace-cli.md), nebo prostřednictvím [ Prostředí PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

> [!NOTE]
> Při odesílání událostí do služby Azure Log Analytics pro první, může dojít ke zpoždění 5 minut.

Ke konfiguraci, monitorování a protokolování pro prostředky Azure digitální dvojče, přečtěte si [jak nakonfigurovat monitorování a protokolování](./how-to-configure-monitoring.md).

Přečtěte si článek [shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](../azure-monitor/platform/diagnostic-logs-overview.md) komplexní přehled nastavení diagnostického protokolu pro vaši instanci digitální dvojče Azure prostřednictvím webu Azure Portal, Azure CLI nebo Powershellu.

>[!IMPORTANT]
> Ujistěte se, že chcete-li vybrat všechny kategorie protokolů, metrik a pracovního prostoru Azure Log Analytics.

### <a name="trace-sensor-telemetry"></a>Telemetrická data ze senzorů trasování

Ujistěte se, že nastavení diagnostiky se ve vaší instanci Azure digitální dvojče povolen, jsou vybrány všechny kategorie protokolu a že protokoly jsou odesílány do služby Azure Log Analytics.

Tak, aby odpovídaly zprávu telemetrická data ze senzorů do jeho příslušného protokolů, můžete zadat ID korelace odesílání dat události. Chcete-li to provést, nastavte `x-ms-client-request-id` vlastnost na identifikátor GUID.

Po odeslání telemetrie, otevřete Azure Log Analytics k dotazování pomocí sady protokolů ID korelace:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Hodnota dotazu | Nahradit hodnotou |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | ID korelace, který byl zadán na data události |

Při protokolování uživatelem definované funkce, tyto protokoly se zobrazí ve vaší instanci Azure Log Analytics s kategorií `UserDefinedFunction`. Pokud chcete je načíst, zadejte následující podmínka dotazu ve službě Azure Log Analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Další informace o operacích výkonný dotazovací najdete [Začínáme s dotazy](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identifikace běžných problémů

Jak Diagnostika a identifikace běžných problémů jsou důležité při řešení potíží s vašeho řešení. Několik problémů, které se běžně vyskytují při vývoji uživatelsky definovaných funkcí jsou shrnuté níž.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="ensure-a-role-assignment-was-created"></a>Ujistěte se, že byl vytvořen přiřazení role

Bez přiřazení role vytvořené v rámci rozhraní API pro správu uživatelem definované funkce nebudou mít přístup k provádět všechny akce, jako je odeslání oznámení, načítání metadat, a nastavení vypočítané hodnoty v rámci topologie.

Zkontrolujte, jestli přiřazení role existuje uživatelem definované funkce prostřednictvím rozhraní API pro správu:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Hodnota parametru | Nahradit hodnotou |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ID uživatelem definované funkce, který se má načíst přiřazení role|

Přečtěte si [vytvoření přiřazení role uživatelem definované funkce](./how-to-user-defined-functions.md), pokud neexistuje žádná přiřazení role.

### <a name="check-if-the-matcher-will-work-for-a-sensors-telemetry"></a>Zaškrtněte, pokud bude fungovat předávaný snímačů telemetrie

Následující volání rozhraní API správy služby vaše instance Azure digitální dvojče bude moci určit, zda daný předávaný platí pro daný senzoru.

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

### <a name="check-what-a-sensor-will-trigger"></a>Zkontrolujte, co se aktivuje senzoru

Následující volání rozhraní API správy služby vaše instance Azure digitální dvojče budou moct určit identifikátory uživatelem definované funkce, které budou aktivovány dané senzor příchozích telemetrických dat:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | ID senzor, který bude odesílat telemetrii |

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

Pokud není přijímání oznámení v rámci aktivované uživatelem definovanou funkci, ujistěte se, že parametr typu objektu vaší topologie odpovídá typu používaného identifikátor, který se používá.

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

1. **Omezení šířky pásma**: Pokud vaše uživatelská funkce překračuje rychlost provádění omezení uvedených v [omezení služby](./concepts-service-limits.md) článku, budou omezeny. Omezení využití sítě má za následek žádné další operace úspěšně provádění, dokud nevyprší platnost omezení.

1. **Data nebyla nalezena**: Pokud vaše uživatelská funkce se pokusí získat přístup k metadatům, která neexistuje, operace se nezdaří.

1. **Nemáte autorizaci**: Pokud už vaše uživatelem definovanou funkci přiřazení rolí, nastavení nebo nemá dostatečná oprávnění pro přístup k určitým metadat od topologie, operace se nezdaří.

## <a name="next-steps"></a>Další postup

Zjistěte, jak povolit [sledování a protokolů](../azure-monitor/platform/activity-logs-overview.md) v digitální dvojče Azure.
