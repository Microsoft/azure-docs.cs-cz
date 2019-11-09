---
title: Dotazování dat ve verzi Preview pro Azure Time Series Insights | Microsoft Docs
description: Dotazování dat ve verzi Preview Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: e660db5db3d1afc14a3c895e6786d1b6a8b82c13
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832410"
---
# <a name="data-querying"></a>Dotazování dat

Azure Time Series Insights Preview umožňuje dotazování dat na události a metadata uložená v prostředí prostřednictvím rozhraní API pro veřejná Surface. Tato rozhraní API se používají taky v [průzkumníkovi Time Series Insights Preview](./time-series-insights-update-explorer.md).

V Time Series Insights jsou k dispozici tři primární kategorie rozhraní API:

* **Rozhraní API prostředí**: Tato rozhraní API povolují dotazy přímo v prostředí Time Series Insights. Příklady dotazů jsou seznam prostředí, ke kterým má volající přístup k metadatům prostředí a.
* **Rozhraní API Time Series model-Query (TSM-Q)** : povoluje operace vytvoření, čtení, aktualizace a odstranění (CRUD) v metadatech uložených v části prostředí modelu časové řady. Příklady jsou instance, typy a hierarchie.
* **Rozhraní API pro Time Series Query (TSQ)** : umožňuje načíst data telemetrie nebo událostí, jak jsou zaznamenána ze zdrojového zprostředkovatele, nebo omezením dat pomocí skalárních a agregačních funkcí uložených částí proměnných. Tato rozhraní API mohou provádět operace pro transformaci, kombinování a použití výpočtů pro data časových řad.

Pro vyjádření výpočtů používá Time Series Insights jazyk výrazů založený na řetězci ve [výrazu Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

## <a name="azure-time-series-insights-preview-core-apis"></a>Rozhraní API pro základní Azure Time Series Insights Preview

Podporují se následující základní rozhraní API.

[Přehled dotazů na ![časové řady](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Rozhraní API prostředí

K dispozici jsou následující rozhraní API prostředí:

* [Získat rozhraní API prostředí](/rest/api/time-series-insights/management/environments/get): vrátí seznam prostředí, ke kterým má volající oprávnění k přístupu.
* [Získat rozhraní API dostupnosti prostředí](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): vrátí distribuci počtu událostí přes časové razítko události `$ts`. Toto rozhraní API pomáhá určit, jestli se v časovém razítku vyskytují nějaké události, a to tak, že vrátí počet událostí, pokud existují.
* [Získat rozhraní API pro schéma událostí](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): vrátí metadata schématu události pro daný rozsah hledání. Toto rozhraní API pomáhá načíst všechna metadata a vlastnosti, které jsou ve schématu k dispozici pro daný rozsah hledání.

## <a name="time-series-model-query-tsm-q-apis"></a>Rozhraní API pro Time Series model – Query (TSM-Q)

K dispozici jsou následující modely časových řad – rozhraní API pro dotazy. Většina těchto rozhraní API podporuje operaci dávkového spouštění, aby umožnila dávkové operace CRUD u více entit modelu časové řady:

* [Rozhraní API pro nastavení modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): povolí možnost *získat* a *opravit* na výchozím typu a název modelu prostředí.
* [API Types](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): povoluje CRUD na typech časových řad a jejich přidružených proměnných.
* [Hierarchie rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): povoluje CRUD v hierarchiích časových řad a jejich přidružených cestách k polím.
* [Rozhraní API instancí](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): povoluje CRUD v instancích časových řad a jejich přidružených polích instance. Rozhraní API instancí navíc podporuje následující operace:
  * [Hledání](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): načte částečný seznam přístupů při hledání instancí časových řad na základě atributů instance.
  * [Navrhnout](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): vyhledá a navrhne částečný seznam přístupů při hledání instancí časových řad na základě atributů instance.

## <a name="time-series-query-tsq-apis"></a>Rozhraní API pro Time Series Query (TSQ)

K dispozici jsou následující rozhraní API pro dotazování časové řady. Tato rozhraní API jsou dostupná pro všechna podporovaná úložiště s více vrstvami v Time Series Insights. Parametry adresy URL dotazu slouží k určení [typu úložiště](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) , na kterém by se měl dotaz spouštět:

* [Získat rozhraní API pro události](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): umožňuje dotazování a načítání Time Series Insights dat z událostí tak, jak jsou zaznamenána v Time Series Insights ze zprostředkovatele zdroje. Toto rozhraní API umožňuje načtení nezpracovaných událostí pro dané ID časové řady a rozsah hledání. Toto rozhraní API podporuje stránkování, aby bylo možné načíst úplnou datovou sadu pro vybraný vstup. 

* [Získat rozhraní API pro řady](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): umožňuje dotazování a načítání Time Series Insights dat ze zaznamenaných událostí pomocí dat zaznamenaných na lince. Vrácené hodnoty jsou založeny na proměnných, které byly definovány v modelu nebo zadány jako vložené. Toto rozhraní API podporuje stránkování, aby bylo možné načíst úplnou datovou sadu pro vybraný vstup. Toto rozhraní API pomáhá definovat počítané vlastnosti nebo sloupce.

    >[!NOTE]
    > Klauzule agregace se ignoruje i v případě, že je zadaná v modelu nebo je zadaná jako vložená.

  Rozhraní API pro získání řady vrátí hodnotu časové řady pro každou proměnnou pro každý interval. Hodnota časové řady je formát, který Time Series Insights používá pro výstup JSON z dotazu. Vrácené hodnoty jsou založené na ID časové řady a sadě zadaných proměnných.

* [Rozhraní API pro agregaci řad](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable): umožňuje dotazování a načítání dat Time Series Insights z zachycených událostí vzorkováním a agregací zaznamenaných dat. Toto rozhraní API podporuje plynulé spouštění pomocí [tokenů pro pokračování](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage).

  Rozhraní API pro agregaci řad vrací hodnotu časové řady pro každou proměnnou pro každý interval. Hodnoty jsou založené na ID časové řady a sadě zadaných proměnných. Rozhraní API pro agregaci řad dosahuje snížení pomocí proměnných uložených v modelu časové řady nebo poskytnutých jako vložené do agregovaných nebo ukázkových dat.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [úložišti a](./time-series-insights-update-storage-ingress.md) příchozím přenosu ve službě Azure Time Series Insights Preview.
- Přečtěte si článek Time Series Insights Preview [Data Modeling](./time-series-insights-update-tsm.md) .
- Zjistěte [osvědčené postupy pro výběr ID časové řady](./time-series-insights-update-how-to-id.md).
