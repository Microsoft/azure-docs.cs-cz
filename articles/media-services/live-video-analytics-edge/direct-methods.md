---
title: Použití přímých metod v živé analýze videí v IoT Edge – Azure
description: Live video Analytics na IoT Edge zveřejňuje několik přímých metod. Přímé metody jsou založeny na konvencích popsaných v tomto tématu.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8b5c16dc72beed4ec757e48461a2fc194c113f8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656236"
---
# <a name="direct-methods"></a>Přímé metody

Live video Analytics na IoT Edge zveřejňuje několik přímých metod, které mohou být vyvolány z IoT Hub. Přímé metody představují interakci požadavek-odpověď se zařízením podobným volání HTTP v tom, že je úspěšné nebo neúspěšné (po uplynutí časového limitu zadaného uživatelem). Tento přístup je užitečný v případě scénářů, kdy se průběh okamžité akce liší podle toho, jestli zařízení bylo schopné reagovat. Další informace naleznete v tématu [pochopení a vyvolání přímých metod z IoT Hub](../../iot-hub/iot-hub-devguide-direct-methods.md).

Toto téma popisuje tyto metody a konvence.

## <a name="conventions"></a>Konvence

Přímé metody jsou založeny na následujících konvencích:

1. Přímé metody mají verzi určenou v hlavních. VEDLEJŠÍ formát (jak je znázorněno v následujícím příkladu):

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. Daná verze služby Live video Analytics v modulu IoT Edge bude podporovat všechny přímé metody až do aktuální verze. Například modul verze 1,3 bude podporovat přímé metody s verzemi 1,3, 1,2, 1,1 a 1,0.
3. Všechny přímé metody jsou synchronní.
4. Výsledky chyby následují po schématu chyb OData.
5. Názvy by měly splňovat následující omezení:
    
    * Pouze alfanumerické znaky a pomlčky, pokud nezačínají a končí pomlčkou
    * Žádné mezery
    * Maximálně 32 znaků

### <a name="example"></a>Příklad

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>Kódy chyb nejvyšší úrovně     

|Status |Kód   |Zpráva|
|---|---|---|
|400|   Důvodu chybného požadavku| Žádost není platná.|
|400|   InvalidResource|    Prostředek není platný.|
|400|   InvalidVersion| Verze rozhraní API není platná.|
|402|   ConnectivityRequired    |Modul Edge vyžaduje správné fungování cloudového připojení.|
|404|   NotFound    |Prostředek se nenašel.|
|409|   Konflikt|   Konflikt operací|
|500|   InternalServerError|    Vnitřní chyba serveru|
|503|   ServerBusy| Zaneprázdněný Server|

### <a name="detailed-error-codes"></a>Podrobné kódy chyb

Podrobná Chyba při ověřování, jako jsou třeba ověřování modulu grafu, se přidala jako podrobnosti chyby:

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|Status|    Podrobný kód   |Description|
|---|---|---|
|400|   GraphValidationError|   Obecné chyby grafu, jako jsou cykly nebo dělení na oddíly atd.|
|400|   ModuleValidationError|  Chyby ověřování specifické pro modul|
|409|   GraphTopologyInUse| Na topologii grafu stále odkazuje jedna nebo více instancí grafu.|
|409|   OperationNotAllowedInState| Požadovanou operaci nejde v aktuálním stavu provést.|
|409|   ResourceValidationError|    Odkazovaný prostředek (příklad: Asset) není v platném stavu.|

## <a name="details"></a>Podrobnosti  

### <a name="graphtopologyget"></a>GraphTopologyGet

Tato přímá metoda načte jednu topologii grafu.

#### <a name="request"></a>Žádost

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Odpověď

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Stavové kódy

| Podmínka | Stavový kód | Podrobný kód chyby |
|--|--|--|
| Entita se našla | 200 | – |
| Obecné chyby uživatele | Rozsah 400 |  |
| Entita se nenašla. | 404 |  |
| Obecné chyby serveru | Rozsah 500 |  |

