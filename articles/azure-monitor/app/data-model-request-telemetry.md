---
title: Datový model pro telemetrii žádostí – Azure Application Insights
description: Application Insights datový model pro telemetrii žádostí
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671898"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetrie žádostí: datový model Application Insights

Položka telemetrie žádosti (v [Application Insights](../../azure-monitor/app/app-insights-overview.md)) představuje logickou sekvenci spuštění spuštěnou externím požadavkem ve vaší aplikaci. Každé spuštění žádosti je identifikováno jedinečnými `ID` a `url` obsahující všechny parametry spuštění. Požadavky můžete seskupovat podle logických `name` a definovat `source` této žádosti. Spuštění kódu může mít za následek `success` nebo `fail` a má určité `duration`. Úspěšné i neúspěšné spuštění může seskupit `resultCode`. Počáteční čas pro telemetrii požadavků definovanou na úrovni obálky.

Telemetrie žádostí podporuje standardní rozšiřitelný model s využitím vlastních `properties` a `measurements`.

## <a name="name"></a>Název

Název žádosti představuje cestu kódu pořízenou pro zpracování žádosti. Nízká hodnota mohutnosti, která umožňuje lepší seskupení požadavků. V případě požadavků HTTP představuje šablonu metoda HTTP a cestu adresy URL, jako `GET /values/{id}` bez skutečné `id` hodnoty.

Application Insights webová sada SDK pošle název žádosti "tak, jak je", s ohledem na velikost písmen. Seskupení v uživatelském rozhraní rozlišuje velká a malá písmena, takže `GET /Home/Index` se počítá odděleně od `GET /home/INDEX`, i když to často vede ke stejnému spuštění kontroleru a akcí. Důvodem je, že adresy URL obecně [rozlišují velká a malá písmena](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Můžete chtít zjistit, jestli u adres URL zadaných velkými `404` došlo k ostatním. Další informace o shromažďování názvů žádostí najdete v tématu ASP.NET Web SDK v [blogovém příspěvku](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maximální délka: 1024 znaků

## <a name="id"></a>ID

Identifikátor instance volání požadavku. Používá se pro korelaci mezi požadavkem a dalšími položkami telemetrie. ID by mělo být globálně jedinečné. Další informace najdete na stránce [korelace](../../azure-monitor/app/correlation.md) .

Maximální délka: 128 znaků

## <a name="url"></a>URL

Žádost o adresu URL se všemi parametry řetězce dotazu

Maximální délka: 2048 znaků

## <a name="source"></a>Zdroj

Zdroj požadavku Příkladem je klíč instrumentace volajícího nebo IP adresy volajícího. Další informace najdete na stránce [korelace](../../azure-monitor/app/correlation.md) .

Maximální délka: 1024 znaků

## <a name="duration"></a>Doba platnosti

Doba trvání žádosti ve formátu: `DD.HH:MM:SS.MMMMMM`. Musí být kladný a menší než `1000`é dny. Toto pole je povinné, protože telemetrie požadavků představuje operaci začínající na začátku a na konci.

## <a name="response-code"></a>Kód odpovědi

Výsledek provedení žádosti Stavový kód HTTP pro požadavky HTTP. Pro jiné typy požadavků může být `HRESULT` typ hodnoty nebo výjimky.

Maximální délka: 1024 znaků

## <a name="success"></a>Úspěch

Označení úspěšného nebo neúspěšného volání. Toto pole je povinné. Pokud není explicitně nastavené na `false` – požadavek se považuje za úspěšný. Nastavte tuto hodnotu na `false`, pokud byla operace přerušena výjimkou nebo vrátila kód výsledku chyby.

Pro webové aplikace Application Insights definovat požadavek jako úspěšný, pokud je kód odpovědi menší než `400` nebo se rovná `401`. Existují však případy, kdy toto výchozí mapování neodpovídá sémantikě aplikace. Kód odpovědi `404` může indikovat "žádné záznamy", které mohou být součástí pravidelného toku. Může také označovat přerušený odkaz. Pro přerušené odkazy můžete dokonce implementovat pokročilejší logiku. Můžete označit poškozené odkazy jako chyby pouze v případě, že jsou tyto odkazy umístěny ve stejné lokalitě pomocí analýzy odkazující adresy URL. Nebo je při použití z mobilní aplikace společnosti označit jako neúspěšná. Podobně `301` a `302` signalizují selhání při přistupování z klienta, který nepodporuje přesměrování.

Částečně přijatý obsah `206` může indikovat selhání celkového požadavku. Application Insights koncový bod například obdrží dávku položek telemetrie jako jeden požadavek. Vrátí `206`, když některé položky v dávce nebyly úspěšně zpracovány. Zvýšená míra `206` označuje problém, který je třeba prozkoumat. Podobná logika se vztahuje na `207` více stavů, kde úspěch může být nejhorší z různých kódů odpovědi.

V [příspěvku na blogu](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/)si můžete přečíst další informace o kódu výsledku žádosti a stavový kód.

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další kroky

- [Zápis vlastní telemetrie žádostí](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Viz [datový model](data-model.md) pro typy Application Insights a datový model.
- Naučte se, jak [nakonfigurovat ASP.NET Core](../../azure-monitor/app/asp-net.md) aplikaci pomocí Application Insights.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované nástrojem Application Insights.
