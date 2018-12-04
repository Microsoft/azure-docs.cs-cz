---
title: Time Series dotazu | Dokumentace Microsoftu
description: Axure Time Series Insights (preview) čas řady dotazů
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: f5370d354833e9507d0794d7bc407a7ea2294e1a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855848"
---
# <a name="time-series-query"></a>Time Series dotazu

**Time Series dotazu** (TSQ) usnadňuje compute a načíst data časové řady, které jsou uloženy v Time Series Insights (TSI) ve velkém měřítku. TSQ využívá výpočetní definice transformace a načtení dat ze služby storage. Dělá to z **čas řady modely** (TSM) nebo prostřednictvím definice vložené proměnné.

TSQ načítá data dvěma různými způsoby. TSQ může načítat data zjištěná od poskytovatele zdroj nebo objem dat jde snížit, nebo můžete rekonstrukce signály využití zadanou metodu tak, aby zákazníci provádět operace transformace, kombinovat a provádí výpočty datech časových řad.

Přehledy dat časových řad je možný přes nativní aktualizace TSI Průzkumníka nebo veřejné rozhraní API povrchu. TSQ také nabízí jazyk výrazů, takže je můžete vytvářet vlastní pokročilé dotazy služby TSI. Tady jsou naše cíle s TSQ:

![Cíl][1]

## <a name="core-apis"></a>Rozhraní API Core

V následující tabulce jsou základní rozhraní API podporuje.

![tsq][2]

### <a name="getmodelsettings-api"></a>getModelSettings rozhraní API

Rozhraní API getModelSettings se používá k vrácení automaticky vytvořený model pro klíč oddílu prostředí.

GetModelSettings rozhraní API mají následující parametry:

* *název*: název modelu chcete načíst.
* *timeSeriesIdProperties*

  * *Jméno*
  * *type*

* *defaultTypeID*

#### <a name="getmodelsettings-json-request-and-response-example"></a>Příklad JSON žádostí a odpovědí getModelSettings

Zadaný požadavek GET HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/modelSettings?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

Odpověď:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "someType1",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

### <a name="gettypes-api"></a>getTypes rozhraní API

GetTypes rozhraní API vrátí všechny časové řady typů a jejich přidružené proměnné pro model.

GetTypes rozhraní API mají následující parametry:

* *typeId*: neměnné jedinečný identifikátor typu.
* *timeSeriesId*: **ID řady času** je jedinečný klíč pro data v rámci datového proudu událostí a modelu. Tento klíč je TSI používá při vytváření oddílů data.
* *Popis*: popis typu.
* *hierarchyIds*: pole přidružené hierarchyIds typu.
* *instanceFields*:
  * *Stav*
  * *Město*

#### <a name="gettypes-json-request-and-response-example"></a>Příklad JSON žádostí a odpovědí getTypes

Zadaný požadavek POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/types/$batch?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

S následujícími JSON text a proměnné atributy:

| Atribut | Požadované nebo volitelné |
| --- | --- |
| **Typ**  |  Požaduje se  |
| **Filtr**  |  Nepovinné |
| **value**  | Hodnotu null nebo není zadaný  |
| **interpolace**  |  Nepovinné |
| **Agregace**  |  Požaduje se |

```JSON
{
    "get": null,
    "put": [
        {
            "name": "SampleType",
            "description": "This is type 2",
            "variables": {
                "Avg Temperature": {
                    "kind": "numeric",
                    "filter": null,
                    "value": { "tsx": "$event.temperature.Double" },
                    "interpolation": "None",
                    "aggregation": {"tsx": "avg($value)"}
                },
                "Count Temperature": {
                    "kind": "aggregate",
                    "filter": null,
                    "value": null,
                    "interpolation": "None",
                    "aggregation": {"tsx": "count()"}
                },
                "Min Temperature": {
                    "kind": "aggregate",
                    "filter": null,
                    "value": null,
                    "interpolation": "None",
                    "aggregation": {"tsx": "min($event.temperature)"}
                },
            }
        }
    ]
}
```

Odpověď:

```JSON
{
    "get": null,
    "put": [
        {
            "timeSeriesType": {
                "id": "fc4f526c-da6e-4b85-87f7-16f6cf9b69be",
                "name": "type2",
                "description": "This is type 2",
                "variables": {
                    "Avg Temperature": {
                        "kind": "numeric",
                        "filter": null,
                        "value": { "tsx": "$event.temperature.Double" },
                        "interpolation": "None",
                        "aggregation": {"tsx": "avg($value)"}
                    },
                    "Count Temperature": {
                        "kind": "aggregate",
                        "filter": null,
                        "value": null,
                        "interpolation": "None",
                        "aggregation": {"tsx": "count()"}
                    },
                    "Min Temperature": {
                        "kind": "aggregate",
                        "filter": null,
                        "value": null,
                        "interpolation": "None",
                        "aggregation": {"tsx": "min($event.temperature)"}
                    }
                }
            },
            "error": null
        }
    ]
}
```

