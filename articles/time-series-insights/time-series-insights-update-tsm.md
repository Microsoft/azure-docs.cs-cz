---
title: Model časové řady v Azure Time Series Insights Preview | Microsoft Docs
description: Principy Azure Time Series Insightsho modelu časových řad.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 802fd444c953c69dfa99fbd49fdf9541cda372ba
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989845"
---
# <a name="time-series-model"></a>Model služby Time Series

Tento dokument popisuje model časových řad, jejich schopnosti a způsob, jak začít sestavovat a aktualizovat vlastní v prostředí Time Series Insights.

> [!TIP]
>  * Přejděte do ukázkového prostředí [farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) pro model živé časové řady.
> * Přečtěte si informace o tom, jak přejít k uživatelskému rozhraní modelu časové řady v [Průzkumníkovi služby Time Series Insights Preview](time-series-insights-update-explorer.md) .

## <a name="summary"></a>Souhrn

Data shromážděná ze zařízení IoT v tradičně neobsahují kontextové informace, což usnadňuje rychlé vyhledání a analýzu senzorů. Hlavním motivací pro model časových řad je snazší hledání a analýzu dat řady IoT/času. Tento cíl dosahuje tím, že umožňuje zpracovat, udržovat a zdokonalovat data časových řad, což vám usnadní přípravu datových sad připravených pro zákazníky pro účely analýzy.

## <a name="scenario-contosos-new-smart-oven"></a>Scénář: nová inteligentní trouba společnosti Contoso

**Vezměte v úvahu fiktivní scénář nové čipové pece společnosti Contoso.** V tomto scénáři předpokládáme, že každá chytrá trouba společnosti Contoso má pět senzorů teploty, jednu pro každé ze čtyř hlavních zapisovacích karet a jednu pro samotnou troubu. Až do poslední doby se každý senzor teploty společnosti Contoso poslal, uloží a rozvizuálů jeho dat. Pro monitorování kuchyňského zařízení se contoso spoléhalo na základní grafy, jeden pro každý senzor.

Zatímco contoso byl spokojen s jejich počátečními daty a vizualizací, bylo patrné několik omezení:

* Zákazníci chtěli zjistit, jak by existovala celková trouba, když je většina hlavních zapisovacích modulů zapnutá, a společnost Contoso měla více obtížnosti při analýze a prezentování sjednocené odpovědi na podmínky celkové trouby.
* Technici společnosti Contoso chtěli ověřit, že hlavní hořáky spouštěné současně by nevedly k neefektivnímu vytahování energie a bylo možné přecházet mezi problémy, které byly vzájemně spojeny s teplotou a napěťovými snímači a jejich umístěním. úložiště.
* Tým pro zajištění kvality společnosti Contoso chtěl auditovat a porovnávat historii mezi dvěma verzemi senzorů a při určování, jaká data patří k dané verzi snímače, došlo k potížím.

Bez možnosti strukturování, uspořádání a definování modelu série časových řad pro chytrou troubu je každý senzor teploty udržován, izolovaný a méně informativních datových bodů. Přepínání těchto datových bodů na užitečné poznatky bylo obtížnější, protože každá sada dat nepracuje nezávisle na ostatních.

Tato omezení ukázala důležitost nástrojů agregace inteligentních dat a vizualizací, které doprovázejí novou troubu společnosti Contoso:

* Vizualizace dat je užitečná v případě, že je možné přidružit a kombinovat data do pohodlného zobrazení. Například zobrazení senzorů napětí spolu s senzory teploty.
* Správa multidimenzionálních dat pro několik entit spolu s funkcemi porovnání, přiblížení a časového rozsahu může být obtížné dosáhnout.

**Model Time Series poskytuje praktické řešení** pro spoustu scénářů, ke kterým došlo v fiktivním příkladu výše:

