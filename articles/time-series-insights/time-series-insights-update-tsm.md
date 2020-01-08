---
title: Model časové řady – Azure Time Series Insights | Microsoft Docs
description: Přečtěte si o modelu časové řady v Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/20/2019
ms.custom: seodec18
ms.openlocfilehash: 7d588e11525e5087f8667da4602797e5299c76f0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374689"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Model časové řady v Azure Time Series Insights Preview

Tento článek popisuje model časových řad, možnosti a způsob, jak začít sestavovat a aktualizovat vlastní modely v prostředí Azure Time Series Insights Preview.

> [!TIP]
>  * Příklad modelu živé časové řady najdete v [ukázkovém prostředí farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) .
> * Přečtěte si informace o tom, jak přejít k uživatelskému rozhraní modelu časové řady v [Průzkumníkovi služby Azure Time Series Insights Preview](time-series-insights-update-explorer.md) .
> * Naučte se [pracovat s modelem časových řad](time-series-insights-update-how-to-tsm.md) pomocí Time Series Insights webovým průzkumníkem.

## <a name="summary"></a>Souhrn

Data shromážděná ze zařízení IoT neobsahují kontextové informace, což usnadňuje rychlé vyhledání a analýzu senzorů. Hlavním motivací pro model časových řad je zjednodušení hledání a analýza dat IoT nebo časových řad. Tento cíl dosahuje tím, že umožňuje zpracovat, udržovat a zdokonalovat data časových řad, což vám usnadní přípravu datových sad připravených pro zákazníky pro účely analýzy.

## <a name="scenario-contosos-new-smart-oven"></a>Scénář: nová inteligentní trouba společnosti Contoso

**Vezměte v úvahu fiktivní scénář čipové pece společnosti Contoso.** V tomto scénáři předpokládáme, že každá chytrá trouba společnosti Contoso má pět senzorů teploty, jednu pro každé ze čtyř hlavních zapisovacích karet a jednu pro samotnou troubu. Až do poslední doby se každý senzor teploty společnosti Contoso poslal, uloží a rozvizuálů jeho dat. Pro monitorování kuchyňského zařízení se contoso spoléhalo na základní grafy, jeden pro každý senzor.

Zatímco contoso byl spokojen s jeho počátečním řešením pro datové a vizualizace, bylo patrné několik omezení:

* Zákazníci chtěli zjistit, jak by celková trouba získala v případě, že většina hlavních zapisovacích modulů byla zapnuta. Společnost Contoso měla více obtížnosti při analýze a prezentování sjednocené odpovědi týkající se podmínek celkové trouby.
* Technici společnosti Contoso chtěli ověřit, že hlavní hořáky spouštěné současně by nevedly k neefektivnímu vyčerpání výkonu. Došlo k potížím s křížovým odkazováním na to, které teplotní a napájecí senzory byly vzájemně spojeny a jak je vyhledat ve Storu.
* Tým pro zajištění kvality společnosti Contoso chtěl auditovat a porovnávat historii mezi dvěma verzemi snímačů. Při určování, jaká data patří k dané verzi snímače, došlo k potížím.

Bez možnosti strukturování, uspořádání a definování modelu série časových řad pro chytrou troubu je každý senzor teploty udržován, izolovaný a méně informativních datových bodů. Přepínání těchto datových bodů na užitečné poznatky bylo obtížnější, protože každá datová sada nepracuje nezávisle na ostatních.

Tato omezení ukázala důležitost nástrojů agregace inteligentních dat a vizualizací, které doprovázejí novou troubu společnosti Contoso:

* Vizualizace dat je užitečná v případě, že je možné přidružit a kombinovat data do pohodlného zobrazení. Příklad zobrazuje senzory napětí spolu s senzory teploty.
* Správa multidimenzionálních dat pro několik entit spolu s funkcemi porovnání, přiblížení a časového rozsahu může být obtížné dosáhnout.

**Model Time Series poskytuje praktické řešení** pro spoustu scénářů, které se vyskytly v tomto fiktivním příkladu:

[Příklad grafu ![Time Series model pro čipovou troubu](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Model časové řady hraje důležitou roli v dotazech a navigaci, protože contextualizes data tím, že umožňuje vykreslovat porovnávání v různých časových intervalech a mezi typy snímačů a zařízení. (**A**) 
* Data jsou dále kontextové, protože data trvalá v modelu časové řady zachovávají výpočty dotazů na časové řady jako proměnné a znovu je používají v době dotazu.
* Model časové řady organizuje a agreguje data pro lepší vizualizaci a možnosti správy. (**B**) 

### <a name="key-capabilities"></a>Klíčové funkce

Díky cíli, který zjednodušuje a usnadňuje správu kontextu časových řad, poskytuje model časových řad následující funkce Time Series Insights ve verzi Preview. Pomůže vám:

* Vytvářejte a spravujte výpočty nebo vzorce s využitím skalárních funkcí, agregačních operací a tak dále.
* Definujte vztahy nadřazenosti a podřízenosti pro povolení navigace, hledání a odkazů.
* Definujte vlastnosti, které jsou přidružené k instancím definovaným jako *pole instance*, a použijte je k vytváření hierarchií.

### <a name="components"></a>Komponenty

Model časové řady má tři základní komponenty:

* [Instance modelů časových řad](#time-series-model-instances)
* [Hierarchie modelů časových řad](#time-series-model-hierarchies)
* [Typy modelů časových řad](#time-series-model-types)

Tyto součásti jsou kombinovány pro určení modelu časové řady a k uspořádání dat Azure Time Series Insights.

[graf přehledu ![časových řad](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Model časových řad se dá vytvořit a spravovat prostřednictvím rozhraní [Time Series Insights Preview](time-series-insights-update-how-to-tsm.md) . Nastavení modelu časové řady lze spravovat prostřednictvím [rozhraní API pro nastavení modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Instance modelů časových řad

*Instance* modelu časové řady představují virtuální reprezentace časových řad.

Ve většině případů se instance jednoznačně identifikují pomocí **deviceId** nebo **assetId**, které se ukládají jako ID časových řad.

Instance obsahují popisné informace spojené s názvem *vlastnosti instance*, jako je ID časové řady, typ, název, popis, hierarchie a pole instance. Minimálně vlastnosti instance obsahují informace o hierarchii.

*Pole instancí* jsou kolekce popisných informací, které mohou obsahovat hodnoty pro úrovně hierarchie, a také výrobce, operátor a tak dále.

Po nakonfigurování zdroje událostí pro prostředí Time Series Insights se instance automaticky zjišťují a vytvoří v modelu časové řady. Instance lze vytvořit nebo aktualizovat prostřednictvím aplikace Time Series Insights Explorer pomocí dotazů na model časové řady.

[Ukázková farma společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) nabízí několik příkladů instancí za provozu.

[Příklad instance modelu ![časové řady](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Vlastnosti instance

Instance jsou definovány pomocí **timeSeriesId**, **typeId**, **Name**, **Description**, **hierarchyIds**a **instanceFields**. Každá instance je mapována pouze na jeden *typ*a jednu nebo více *hierarchií*.

| Vlastnost | Popis |
| --- | ---|
| timeSeriesId | Identifikátor UUID časové řady, ke které je instance přidružena. |
| typeId | Identifikátor UUID typu modelu časové řady, ke kterému je instance přidružena. Ve výchozím nastavení se všechny zjištěné nové instance přidružit k výchozímu typu.
| jméno | Vlastnost **Name** je volitelná a rozlišuje velká a malá písmena. Pokud není **název** k dispozici, použije se výchozí hodnota **timeSeriesId**. Pokud je zadán název, je **timeSeriesId** stále k dispozici. [](time-series-insights-update-explorer.md#4-time-series-well) |
| description | Textový popis instance. |
| hierarchyIds | Definuje, do kterých hierarchií patří instance. |
| instanceFields | Vlastnosti instance a všech statických dat, která definují instanci. Definují hodnoty vlastností hierarchie nebo mimo hierarchii a zároveň podporují indexování k provádění operací vyhledávání. |

> [!NOTE]
> Hierarchie jsou sestaveny pomocí polí instance. Další **instanceFields** lze přidat pro další definice vlastností instance.

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
> Pro Time Series Insights rozhraní API instance a podporu vytváření, čtení, aktualizace a odstranění (CRUD) si přečtěte článek [dotazování na data](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) a [dokumentaci instance rozhraní API instance](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarchie modelů časových řad

*Hierarchie* modelů časových řad organizují instance zadáním názvů vlastností a jejich vztahů.

V daném Time Series Insights prostředí můžete nakonfigurovat více hierarchií. Instance modelu časové řady může být namapována na jednu hierarchii nebo na více hierarchií (relace m:n).

Ukázkové klientské rozhraní [farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) zobrazuje standardní instanci a hierarchii typů.

[Příklad hierarchie modelu ![časové řady](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definice hierarchie

Hierarchie jsou definovány podle **ID**, **názvu**a **zdroje**hierarchie.

| Vlastnost | Popis |
| ---| ---|
| id | Jedinečný identifikátor pro hierarchii, který se používá například při definování instance. |
| jméno | Řetězec, který slouží k zadání názvu hierarchie. |
| source | Určuje organizační hierarchii nebo cestu, která je nejnižším pořadím nadřazeného a podřízeného objektu hierarchie, kterou uživatelé chtějí vytvořit. Vlastnosti nadřazeného a podřízeného objektu mapují pole instance. |

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

V předchozím příkladu JSON:

* `Location` definuje hierarchii s nadřazenými `states` a podřízenými `cities`. Každý `location` může mít více `states`, což může mít více `cities`.
* `ManufactureDate` definuje hierarchii s nadřazenými `year` a podřízenými `month`. Každý `ManufactureDate` může mít více `years`, což může mít více `months`.

> [!TIP]
> Informace o rozhraních API instancí Time Series Insights a podpoře CRUD najdete v článku [dotazování na data](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) a v [dokumentaci k rozhraní API hierarchie REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Příklad hierarchie

Vezměte v úvahu příklad, kdy hierarchie **H1** má `building`, `floor`a `room` jako součást definice **instanceFieldNames** :

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

Vzhledem k polím instance použitým v předchozí definici a několika časových řadách se atributy a hodnoty hierarchie zobrazí, jak je uvedeno v následující tabulce:

| ID časové řady | Pole instance |
| --- | --- |
| ID1 | "sestavování" = "1000", "patra" = "10", "místnost" = "55"  |
| ID2 | "sestavování" = "1000", "místnost" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "sestavování" = "1000", "patra" = "10"  |
| ID5 | Není nastaven žádný z "budova", "patra" ani "místnost". |

Time Series **ID1** a **ID4** se zobrazují jako součást hierarchie **H1** v [Azure Time Series Insights Exploreru](time-series-insights-update-explorer.md) , protože mají plně definované a správně seřazené parametry *sestavení*, *podlah*a *místností* .

Ostatní jsou klasifikovány v rámci *nenadřazených instancí* , protože neodpovídají zadané hierarchii dat.

## <a name="time-series-model-types"></a>Typy modelů časových řad

*Typy* modelů časových řad vám pomůžou definovat proměnné nebo vzorce pro provádění výpočtů. Typy jsou spojeny s konkrétní instancí Time Series Insights.

Typ může mít jednu nebo více proměnných. Například instance modelu časové řady může být typu *snímače teploty*, který se skládá z proměnných *Průměrná teplota*, *Minimální teplota*a *Maximální teplota*.

[Ukázka farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) vizualizuje několik typů modelů časových řad přidružených ke svým příslušným instancím.

[Příklad typu modelu časové řady ![](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Podporu rozhraní API instancí Time Series Insights a CRUD najdete v článku [dotazování na data](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) a v [dokumentaci k rozhraní API typu REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Vlastnosti typu

Typy modelů časových řad jsou definovány podle **ID**, **názvu**, **popisu**a **proměnných**.

| Vlastnost | Popis |
| ---| ---|
| id | Identifikátor UUID pro typ. |
| jméno | Řetězec, který slouží k zadání názvu pro typ. |
| description | Popis řetězce pro typ. |
| proměnné | Zadejte proměnné přidružené k typu. |

Typy odpovídají následujícímu příkladu JSON:

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

Time Series Insights typy mohou mít mnoho proměnných, které určují vzorce a pravidla výpočtu pro události.

Každá proměnná může být jeden ze tří *typů*: *Číselná*, *kategorií*a *Aggregate*.

* **Číselné** typy fungují se souvislými hodnotami. 
* **Kategorií** druhy pracují s definovanou sadou diskrétních hodnot.
* **Agregované** hodnoty spojují více proměnných jednoho druhu (buď všechny číselné, nebo všechny kategorií).

V následující tabulce jsou uvedeny vlastnosti, které jsou relevantní pro jednotlivé druhy proměnných.

[Tabulka proměnných modelu časové řady ![](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Číselné proměnné

| Proměnná – vlastnost | Popis |
| --- | ---|
| Filtr proměnných | Filtry jsou volitelné podmíněné klauzule, které omezují počet řádků, které se považují za výpočet. |
| Hodnota proměnné | Hodnoty telemetrie používané pro výpočet pocházející ze zařízení nebo senzorů nebo transformované pomocí výrazů Time Series. Proměnné číselného typu musí být typu *Double*.|
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
| Hodnota proměnné | Hodnoty telemetrie používané pro výpočet pocházející ze zařízení nebo senzorů. Proměnné kategorií druhu musí být buď *Long* , nebo *String*. |
| Proměnlivá interpolace | Interpolace určuje, jak rekonstruovat signál pomocí stávajících dat. Možnost interpolace *kroku* je k dispozici pro proměnné kategorií. |
| Kategorie proměnných | Kategorie vytvoří mapování mezi hodnotami, které přicházejí ze zařízení nebo senzorů do popisku. |
| Výchozí kategorie proměnných | Výchozí kategorie je určena pro všechny hodnoty, které nejsou namapované ve vlastnosti "Categories". |

Proměnné odpovídají následujícímu příkladu JSON:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)" 
},
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