### <a name="gethierarchies-api"></a>getHierarchies rozhraní API

GetHierarchies rozhraní API vrátí všechny časové řady hierarchie a všechny jejich související pole cesty.

GetHierarchies rozhraní API mají následující parametry:

* *ID*: jednoznačně identifikující klíč pro hierarchii.
* *název*: název hierarchie
* *Zdroj*
* *instanceFields*
  * *Rok*
  * *Měsíc*

#### <a name="gethierarchies-json-request-and-response-example"></a>Příklad JSON žádostí a odpovědí getHierarchies

Zadaný požadavek POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/hierarchies/$batch?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

S text JSON:

```JSON
{
    "get": null,
    "put": [
        {
            "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
            "name": "Location",
            "source": {
                "instanceFieldNames": [
                    "state",
                    "city"
                ]
            }
        }
    ]
}
```

Odpověď:

```JSON
{
    "get": null,
    "put": [
        {
            "hierarchy": {
                "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
                "name": "Location",
                "source": {
                    "instanceFieldNames": [
                        "state",
                        "city"
                    ]
                }
            },
            "error": null
        }
    ]
}
```

### <a name="getinstances-api"></a>getInstances rozhraní API

GetInstances rozhraní API se používá k vrácení všech instancí řady času a jejich přidružené instance pole.

GetInstances rozhraní API mají následující parametry:

* *název*: název přidružený k instanci, použít pro dotazování, stejně jako nahrazením uživatelské prostředí
* *typeId*: jednoznačně identifikující klíč typu.
* *timeSeriesId*: **ID řady času** je jedinečný klíč pro data v rámci datového proudu událostí a modelu. Tento klíč je TSI používá při vytváření oddílů data.
* *Popis*: popis instance.
* *hierarchyIds*: pole jednoho nebo více hierarchyIds jednoznačně definující každá hierarchie.
* *instanceFields*:
  * *Stav*
  * *Město*

#### <a name="getinstances-json-request-and-response-example"></a>Příklad JSON žádostí a odpovědí getInstances

Zadaný požadavek POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/instances/$batch?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

S text JSON:

```JSON
{
    "get": null,
    "put": [
        {
            "name": "SampleName",
            "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
            "timeSeriesId": [
                "samplePartitionKeyValueOne"
            ],
            "description": "floor 100",
            "hierarchyIds": [
                "e37a4666-9650-42e6-a6d2-788f12d11158"
            ],
            "instanceFields": {
                "state": "California",
                "city": "Los Angeles"
            }
        }
    ]
}
```

Odpověď:

```JSON
{
    "get": null,
    "put": [
        {
            "instance": null,
            "error": null
        },
        {
            "instance": null,
            "error": null
        }
    ]
}
```

### <a name="aggregateseries-api"></a>aggregateSeries rozhraní API

AggregateSeries rozhraní API umožňuje dotazování a načítání dat TSI z zachycené události vzorkování a agregování zaznamenaná data pomocí funkce agregace nebo vzorek.

AggregateSeries rozhraní API mají následující parametry:

* *timeSeriesId*: **ID řady času** je jedinečný klíč pro data v rámci datového proudu událostí a modelu. Tento klíč je TSI používá při vytváření oddílů data.
* *searchSpan*: časový rozsah a intervalu velikosti pro tento agregační výraz.
* *Filtr*: volitelný predikát klauzuli.
* *Interval*: Interval, ve kterém by měl vypočítaného data.
* *ProjectedVariables*: proměnné, které jsou v oboru, které chcete vypočítat.
* *InlineVariables(optional)*: definice proměnné, které chceme, aby buď přepsat z této odesílaných prostřednictvím TSM nebo zadaný vložený pro výpočet.

GetSeries rozhraní API vrátí TSV pro každou proměnnou pro každý interval na základě zadaného **ID řady času** a sadu zadané proměnné. GetSeries API dosahuje snížení využití proměnné uloženy v TSM nebo vložený, aby se agregace nebo ukázková data k dispozici.

> [!NOTE]
> Proměnné interpolace se momentálně nepodporuje.

Podporované typy:

* `Min`
* `Max`
* `Sum`
* `Count`
* `Average`

#### <a name="aggregateseries-json-request-and-response-example"></a>Příklad JSON žádostí a odpovědí aggregateSeries

