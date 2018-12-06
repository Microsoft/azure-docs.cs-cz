---
title: Dotazování dat v Azure Time Series Insights (preview) | Dokumentace Microsoftu
description: Dotazování na data Azure Time Series Insights (preview)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 00ef6eed23d1645320c28123d6670230cdd725c9
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964913"
---
# <a name="data-querying"></a>Dotazování dat

Azure Time Series Insights (TSI) zajišťuje dat při dotazování na události a metadata uložená v prostředí prostřednictvím veřejného rozhraní API a surface. Tato rozhraní API používají také u [Průzkumníku TSI](./time-series-insights-update-explorer.md).

Existují tři primární kategorie rozhraní API, které jsou k dispozici ve službě Azure TSI:

* Rozhraní API prostředí povolte dotazy služby TSI prostředí samotné, např. seznam prostředí volajícího má přístup k prostředí metadata, např.

* Řada dotazu modelu rozhraní API (TSM-Q) povolit vytvoření, čtení, aktualizace a odstranění týkající se metadata uložená v prostředí součástí modelu časové řady. Podobně jako instance, typy, hierarchie, atd.

* Čas řady dotazů (TSQ) rozhraní API povolit načítání dat události, protože je zaznamenán od poskytovatele zdroj, nebo můžete provádět operace transformace, kombinovat a provádí výpočty datech časových řad.

[Jazyk výrazů řady čas](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) (TSX) je výkonný, čtvrtý, kategorie. Využívá čas řady modely (TSM) umožňuje složení pokročilé výpočtu.

## <a name="azure-time-series-insights-core-apis"></a>Azure Time Series Insights základní rozhraní API

V následující tabulce jsou základní rozhraní API podporuje.

![tsq][1]

### <a name="the-environment-apis"></a>Rozhraní API prostředí

Tady jsou k dispozici rozhraní API prostředí:

* [Rozhraní API prostředí](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): vrátí seznam prostředí, že má volající autorizaci pro přístup k.
* [Rozhraní API dostupnost prostředí](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): vrací distribuce počet událostí v časové razítko události `$ts`. Toto rozhraní API pomáhá zjistit, jestli jsou všechny události v časové razítko tak, že vrací počet událostí, pokud existují.
* [Rozhraní API schématu událostí](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): vrací metadata schématu událostí pro daný hledaný rozpětí. Toto rozhraní API pomáhá načíst všechny metadat/vlastnosti dostupné ve schématu pro daný hledaný rozpětí.

### <a name="time-series-model-query-tsm-q-apis"></a>Rozhraní API pro čas řady Model-Query (TSM-Q)

Následují API modelu dotazu řady čas k dispozici:

* [Model nastavení API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): umožňuje získat a oprava na výchozí typ a název modelu prostředí.
* [Typy rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): umožňuje CRUD u čas řady typů a jejich přidružené proměnných.
* [Rozhraní API hierarchie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): umožňuje CRUD v časové řadě hierarchie a jejich přidružené pole cesty.
* [Instance rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): umožňuje CRUD u instance řady času a jejich polí přidruženou instanci.

### <a name="the-time-series-query-tsq-apis"></a>Rozhraní API čas řady dotazů (TSQ)

Následují dotazu API řady čas k dispozici:

* [Získání rozhraní API pro události](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): rozhraní API získat události umožňuje dotazování a načítání dat TSI z událostí, protože se zaznamenávají do Azure TSI od poskytovatele zdroj.

* [Rozhraní API řady](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): umožňuje dotazování a načítání dat Azure TSI z zachycené události s využitím dat zaznamenaných v přenosu pomocí proměnných v modelu nebo zadaný vložený definovat.

    >[!NOTE]
    > Klauzule agregace je ignorován i v případě, že zadaný v modelu nebo poskytnout vložený.

  Rozhraní API pro získání řady vrátí TSV (časovou řadu hodnotu, formát TSI používá pro výstup JSON z dotazu) pro každou proměnnou pro každý interval na základě zadaného **ID řady času** a sadu zadané proměnné.

* [Agregovat řady API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): umožňuje dotazování a načítání dat TSI z zachycené události vzorkování a agregování zaznamenaná data.

  Agregační řady rozhraní API vrátí TSV pro každou proměnnou pro každý interval na základě zadaného **ID řady času** a sadu zadané proměnné. Agregační API řady dosahuje snížení využití proměnné uloženy v TSM nebo vložený, aby se agregace nebo ukázková data k dispozici.

  Podporované typy: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Další postup

Přečtěte si [Azure TSI (Preview) úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

Přečtěte si informace o [modelování dat](./time-series-insights-update-tsm.md).

Přečtěte si informace o [osvědčené postupy při výběru ID řady času](./time-series-insights-update-how-to-id.md).

<!-- Images -->
[1]: media/v2-update-tsq/tsq.png
