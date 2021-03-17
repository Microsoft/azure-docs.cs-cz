---
author: baanders
description: zahrnout soubor pro možnosti filtru tras pro digitální vlákna Azure
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: d93f484e318c10489eb1db3e9c65c6e0c7479c90
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98859944"
---
| Název filtru | Popis | Filtrovat schéma textu | Podporované hodnoty | 
| --- | --- | --- | --- |
| True nebo false | Umožňuje vytvoření trasy bez filtrování nebo zakázání trasy, takže se neodesílají žádné události. | `<true/false>` | `true` = trasa je povolená bez filtrování. <br> `false` = trasa je zakázána. |
| Typ | [Typ toku událostí](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) prostřednictvím vaší digitální instance | `type = '<eventType>'` | Tady jsou možné hodnoty typu události: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Zdroj | Název instance digitálního vlákna Azure | `source = '<hostname>'`| Tady jsou možné hodnoty názvu hostitele: <br> **Pro oznámení**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **Pro telemetrii**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| Předmět | Popis události v kontextu výše uvedeného zdroje událostí | `subject = '<subject>'` | Tady jsou možné hodnoty předmětu: <br>**Pro oznámení**: subjekt je `<twinid>` <br> nebo formát identifikátoru URI pro předměty, které jsou jednoznačně identifikované více částmi nebo identifikátory:<br>`<twinid>/relationships/<relationshipid>`<br> **Pro telemetrii**: subjekt je cesta k komponentě (Pokud se telemetrie generuje z vlákna), jako je například `comp1.comp2` . Pokud telemetrie není vygenerována ze součásti, je pole předmětu prázdné. |
| Schéma dat | ID modelu DTDL | `dataschema = '<model-dtmi-ID>'` | **Pro telemetrii**: schéma dat je ID modelu vlákna nebo komponenty, která tuto telemetrii emituje. Například `dtmi:example:com:floor4;2`. <br>**Pro oznámení (vytvořit/odstranit)**: ke schématu dat je možné přistupovat v těle oznámení na adrese `$body.$metadata.$model` . <br>**Pro oznámení (aktualizace)**: k datovému schématu se dá v těle oznámení na adrese `$body.modelId`|
| Typ obsahu | Typ obsahu hodnoty dat | `datacontenttype = '<contentType>'` | Typ obsahu je `application/json` |
| Specifikace verze | Verze schématu události, kterou používáte | `specversion = '<version>'` | Verze musí být `1.0` . To označuje schéma CloudEvents verze 1,0. |
| Tělo oznámení | Odkaz na libovolnou vlastnost v `data` poli oznámení | `$body.<property>` | Příklady oznámení naleznete v tématu [*How to: rerozuměl data události*](../articles/digital-twins/how-to-interpret-event-data.md) . Na libovolnou vlastnost v `data` poli se dá odkazovat pomocí `$body`

Následující datové typy jsou podporovány jako hodnoty vrácené odkazy na výše uvedená data:

| Datový typ | Příklad |
|-|-|-|
|**Řetězec**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**Integer**|`$body.errorCode > 200`|
|**Klepat**|`$body.temperature <= 5.5`|
|**Logick**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

Při definování filtrů tras se podporují následující operátory:

|Rodina|Operátory|Příklad|
|-|-|-|
|Logické|A, NEBO ()|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Porovnání|<, <=, >, >=, =,! =|`$body.temperature <= 5.5`

Při definování filtrů tras jsou podporovány následující funkce:

|Funkce|Popis|Příklad|
|--|--|--|
|STARTS_WITH (x, y)|Vrátí hodnotu true, pokud hodnota `x` začíná řetězcem `y` .|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH (x, y) | Vrátí hodnotu true, pokud hodnota `x` končí řetězcem `y` .|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|OBSAHUJE (x, y)| Vrátí hodnotu true, pokud hodnota `x` obsahuje řetězec `y` .|`CONTAINS(subject, '<twinID>')`|

Když implementujete nebo aktualizujete filtr, může tato změna trvat několik minut, než se v datovém kanálu projeví.