Zadaný požadavek POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

S text JSON:

```JSON
{
    "aggregateSeries": {
        "timeSeriesId": ["da2754af-cc51-46b3-b18f-6231f8781a12","PU.98"],
        "searchSpan": {
            "from": {"dateTime": "2016-08-01T00:00:00Z"},
            "to": {"dateTime": "2016-08-01T00:10:00Z"}
        },
        //Optional
        //If provided, Reduction should be specified in Variable Definition
        "interval": "PT1M",
        "filter": null, //Optional
        "projectedVariables": [
            "Count",
            "Average Temperature",
            "Min Temperature"
        ],
        "inlineVariables": {
            "Average Temperature": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.temperature.Double" },
                "interpolation": "None",
                "aggregation": {"tsx": "avg($value)"}
            },
            //Default Type Count Experience
            "Count": {
                "kind": "aggregate",
                "filter": null,
                "value": null,
                "interpolation": "None",
                "aggregation": {"tsx": "count()"}
            },
            "Min Temperature": {
                "kind": "aggregate",
                "filter": null,
                "value": null,
                "interpolation": "None",
                "aggregation": {"tsx": "min($event.temperature)"}
            },
        }
    }
}
```

Odpověď:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:00Z","2016-08-01T00:02:00Z","2016-08-01T00:03:00Z","2016-08-01T00:04:00Z",
        "2016-08-01T00:05:00Z","2016-08-01T00:06:00Z","2016-08-01T00:07:00Z","2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Average Temperature",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316]
        },
        {
            "name": "Count Temperature",
            "type": "Double",
            "values": [ 60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0 ]
        },
        {
            "name": "Min Temperature",
            "type": "Double",
            "values": [ 68.645954260192127,68.790954260192578,68.990954260193249,69.190954260193919,69.39095426019459,69.590954260195261,69.790954260195932,69.9909542601966,70.190954260197273,70.390954260197944 ]
        }
    ]
}
```

### <a name="getseries-api"></a>getSeries rozhraní API

GetSeries rozhraní API umožňuje dotazování a načítání dat TSI z zachycené události s využitím dat zaznamenaných v přenosu pomocí proměnných v modelu definovat nebo poskytnout vložený.

> [!Note]
> Pokud klauzule interpolace a agregace jsou uvedeny v proměnné, nebo je zadán interval, bude se ignorovat.

GetSeries rozhraní API mají následující parametry:

* *timeSeriesId*: **ID řady času** je jedinečný klíč pro data v rámci datového proudu událostí a modelu. Tento klíč je TSI používá při vytváření oddílů data.
* *searchSpan*: časový rozsah a intervalu velikosti pro tento agregační výraz.
* *Filtr*: volitelný predikát klauzuli.
* *ProjectedVariables*: proměnné, které jsou v oboru, které chcete vypočítat.
* *InlineVariables(optional)*: definice proměnné, které chceme, aby buď přepsat z této odesílaných prostřednictvím TSM nebo zadaný vložený pro výpočet.

GetSeries rozhraní API vrátí TSV pro každou proměnnou, na základě zadaného **ID řady času** a sadu zadané proměnné. GetSeries, který dosahuje rozhraní API nepodporuje intervalech nebo proměnné snížení/interpolace.  

#### <a name="getseries-json-request-and-response-example"></a>Příklad JSON žádostí a odpovědí getSeries

Zadaný požadavek POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

S text JSON:

```JSON
{
    "getSeries": {
        "timeSeriesId": ["da2754af-cc51-46b3-b18f-6231f8781a12","PU.98"],
        "searchSpan": {
            "from": {"dateTime": "2016-08-01T00:00:00Z"},
            "to": {"dateTime": "2016-08-01T00:10:00Z"}
        },
        "interval": null, //Null or not specified
        "filter": null, //Optional
        "projectedVariables": [
            "Temperature",
            "Pressure",
        ],
        "inlineVariables": {
            "Temperature": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.temperature.Double" },
                "interpolation": "None", // null, not specified or ignored if specified
                "aggregation": {"tsx": "avg($value)"} //null, not specified or ignored if specified
            },
            "Pressure": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.pressure.Double" },
                "interpolation": "None", // null, not specified or ignored if specified
                "aggregation": {"tsx": "avg($value)"} //null, not specified or ignored if specified
            }
        }
    }
}
```

Odpověď:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:00Z","2016-08-01T00:02:00Z","2016-08-01T00:03:00Z","2016-08-01T00:04:00Z",
        "2016-08-01T00:05:00Z","2016-08-01T00:06:00Z","2016-08-01T00:07:00Z","2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Temperature",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316 ]
        },
        {
            "name": "Pressure",
            "type": "Double",
            "values": [ 68.645954260192127,68.790954260192578,68.990954260193249,69.190954260193919,69.39095426019459,69.590954260195261,69.790954260195932,69.9909542601966,70.190954260197273,70.390954260197944 ]
        }
    ]
}
```

