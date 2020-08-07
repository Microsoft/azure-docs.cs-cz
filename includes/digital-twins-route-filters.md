---
author: baanders
description: zahrnout soubor pro možnosti filtru tras pro digitální vlákna Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: c5b78d80b1aa958af50b81dc152b4a746ce85f70
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902184"
---
| Název filtru | Popis | Filtrovat schéma textu | Podporované hodnoty | 
| --- | --- | --- | --- |
| True nebo false | Umožňuje vytvoření trasy bez filtrování nebo zakázání trasy, takže se neodesílají žádné události. | `<true/false>` | `true`= trasa je povolená bez filtrování. <br> `false`= trasa je zakázána. |
| Typ | [Typ toku událostí](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) prostřednictvím vaší digitální instance | `type = '<eventType>'` | Tady jsou možné hodnoty typu události: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Zdroj | Název instance digitálního vlákna Azure | `source = '<hostname>'`| Tady jsou možné hodnoty názvu hostitele: <br> **Pro oznámení**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Pro telemetrii**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Předmět | Popis události v kontextu výše uvedeného zdroje událostí | `subject = '<subject>'` | Tady jsou možné hodnoty předmětu: <br>**Pro oznámení**: subjekt je`<twinid>` <br> nebo formát identifikátoru URI pro předměty, které jsou jednoznačně identifikované více částmi nebo identifikátory:<br>`<twinid>/relationships/<relationshipid>`<br> **Pro telemetrii**: subjekt je cesta k komponentě (Pokud se telemetrie generuje z vlákna), jako je například `comp1.comp2` . Pokud telemetrie není vygenerována ze součásti, je pole předmětu prázdné. |
| Schéma dat | ID modelu DTDL | `dataschema = '<model-dtmi-ID>'` | **Pro telemetrii**: schéma dat je ID modelu vlákna nebo komponenty, která tuto telemetrii emituje. Například `dtmi:example:com:floor4;2`. <br>**Pro oznámení**: schéma dat se nepodporuje.|
| Typ obsahu | Typ obsahu hodnoty dat | `datacontenttype = '<contentType>'` | Typ obsahu je`application/json` |
| Specifikace verze | Verze schématu události, kterou používáte | `specversion = '<version>'` | Verze musí být `1.0` . To označuje schéma CloudEvents verze 1,0. |

Filtry je také možné kombinovat pomocí následujících operací:

| Název filtru | Filtrovat schéma textu | Příklad | 
| --- | --- | --- |
| A/NEBO | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| A/NEBO | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| Vnořené operace | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> Během období Preview je podporována pouze řetězcová rovnost (=,! =).

Když implementujete nebo aktualizujete filtr, může tato změna trvat několik minut, než se v datovém kanálu projeví.