### <a name="graphtopologyset"></a>GraphTopologySet

Vytvoří jedinou topologii, pokud neexistuje žádný z nich se zadaným názvem nebo aktualizace a stávající se stejným názvem.

Klíčové aspekty:

* Topologii je možné volně aktualizovat, ale neodkazuje na ni žádný graf.
* Topologii lze volně aktualizovat, pokud jsou všechny odkazy na grafy deaktivovány, dokud jsou:

    * Nově přidané parametry mají výchozí hodnoty.
    * Na odebrané parametry neodkazuje žádný graf.
* Aktualizace topologie nejsou povoleny, pokud existují aktivní grafy.

#### <a name="request"></a>Žádost

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>Odpověď

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Stavové kódy

|Podmínka  |Stavový kód    |Podrobný kód chyby|
|---|---|---|
Existující entita se aktualizovala. |200|   –|
Vytvořila se nová entita.  |201|   –|
Obecné chyby uživatele |Rozsah 400  ||
Chyby ověřování grafu |400    |GraphValidationError|
Chyby ověřování modulu|   400 |ModuleValidationError|
Obecné chyby serveru   |Rozsah 500  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

Odstraní jednu topologii grafu.

#### <a name="request"></a>Žádost

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Odpověď

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Stavové kódy

| Podmínka | Stavový kód | Podrobný kód chyby |
|--|--|--|
| Entita se odstranila | 200 | – |
| Entita se nenašla. | 204 | – |
| Obecné chyby uživatele | Rozsah 400 |  |
| Na topologii grafu odkazuje jedna nebo více instancí grafu. | 409 | GraphTopologyInUse |
| Obecné chyby serveru | Rozsah 500 |  |

### <a name="graphtopologylist"></a>GraphTopologyList

Načte seznam všech topologií grafu, které odpovídají kritériím filtru.

#### <a name="request"></a>Žádost

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Odpověď

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Podpora filtru

|Operace |Pole (y)    |Operátory|
|---|---|---|
|$orderby|name  |ASC|


#### <a name="status-codes"></a>Stavové kódy

| Podmínka | Stavový kód | Podrobný kód chyby |
|--|--|--|
| Success | 200 | – |
| Obecné chyby uživatele | Rozsah 400 |  |
| Obecné chyby serveru | Rozsah 500 |  |

### <a name="graphinstanceget"></a>GraphInstanceGet

Načte jednu instanci grafu:

#### <a name="request"></a>Žádost

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Odpověď

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>Stavové kódy

| Podmínka | Stavový kód | Podrobný kód chyby |
|--|--|--|
| Entita se našla | 200 | – |
| Obecné chyby uživatele | Rozsah 400 |  |
| Entita se nenašla. | 404 |  |
| Obecné chyby serveru | Rozsah 500 |  |

### <a name="graphinstanceset"></a>GraphInstanceSet

Vytvoří jednu instanci grafu, pokud neexistuje žádný z nich se zadaným názvem nebo aktualizace a stávající se stejným názvem.

Klíčové aspekty:

* Instance graphu se může volně aktualizovat, dokud je ve stavu deaktivováno.

    * U každého aktualizovaného grafu se znovu ověří.
* Aktualizace instancí grafu jsou částečně omezené, zatímco graf není ve stavu "neaktivní".
* Aktualizace instancí grafu nejsou povoleny v aktivních grafech.

#### <a name="request"></a>Žádost

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>Odpověď

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>Stavové kódy

| Podmínka | Stavový kód | Podrobný kód chyby |
|--|--|--|
| Existující entita se aktualizovala. | 200 | – |
| Vytvořila se nová entita. | 201 | – |
| Obecné chyby uživatele | Rozsah 400 |  |
| Chyby ověřování grafu | 400 | GraphValidationError |
| Chyby ověřování modulu | 400 | ModuleValidationError |
| Chyby ověřování prostředků | 409 | ResourceValidationError |
| Obecné chyby serveru | Rozsah 500 |  |  |

### <a name="graphinstancedelete"></a>GraphInstanceDelete

