---
title: Dotazování na data – Azure Time Series Insights Gen2 | Microsoft Docs
description: Základy dotazování dat a REST API přehled Azure Time Series Insights Gen2.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: b1b055fa7f083bd8bccda16498e2894d5d67eace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374129"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Dotazy na data z Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 umožňuje dotazování dat na události a metadata uložená v prostředí prostřednictvím rozhraní API pro veřejná Surface. Tato rozhraní API se používají také v [průzkumníkovi Azure Time Series Insights TSI](./concepts-ux-panels.md).

V Azure Time Series Insights Gen2 jsou k dispozici tři primární kategorie rozhraní API:

* **Rozhraní API prostředí**: Tato rozhraní API povolují dotazy v prostředí Azure Time Series Insights Gen2 samotné. Ty lze použít ke shromáždění seznamu prostředí, ke kterým má volající přístup, a k metadatům prostředí.
* **Rozhraní API Time series Model-Query (TSM-Q)**: povoluje operace vytvoření, čtení, aktualizace a odstranění (CRUD) v metadatech uložených v modelu časových řad prostředí. Ty je možné použít pro přístup k instancím, typům a hierarchiím a k jejich úpravám.
* **Rozhraní API pro Time Series Query (TSQ)**: umožňuje načíst data telemetrie nebo událostí, jak jsou zaznamenána od poskytovatele zdroje, a umožňuje provádět výpočty a agregace dat pomocí pokročilých skalárních a agregačních funkcí.

Azure Time Series Insights Gen2 používá pro vyjádření výpočtů v [proměnných časových řad](./concepts-variables.md)bohatý jazyk výrazů založený na řetězci, [výraz Time Series (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax).

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Přehled rozhraní Gen2 API Azure Time Series Insights

Podporují se následující základní rozhraní API.

[![Přehled dotazů na časové řady](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Rozhraní API prostředí

* [Získat rozhraní API prostředí](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment): vrátí seznam prostředí, ke kterým má volající oprávnění k přístupu.
* [Získat rozhraní API pro dostupnost prostředí](/rest/api/time-series-insights/dataaccessgen2/query/getavailability): vrátí distribuci počtu událostí přes časové razítko události `$ts` . Toto rozhraní API pomáhá určit, jestli se v prostředí vyskytují nějaké události, a to tak, že vrátí počet událostí v časových intervalech, pokud existují.
* [Získat rozhraní API pro schéma událostí](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): vrátí metadata schématu události pro daný rozsah hledání. Toto rozhraní API pomáhá načíst všechna metadata a vlastnosti, které jsou ve schématu k dispozici pro daný rozsah hledání.

## <a name="time-series-model-query-tsm-q-apis"></a>Rozhraní API Time Series Model-Query (TSM-Q)

Většina těchto rozhraní API podporuje operaci dávkového spouštění, aby umožnila dávkové operace CRUD u více entit modelu časové řady:

* [Rozhraní API pro nastavení modelu](/rest/api/time-series-insights/reference-model-apis): povolí možnost *získat* a *opravit* na výchozím typu a název modelu prostředí.
* [API Types](/rest/api/time-series-insights/reference-model-apis#types-api): povoluje CRUD na typech časových řad a jejich přidružených proměnných.
* [Hierarchie rozhraní API](/rest/api/time-series-insights/reference-model-apis#hierarchies-api): povoluje CRUD v hierarchiích časových řad a jejich přidružených cestách k polím.
* [Rozhraní API instancí](/rest/api/time-series-insights/reference-model-apis#instances-api): povoluje CRUD v instancích časových řad a jejich přidružených polích instance. Rozhraní API instancí navíc podporuje následující operace:
  * [Hledání](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): načte částečný seznam přístupů při hledání instancí časových řad na základě atributů instance.
  * [Navrhnout](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest): vyhledá a navrhne částečný seznam přístupů při hledání instancí časových řad na základě atributů instance.

## <a name="time-series-query-tsq-apis"></a>Rozhraní API pro Time Series Query (TSQ)

Tato rozhraní API jsou v našem řešení úložiště s více vrstvami k dispozici v obou úložištích (teplé i studené). 

* [Získat API pro události](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): umožňuje dotazování a načítání nezpracovaných událostí a přidružených časových razítek událostí, protože se zaznamenávají do Azure Time Series Insights Gen2 od poskytovatele zdroje. Toto rozhraní API umožňuje načtení nezpracovaných událostí pro dané ID časové řady a rozsah hledání. Toto rozhraní API podporuje stránkování, které načte datovou sadu kompletních odpovědí pro vybraný vstup.

  > [!IMPORTANT]
  > Jako součást [nadcházejících změn pro pravidla sloučení a uvozovací znaky JSON](./ingestion-rules-update.md)budou pole uložená jako **dynamický** typ. Vlastnosti datové části uložené jako tento typ jsou **přístupné jenom prostřednictvím rozhraní API pro získání událostí**.

* [Získat rozhraní API pro řady](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): umožňuje dotazování a načítání vypočítaných hodnot a přidružených časových razítek událostí pomocí výpočtů definovaných proměnnými u nezpracovaných událostí. Tyto proměnné lze definovat buď v modelu časové řady, nebo v zadaném vloženém dotazu. Toto rozhraní API podporuje stránkování, které načte datovou sadu kompletních odpovědí pro vybraný vstup.

* [Rozhraní API pro agregaci řad](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): umožňuje dotazování a načítání agregovaných hodnot a přidružených časových razítek intervalu použitím výpočtů definovaných proměnnými u nezpracovaných událostí. Tyto proměnné lze definovat buď v modelu časové řady, nebo v zadaném vloženém dotazu. Toto rozhraní API podporuje stránkování, které načte datovou sadu kompletních odpovědí pro vybraný vstup.
  
  V případě zadaného rozsahu hledání a intervalu vrátí toto rozhraní API agregovanou odezvu na jeden interval na proměnnou pro ID časové řady. Počet intervalů v datové sadě odpovědí se počítá vynásobením epocha (počet milisekund, které uplynuly od operačního systému UNIX epocha-LED 1. ledna 1970), a rozdělením značek podle velikosti rozsahu intervalu určeného v dotazu.

  Časová razítka vrácená v sadě odpovědí jsou levé hranice intervalu, nikoli vzorky událostí z intervalu.


### <a name="selecting-store-type"></a>Výběr typu úložiště

Výše uvedená rozhraní API lze spustit pouze proti jednomu z těchto dvou typů úložiště (studená nebo teplá) v rámci jednoho volání. Parametry adresy URL dotazu se používají k určení [typu úložiště](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) , ve kterém se má dotaz spustit. 

Pokud není zadán žádný parametr, dotaz bude ve výchozím nastavení spuštěn v chladírenském skladu. Pokud dotaz pokrývá časový rozsah, který se překrývá s studeným i teplým obchodem, doporučuje se směrovat dotaz do studeného úložiště pro dosažení optimálního prostředí, protože úložiště tepla bude obsahovat pouze částečná data. 

[Azure Time Series Insights Explorer](./concepts-ux-panels.md) a [konektor Power BI](./how-to-connect-power-bi.md) volají volání výše uvedených rozhraní API a automaticky vybere správný parametr storeType, kde je to relevantní. 


## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o různých proměnných, které lze definovat v [modelu časové řady](./concepts-model-overview.md).
* Přečtěte si další informace o tom, jak zadávat dotazy na data z [průzkumníka Azure Time Series Insights](./concepts-ux-panels.md).
