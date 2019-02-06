---
title: Dotaz na směrování zpráv služby Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – syntaxe dotazu pro směrování zpráv ve službě Azure IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 94d3599fe919cf648be7115be68002d2aa458ee3
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744837"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Syntaxi dotazů směrování zpráv služby IoT Hub

Směrování zpráv umožňuje uživatelům totiž směrovat různé datové typy, telemetrická data zprávy typu zařízení a události životního cyklu zařízení, dvojče zařízení události změn do různých koncových bodů. Bohaté dotazy můžete také použít k těmto datům před směrování pro příjem dat, které vás zajímají. Tento článek popisuje směrování dotazovací jazyk zpráv služby IoT Hub a poskytuje některé běžné vzory dotazů.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Směrování zpráv umožňuje dotazování na vlastnosti zprávy a tělo zprávy stejně jako značky dvojčat zařízení a vlastnosti dvojčete zařízení. Pokud tělo zprávy není JSON, směrování zpráv může stále směrovat zprávy, ale dotazy nejde použít pro text zprávy.  Dotazy jsou popsány jako logické výrazy, kde je logická hodnota PRAVDA dotaz úspěšný, která směruje všechna příchozí data a logickou hodnotu false selže dotaz a směrovat žádná data. Pokud je výraz vyhodnocen jako null nebo nedefinovaný, je považován za hodnotu false a vygeneruje se chyba v diagnostických protokolů v případě selhání. Syntaxe dotazu musí být správná pro trasy, která má být uloženy a vyhodnotí.  

## <a name="message-routing-query-based-on-message-properties"></a>Směrování dotazů zprávy podle vlastnosti zprávy 

Definuje služby IoT Hub [běžný formát](iot-hub-devguide-messages-construct.md) pro všechna zařízení cloud zasílání zpráv pro interoperabilitu mezi protokoly. Zprávy služby IoT Hub se předpokládá následující reprezentaci JSON zprávy. Vlastnosti systému jsou přidány pro všechny uživatele a identifikaci obsahu zprávy. Uživatelé mohou přidávat vlastnosti aplikace selektivně ke zprávě. Doporučujeme použít názvy vlastností jedinečný, protože zasílání zpráv typu zařízení cloud služby IoT Hub není malá a velká písmena. Například pokud máte více vlastností se stejným názvem, IoT Hub se odeslat pouze jedna z vlastností.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Systémové vlastnosti

Vlastnosti systému identifikaci obsahu a zdrojové zpráv. 

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| contentType | řetězec | Uživatel Určuje typ obsahu zprávy. Povolit dotazu na obsah zprávy, by měl být tato hodnota nastavena application/JSON. |
| contentEncoding | řetězec | Uživatel Určuje typ kódování zprávy. Povolené hodnoty jsou UTF-8, UTF-16, UTF-32, pokud typ obsahu je nastaven na application/JSON. |
| iothub-connection-device-id | řetězec | Tato hodnota nastavena ve službě IoT Hub a identifikuje ID zařízení. Dotaz, použijte `$connectionDeviceId`. |
| iothub-enqueuedtime | řetězec | Tato hodnota nastavena ve službě IoT Hub a představuje skutečný čas zařadit zprávu ve formátu UTC. Dotaz, použijte `enqueuedTime`. |

Jak je popsáno v [zpráv IoT Hub](iot-hub-devguide-messages-construct.md), existují další systémové vlastnosti ve zprávě. Kromě **contentType**, **contentEncoding**, a **enqueuedTime**, **connectionDeviceId** a  **connectionModuleId** může být dotazována také.

### <a name="application-properties"></a>Vlastnosti aplikace

Vlastnosti aplikace jsou uživatelem definované řetězce, které lze přidat do zprávy. Tato pole jsou volitelná.  

### <a name="query-expressions"></a>Výrazy dotazů

Dotaz na vlastnosti zprávy systému je potřeba mít předponu `$` symbol. Dotazy na vlastnosti aplikace, ke kterým se přistupuje pomocí názvu a by neměly mít předponu `$`symbol. Pokud název vlastnosti aplikace začíná `$`, pak služby IoT Hub se ji vyhledejte ve vlastnostech systému a nebyl nalezen, potom bude vypadat ve vlastnostech aplikace. Příklad: 

K dotazování na vlastnosti contentEncoding systému 

```sql
$contentEncoding = 'UTF-8'
```

Dotaz na vlastnost processingPath aplikace:

```sql
processingPath = 'hot'
```

Kombinace těchto dotazů, můžete logické výrazy a funkce:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Úplný seznam podporovaných operátory a funkce jsou uvedené v [výraz a podmínky](iot-hub-devguide-query-language.md#expressions-and-conditions)

## <a name="message-routing-query-based-on-message-body"></a>Zpráva směrovací dotaz založený na textu zprávy 

Povolit dotazování v textu zprávy, musí být zpráva v JSON zakódován do kódování UTF-8, UTF-16 nebo UTF-32. `contentType` Musí být nastaveno na `application/JSON` a `contentEncoding` na jednu z podporovaných kódování UTF ve vlastnosti systému. Pokud tyto vlastnosti nejsou zadané, nebudou služby IoT Hub vyhodnocení výrazu dotazu na obsah zprávy. 

Následující příklad ukazuje, jak vytvořit zprávu s správně formát a je kódovaný text JSON: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

### <a name="query-expressions"></a>Výrazy dotazů

Dotaz na text zprávy musí mít předponu `$body`. Text odkazu, odkaz na pole text nebo více odkazů na text můžete použít ve výrazu dotazu. Výraz dotazu můžete také kombinovat odkaz tělo zprávy systému vlastnostmi a referenční dokumentace k vlastnostem zprávy aplikace. Následující příklad, jsou všechny výrazy platný dotaz: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Zpráva směrovací dotaz založený na dvojče zařízení 

Směrování zpráv umožňuje dotazování na [dvojče zařízení](iot-hub-devguide-device-twins.md) značek a vlastností, které jsou objekty JSON. Všimněte si, že při dotazování u dvojče modulu se nepodporuje. Ukázka značek dvojče zařízení a vlastností je uveden níže.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Výrazy dotazů

Dotaz na text zprávy musí mít předponu `$twin`. Výraz dotazu, můžete také kombinovat dvojčete značku nebo vlastnost odkaz s text odkazu, vlastnosti zprávy systému a referenční dokumentace k vlastnostem zprávy aplikace. Doporučujeme používat jedinečné názvy značek a vlastnosti, protože dotaz není malá a velká písmena. Také nepoužívejte `twin`, `$twin`, `body`, nebo `$body`, jako názvy vlastností. Následující příklad, jsou všechny výrazy platný dotaz: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>Další postup

* Další informace o [směrování zpráv](iot-hub-devguide-messages-d2c.md).
* Zkuste [zprávy směrování kurzu](tutorial-routing.md).