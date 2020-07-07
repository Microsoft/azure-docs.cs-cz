---
title: Dotazování na data ve verzi Preview – Azure Time Series Insights | Microsoft Docs
description: Přehled funkcí dotazování dat a REST API HTTP Azure Time Series Insights ve verzi Preview.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80284887"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Dotazování na data v Azure Time Series Insights Preview

Azure Time Series Insights povoluje dotazování dat na události a metadata uložená v prostředí prostřednictvím rozhraní API pro veřejné plochy. Tato rozhraní API jsou také používána v [průzkumníkovi Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

V Time Series Insights jsou k dispozici tři primární kategorie rozhraní API:

* **Rozhraní API prostředí**: Tato rozhraní API povolují dotazy přímo v prostředí Time Series Insights. Ty lze použít ke shromáždění seznamu prostředí, ke kterým má volající přístup, a k metadatům prostředí.
* **Rozhraní API Time Series model-Query (TSM-Q)**: povoluje operace vytvoření, čtení, aktualizace a odstranění (CRUD) v metadatech uložených v modelu časové řady prostředí. Ty je možné použít pro přístup k instancím, typům a hierarchiím a k jejich úpravám.
* **Rozhraní API pro Time Series Query (TSQ)**: umožňuje načíst data telemetrie nebo událostí, jak jsou zaznamenána od poskytovatele zdroje, a umožňuje provádět výpočty a agregace dat pomocí pokročilých skalárních a agregačních funkcí.

Pro vyjádření výpočtů používá Time Series Insights jazyk výrazů založený na řetězci ve [výrazu Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

## <a name="azure-time-series-insights-core-apis"></a>Rozhraní API pro Azure Time Series Insights Core

Podporují se následující základní rozhraní API.

[![Přehled dotazů na časové řady](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Rozhraní API prostředí

* [Získat rozhraní API prostředí](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): vrátí seznam prostředí, ke kterým má volající oprávnění k přístupu.
* [Získat rozhraní API pro dostupnost prostředí](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): vrátí distribuci počtu událostí přes časové razítko události `$ts` . Toto rozhraní API pomáhá určit, jestli se v prostředí vyskytují nějaké události, a to tak, že vrátí počet událostí v časových intervalech, pokud existují.
* [Získat rozhraní API pro schéma událostí](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): vrátí metadata schématu události pro daný rozsah hledání. Toto rozhraní API pomáhá načíst všechna metadata a vlastnosti, které jsou ve schématu k dispozici pro daný rozsah hledání.

## <a name="time-series-model-query-tsm-q-apis"></a>Rozhraní API pro Time Series model – Query (TSM-Q)

Většina těchto rozhraní API podporuje operaci dávkového spouštění, aby umožnila dávkové operace CRUD u více entit modelu časové řady:

* [Rozhraní API pro nastavení modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api): povolí možnost *získat* a *opravit* na výchozím typu a název modelu prostředí.
* [API Types](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api): povoluje CRUD na typech časových řad a jejich přidružených proměnných.
* [Hierarchie rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api): povoluje CRUD v hierarchiích časových řad a jejich přidružených cestách k polím.
* [Rozhraní API instancí](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): povoluje CRUD v instancích časových řad a jejich přidružených polích instance. Rozhraní API instancí navíc podporuje následující operace:
  * [Hledání](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): načte částečný seznam přístupů při hledání instancí časových řad na základě atributů instance.
  * [Navrhnout](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): vyhledá a navrhne částečný seznam přístupů při hledání instancí časových řad na základě atributů instance.

## <a name="time-series-query-tsq-apis"></a>Rozhraní API pro Time Series Query (TSQ)

Tato rozhraní API jsou k dispozici ve všech obchodech v naší řešení úložiště s více vrstvami v Time Series Insights. Parametry adresy URL dotazu slouží k určení [typu úložiště](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) , na kterém by se měl dotaz spouštět:

* [Získat rozhraní API pro události](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): umožňuje dotazování a načítání nezpracovaných událostí a přidružených časových razítek událostí, protože se zaznamenávají v Time Series Insights ze zprostředkovatele zdroje. Toto rozhraní API umožňuje načtení nezpracovaných událostí pro dané ID časové řady a rozsah hledání. Toto rozhraní API podporuje stránkování, které načte datovou sadu kompletních odpovědí pro vybraný vstup. 

* [Získat rozhraní API pro řady](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): umožňuje dotazování a načítání vypočítaných hodnot a přidružených časových razítek událostí pomocí výpočtů definovaných proměnnými u nezpracovaných událostí. Tyto proměnné lze definovat buď v modelu časové řady, nebo v zadaném vloženém dotazu. Toto rozhraní API podporuje stránkování, které načte datovou sadu kompletních odpovědí pro vybraný vstup. 

* [Rozhraní API pro agregaci řad](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries): umožňuje dotazování a načítání agregovaných hodnot a přidružených časových razítek intervalu použitím výpočtů definovaných proměnnými u nezpracovaných událostí. Tyto proměnné lze definovat buď v modelu časové řady, nebo v zadaném vloženém dotazu. Toto rozhraní API podporuje stránkování, které načte datovou sadu kompletních odpovědí pro vybraný vstup. 
  
  V případě zadaného rozsahu hledání a intervalu vrátí toto rozhraní API agregovanou odpověď na proměnnou za interval pro ID časové řady. Počet intervalů v datové sadě odpovědí se počítá vynásobením epocha (počet milisekund, které uplynuly od operačního systému UNIX epocha-LED 1. ledna 1970), a rozdělením značek podle velikosti rozsahu intervalu určeného v dotazu.

  Časová razítka vrácená v sadě odpovědí jsou levé hranice intervalu, nikoli vzorky událostí z intervalu. 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o různých proměnných, které lze definovat v [modelu časové řady](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Přečtěte si další informace o tom, jak zadávat dotazy na data z [průzkumníka Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
