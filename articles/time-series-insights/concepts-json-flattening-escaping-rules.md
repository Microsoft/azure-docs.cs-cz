---
title: Pravidla sloučení a uvozovacích znaků JSON – Azure Time Series Insights Gen2 | Microsoft Docs
description: Seznamte se se sloučením JSON, uvozovacími znaky a zpracováním pole v Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 9f768982e69f785c146f026040a91f7a63eef64c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673447"
---
# <a name="json-flattening-escaping-and-array-handling"></a>Zplošťování, uvozování a zpracování polí JSON

Prostředí Azure Time Series Insights Gen2 bude dynamicky vytvářet sloupce teplého a studeného úložiště, a to podle konkrétní sady zásad vytváření názvů. Když je událost ingestovaná, použije se pro datovou část a názvy vlastností JSON sada pravidel. Mezi ně patří uvozovací znaky určitých speciálních znaků a sloučení vnořených objektů JSON. Je důležité znát tato pravidla, abyste porozuměli tomu, jak bude mít tvar JSON vliv na to, jak se vaše události ukládají a dotazují. Úplný seznam pravidel najdete v následující tabulce. Příklady & B také ukazují, jak je možné efektivně dávkovat více časových řad v poli.

> [!IMPORTANT]
>
> * Než začnete vybírat [vlastnost ID časové řady](./how-to-select-tsid.md) nebo vlastní [časové razítko](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)zdroje událostí, zkontrolujte níže uvedená pravidla. Pokud je vaše ID TS nebo časové razítko v rámci vnořeného objektu nebo má jeden nebo více speciálních znaků níže, je důležité zajistit, aby název vlastnosti, který zadáte, odpovídal názvu sloupce *po* použití pravidel pro přijímání zpráv. Viz příklad [B](concepts-json-flattening-escaping-rules.md#example-b) níže.

| Pravidlo | Ukázkový kód JSON | [Syntaxe výrazů časové řady](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Název sloupce vlastnosti v Parquet
|---|---|---|---|
| Azure Time Series Insights datový typ Gen2 se přidá na konec názvu sloupce jako "_ \<dataType\> ". | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| [Vlastnost časového razítka](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) zdroje události bude uložena v Azure Time Series Insights Gen2 jako "timestamp" v úložišti a hodnota uložená v UTC. Vlastnost časového razítka zdroje událostí můžete přizpůsobit tak, aby splňovala požadavky vašeho řešení, ale název sloupce v teplém a studeném úložišti je "timestamp". Další vlastnosti formátu JSON, které nejsou časové razítko zdroje událostí, budou uloženy s názvem "_datetime" v názvu sloupce, jak je uvedeno v pravidle výše.  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| Názvy vlastností JSON, které obsahují speciální znaky. [\ a ' jsou uvozeny pomocí znaků [a].  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| V rámci [a] Existují další uvozovací znaky jednoduchých uvozovek a zpětná lomítka. Jedna uvozovka se zapíše jako \ a zpětné lomítko se zapíše jako \\\ | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| Vnořené objekty JSON jsou shrnuty s tečkou jako s oddělovačem. Je podporováno vnořování až na 10 úrovní. |  ```"series": {"value" : 316 }``` | `$event.series.value.Long`, `$event['series']['value'].Long` nebo `$event.series['value'].Long` |  `series.value_long` |
| Pole primitivních typů se ukládají jako dynamický typ. |  ```"values": [154, 149, 147]``` | Dynamické typy se dají načíst jenom prostřednictvím rozhraní API [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) . | `values_dynamic` |
| Pole obsahující objekty mají v závislosti na obsahu objektu dvě chování: Pokud jsou ID TS nebo vlastnosti časového razítka v rámci objektů v poli, pole bude nekumulativní, takže počáteční datová část JSON vytvoří více událostí. To umožňuje dávkovat více událostí do jedné struktury JSON. Všechny vlastnosti nejvyšší úrovně, které jsou partnery pro pole, budou uloženy s každým nenasazeným objektem. Pokud vaše ID TS a časové razítko *nejsou v poli* , uloží se celý jako dynamický typ. | Viz příklady [a](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b)a [C](concepts-json-flattening-escaping-rules.md#example-c) níže.
| Pole obsahující smíšené prvky se nesloučí. |  ```"values": ["foo", {"bar" : 149}, 147]``` | Dynamické typy se dají načíst jenom prostřednictvím rozhraní API [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) . | `values_dynamic` |
| 512 znaků je limit názvů vlastností JSON. Pokud název překračuje 512 znaků, bude zkrácen na 512 a "_< ' hodnotou hash ' > ' je připojen. **Všimněte si** , že to platí i pro názvy vlastností, které jsou zřetězené z objektu, který je sloučený, a označuje cestu k vnořenému objektu. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>Porozumění duálnímu chování pro pole

Pole objektů budou buď uložena celá, nebo rozdělena do více událostí v závislosti na způsobu modelování dat. To umožňuje použít pole pro dávkové události a vyhnout se opakujícím se vlastnostem telemetrie, které jsou definovány na úrovni kořenového objektu. Dávkování může být výhodné, protože má za následek méně Event Hubs nebo IoT Hub odeslaných zpráv.

V některých případech však pole obsahující objekty jsou smysluplná pouze v kontextu jiných hodnot. Vytvořením více událostí by bylo vygenerováno nevýznam dat. Chcete-li zajistit, aby pole objektů bylo uloženo jako dynamický typ, postupujte podle pokynů níže v níže uvedeném návodu k modelování dat a podívejte se na [příklad C](concepts-json-flattening-escaping-rules.md#example-c) .

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>Jak zjistit, jestli moje pole objektů vytvoří více událostí

Pokud je jedna nebo více vašich správných IDENTIFIKÁTORů časových řad v rámci objektů v poli vnořené, *nebo* Pokud je vaše vlastnost časového razítka zdroje události vnořená, modul pro příjem hodnot rozdělí na vytvoření více událostí. Názvy vlastností, které jste zadali pro vaše ID TS a/nebo časové razítko, by se měly řídit výše uvedenými pravidly sloučení, a proto budou označovat tvar vašeho JSON. Podívejte se na následující příklady a podívejte se na návod, jak [Vybrat vlastnost ID časové řady.](./how-to-select-tsid.md)

### <a name="example-a"></a>Příklad A

ID časové řady v kořenu objektu a ve vnořeném časovém razítku \
**ID časové řady prostředí:**`"id"`\
**Časové razítko zdroje události:**`"values.time"`\
**Datová část JSON:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Výsledek v souboru Parquet:**\
Výše uvedená konfigurace a datová část vytvoří tři sloupce a čtyři události.

| časové razítko  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>Příklad B

ID složené časové řady s jednou vnořenou vlastností \
**ID časové řady prostředí:** `"plantId"` ani `"telemetry.tagId"`\
**Časové razítko zdroje události:**`"timestamp"`\
**Datová část JSON:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Výsledek v souboru Parquet:**\
Výše uvedená konfigurace a datová část vytvoří čtyři sloupce a šest událostí.

| časové razítko  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31,149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20,560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30,9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19,960796 |

### <a name="example-c"></a>Příklad C

ID časové řady a časové razítko jsou v objektu root \
**ID časové řady prostředí:**`"id"`\
**Časové razítko zdroje události:**`"timestamp"`\
**Datová část JSON:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Výsledek v souboru Parquet:**\
Výše uvedená konfigurace a datová část vytvoří tři sloupce a jednu událost.

| časové razítko  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>Další kroky

* Princip [omezení propustnosti](./concepts-streaming-ingress-throughput-limits.md) vašeho prostředí