### <a name="getevents-api"></a>getEvents rozhraní API

GetEvents rozhraní API umožňuje dotazování a načítání dat TSI z událostí, protože jsou zaznamenány v Time Series Insights od poskytovatele zdroj.

GetEvents rozhraní API mají následující parametry:

* *timeSeriesId*: **ID řady času** je jedinečný klíč pro data v rámci datového proudu událostí a modelu. Tento klíč je TSI používá při vytváření oddílů data.
* *searchSpan*: časový rozsah a intervalu velikosti pro tento agregační výraz.
* *Filtr*: můžete zadat hodnoty predikátu k filtrování dotazů, ale vyžadujete.
* *(volitelné) projectedProperties*: umožňuje filtrování sloupce nebo vlastnosti.

GetEvents rozhraní API vrátí nezpracované hodnoty z zachycené události uložené ve službě TSI pro danou **ID řady času** a časový rozsah. Definice proměnných (TSM ani definice proměnných se používají) nevyžaduje.

### <a name="getevents-json-request-and-response-example"></a>Příklad JSON žádostí a odpovědí getEvents

Zadaný požadavek POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

S text JSON:

```JSON
{
    "getEvents": {
        // Required and Supports 3 Key as tsId
        "timeSeriesId": [
            "PU.123","W00158","ABN.9890"
        ],
        // Required.
        "searchSpan": {
            "from": "2018-08-01T17:01:00Z",
            "to": "2018-08-20T17:01:00Z",
        },
        // Optional.
        "filter": {
            "tsx": "($event.Value != null) OR ($event.Status.String == 'Good')"
        },
        // Optional – array of simple TSXs that refer exactly one property
        // If not specified, we will return all the properties.
        "projectedProperties": [ 
            {"propertyName" : "Volts", "type": "double" } 
        ]
    }
}
```

Odpověď:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:03Z","2016-08-01T00:02:05Z","2016-08-01T00:03:00Z",
        "2016-08-01T00:04:00Z","2016-08-01T00:05:05Z","2016-08-01T00:06:00Z","2016-08-01T00:07:08Z",
        "2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Volts",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316 ]
        }
    ]
}
```

### <a name="supported-query-operators"></a>Operátory podporovaných dotazů

Filtrování:

* `HAS`
* `IN`
* `AND`
* `AND NOT`
* `OR`
* `>`
* `<`
* `<=`
* `>=`
* `!=`
* `<>`
* `/`
* `*`

Dočasné:

* `From`
* `To`
* `First/Last` (Pouze rozhraní API)

Transformace a agregace:

* `MEASURE`
* `SUM` (pro míru)
* `COUNT` (události)
* `AVG` (pro míru)
* `MIN` (pro míru)
* `MAX` (pro míru).
* `GROUP BY` (zařazené do kategorií sloupec)
* `ORDER BY ASC, DSC` (relativní časové razítko)
* `DateHistogram` (velikost sady)

## <a name="tsq-api-limits"></a>Rozhraní API TSQ omezení

> [!NOTE]
> Následující tabulka určuje omezení jako **11/20/18**.

| Parametr | Omezení |
| --- | --- |
| Velikost požadavku TSQ | 32 KB. |
| TSQ Limit doby odezvy | 16 MB |
| Limit TSQ paralelní dotaz | 20 pro každé prostředí |
| Získat události | Velikost 16 MB nebo 30s čas |
| Platnost tokenu | 1 hodina |
| Získat řady | 500 000 intervalech nebo časová razítka nebo velikost 16 MB |
| Minimum | Interval omezení 1 ms |
| Maximum | 50 předpokládané proměnné |
| Agregační řady | 500 000 intervalech nebo časová razítka nebo velikost 16 MB |
| Minimum | Interval omezení 1 ms |
| Maximum | 50 předpokládané proměnné |

## <a name="time-series-query-tutorial"></a>Kurz čas řady dotazů

Úplný kurz TSQ, poskytneme vám v budoucnosti.

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>Další postup

Přečtěte si [Azure TSI (preview) úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

Přečtěte si informace o novém [modelu časové řady](./time-series-insights-update-tsm.md).

Přečtěte si informace o [osvědčené postupy při výběru ID řady času](./time-series-insights-update-how-to-id.md).

<!-- Images -->
[1]: media/v2-update-tsq/goal.png
[2]: media/v2-update-tsq/tsq.png