[vytváření grafů ![ch modelů časových řad](media/v2-update-tsm/tsi-charting.png)](media/v2-update-tsm/tsi-charting.png#lightbox)

* Model časové řady hraje důležitou roli v dotazech a navigaci, protože contextualizes data tím, že umožňuje vykreslovat porovnávání v různých časových intervalech a mezi typy snímačů a zařízení.
* Data jsou dále kontextové, protože data trvalá v modelu časové řady zachovávají výpočty dotazů časových řad jako proměnné a používají je v době dotazu.
* Model časových řad organizují a agreguje data pro lepší vizualizaci a možnosti správy.

### <a name="key-capabilities"></a>Klíčové funkce

Díky cíli, který zjednodušuje a usnadňuje správu kontextu časových řad, poskytuje model časových řad následující funkce Time Series Insights ve verzi Preview. Pomůže vám:

* Vytvářejte a spravujte výpočty nebo vzorce s využitím skalárních funkcí, agregačních operací a tak dále.
* Definujte vztahy nadřazenosti a podřízenosti pro povolení navigace, hledání a odkazů.
* Definujte vlastnosti, které jsou přidružené k instancím definovaným jako *pole instance* a použijte je k vytváření hierarchií.

### <a name="components"></a>Komponenty

Model časové řady má tři základní komponenty:

* [Instance modelů časových řad](#time-series-model-instances)
* [Hierarchie modelů časových řad](#time-series-model-hierarchies)
* [Typy modelů časových řad](#time-series-model-types)

Tyto součásti jsou kombinovány pro určení modelu časové řady a k uspořádání dat Azure Time Series Insights.

[Přehled modelu časové řady ![](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

Model časových řad se dá vytvořit a spravovat prostřednictvím rozhraní [Time Series Insights Preview](time-series-insights-update-how-to-tsm.md) . Nastavení modelu časové řady lze spravovat prostřednictvím [rozhraní API pro nastavení modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Instance modelů časových řad

*Instance* modelu časové řady představují virtuální reprezentace časových řad.

Ve většině případů jsou instance jednoznačně identifikované identifikátorem **deviceId** nebo **assetId**, ukládají se jako ID časových řad.

Instance obsahují popisné informace, které se nazývají *vlastnosti instance* , jako je ID časové řady, typ, název, popis, hierarchie a pole instance. Minimálně vlastnosti instance obsahují informace o hierarchii.

Pole instancí jsou kolekce popisných informací, které mohou obsahovat hodnoty pro úrovně hierarchie, a také výrobce, operátor atd.

Jakmile je zdroj události nakonfigurovaný pro Time Series Insights prostředí, instance se automaticky vyhledají a vytvoří v modelu časové řady. Instance lze vytvořit nebo aktualizovat prostřednictvím aplikace Time Series Insights Explorer s použitím dotazů typu Time Series model.

[Ukázková farma společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) nabízí několik příkladů instancí za provozu.

[instance modelů ![časové řady](media/v2-update-tsm/instance.png)](media/v2-update-tsm/instance.png#lightbox)

### <a name="instance-properties"></a>Vlastnosti instance

Instance jsou definovány pomocí **timeSeriesId**, **typeId**, **Name**, **Description**, **hierarchyIds**a **instanceFields**. Každá instance je mapována pouze na jeden *typ*a jednu nebo více *hierarchií*.

| Vlastnost | Popis |
| --- | ---|
| timeSeriesId | Identifikátor UUID časové řady, ke které je instance přidružena. |
| TypeId | Identifikátor UUID typu modelu časové řady, ke kterému je instance přidružena. Ve výchozím nastavení se všechny zjištěné nové instance přidružit k výchozímu typu.|
| jméno | Vlastnost *Name* je volitelná a rozlišuje velká a malá písmena. Pokud není *název* k dispozici, bude výchozím nastavením *timeSeriesId*. Je-li zadán *název* , bude *timeSeriesId* stále k dispozici [i](time-series-insights-update-explorer.md#preview-well)nadále. |
| description | Textový popis instance. |
| hierarchyIds | Definuje, do kterých hierarchií patří instance. |
| instanceFields | *instanceFields* jsou vlastnosti instance a všech statických dat, která definují instanci. Definují hodnoty vlastností hierarchie nebo mimo hierarchii a zároveň podporují indexování k provádění operací vyhledávání. |

> [!NOTE]
> Hierarchie jsou sestaveny pomocí **polí instance**a další **instanceFields** lze přidat pro další definici vlastnosti instance.

Instance mají následující reprezentaci JSON:

```JSON
{
    "timeSeriesId": ["PU2"],
    "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
    "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
    "description": "Pump #2",
    "instanceFields": {
        "Location": "Redmond",
        "Fleet": "Fleet 5",
        "Unit": "Pump Unit 3",
        "Manufacturer": "Contoso",
        "ScalePres": "0.54",
        "scaleTemp": "0.54"
    }
}
```

> [!TIP]
> Informace o rozhraních API instancí Time Series Insights a podpoře CRUD najdete v článku [dotazování na data](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) a v [dokumentaci k rozhraní API instance rozhraní API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarchie modelů časových řad

*Hierarchie* modelů časových řad organizují instance zadáním názvů vlastností a jejich vztahů.

V daném Time Series Insights prostředí můžete nakonfigurovat více hierarchií. Instance modelu časové řady může být namapována na jednu hierarchii nebo na více hierarchií (relace m:n).

Ukázkové klientské rozhraní [farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) zobrazuje standardní instanci a hierarchii typů.

[![hierarchie modelů časových řad](media/v2-update-tsm/hierarchy.png)](media/v2-update-tsm/hierarchy.png#lightbox)

### <a name="hierarchy-definition"></a>Definice hierarchie

Hierarchie jsou definovány podle **ID**, **názvu**a **zdroje**hierarchie.

| Vlastnost | Popis |
| ---| ---|
| id | Jedinečný identifikátor pro použitou hierarchii, například při definování instance. |
| jméno | Řetězec, který slouží k zadání názvu hierarchie. |
| source | Určuje organizační hierarchii nebo cestu, která je nejnižším pořadím nadřazeného a podřízeného objektu hierarchie, kterou uživatelé chtějí vytvořit. Vlastnosti nadřazeného a podřízeného objektu mapují *pole instance*. |

Hierarchie se ve formátu JSON reprezentují jako:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

Shora

* `Location` definuje hierarchii s nadřazenými `states` a podřízenými `cities`. Každý `location` může mít více `states`, které mohou mít více `cities`.
* `ManufactureDate` definuje hierarchii s nadřazenými `year` a podřízenými `month`. Každý `ManufactureDate` může mít více `years`, které mohou mít více `months`.

> [!TIP]
> Informace o rozhraních API instancí Time Series Insights a podpoře CRUD najdete v článku věnovaném [dotazování na data](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) a v [dokumentaci k rozhraní API hierarchie REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Příklad hierarchie

Vezměte v úvahu příklad, kdy hierarchie **H1** má jako součást definice **instanceFieldNames** *sestavení*, *podlahu*a *místnost* :

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

V případě **polí instance** použitých v definici výše a několika časových řad se atributy a hodnoty hierarchie zobrazí, jak je znázorněno v následující tabulce:

| ID časové řady | Pole instance |
| --- | --- |
| ID1 | "sestavování" = "1000", "patra" = "10", "místnost" = "55"  |
| ID 2 | "sestavování" = "1000", "místnost" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "sestavování" = "1000", "patra" = "10"  |
| ID5 | Není nastavené žádné možnosti "budova", "patra" ani "místnost". |

Time Series **ID1** a **ID4** se zobrazí jako součást hierarchie **H1** v [Azure Time Series Insights Exploreru](time-series-insights-update-explorer.md) , protože mají plně definované a správně seřazené *budova*, *podlahu*a *místnost* . ukazatelů.

Ostatní budou klasifikovány v rámci *nenadřazených instancí* , protože neodpovídají zadané hierarchii dat.

## <a name="time-series-model-types"></a>Typy modelů časových řad

*Typy* modelů časových řad vám pomůžou definovat proměnné nebo vzorce pro provádění výpočtů. Typy jsou spojeny s konkrétní instancí Time Series Insights.

Typ může mít jednu nebo více proměnných. Například instance modelu časové řady může být typu *snímače teploty*, který se skládá z proměnných *Průměrná teplota*, *Minimální teplota*a *Maximální teplota*.

[Ukázka farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) vizualizuje několik typů modelů časových řad přidružených ke svým příslušným instancím.

[![typy modelů časových řad](media/v2-update-tsm/types.png)](media/v2-update-tsm/types.png#lightbox)

> [!TIP]
> Informace o rozhraních API instancí Time Series Insights a podpoře CRUD najdete v článku věnovaném [dotazování na data](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) a v [dokumentaci k rozhraní API typu REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Vlastnosti typu

Typy modelu časové řady jsou definovány **identifikátorem**, **názvem**, **popisem**a **proměnnými**.

| Vlastnost | Popis |
| ---| ---|
| id | Identifikátor UUID pro typ. |
| jméno | Řetězec, který slouží k zadání názvu pro typ. |
| description | Popis řetězce pro typ. |
| proměnné | Zadejte proměnné přidružené k typu. |

Typy budou odpovídat následujícímu příkladu JSON:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>Proměnné

Typy Time Series Insights můžou mít mnoho proměnných, které určují pravidla vzorce a výpočtu pro události.

Každá proměnná může být jeden ze tří *typů*: *Číselná*, *kategorií*a *Aggregate*.

* *Číselné* typy fungují se souvislými hodnotami. 
* *Kategorií* druhy pracují s definovanou sadou diskrétních hodnot.
* *Agregované* hodnoty spojují více proměnných jednoho druhu (buď všechny *číselné* , nebo všechny *kategorií*).

V následující tabulce jsou uvedeny vlastnosti, které jsou relevantní pro jednotlivé druhy proměnných.

[![typy modelů časových řad](media/v2-update-tsm/variable-table.png)](media/v2-update-tsm/variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Číselné proměnné

| Proměnná – vlastnost | Popis |
| --- | ---|
| Filtr proměnných | Filtry jsou volitelné podmíněné klauzule, které omezují počet řádků, které se považují za výpočet. |
| Hodnota proměnné | Hodnoty telemetrie používané pro výpočet pocházející ze zařízení nebo senzorů nebo transformovaných pomocí výrazů Time Series. Proměnné číselného typu musí být typu *Double*.|
| Proměnlivá interpolace | Interpolace určuje, jak rekonstruovat signál pomocí stávajících dat. Možnosti *kroku* a *lineární* interpolace jsou k dispozici pro číselné proměnné. |
| Agregace proměnných | Podpora výpočtů prostřednictvím operátorů *AVG*, *min*, *Max*, *Sum*, *Count*, *First*, *Last* a Time-Weighted (*AVG*, *min*, *Max*, *Sum*, *Left*). |

Proměnné odpovídají následujícímu příkladu JSON:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Proměnné kategorií

| Proměnná – vlastnost | Popis |
| --- | ---|
| Filtr proměnných | Filtry jsou volitelné podmíněné klauzule, které omezují počet řádků, které se považují za výpočet. |
| Hodnota proměnné | Hodnoty telemetrie používané pro výpočet ze zařízení nebo senzorů. Proměnné kategorií druhu musí být buď *Long* , nebo *String*. |
| Proměnlivá interpolace | Interpolace určuje, jak rekonstruovat signál pomocí stávajících dat. Možnost interpolace *kroku* je k dispozici pro proměnné kategorií. |
| Kategorie proměnných | Kategorie vytvoří mapování mezi hodnotami, které přicházejí ze zařízení nebo senzorů do popisku. |
| Výchozí kategorie proměnných | Výchozí kategorie je určena pro všechny hodnoty, které nejsou namapované ve vlastnosti "Categories". |

Proměnné odpovídají následujícímu příkladu JSON:

```JSON
"Status": {
  "kind": "categorical",
  "value": "toLong($event.[Status].Double)",
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Agregační proměnné

| Proměnná – vlastnost | Popis |
| --- | ---|
| Filtr proměnných | Filtry jsou volitelné podmíněné klauzule, které omezují počet řádků, které se považují za výpočet. |
| Agregace proměnných | Podpora výpočtů prostřednictvím funkce *AVG*, *min*, *Max*, *Sum*, *Count*, *First*, *Last*. |

Proměnné odpovídají následujícímu příkladu JSON:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Proměnné jsou uloženy v definici typu modelu časové řady a lze je poskytnout prostřednictvím [rozhraní API pro dotaz](time-series-insights-update-tsq.md) pro přepsání uložené definice.

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Azure Time Series Insights a příchozí úložiště ve verzi Preview](./time-series-insights-update-storage-ingress.md).

- Přečtěte si o běžných operacích modelu časových řad v [modelování dat ve službě Azure Time Series Insights Preview](./time-series-insights-update-how-to-tsm.md) .

- Přečtěte si referenční dokumentaci k novému [modelu časové řady](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) .
