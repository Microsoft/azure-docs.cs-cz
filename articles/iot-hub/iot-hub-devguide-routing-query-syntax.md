---
title: Dotaz na směrování zpráv služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Přečtěte si o jazyce směrování zpráv služby IoT Hub, který můžete použít k použití rozšířených dotazů na zprávy, abyste mohli přijímat data, která jsou pro vás důležitá.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: b76ef431e4c0ad63929378c1f48c6ab06776cb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271106"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Syntaxe dotazu směrování zpráv IoT Hubu

Směrování zpráv umožňuje uživatelům směrovat různé datové typy konkrétně, telemetrické zprávy zařízení, události životního cyklu zařízení a události změny dvojčete zařízení do různých koncových bodů. Můžete také použít rozšířené dotazy na tato data před směrováním je přijímat data, která jsou pro vás důležité. Tento článek popisuje směrovací dotazový jazyk zpráv služby IoT Hub a poskytuje některé běžné vzory dotazů.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Směrování zpráv umožňuje dotaz na vlastnosti zprávy a text zprávy, stejně jako značky dvojčete zařízení a vlastnosti dvojčete zařízení. Pokud tělo zprávy není JSON, směrování zpráv může stále směrovat zprávu, ale dotazy nelze použít na text zprávy.  Dotazy jsou popsány jako logické výrazy, kde logická hodnota true způsobí, že dotaz bude úspěšný, který směruje všechna příchozí data, a logická hodnota false neselže dotaz a žádná data nebudou směrována. Pokud výraz vyhodnotí na null nebo undefined, je považován za false a chyba bude generována v diagnostických protokolech v případě selhání. Syntaxe dotazu musí být správná, aby trasa byla uložena a vyhodnocena.  

## <a name="message-routing-query-based-on-message-properties"></a>Směrovací dotaz zprávy založený na vlastnostech zprávy 

Služba IoT Hub definuje [společný formát](iot-hub-devguide-messages-construct.md) pro všechny zasílání zpráv mezi zařízeními a cloudy pro interoperabilitu napříč protokoly. Zpráva služby IoT Hub předpokládá následující reprezentaci JSON zprávy. Vlastnosti systému jsou přidány pro všechny uživatele a identifikovat obsah zprávy. Uživatelé mohou selektivně přidat vlastnosti aplikace do zprávy. Doporučujeme používat jedinečné názvy vlastností, protože zasílání zpráv mezi zařízeními a cloudem služby IoT Hub nerozlišuje malá a velká písmena. Například pokud máte více vlastností se stejným názvem, služby IoT Hub odešle pouze jednu z vlastností.  

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

Vlastnosti systému pomáhají identifikovat obsah a zdroj zpráv. 

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Contenttype | řetězec | Uživatel určuje typ obsahu zprávy. Chcete-li povolit dotaz na text zprávy, tato hodnota by měla být nastavena aplikace/JSON. |
| contentEncoding | řetězec | Uživatel určuje typ kódování zprávy. Povolené hodnoty jsou UTF-8, UTF-16, UTF-32, pokud je contentType nastaven na aplikaci/JSON. |
| iothub-connection-device-id | řetězec | Tato hodnota je nastavena službou IoT Hub a identifikuje ID zařízení. Chcete-li `$connectionDeviceId`dotazovat, použijte . |
| iothub-enqueuedtime | řetězec | Tato hodnota je nastavena službou IoT Hub a představuje skutečný čas enqueuing zprávy v UTC. Chcete-li `enqueuedTime`dotazovat, použijte . |
| název iothub-interface | řetězec | Tato hodnota je nastavena uživatelem a představuje název rozhraní digitální dvojče, který implementuje zprávu telemetrie. Chcete-li `$interfaceName`dotazovat, použijte . Tato funkce je dostupná jako součást veřejné verze [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). |

Jak je popsáno ve [zprávách služby IoT Hub](iot-hub-devguide-messages-construct.md), existují další vlastnosti systému ve zprávě. Kromě **contentType**, **contentEncoding**, a **enqueuedTime**, **connectionDeviceId** a **connectionModuleId** lze také dotazovat.

### <a name="application-properties"></a>Vlastnosti aplikace

