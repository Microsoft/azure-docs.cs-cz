---
title: Dotazování dat ve verzi Preview pro Azure Time Series Insights | Microsoft Docs
description: Dotazování dat ve verzi Preview Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 0b2f89a846747eaf78c60077372b48802506731e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553379"
---
# <a name="data-querying"></a>Dotazování dat

Azure Time Series Insights Preview umožňuje dotazování dat na události a metadata uložená v prostředí prostřednictvím rozhraní API pro veřejná Surface. Tato rozhraní API se používají taky v [průzkumníkovi Time Series Insights Preview](./time-series-insights-update-explorer.md).

V Time Series Insights jsou k dispozici tři primární kategorie rozhraní API:

* **Rozhraní API prostředí**: povolí dotazy samotného prostředí Time Series Insights. Příklady dotazů jsou seznam prostředí, ke kterým má volající přístup k metadatům prostředí a.

* **Rozhraní API Time Series model-Query (TSM-Q)** : povoluje operace vytvoření, čtení, aktualizace a odstranění (CRUD) v metadatech uložených v části prostředí modelu časové řady. Příklady jsou instance, typy a hierarchie.

* **Rozhraní API pro Time Series Query (TSQ)** : umožňuje načtení dat událostí, která se zaznamenávají ze zprostředkovatele zdroje. Tato rozhraní API mohou provádět operace pro transformaci, kombinování a provádění výpočtů pro data časových řad.

[Jazyk výrazu Time Series (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) je výkonná čtvrtá kategorie. Používá modely časových řad k povolení složení pokročilého výpočtu.

## <a name="azure-time-series-insights-preview-core-apis"></a>Rozhraní API pro základní Azure Time Series Insights Preview

Podporují se následující základní rozhraní API.

[Přehled dotazu na ![Time Series](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Rozhraní API prostředí

K dispozici jsou následující rozhraní API prostředí:

* [Získat rozhraní API prostředí](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): vrátí seznam prostředí, ke kterým má volající oprávnění k přístupu.
* [Získat rozhraní API dostupnosti prostředí](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): vrátí distribuci počtu událostí přes časové razítko události `$ts`. Toto rozhraní API pomáhá určit, jestli se v časovém razítku vyskytují nějaké události, a to tak, že vrátí počet událostí, pokud existují.
* [Získat rozhraní API pro schéma událostí](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): vrátí metadata schématu události pro daný rozsah hledání. Toto rozhraní API pomáhá načíst všechna metadata a vlastnosti, které jsou ve schématu k dispozici pro daný rozsah hledání.

## <a name="time-series-model-query-tsm-q-apis"></a>Rozhraní API pro Time Series model – Query (TSM-Q)

K dispozici jsou následující modely časových řad – rozhraní API pro dotazy:

* [Rozhraní API pro nastavení modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): povolí možnost získat a opravit na výchozím typu a název modelu prostředí.
* [API Types](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): povoluje CRUD na typech časových řad a jejich přidružených proměnných.
* [Hierarchie rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): povoluje CRUD v hierarchiích časových řad a jejich přidružených cestách k polím.
* [Rozhraní API instancí](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): povoluje CRUD v instancích časových řad a jejich přidružených polích instance.

## <a name="time-series-query-tsq-apis"></a>Rozhraní API pro Time Series Query (TSQ)

K dispozici jsou následující rozhraní API pro dotazování časových řad:

* [Získat rozhraní API pro události](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): umožňuje dotazování a načítání Time Series Insights dat z událostí tak, jak jsou zaznamenána v Time Series Insights ze zprostředkovatele zdroje.

* [Získat rozhraní API pro řady](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): umožňuje dotazování a načítání Time Series Insights dat ze zaznamenaných událostí pomocí dat zaznamenaných na lince. Vrácené hodnoty jsou založeny na proměnných, které byly definovány v modelu nebo zadány jako vložené.

    >[!NOTE]
    > Klauzule agregace se ignoruje i v případě, že je zadaná v modelu nebo je zadaná jako vložená.

  Rozhraní API pro získání řady vrátí hodnotu časové řady pro každou proměnnou pro každý interval. Hodnota časové řady je formát, který Time Series Insights používá pro výstup JSON z dotazu. Vrácené hodnoty jsou založené na ID časové řady a sadě zadaných proměnných.

* [Rozhraní API pro agregaci řad](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): umožňuje dotazování a načítání dat Time Series Insights z zachycených událostí vzorkováním a agregací zaznamenaných dat.

  Rozhraní API pro agregaci řad vrací hodnotu časové řady pro každou proměnnou pro každý interval. Hodnoty jsou založené na ID časové řady a sadě zadaných proměnných. Rozhraní API pro agregaci řad dosahuje snížení pomocí proměnných uložených v modelu časové řady nebo poskytnutých jako vložené do agregovaných nebo ukázkových dat.

  Podporované agregované typy: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o službě [Storage a](./time-series-insights-update-storage-ingress.md) příchozím přenosu ve službě Azure Time Series Insights Preview.

- Přečtěte si článek Time Series Insights Preview [Data Modeling](./time-series-insights-update-tsm.md) .

- [Při volbě ID časové řady se objevují osvědčené postupy](./time-series-insights-update-how-to-id.md).
