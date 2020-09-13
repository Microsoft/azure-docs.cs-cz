---
title: Model časové řady – Azure Time Series Insights Gen2 | Microsoft Docs
description: Přečtěte si o modelu časové řady v Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.custom: seodec18
ms.openlocfilehash: 53db53f60166c3b5afa117a60a99e3429a14576d
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488554"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Model časové řady v Azure Time Series Insights Gen2

Tento článek popisuje model časových řad, možnosti a způsob, jak začít sestavovat a aktualizovat vlastní modely v prostředí Azure Time Series Insights Gen2.

> [!TIP]
>
> * Příklad modelu živé časové řady najdete v [ukázkovém prostředí farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) .
> * Naučte se [pracovat s modelem časových řad](/azure/time-series-insights/how-to-edit-your-model) pomocí Azure Time Series Insights Exploreru.

## <a name="summary"></a>Shrnutí

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

[![Příklad vytváření grafů pro inteligentní troubu v modelu časové řady](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Model časové řady hraje důležitou roli v dotazech a navigaci, protože contextualizes data tím, že umožňuje vykreslovat porovnávání v různých časových intervalech a mezi typy snímačů a zařízení. (**A**)
* Data jsou dále kontextové, protože data trvalá v modelu časové řady zachovávají výpočty dotazů na časové řady jako proměnné a znovu je používají v době dotazu.
* Model časové řady organizuje a agreguje data pro lepší vizualizaci a možnosti správy. (**B**)

### <a name="key-capabilities"></a>Klíčové funkce

S cílem zjednodušit a zjednodušit správu kontextu časových řad, model časových řad umožňuje následující funkce Azure Time Series Insights Gen2. Pomůže vám:

* Vytvářejte a spravujte výpočty nebo vzorce s využitím skalárních funkcí, agregačních operací a tak dále.
* Definujte vztahy nadřazenosti a podřízenosti pro povolení navigace, hledání a odkazů.
* Definujte vlastnosti, které jsou přidružené k instancím definovaným jako *pole instance*, a použijte je k vytváření hierarchií.

### <a name="components"></a>Komponenty

Model časové řady má tři základní komponenty:

* [Instance modelů časových řad](#time-series-model-instances)
* [Hierarchie modelů časových řad](#time-series-model-hierarchies)
* [Typy modelů časových řad](#time-series-model-types)

Tyto součásti jsou kombinovány pro určení modelu časové řady a k uspořádání dat.

[![Graf s přehledem modelu časové řady](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Model časových řad se dá vytvořit a spravovat pomocí [Azure Time Series Insights Exploreru](/azure/time-series-insights/concepts-model-overview). Nastavení modelu časové řady lze spravovat prostřednictvím [rozhraní API pro nastavení modelu](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis).

## <a name="time-series-model-instances"></a>Instance modelů časových řad

*Instance* modelu časové řady představují virtuální reprezentace časových řad.

Ve většině případů se instance jednoznačně identifikují pomocí **deviceId** nebo **assetId**, které se ukládají jako ID časových řad.

Instance obsahují popisné informace spojené s názvem *vlastnosti instance*, jako je ID časové řady, typ, název, popis, hierarchie a pole instance. Minimálně vlastnosti instance obsahují informace o hierarchii.

*Pole instancí* jsou kolekce popisných informací, které mohou obsahovat hodnoty pro úrovně hierarchie, a také výrobce, operátor a tak dále.

Po nakonfigurování zdroje událostí pro prostředí Azure Time Series Insights Gen2 jsou instance automaticky zjišťovány a vytvořeny v modelu časové řady. Instance lze vytvořit nebo aktualizovat prostřednictvím aplikace Azure Time Series Insights Explorer pomocí dotazů na model časové řady.

[Ukázková farma společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) nabízí několik příkladů instancí za provozu.

[![Příklad instance modelu časové řady](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Vlastnosti instance

Instance jsou definovány pomocí **timeSeriesId**, **typeId**, **Name**, **Description**, **hierarchyIds**a **instanceFields**. Každá instance je mapována pouze na jeden *typ*a jednu nebo více *hierarchií*.

| Vlastnost | Popis |
| --- | ---|
| timeSeriesId | Jedinečné ID časové řady, ke které je instance přidružena. Ve většině případů jsou instance jednoznačně identifikované vlastností, jako je deviceId nebo assetId. V některých případech lze použít více specifických IDENTIFIKÁTORů s kombinací až tří vlastností. |
| typeId | Jedinečné ID řetězce s rozlišením velkých a malých písmen pro typ modelu časové řady, ke kterému je instance přidružena. Ve výchozím nastavení se všechny zjištěné nové instance přidružit k výchozímu typu.
| name | Vlastnost **Name** je volitelná a rozlišuje velká a malá písmena. Pokud není **název** k dispozici, použije se výchozí hodnota **timeSeriesId**. Pokud je zadán název, je **timeSeriesId** stále k dispozici. [well](time-series-insights-update-explorer.md#4-time-series-well) |
| Popis | Textový popis instance. |
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
> Pro podporu vytváření, čtení, aktualizace a odstraňování (CRUD) rozhraní API si přečtěte článek [dotazování na data](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) a [dokumentaci k rozhraní API instance rozhraní API REST](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarchie modelů časových řad

*Hierarchie* modelů časových řad organizují instance zadáním názvů vlastností a jejich vztahů.

V daném prostředí Azure Time Series Insights Gen2 můžete nakonfigurovat více hierarchií. Instance modelu časové řady může být namapována na jednu hierarchii nebo na více hierarchií (relace m:n).

[Ukázka farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) zobrazuje standardní instanci a hierarchii typů.

[![Příklad hierarchie modelu časové řady](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definice hierarchie

Hierarchie jsou definovány podle **ID**, **názvu**a **zdroje**hierarchie.

| Vlastnost | Popis |
| ---| ---|
| id | Jedinečný identifikátor pro hierarchii, který se používá například při definování instance. |
| name | Řetězec, který slouží k zadání názvu hierarchie. |
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

* `Location` definuje hierarchii s nadřazenou `states` a podřízenou položkou `cities` . Každý `location` může mít více `states` , což může mít více `cities` .
* `ManufactureDate` definuje hierarchii s nadřazenou `year` a podřízenou položkou `month` . Každý `ManufactureDate` může mít více `years` , což může mít více `months` .

> [!TIP]
> Pro podporu rozhraní API pro vytváření, čtení, aktualizaci a odstraňování (CRUD) si přečtěte článek [dotazování na data](concepts-query-overview.md#time-series-model-query-tsm-q-apis) a [dokumentaci k rozhraní API pro rozhraní API pro hierarchii](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#hierarchies-api).

### <a name="hierarchy-example"></a>Příklad hierarchie

Vezměte v úvahu příklad, kdy hierarchie **H1** má `building` , `floor` a `room` jako součást definice **instanceFieldNames** :

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
| ID 2 | "sestavování" = "1000", "místnost" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "sestavování" = "1000", "patra" = "10"  |
| ID5 | Není nastaven žádný z "budova", "patra" ani "místnost". |

Time Series **ID1** a **ID4** se zobrazují jako součást hierarchie **H1** v [Azure Time Series Insights Exploreru](time-series-insights-update-explorer.md) , protože mají plně definované a správně seřazené parametry *sestavení*, *podlah*a *místností* .

Ostatní jsou klasifikovány v rámci *nenadřazených instancí* , protože neodpovídají zadané hierarchii dat.

## <a name="time-series-model-types"></a>Typy modelů časových řad

*Typy* modelů časových řad vám pomůžou definovat proměnné nebo vzorce pro provádění výpočtů. Typy jsou přidruženy k určité instanci.

Typ může mít jednu nebo více proměnných. Například instance modelu časové řady může být typu *snímače teploty*, který se skládá z proměnných *Průměrná teplota*, *Minimální teplota*a *Maximální teplota*.

[Ukázka farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples) vizualizuje několik typů modelů časových řad přidružených ke svým příslušným instancím.

[![Příklad typu modelu časové řady](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> V případě podpory typu rozhraní API vytváření, čtení, aktualizace a odstraňování (CRUD) si přečtěte článek [dotazování na data](concepts-query-overview.md#time-series-model-query-tsm-q-apis) a [dokumentaci typu rozhraní API REST](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#types-api).

### <a name="type-properties"></a>Vlastnosti typu

Typy modelů časových řad jsou definovány podle **ID**, **názvu**, **popisu**a **proměnných**.

| Vlastnost | Popis |
| ---| ---|
| id | Jedinečné ID řetězce rozlišující velká a malá písmena pro daný typ. |
| name | Řetězec, který slouží k zadání názvu pro typ. |
| Popis | Popis řetězce pro typ. |
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
              "tsx": "$event['Speed-Sensor'].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "right($value)"
          }
        }
      }
    }
  ]
}
```

Typy modelů časových řad můžou mít mnoho proměnných, které určují pravidla vzorce a výpočtu pro události. Přečtěte si další informace o [Definování proměnných modelu časové řady](./concepts-variables.md) .

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak upravit model prostřednictvím rozhraní API, najdete v referenční dokumentaci k [modelu časové řady](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis) .

* Prozkoumejte vzorce a výpočty, které můžete vytvořit pomocí [proměnných modelu časové řady](./concepts-variables.md) .

* Další informace o [dotazování na data](concepts-query-overview.md) v Azure Time Series Insights Gen2
