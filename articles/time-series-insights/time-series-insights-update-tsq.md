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
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 29418345aaa8042e50b1297541cac3af0a3c1504
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935198"
---
# <a name="data-querying"></a>Dotazování dat

Azure Time Series Insights Preview umožňuje dotazování dat na události a metadata uložená v prostředí prostřednictvím rozhraní API pro veřejná Surface. Tato rozhraní API se používají taky v [průzkumníkovi Time Series Insights Preview](./time-series-insights-update-explorer.md).

V Time Series Insights jsou k dispozici tři primární kategorie rozhraní API:

* **Rozhraní API prostředí**: Povoluje dotazy samotného Time Series Insightsho prostředí. Příklady dotazů jsou seznam prostředí, ke kterým má volající přístup k metadatům prostředí a.

* **Rozhraní API pro Time Series model – Query (TSM-Q)** : Povoluje operace vytvoření, čtení, aktualizace a odstranění u metadat uložených v části prostředí modelu časové řady. Příklady jsou instance, typy a hierarchie.

* **Rozhraní API pro Time Series Query (TSQ)** : Umožňuje načtení dat událostí, která se zaznamenávají ze zprostředkovatele zdroje. Tato rozhraní API mohou provádět operace pro transformaci, kombinování a provádění výpočtů pro data časových řad.

[Jazyk výrazu Time Series (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) je výkonná čtvrtá kategorie. Používá modely časových řad k povolení složení pokročilého výpočtu.

## <a name="azure-time-series-insights-preview-core-apis"></a>Rozhraní API pro základní Azure Time Series Insights Preview

Podporují se následující základní rozhraní API.

[![Přehled dotazů na časové řady](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Rozhraní API prostředí

K dispozici jsou následující rozhraní API prostředí:

* [Získat rozhraní API prostředí](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): Vrátí seznam prostředí, ke kterým má volající oprávnění k přístupu.
* [Získat rozhraní API dostupnosti prostředí](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Vrátí distribuci počtu událostí přes časové razítko `$ts`události. Toto rozhraní API pomáhá určit, jestli se v časovém razítku vyskytují nějaké události, a to tak, že vrátí počet událostí, pokud existují.
* [Získat rozhraní API schématu událostí](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Vrátí metadata schématu události pro daný rozsah hledání. Toto rozhraní API pomáhá načíst všechna metadata a vlastnosti, které jsou ve schématu k dispozici pro daný rozsah hledání.

## <a name="time-series-model-query-tsm-q-apis"></a>Rozhraní API pro Time Series model – Query (TSM-Q)

K dispozici jsou následující modely časových řad – rozhraní API pro dotazy:

* [Rozhraní API pro nastavení modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Povolí možnost získat a opravit na výchozím typu a název modelu prostředí.
* [Rozhraní API pro typy](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Povoluje CRUD na typech časových řad a jejich přidružených proměnných.
* [Rozhraní API hierarchií](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Povoluje CRUD hierarchií časových řad a jejich přidružených cest k polím.
* [Rozhraní API instancí](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Povoluje CRUD u instancí časových řad a jejich přidružených polí instance.

## <a name="time-series-query-tsq-apis"></a>Rozhraní API pro Time Series Query (TSQ)

K dispozici jsou následující rozhraní API pro dotazování časových řad:

* [Získat rozhraní API pro události](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Povoluje dotazování a načítání Time Series Insights dat z událostí, protože jsou zaznamenána v Time Series Insights ze zprostředkovatele zdroje.

* [Získat rozhraní API pro řady](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Umožňuje dotazování a načítání dat Time Series Insights ze zaznamenaných událostí pomocí dat zaznamenaných na lince. Vrácené hodnoty jsou založeny na proměnných, které byly definovány v modelu nebo zadány jako vložené.

    >[!NOTE]
    > Klauzule agregace se ignoruje i v případě, že je zadaná v modelu nebo je zadaná jako vložená.

  Rozhraní API pro získání řady vrátí hodnotu časové řady pro každou proměnnou pro každý interval. Hodnota časové řady je formát, který Time Series Insights používá pro výstup JSON z dotazu. Vrácené hodnoty jsou založené na ID časové řady a sadě zadaných proměnných.

* [Rozhraní API pro agregaci řad](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): Umožňuje dotazování a načítání dat Time Series Insights z zachycených událostí vzorkováním a agregací zaznamenaných dat.

  Rozhraní API pro agregaci řad vrací hodnotu časové řady pro každou proměnnou pro každý interval. Hodnoty jsou založené na ID časové řady a sadě zadaných proměnných. Rozhraní API pro agregaci řad dosahuje snížení pomocí proměnných uložených v modelu časové řady nebo poskytnutých jako vložené do agregovaných nebo ukázkových dat.

  Podporované agregované typy `Min`: `Max`, `Sum`, `Count`,,`Average`

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o službě [Storage a](./time-series-insights-update-storage-ingress.md) příchozím přenosu ve službě Azure Time Series Insights Preview.

- Přečtěte si článek Time Series Insights Preview [Data Modeling](./time-series-insights-update-tsm.md) .

- [Při volbě ID časové řady se](./time-series-insights-update-how-to-id.md)objevují osvědčené postupy.
