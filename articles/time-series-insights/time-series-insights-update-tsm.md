---
title: Model časové řady – Přehledy azure časové řady | Dokumenty společnosti Microsoft
description: Přečtěte si o modelu časové řady v azure time series insights preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 648578563a0e53d3ed5bda6ab47f85c3c6a2a24e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476650"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Model časové řady ve verzi Azure Time Series Insights Preview

Tento článek popisuje model časové řady, možnosti a jak začít vytvářet a aktualizovat vlastní modely v prostředí Azure Time Series Insights Preview.

> [!TIP]
>  * Přejděte do ukázkového prostředí [větrné farmy Contoso,](https://insights.timeseries.azure.com/preview/samples) kde najdete příklad modelu časové řady.
> * Přečtěte si o [průzkumníku Náhled přehledů Azure Time Series Insights,](time-series-insights-update-explorer.md) kde se dozvíte, jak se orientovat v uzu modelu časové řady.
> * [Naučte se pracovat s modelem časových řad](time-series-insights-update-how-to-tsm.md) pomocí webového průzkumníka Time Series Insights.

## <a name="summary"></a>Souhrn

Data shromážděná ze zařízení IoT tradičně postrádají kontextové informace, což ztěžuje rychlé hledání a analýzu senzorů. Hlavní motivací pro model časové řady je zjednodušení hledání a analýzy dat IoT nebo Time Series. Tohoto cíle dosahuje tím, že umožňuje kurování, údržbu a obohacení dat časových řad, které pomáhají připravit datové sady připravené pro spotřebitele pro analýzu.

## <a name="scenario-contosos-new-smart-oven"></a>Scénář: Nová inteligentní trouba společnosti Contoso

**Vezměme si fiktivní scénář inteligentní trouby Contoso.** V tomto scénáři předpokládejme, že každá inteligentní trouba Contoso má pět teplotních senzorů, jeden pro každý ze čtyř horních hořáků a jeden pro samotnou troubu. Až donedávna každý teplotní senzor Contoso odesílal, ukládal a vizualizoval svá data jednotlivě. Pro monitorování kuchyňských spotřebičů se společnost Contoso spoléhala na základní grafy, jeden pro každý jednotlivý senzor.

Zatímco společnost Contoso byla spokojena se svým počátečním řešením dat a vizualizace, ukázalo se několik omezení:

* Zákazníci chtěli vědět, jak horké celkové trouby by si, když většina z horní hořáky byly na. Společnost Contoso měla větší potíže s analýzou a předložením jednotné odpovědi o podmínkách celkové trouby.
* Inženýři společnosti Contoso chtěli ověřit, že současné spuštění horních hořáků nepovede k neefektivnímu čerpání energie. Tam byl problém cross-odkazování, které teploty a napětí senzory byly spojeny s sebou a jak je lokalizovat v obchodě.
* Tým společnosti Contoso pro zajištění kvality chtěl auditovat a porovnávat historii mezi dvěma verzemi senzorů. Bylo obtížné určit, jaká data patří ke které verzi senzoru.

Bez schopnosti strukturovat, organizovat a definovat zastřešující model časových řad inteligentní trouby udržuje každý teplotní senzor vykloubené, izolované a méně informativní datové body. Přeměna těchto datových bodů na užitečné přehledy byla obtížnější, protože každá datová sada žila nezávisle na ostatních.

Tato omezení odhalila důležitost inteligentních nástrojů pro agregaci a vizualizaci dat, které doprovázejí novou troubu společnosti Contoso:

* Vizualizace dat se ukáže jako užitečná, když jste schopni přidružit a kombinovat data do pohodlného zobrazení. Příkladem jsou snímače napětí spolu s teplotními senzory.
* Správa vícerozměrných dat pro několik entit spolu s funkcemi porovnání, přiblížení a časového rozsahu může být obtížné dosáhnout.

**Model časové řady poskytuje pohodlné řešení** pro mnoho scénářů, se kterými se setkáváte v tomto fiktivním příkladu:

[![Příklad vytváření grafů inteligentních pecí Model řady](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Model časové řady hraje důležitou roli v dotazech a navigaci, protože kontextualizuje data tím, že umožňuje porovnání napříč časovými rozsahy a mezi druhy senzorů a zařízení. (**A**) 
* Data jsou dále kontextualizována, protože data trvalá v modelu časové řady zachová výpočty dotazů časových řad jako proměnné a znovu je použije v době dotazu.
* Model časové řady organizuje a agreguje data pro lepší možnosti vizualizace a správy. (**B**) 

### <a name="key-capabilities"></a>Klíčové funkce

S cílem, aby bylo jednoduché a snadné spravovat kontextualizaci časových řad, time series model umožňuje následující funkce v Time Series Insights Preview. To vám pomůže:

* Vytvářet a spravovat výpočty nebo vzorce využívající skalární funkce, agregační operace a tak dále.
* Definujte vztahy nadřazený podřízený povolit navigaci, vyhledávání a odkaz.
* Definujte vlastnosti, které jsou přidruženy k instancím, definované jako *pole instancí*, a použijte je k vytvoření hierarchií.

### <a name="components"></a>Komponenty

Model časové řady má tři základní komponenty:

* [Instance modelu časové řady](#time-series-model-instances)
* [Hierarchie modelů časových řad](#time-series-model-hierarchies)
* [Typy modelů časových řad](#time-series-model-types)

Tyto součásti jsou kombinovány tak, aby určily model časové řady a uspořádaly data Azure Time Series Insights.

[![Graf přehledu modelu časové řady](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Model časové řady lze vytvořit a spravovat prostřednictvím rozhraní [Time Series Insights Preview.](time-series-insights-update-how-to-tsm.md) Nastavení modelu časové řady lze spravovat pomocí [rozhraní API pro nastavení modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Instance modelu časové řady

*Instance* modelu časové řady jsou virtuální reprezentace samotných časových řad.

Ve většině případů jsou instance jednoznačně identifikovány **deviceId** nebo **assetId**, které jsou uloženy jako ID časových řad.

Instance mají popisné informace s nimi spojené s názvem *vlastnosti instance*, jako je ID časové řady, typ, název, popis, hierarchie a pole instancí. Vlastnosti instance zahrnují minimálně informace o hierarchii.

*Pole instancí* jsou kolekcí popisných informací, které mohou obsahovat hodnoty pro úrovně hierarchie a také pro výrobce, operátora a tak dále.

Po nakonfigurování zdroje událostí pro prostředí Time Series Insights jsou instance automaticky zjištěny a vytvořeny v modelu časové řady. Instance lze vytvořit nebo aktualizovat pomocí průzkumníka Time Series Insights pomocí dotazů model časové řady.

[Ukázka větrné farmy Contoso](https://insights.timeseries.azure.com/preview/samples) poskytuje několik příkladů živých instancí.

[![Příklad instance modelu časové řady](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Vlastnosti instance

Instance jsou definovány **podle timeSeriesId**, **typeId**, **název**, **popis**, **hierarchyIds**a **instanceFields**. Každá instance se mapuje pouze na jeden *typ*a na jednu nebo více *hierarchií*.

| Vlastnost | Popis |
| --- | ---|
| timeSeriesId | UUID časové řady, ke které je instance přidružena. |
| Typeid | UUID typu modelu časové řady, ke kterým je instance přidružena. Ve výchozím nastavení jsou všechny zjištěné nové instance přidruženy k výchozímu typu.
| jméno | Vlastnost **name** je volitelná a rozlišuje malá a velká písmena. Pokud **název** není k dispozici, je výchozí **timeSeriesId**. Pokud je k dispozici název, **timeSeriesId** je stále k dispozici ve [studni](time-series-insights-update-explorer.md#4-time-series-well). |
| description | Textový popis instance. |
| hierarchyIds | Definuje, do kterých hierarchií instanci patří. |
| instancePole | Vlastnosti instance a všechna statická data, která definuje instanci. Definují hodnoty vlastností hierarchie nebo nehierarchie a zároveň podporují indexování k provádění vyhledávacích operací. |

> [!NOTE]
> Hierarchie jsou vytvořeny pomocí polí instancí. Další **instanceFields** lze přidat pro další definice vlastností instance.

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
> Pro rozhraní API instance Time Series Insights a vytvoření, čtení, aktualizaci a odstranění (CRUD) podpory, přečtěte si [dotazování dat](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) článku a [instance API REST dokumentace](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarchie modelů časových řad

Hierarchie modelu *časových* řad uspořádejte instance zadáním názvů vlastností a jejich vztahů.

V daném prostředí Time Series Insights můžete nakonfigurovat více hierarchií. Instance modelu časové řady může být mapována na jednu hierarchii nebo více hierarchií (vztah N:N).

[Rozhraní demo klienta Větrné farmy Contoso](https://insights.timeseries.azure.com/preview/samples) zobrazuje standardní hierarchii instancí a typu.

[![Příklad hierarchie modelu časové řady](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definice hierarchie

Hierarchie jsou definovány **id**hierarchie , **názvem**a **zdrojem**.

| Vlastnost | Popis |
| ---| ---|
| id | Jedinečný identifikátor hierarchie, který se používá například při definování instance. |
| jméno | Řetězec používaný k zadání názvu hierarchie. |
| source | Určuje organizační hierarchii nebo cestu, což je pořadí nadřazených členů hierarchie shora dolů, které uživatelé chtějí vytvořit. Vlastnosti nadřazeného podřízeného mapují pole instancí. |

Hierarchie jsou v JSON reprezentovány jako:

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

* `Location`definuje hierarchii s `states` nadřazeným a podřízeným `cities`. Každý `location` může `states`mít více , což `cities`může mít více .
* `ManufactureDate`definuje hierarchii s `year` nadřazeným a podřízeným `month`. Každý `ManufactureDate` může `years`mít více , což `months`může mít více .

> [!TIP]
> Pro rozhraní API instance Time Series Insights a podporu CRUD si přečtěte článek [dotazování na data](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) a [dokumentaci rozhraní API hierarchie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)REST .

### <a name="hierarchy-example"></a>Příklad hierarchie

Vezměme si příklad, `floor`kde `room` hierarchie **H1** má `building`, a jako součást jeho **instanceFieldNames** definice:

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

Vzhledem k polím instancí použitým v předchozí definici a několika časových řadách se atributy a hodnoty hierarchie zobrazí tak, jak je znázorněno v následující tabulce:

| ID časové řady | Pole instance |
| --- | --- |
| ID1 | "budova" = "1000", "podlaha" = "10", "místnost" = "55"  |
| ID2 | "budova" = "1000", "pokoj" = "55" |
| ID3 | "podlaha" = "10" |
| ID4 | "budova" = "1000", "podlaha" = "10"  |
| ID5 | Žádná z "budovy", "podlaha", nebo "pokoj" je nastavena. |

Časové řady **ID1** a **ID4** se zobrazují jako součást hierarchie **H1** v [průzkumníku Azure Time Series Insights,](time-series-insights-update-explorer.md) protože mají plně definované a správně uspořádané parametry *budovy*, *podlahy*a *místnosti.*

Ostatní jsou klasifikovány v *nenadřazené instance,* protože neodpovídají zadané hierarchii dat.

## <a name="time-series-model-types"></a>Typy modelů časových řad

*Typy* modelů časových řad pomáhají definovat proměnné nebo vzorce pro výpočty. Typy jsou přidruženy k určité instanci Time Series Insights.

Typ může mít jednu nebo více proměnných. Například instance Model časové řady může být typu *Teplotní čidlo*, který se skládá z proměnných *avg teplota*, *min teplota*a max *teplota*.

[Ukázka větrné farmy Contoso](https://insights.timeseries.azure.com/preview/samples) vizualizuje několik typů modelů časových řad přidružených k jejich příslušným instancím.

[![Příklad typu modelu časové řady](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Pro rozhraní API instance Time Series Insights a podporu CRUD si přečtěte článek [o dotazování na data](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) a [dokumentaci typu API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Vlastnosti typu

Typy modelů časových řad jsou definovány **id**, **názvem**, **popisem**a **proměnnými**.

| Vlastnost | Popis |
| ---| ---|
| id | UUID pro typ. |
| jméno | Řetězec používaný k zadání názvu typu. |
| description | Popis řetězce pro typ. |
| Proměnné | Zadejte proměnné přidružené k typu. |

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

Typy Time Series Insights mohou mít mnoho proměnných, které určují pravidla vzorce a výpočtu událostí.

Každá proměnná může být jeden ze tří *druhů*: *číselný*, *kategorický*a *agregační*.

* **Číselné** druhy pracují s průběžnými hodnotami. 
* **Kategorické** druhy pracují s definovanou sadou diskrétních hodnot.
* **Agregované** hodnoty kombinují více proměnných jednoho druhu (všechny číselné nebo všechny kategorické).

V následující tabulce jsou uvedeny vlastnosti, které jsou relevantní pro každý druh proměnné.

[![Tabulka proměnných Model časové řady](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Číselné proměnné

| Proměnná vlastnost | Popis |
| --- | ---|
| Filtr proměnných | Filtry jsou volitelné podmíněné klauzule omezit počet řádků, které jsou považovány za výpočty. |
| Hodnota proměnné | Telemetrické hodnoty používané pro výpočty pocházející ze zařízení nebo senzorů nebo transformované pomocí výrazů časové řady. Číselné proměnné druhu musí být typu *Double*.|
| Variabilní interpolace | Interpolace určuje, jak rekonstruovat signál pomocí existujících dat. *Možnosti kroková* a *Lineární* interpolace jsou k dispozici pro číselné proměnné. |
| Agregace proměnných | Podpora výpočtu prostřednictvím *Operátory Prům,* *Min*, *Max*, *Součet*, *Počet*, *První*, *Poslední* a časově vážený (*Průměr*, *Min*, *Max*, *Součet*, *Vlevo*) operátory. |

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

#### <a name="categorical-variables"></a>Kategorické proměnné

| Proměnná vlastnost | Popis |
| --- | ---|
| Filtr proměnných | Filtry jsou volitelné podmíněné klauzule omezit počet řádků, které jsou považovány za výpočty. |
| Hodnota proměnné | Telemetrické hodnoty používané pro výpočty pocházející ze zařízení nebo senzorů. Proměnné kategorického druhu musí být *dlouhé* nebo *stringové*. |
| Variabilní interpolace | Interpolace určuje, jak rekonstruovat signál pomocí existujících dat. Možnost *Interpolace kroku* je k dispozici pro kategorické proměnné. |
| Variabilní kategorie | Kategorie vytvářejí mapování mezi hodnotami přicházejícími ze zařízení nebo senzorů na štítek. |
| Výchozí proměnná kategorie | Výchozí kategorie je pro všechny hodnoty, které nejsou mapovány ve vlastnosti "kategorie". |

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
      "values": [0, 1, 2],
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

| Proměnná vlastnost | Popis |
| --- | ---|
| Filtr proměnných | Filtry jsou volitelné podmíněné klauzule omezit počet řádků, které jsou považovány za výpočty. |
| Agregace proměnných | Podpora výpočtu prostřednictvím *Avg*, *Min*, *Max*, *Sum*, *Count*, *First*, *Last*. |

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

Proměnné jsou uloženy v definici typu modelu časové řady a mohou být poskytnuty vložených prostřednictvím [dotazu API](time-series-insights-update-tsq.md) přepsat uloženou definici.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [úložiště a příchozí přenos dat přehledů Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).

- Informace o běžných operacích modelu časové řady v [datovém modelování ve verzi Azure Time Series Insights Preview](./time-series-insights-update-how-to-tsm.md)

- Přečtěte si novou referenční dokumentaci [k modelu časové řady.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model)
