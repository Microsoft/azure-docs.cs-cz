---
title: Dotazování dat v Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Dotazování dat Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 48a9a1d0f79a7a36b90fa87651a5283cba87de20
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275801"
---
# <a name="data-querying"></a>Dotazování dat

Azure čas Series Insights ve verzi Preview umožňuje dotazování na data na události a metadata uložená v prostředí prostřednictvím veřejného rozhraní API a surface. Tato rozhraní API také používají [čas Series Insights ve verzi Preview explorer](./time-series-insights-update-explorer.md).

Tři hlavní kategorie rozhraní API jsou k dispozici v Time Series Insights:

* **Rozhraní API prostředí**: Umožňuje dotazům prostředí Time Series Insights, samotného. Příklady dotazů jsou v seznamu prostředí, které volající nemá přístup k a metadata prostředí.

* **Time Series Model-Query (TSM-Q) API**: Umožňuje vytvořit, číst, aktualizovat a odstranit operace na metadata uložená v prostředí součástí modelu časové řady. Příklady jsou instance, typy a hierarchie.

* **Time Series dotazování rozhraní API (TSQ)**: Umožňuje načítání dat události, protože se zaznamená od poskytovatele zdroj. Tato rozhraní API můžete provádět operace transformace, kombinovat a provádí výpočty datech časových řad.

[Čas řady výrazu (TSX) jazyka](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) je výkonný čtvrtá kategorie. Povolit skládání pokročilé výpočetní používá čas řady modely.

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure základní čas Series Insights ve verzi Preview rozhraní API

Následující základní rozhraní API jsou podporovány.

![tsq][1]

### <a name="environment-apis"></a>Rozhraní API prostředí

K dispozici jsou následující rozhraní API prostředí:

* [Prostředí API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): Vrátí seznam prostředí, jestli má volající autorizaci pro přístup k.
* [Zjistit dostupnost prostředí rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Vrací distribuce počet událostí v časové razítko události `$ts`. Toto rozhraní API pomáhá, určete, jestli jsou všechny události v časové razítko tak, že vrací počet událostí, pokud nějaké existují.
* [Získat schéma událostí rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Vrátí metadata schématu událostí pro daný hledaný rozpětí. Toto rozhraní API pomáhá načíst všechny metadat a vlastností, které jsou k dispozici ve schématu pro daný hledaný rozpětí.

### <a name="time-series-model-query-tsm-q-apis"></a>Rozhraní API pro čas řady Model-Query (TSM-Q)

K dispozici jsou následující rozhraní API modelu dotazu řady čas:

* [Model nastavení API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Umožňuje získat a oprava na výchozí typ a název modelu prostředí.
* [Typy rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Povolí CRUD na časové řady typů a jejich přidružené proměnné.
* [Rozhraní API hierarchie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Umožňuje CRUD v časové řadě hierarchie a jejich přidružené pole cesty.
* [Instance rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Povolí CRUD na časové řady instancí a jejich polí přidruženou instanci.

### <a name="time-series-query-tsq-apis"></a>Čas řady dotazů (TSQ) rozhraní API

K dispozici jsou následující rozhraní API čas řady dotazů:

* [Získat události rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Umožňuje dotazování a načítání dat Time Series Insights z událostí, protože jsou zaznamenány v Time Series Insights od poskytovatele zdroj.

* [Získat řady API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Umožňuje dotazování a načítání dat Time Series Insights z zachycené události s využitím dat zaznamenaných v přenosu. Hodnoty, které jsou vráceny, jsou založeny na proměnné, které byly definovány v modelu nebo vložené k dispozici.

    >[!NOTE]
    > V klauzuli agregace je ignorován, i v případě, že má zadaný v modelu nebo vložené k dispozici.

  Rozhraní API pro získání řady vrátí hodnotu Time Series pro každou proměnnou v každém intervalu. Hodnotu Time Series je formát, který používá Time Series Insights pro výstup JSON z dotazu. Hodnoty, které jsou vráceny, jsou založeny na ID řady času a sadu proměnných, které byly k dispozici.

* [Agregovat řady API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): Umožňuje dotazování a načítání dat Time Series Insights z zachycené události vzorkování a agregování zaznamenaná data.

  Agregační řady rozhraní API vrátí hodnotu Time Series pro každou proměnnou v každém intervalu. Hodnoty jsou založeny na ID řady času a sadu proměnných, které byly k dispozici. Agregační API řady dosahuje snížení pomocí proměnné uloženy v modelu časové řady nebo vložený, aby se agregace nebo ukázková data k dispozici.

  Podporované typy: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Další postup

Přečtěte si informace o:

- [Azure storage čas Series Insights ve verzi Preview a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md)
- [Modelování dat](./time-series-insights-update-tsm.md)
- [Osvědčené postupy při výběru ID řady času](./time-series-insights-update-how-to-id.md)

<!-- Images -->
[1]: media/v2-update-tsq/tsq.png