Vlastnosti aplikace jsou uživatelem definované řetězce, které lze přidat do zprávy. Tato pole jsou nepovinná.  

### <a name="query-expressions"></a>Výrazy dotazu

Dotaz na vlastnosti systému zpráv musí `$` být předponou se symbolem. Dotazy na vlastnosti aplikace jsou přístupné s jejich názvem `$`a neměly by být předponou se symbolem. Pokud název vlastnosti aplikace `$`začíná na , bude služba IoT Hub hledat ve vlastnostech systému a není nalezena, bude vypadat ve vlastnostech aplikace. Například: 

Dotaz na obsah systémových vlastnostíKódování 

```sql
$contentEncoding = 'UTF-8'
```

Dotaz na zpracování vlastností aplikaceCesta:

```sql
processingPath = 'hot'
```

Chcete-li kombinovat tyto dotazy, můžete použít logické výrazy a funkce:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Úplný seznam podporovaných operátorů a funkcí je zobrazen ve [výrazu a podmínky](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Směrovací dotaz zprávy založený na textu zprávy

Chcete-li povolit dotazování na text zprávy, zpráva by měla být v JSON kódované v UTF-8, UTF-16 nebo UTF-32. Musí `contentType` být nastavena na `application/JSON` a `contentEncoding` na jeden z podporovaných kódování UTF ve vlastnosti systému. Pokud tyto vlastnosti nejsou zadány, služba IoT Hub nevyhodnotí výraz dotazu v textu zprávy. 

Následující příklad ukazuje, jak vytvořit zprávu se správně vytvořeným a kódovaným tělem JSON: 

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

> [!NOTE] 
> To ukazuje, jak zacházet kódování těla v javascriptu. Pokud chcete zobrazit ukázku v C#, stáhněte si [ukázky Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozbalte soubor master.zip. Visual Studio řešení *SimulatedDevice*je Program.cs soubor ukazuje, jak kódovat a odesílat zprávy do centra IoT Hub. Jedná se o stejnou ukázku použitou pro testování směrování zpráv, jak je vysvětleno v [kurzu směrování zpráv](tutorial-routing.md). V dolní části Program.cs má také metodu číst v jednom z kódovaných souborů, dekódovat a zapisovat zpět jako ASCII, takže si můžete přečíst. 


### <a name="query-expressions"></a>Výrazy dotazu

Dotaz na text zprávy musí být `$body`předponou s . Ve výrazu dotazu můžete použít odkaz na tělo, body array nebo více odkazů na tělo. Výraz dotazu může také kombinovat textovou referenci s vlastnostmi systému zpráv a odkaz na vlastnosti aplikace zpráv. Například následující jsou všechny platné výrazy dotazu: 

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

## <a name="message-routing-query-based-on-device-twin"></a>Směrovací dotaz zprávy založený na dvojčeti zařízení 

Směrování zpráv umožňuje dotaz na značky a vlastnosti [Dvojče zařízení,](iot-hub-devguide-device-twins.md) což jsou objekty JSON. Dotazování na dvojče modulu je také podporováno. Ukázka značek a vlastností dvojčete zařízení je uvedena níže.

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

### <a name="query-expressions"></a>Výrazy dotazu

Dotaz na dvojče zprávy musí být `$twin`předponou s . Výraz dotazu může také kombinovat značku dvojčete nebo odkaz na vlastnosti s odkazem na text, vlastnosti systému zpráv a odkaz na vlastnosti aplikace zprávy. Doporučujeme používat jedinečné názvy ve značkách a vlastnostech, protože dotaz nerozlišuje malá a velká písmena. To platí pro dvojčata zařízení a dvojčata modulu. Také zdržet `twin` `$twin`se `body`používání `$body`, , , nebo , jako názvy vlastností. Například následující jsou všechny platné výrazy dotazu: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Směrovací dotaz na tělo nebo dvojče zařízení s tečkou v datové části nebo názvu vlastnosti není podporován.

## <a name="next-steps"></a>Další kroky

* Informace o [směrování zpráv](iot-hub-devguide-messages-d2c.md).
* Vyzkoušejte [kurz směrování zpráv](tutorial-routing.md).
