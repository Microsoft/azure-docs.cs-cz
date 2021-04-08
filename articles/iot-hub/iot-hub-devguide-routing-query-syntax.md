---
title: Dotaz na směrování zpráv v Azure IoT Hub | Microsoft Docs
description: Přečtěte si o dotazovacím jazyce pro směrování zpráv IoT Hub, který můžete použít k použití bohatých dotazů na zprávy pro příjem dat, která vás zajímají.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 83c290adea02915db1dc52bd359b4d3165611522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92547703"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Syntaxe dotazu směrování zpráv IoT Hubu

Směrování zpráv umožňuje uživatelům směrovat různé datové typy, konkrétně zprávy telemetrie zařízení, události životního cyklu zařízení a události s dvojitou změnou událostí, do různých koncových bodů. Na tato data můžete také použít formátované dotazy a teprve potom je směrovat, abyste získali data, která vás zajímají. Tento článek popisuje dotazovací jazyk směrování zpráv IoT Hub a poskytuje některé běžné vzory dotazů.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Směrování zpráv vám umožní dotazovat se na vlastnosti zprávy a text zprávy a také na vlastní značky zařízení a vlastnosti, které jsou v zařízení. Pokud tělo zprávy není JSON, směrování zpráv stále může směrovat zprávu, ale dotazy nelze použít na tělo zprávy.  Dotazy jsou popsány jako logické výrazy, kde logická hodnota true provede dotaz úspěšně, který směruje všechna příchozí data, a logická hodnota false neprojde dotazem a nebudou směrována žádná data. Pokud je výraz vyhodnocen jako null nebo nedefinován, je považován za false a v případě selhání bude vygenerována chyba v IoT Hub [trasy protokolu prostředků](monitor-iot-hub-reference.md#routes) . Syntaxe dotazu musí být správná, aby bylo možné trasu uložit a vyhodnotit.  

## <a name="message-routing-query-based-on-message-properties"></a>Dotaz na směrování zpráv na základě vlastností zprávy 

IoT Hub definuje [společný formát](iot-hub-devguide-messages-construct.md) pro všechny zprávy typu zařízení-Cloud pro interoperabilitu napříč protokoly. IoT Hub zpráva předpokládá následující reprezentace zprávy ve formátu JSON. Vlastnosti systému jsou přidány pro všechny uživatele a identifikují obsah zprávy. Uživatelé můžou do zprávy selektivně přidat vlastnosti aplikace. Doporučujeme používat jedinečné názvy vlastností, jako je IoT Hub zasílání zpráv ze zařízení na Cloud nerozlišuje velká a malá písmena. Například pokud máte více vlastností se stejným názvem, IoT Hub bude odeslána pouze jedna z vlastností.  

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

Vlastnosti systému vám pomůžou identifikovat obsah a zdroj zpráv. 

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| Třída | řetězec | Uživatel Určuje typ obsahu zprávy. Pokud chcete pro tělo zprávy zapnout dotaz, měla by být tato hodnota nastavená na Application/JSON. |
| contentEncoding | řetězec | Uživatel Určuje typ kódování zprávy. Povolené hodnoty jsou UTF-8, UTF-16, UTF-32, pokud je contentType nastavený na Application/JSON. |
| iothub-ID zařízení-připojení | řetězec | Tato hodnota je nastavena IoT Hub a identifikuje ID zařízení. K dotazování použijte `$connectionDeviceId` . |
| iothub – enqueuedtime | řetězec | Tato hodnota je nastavena IoT Hub a představuje skutečný čas enqueuing zprávy ve standardu UTC. K dotazování použijte `enqueuedTime` . |
| DT-DataSchema | řetězec |  Tuto hodnotu nastavuje služba IoT Hub pro zprávy ze zařízení do cloudu. Obsahuje ID modelu zařízení nastavené v připojení zařízení. K dotazování použijte `$dt-dataschema` . |
| DT – předmět | řetězec | Název součásti odesílající zprávy typu zařízení-Cloud. K dotazování použijte `$dt-subject` . |

Jak je popsáno v [IoT Hub zprávy](iot-hub-devguide-messages-construct.md), ve zprávě jsou další vlastnosti systému. Kromě výše uvedených vlastností v předchozí tabulce můžete také zadat dotaz na **connectionDeviceId**, **connectionModuleId**.

### <a name="application-properties"></a>Vlastnosti aplikace

Vlastnosti aplikace jsou uživatelsky definované řetězce, které lze přidat do zprávy. Tato pole jsou volitelná.  

### <a name="query-expressions"></a>Výrazy dotazů

Dotaz na vlastnosti systému zpráv musí být s `$` symbolem předpony. Dotazy na vlastnosti aplikace jsou k dispozici s jejich názvem a neměly by obsahovat předponu `$` . Pokud název vlastnosti aplikace začíná `$` na, IoT Hub ho vyhledat ve vlastnostech systému a nebude nalezen, bude vypadat ve vlastnostech aplikace. Například: 

Dotaz na systémovou vlastnost contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Dotaz na vlastnost processingPath aplikace:

```sql
processingPath = 'hot'
```

Chcete-li kombinovat tyto dotazy, můžete použít logické výrazy a funkce:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

V části [výraz a podmínky](iot-hub-devguide-query-language.md#expressions-and-conditions)se zobrazí úplný seznam podporovaných operátorů a funkcí.

## <a name="message-routing-query-based-on-message-body"></a>Dotaz na směrování zpráv na základě textu zprávy

Pokud chcete povolit dotazování na tělo zprávy, měla by být zpráva ve formátu JSON, který je kódovaný v kódování UTF-8, UTF-16 nebo UTF-32. `contentType`Musí být nastavené na `application/JSON` a `contentEncoding` na jedno z podporovaných kódování UTF ve vlastnosti System. Nejsou-li tyto vlastnosti zadány, IoT Hub nevyhodnotí výraz dotazu v textu zprávy. 

Následující příklad ukazuje, jak vytvořit zprávu s správně vytvořeným a kódovaným textem JSON: 

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
> Ukazuje, jak zpracovat kódování těla v JavaScriptu. Pokud chcete vidět ukázku v jazyce C#, Stáhněte si ukázky pro [Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozbalte soubor master.zip. Soubor programu *SimulatedDevice* řešení sady Visual Studio ukazuje, jak zakódovat a odeslat zprávy do IoT Hub. Tato ukázka se používá pro testování směrování zpráv, jak je vysvětleno v [kurzu směrování zpráv](tutorial-routing.md). V dolní části programu. cs má také metodu, jak číst v jednom ze zakódovaných souborů, dekódovat ho a zapsat ho jako ASCII, abyste ho mohli přečíst. 


### <a name="query-expressions"></a>Výrazy dotazů

Dotaz na text zprávy musí být s předponou `$body` . Ve výrazu dotazu můžete použít odkaz na tělo, odkaz na pole textu nebo několik odkazů na text. Výraz dotazu může také kombinovat odkaz na tělo s vlastnostmi systému zpráv a odkazem na vlastnosti aplikace zprávy. Například následující jsou všechny platné výrazy dotazu: 

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

## <a name="message-routing-query-based-on-device-twin"></a>Dotaz na směrování zpráv na základě vlákna zařízení 

Směrování zpráv vám umožní dotazovat se na značky a vlastnosti v [zařízení](iot-hub-devguide-device-twins.md) , které jsou objekty JSON. Podporuje se i dotaz na nevlákenný modul. Ukázka dvojitých značek a vlastností zařízení je uvedená níže.

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

Dotaz na vlákna zprávy musí být s předponou `$twin` . Výraz dotazu může také kombinovat odkaz na dvojitou značku nebo vlastnost s odkazem na tělo, vlastnostmi systému zprávy a odkazem na vlastnosti aplikace zprávy. Doporučujeme použít v tagu a vlastnostech jedinečné názvy, protože dotaz nerozlišuje velká a malá písmena. To platí pro vlákna zařízení i pro vlákna modulu. Také upustí od použití `twin` , `$twin` , `body` nebo `$body` jako názvů vlastností. Například následující jsou všechny platné výrazy dotazu: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Dotaz na směrování na základě textu nebo zařízení, který je v určité době v názvu datové části nebo vlastnosti, není podporován.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [směrování zpráv](iot-hub-devguide-messages-d2c.md).
* Vyzkoušejte [kurz směrování zpráv](tutorial-routing.md).
