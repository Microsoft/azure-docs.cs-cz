---
title: Migrace na nové verze rozhraní API Azure Time Series Insights Gen2 | Microsoft Docs
description: Jak aktualizovat Azure Time Series Insights Gen2 prostředí, aby používala nové všeobecně dostupné verze.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: shresha
ms.openlocfilehash: 30c3fa837f84ff318e9a03a8a11ef6259890f4bb
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653736"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>Migrace na nové verze rozhraní API Azure Time Series Insights Gen2

## <a name="overview"></a>Přehled

Pokud jste vytvořili prostředí Azure Time Series Insights Gen2, pokud bylo v Public Preview (před 16. července 2020), aktualizujte prosím prostředí TSI, aby používalo nové všeobecně dostupné verze rozhraní API, a to podle postupu popsaného v tomto článku.

Nová verze rozhraní API je `2020-07-31` a používá aktualizovanou [syntaxi výrazu časové řady](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

Uživatelé musí migrovat [proměnné modelu časové řady](./concepts-variables.md)svého prostředí, uložené dotazy, Power BI dotazy a libovolné vlastní nástroje, které volají koncové body rozhraní API. Pokud máte nějaké dotazy nebo obavy týkající se procesu migrace, odešlete lístek podpory prostřednictvím Azure Portal a zmiňujte si tento dokument.

> [!IMPORTANT]
> Verze Preview rozhraní API `2018-11-01-preview` bude i nadále podporována až do října 31 2020. Než se pustíte do provozu, dokončete prosím všechny příslušné kroky této migrace.

## <a name="migrate-time-series-model-and-saved-queries"></a>Migrace modelu časové řady a uložených dotazů

Aby uživatelé mohli migrovat [proměnné modelu časové řady](./concepts-variables.md) a uložené dotazy, je k dispozici integrovaný nástroj prostřednictvím [Průzkumníka Azure Time Series Insights](https://insights.timeseries.azure.com). Přejděte do prostředí, které chcete migrovat, a postupujte podle následujících kroků. **Migraci můžete dokončit částečně a vrátit se k jejímu dokončení později, ale žádná z aktualizací nebude možné vrátit zpět.**

> [!NOTE]
> Aby bylo možné provádět aktualizace modelu časové řady a uložených dotazů, musíte být přispěvatelem tohoto prostředí. Pokud nejste přispěvatel, budete moct migrovat jenom svoje osobní uložené dotazy. Než budete pokračovat, zkontrolujte prosím [zásady přístupu k prostředí](./concepts-access-policies.md) a úroveň přístupu.

1. V Průzkumníkovi se zobrazí výzva, aby se aktualizovala syntaxe používaná proměnnými modelu časové řady a uloženými dotazy.

    [![Výzv](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    Pokud oznámení omylem uzavřete, najdete ho na panelu oznámení.

1. Kliknutím na **Zobrazit aktualizace** otevřete nástroj pro migraci.

1. Klikněte na tlačítko **typy stahování**. Vzhledem k tomu, že migrace přepíše aktuální typy pro změnu syntaxe proměnných, bude nutné uložit kopii aktuálních typů. Nástroj vám po stažení typů upozorní.

    [![Typy stažení](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klikněte na **Aktualizovat proměnné**. Nástroj vás upozorní, když byly proměnné aktualizovány.

    > [!IMPORTANT]
    > Pokud vaše typy aktualizujete, vlastní aplikace, které používají starší verzi rozhraní API ( `2018-11-01-preview` ), budou muset pokračovat v práci pomocí nové verze rozhraní API ( `2020-07-31` ). Pokud si nejste jisti, jakou verzi rozhraní API používáte, před aktualizací se poraďte se správcem. Nástroj pro migraci můžete zavřít a vrátit se k těmto krokům později. Přečtěte si další informace o [tom, jak migrovat vlastní aplikace](#migrate-custom-applications).

    [![Aktualizovat proměnné](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klikněte na **aktualizovat uložené dotazy**. Tento nástroj vás upozorní na to, že byly uložené dotazy aktualizované.

    [![Aktualizace uložených dotazů](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klikněte na **Hotovo**.

    [![Migrace dokončena](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

Projděte si aktualizované prostředí s použitím grafu některých nově vytvořených proměnných a uložených dotazů. Pokud při vytváření grafů dojde k neočekávanému chování, pošlete nám prosím svůj názor pomocí nástroje pro zpětnou vazbu v Průzkumníkovi.

## <a name="migrate-power-bi-queries"></a>Migrace Power BI dotazů

Pokud jste vygenerovali dotazy pomocí konektoru Power BI, provádí volání Azure Time Series Insights pomocí verze Preview rozhraní API a staré syntaxe výrazů časové řady. Tyto dotazy budou i nadále úspěšně získávat data, dokud není rozhraní API pro náhled zastaralé.

Chcete-li aktualizovat dotazy na použití nové verze rozhraní API a nové syntaxe výrazů časové řady, bude nutné dotazy v Průzkumníkovi znovu vygenerovat. Přečtěte si další informace o tom, jak [vytvářet dotazy pomocí konektoru Power BI](./how-to-connect-power-bi.md).

> [!NOTE]
> Je nutné použít Power BI Desktop verze z července 2020. Pokud ne, může se zobrazit [neplatná chyba verze datové části dotazu](./how-to-diagnose-troubleshoot.md#problem-power-bi-connector-shows-unable-to-connect).

## <a name="migrate-custom-applications"></a>Migrace vlastních aplikací

Pokud vaše vlastní aplikace provádí volání následujících koncových bodů REST, stačí aktualizovat verzi rozhraní API na `2020-07-31` v identifikátoru URI:

- Rozhraní API modelu časové řady
  - Rozhraní API pro nastavení modelu
    - [Čtěte](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [Aktualizace](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - Rozhraní API instancí
    - [Všechny operace Batch](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [Seznam](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [Hledání](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [Návrh](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - Rozhraní API hierarchie
    - [Všechny operace Batch](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [Seznam](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - Rozhraní API typů
    - [Odstranit, získat operace](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [Seznam](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

V následujících koncových bodech REST musíte aktualizovat verzi rozhraní API na identifikátor URI a zajistěte, aby `2020-07-31` všechny výskyty `tsx` vlastnosti používaly aktualizovanou [syntaxi výrazu časové řady](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

- Rozhraní API typů
  - [Operace Put](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- Rozhraní API pro dotazy
  - [GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [Getseries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>Příklady

#### <a name="typesbatchput"></a>TypesBatchPut

Původní text požadavku (používaný uživatelem `2018-11-01-preview` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Tělo aktualizovaného požadavku (používá `2020-07-31` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Alternativně `filter` může být také `$event.Mode.String = 'outdoor'` . Aby se `value` vyhnula speciální znak (), musí se použít hranaté závorky `_` .

#### <a name="getevents"></a>GetEvents

Původní text požadavku (používaný uživatelem `2018-11-01-preview` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Tělo aktualizovaného požadavku (používá `2020-07-31` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Alternativně `filter` může být také `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` .

#### <a name="getseries"></a>Getseries

Původní text požadavku (používaný uživatelem `2018-11-01-preview` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Tělo aktualizovaného požadavku (používá `2020-07-31` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Alternativně `value` může být také `$event['Bar-Pressure-Offset'].Double` . Pokud není zadán žádný datový typ, je vždy považován datový typ za Double. Znak závorky musí být použit k úniku speciálního znaku ( `-` ).

#### <a name="aggregateseries"></a>AggregateSeries

Původní text požadavku (používaný uživatelem `2018-11-01-preview` ):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Tělo aktualizovaného požadavku (používá `2020-07-31` ):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Alternativně `value` může být také `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` .

### <a name="potential-errors"></a>Potenciální chyby

#### <a name="invalidinput"></a>InvalidInput

Pokud se zobrazí následující chyba, použijete novou verzi rozhraní API (), `2020-07-31` ale neaktualizovala se syntaxe TSX. Podívejte se prosím na [syntaxi výrazu časové řady](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) a výše uvedené příklady migrace. Před opětovným `tsx` odesláním žádosti rozhraní API se ujistěte, že jsou všechny vlastnosti správně aktualizované.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

- Otestujte prostředí pomocí [Azure Time Series Insights Exploreru](./concepts-ux-panels.md) nebo pomocí vlastní aplikace.