Odstraní jednu instanci grafu.

Klíčové aspekty:

* Odstranit lze pouze deaktivované grafy.

#### <a name="request"></a>Žádost

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Odpověď

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Stavové kódy

| Podmínka | Stavový kód | Podrobný kód chyby |
|--|--|--|
| Graf se úspěšně odstranil. | 200 | – |
| Graf se nenašel. | 204 | – |
| Obecné chyby uživatele | Rozsah 400 |  |
| Graf není ve stavu Zastaveno. | 409 | OperationNotAllowedInState |
| Obecné chyby serveru | Rozsah 500 |  |

### <a name="graphinstancelist"></a>GraphInstanceList

To se podobá GraphTopologyList. Umožňuje použít k zobrazení výčtu instancí grafu.
Načte seznam všech instancí grafů, které odpovídají kritériím filtru.

#### <a name="request"></a>Žádost

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Odpověď

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Podpora filtru

|Operace  |   Pole (y)|   Operátory|
|---|---|---|
|$orderby|  name|   ASC|

#### <a name="status-codes"></a>Stavové kódy

| Podmínka | Stavový kód | Podrobný kód chyby |
|--|--|--|
| Success | 200 | – |
| Obecné chyby uživatele | Rozsah 400 |  |
| Obecné chyby serveru | Rozsah 500 |  |

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

Aktivuje jednu instanci grafu. 

Klíčové aspekty

* Metoda vrací pouze v případě, že je graf aktivovaný. 
* Při aktivaci aplikace Graph předpokládá stav "Aktivace".

    * Operace list/Get v grafu vrátila graf ve správném stavu.
* Idempotence

    * Spuštění grafu se stavem "Aktivace" se chová stejným způsobem jako při deaktivaci grafu (to znamená, že bloky volání až do aktivace grafu).
    * Aktivace grafu pro stav "aktivní" se úspěšně vrátí.

#### <a name="request"></a>Žádost

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Odpověď

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Stavové kódy

| Podmínka | Stavový kód | Podrobný kód chyby |
|--|--|--|
| Graf se úspěšně aktivoval. | 200 | – |
| Vytvořila se nová entita. | 201 | – |
| Obecné chyby uživatele | Rozsah 400 |  |
| Chyby ověřování modulu | 400 | ModuleValidationError |
| Chyby ověřování prostředků | 409 | ResourceValidationError |
| Graf je ve stavu deaktivace. | 409 | OperationNotAllowedInState |
| Obecné chyby serveru | Rozsah 500 |  |

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

Deaktivuje jednu instanci grafu. Deaktivace grafu bez problémů deaktivuje zpracování médií a zajistí, že všechny události a média, která jsou na hraničních počítačích plynule uložená, se pokaždé uloží do cloudu, kdykoli je to možné.

Klíčové aspekty:

* Metoda se vrátí jenom při deaktivaci grafu.
* Při deaktivaci se předpokládá, že se v grafu předpokládá stav "deaktivace".

    * Operace list/Get v grafu vrátila graf ve správném stavu.
    * Zastavení se dokončí jenom v případě, že se všechna média nahrála do cloudu.
* Idempotence

    * Deaktivace grafu se stavem deaktivace se chová stejně jako v případě deaktivace grafu (to znamená, že bloky volání až do deaktivace grafu).
    * Deaktivace grafu pro stav "neaktivní" se okamžitě vrátí.

#### <a name="request"></a>Žádost

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Odpověď

```
{
    "status": 200,
    "payload": null
}
```

| Podmínka | Stavový kód | Podrobný kód chyby |
|--|--|--|
| Graf se úspěšně aktivoval. | 200 | – |
| Vytvořila se nová entita. | 201 | – |
| Obecné chyby uživatele | Rozsah 400 |  |
| Graf je ve stavu aktivace | 409 | OperationNotAllowedInState |
| Obecné chyby serveru | Rozsah 500 |  |

## <a name="next-steps"></a>Další kroky

[Schéma konfigurace dvojčat modulů](module-twin-configuration-schema.md)
