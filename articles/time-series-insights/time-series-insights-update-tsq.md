---
title: Dotazování na data ve verzi Preview – Přehledy azure časové řady | Dokumenty společnosti Microsoft
description: Koncepty dotazování na data a přehled rozhraní HTTP REST API ve verzi Azure Time Series Insights Preview.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284887"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Dotazování na data ve verzi Azure Time Series Insights Preview

Azure Time Series Insights umožňuje dotazování dat na události a metadata uložená v prostředí prostřednictvím veřejných povrchových api. Tato rozhraní API jsou také používány [Průzkumníkem přehledů časové řady](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

V přehledech time series jsou k dispozici tři primární kategorie rozhraní API:

* **Prostředí API**: Tato api umožňují dotazy na time series insights samotné prostředí. Ty lze použít ke shromažďování seznamu prostředí volající má přístup a metadata prostředí.
* **Časová řada Model-Query (TSM-Q) API**: Umožňuje vytvářet, číst, aktualizovat a odstraňovat (CRUD) operace na metadata uložená v modelu časové řady prostředí. Ty lze použít pro přístup a úpravy instancí, typů a hierarchií.
* **Časová řada dotazu (TSQ) API**: Umožňuje načítání telemetrie nebo události data, jak je zaznamenána od poskytovatele zdroje a umožňuje výkonné výpočty a agregace na data pomocí pokročilé skalární a agregační funkce.

Time Series Insights používá pro vyjádření výpočtů bohatý jazyk výrazů založený na [řetězecích, výraz časové řady (TSX).](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="azure-time-series-insights-core-apis"></a>Základní api Azure Time Series Insights

Jsou podporována následující základní řešení api.

[![Přehled dotazu časové řady](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Api prostředí

* [Get Environments API](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): Vrátí seznam prostředí, ke kterým má volající oprávnění.
* [Získat rozhraní API pro dostupnost prostředí](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): Vrátí distribuci `$ts`počtu událostí v časovém razítku události . Toto rozhraní API pomáhá určit, pokud existují nějaké události v prostředí vrácením počet událostí rozdělených do časových intervalech, pokud existují.
* [Získat rozhraní API schématu událostí](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): Vrátí metadata schématu události pro dané rozpětí hledání. Toto rozhraní API pomáhá načíst všechna metadata a vlastnosti, které jsou k dispozici ve schématu pro dané rozpětí hledání.

## <a name="time-series-model-query-tsm-q-apis"></a>Api pro dotaz na modelové řady (TSM-Q) časové řady

Většina těchto řešení API podporuje operaci dávkového spuštění, aby bylo možné povolit dávkové operace CRUD na více entitách modelu časové řady:

* [Rozhraní API pro nastavení modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api): Povolí funkce *GET* a *PATCH* u výchozího typu a názvu modelu prostředí.
* [Typy rozhraní API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api)Povolí crud na typy časové řady a jejich přidružené proměnné.
* [Rozhraní API hierarchií:](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api)Povolí hierarchie CRUD v hierarchiích časových řad a jejich přidružených trasách polí.
* [Instance API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api)Povolí crud na instance časové řady a jejich přidružené pole instancí. Rozhraní API instancí navíc podporuje následující operace:
  * [Hledání](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Načte částečný seznam přístupů při hledání instancí časových řad na základě atributů instance.
  * [Navrhnout](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): Vyhledá a navrhne částečný seznam přístupů při hledání instancí časových řad na základě atributů instance.

## <a name="time-series-query-tsq-apis"></a>Api dotazu časové řady (TSQ)

Tato rozhraní API jsou k dispozici ve všech obou obchodech v našem vícevrstvém úložném řešení v Time Series Insights. Parametry adresy URL dotazu se používají k určení [typu obchodu,](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) na který by měl dotaz provést:

* [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): Povolí dotaz a načítání nezpracovaných událostí a souvisejících časových razítek událostí, jak jsou zaznamenány v Time Series Insights od zdrojového zprostředkovatele. Toto rozhraní API umožňuje načítání nezpracovaných událostí pro dané ID časové řady a rozsah vyhledávání. Toto rozhraní API podporuje stránkování k načtení úplné datové sady odpovědí pro vybraný vstup. 

* [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): Umožňuje dotaz a načítání vypočítaných hodnot a souvisejících časových razítek událostí použitím výpočtů definovaných proměnnými na nezpracovaná události. Tyto proměnné lze definovat v modelu časové řady nebo zadejte vpovězi v dotazu. Toto rozhraní API podporuje stránkování k načtení úplné datové sady odpovědí pro vybraný vstup. 

* [Agregovat rozhraní API řady](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries): Umožňuje dotaz a načítání agregovaných hodnot a souvisejících časových razítek intervalu použitím výpočtů definovaných proměnnými na nezpracovaná události. Tyto proměnné lze definovat v modelu časové řady nebo zadejte vpovězi v dotazu. Toto rozhraní API podporuje stránkování k načtení úplné datové sady odpovědí pro vybraný vstup. 
  
  Pro zadané rozpětí vyhledávání a interval toto rozhraní API vrátí agregovní odpověď na proměnnou za interval pro ID časové řady. Počet intervalů v datové sadě odpovědí se vypočítá počítáním epochových značek (počet milisekund, které uplynuly od unixové epochy - 1.ledna, 1970) a vydělením značek velikostí intervalového rozsahu zadanou v dotazu.

  Časová razítka vrácená v sadě odpovědí jsou hranice levého intervalu, nikoli vzorkované události z intervalu. 

## <a name="next-steps"></a>Další kroky

- Přečtěte si více o různých proměnných, které lze definovat v [modelu časové řady](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Přečtěte si další informace o tom, jak dotazovat data z [Průzkumníka přehledů časové řady](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
