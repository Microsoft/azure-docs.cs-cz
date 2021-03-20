---
title: Datový model pro telemetrii žádostí – Azure Application Insights
description: Application Insights datový model pro telemetrii žádostí
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 7a352f4ce3528d395599a91b53031c74b0873152
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320557"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetrie žádostí: datový model Application Insights

Položka telemetrie žádosti (v [Application Insights](./app-insights-overview.md)) představuje logickou sekvenci spuštění spuštěnou externím požadavkem ve vaší aplikaci. Každé spuštění žádosti je identifikováno jedinečnými `ID` a `url` obsahujícími všechny parametry spuštění. Požadavky můžete seskupit podle logických požadavků `name` a definovat `source` tuto žádost. Spuštění kódu může mít za následek `success` nebo `fail` a má nějaký určitý `duration` . Úspěšnost i spuštění při selhání mohou být seskupeny `resultCode` . Počáteční čas pro telemetrii požadavků definovanou na úrovni obálky.

Telemetrie žádostí podporuje standardní rozšiřitelný model pomocí Custom `properties` a `measurements` .

## <a name="name"></a>Name

Název žádosti představuje cestu kódu pořízenou pro zpracování žádosti. Nízká hodnota mohutnosti, která umožňuje lepší seskupení požadavků. V případě požadavků HTTP představuje šablonu metoda HTTP a cestu adresy URL, například `GET /values/{id}` bez skutečné `id` hodnoty.

Application Insights webová sada SDK pošle název žádosti "tak, jak je", s ohledem na velikost písmen. Seskupení v uživatelském rozhraní rozlišuje velká a malá písmena, takže `GET /Home/Index` se počítá odděleně od sebe, `GET /home/INDEX` i když to často vede ke stejnému spuštění kontroleru a akcí. Důvodem je, že adresy URL obecně [rozlišují velká a malá písmena](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Možná budete chtít zjistit, jestli `404` se pro adresy URL zadané velkými písmeny nastaly všechny. Další informace o shromažďování názvů žádostí najdete v tématu ASP.NET Web SDK v [blogovém příspěvku](https://apmtips.com/posts/2015-02-23-request-name-and-url/).

Maximální délka: 1024 znaků

## <a name="id"></a>ID

Identifikátor instance volání požadavku. Používá se pro korelaci mezi požadavkem a dalšími položkami telemetrie. ID by mělo být globálně jedinečné. Další informace najdete na stránce [korelace](./correlation.md) .

Maximální délka: 128 znaků

## <a name="url"></a>URL

Žádost o adresu URL se všemi parametry řetězce dotazu

Maximální délka: 2048 znaků

## <a name="source"></a>Zdroj

Zdroj požadavku Příkladem je klíč instrumentace volajícího nebo IP adresy volajícího. Další informace najdete na stránce [korelace](./correlation.md) .

Maximální délka: 1024 znaků

## <a name="duration"></a>Doba trvání

Doba trvání žádosti ve formátu: `DD.HH:MM:SS.MMMMMM` . Musí být kladný a menší než `1000` počet dní. Toto pole je povinné, protože telemetrie požadavků představuje operaci začínající na začátku a na konci.

## <a name="response-code"></a>Kód odpovědi

Výsledek provedení žádosti Stavový kód HTTP pro požadavky HTTP. Může to být `HRESULT` typ hodnoty nebo výjimky pro jiné typy požadavků.

Maximální délka: 1024 znaků

## <a name="success"></a>Success

Označení úspěšného nebo neúspěšného volání. Toto pole je vyžadováno. Pokud není explicitně nastaveno na `false` -, je žádost považována za úspěšnou. Nastavte tuto hodnotu na, `false` Pokud byla operace přerušena výjimkou nebo vrátila kód výsledku chyby.

Pro webové aplikace Application Insights definovat požadavek jako úspěšný, pokud je kód odpovědi menší `400` nebo roven `401` . Existují však případy, kdy toto výchozí mapování neodpovídá sémantikě aplikace. Kód odpovědi `404` může označovat položku "žádné záznamy", která může být součástí pravidelného toku. Může také označovat přerušený odkaz. Pro přerušené odkazy můžete dokonce implementovat pokročilejší logiku. Můžete označit poškozené odkazy jako chyby pouze v případě, že jsou tyto odkazy umístěny ve stejné lokalitě pomocí analýzy odkazující adresy URL. Nebo je při použití z mobilní aplikace společnosti označit jako neúspěšná. Podobně `301` a `302` označuje selhání při přistupování z klienta, který nepodporuje přesměrování.

Částečně přijatý obsah `206` může indikovat selhání celkové žádosti. Application Insights koncový bod například obdrží dávku položek telemetrie jako jeden požadavek. Vrátí se, `206` když některé položky v dávce nebyly úspěšně zpracovány. Zvýšená míra `206` znamená problém, který je třeba prozkoumat. Podobná logika se vztahuje na `207` Vícenásobný stav, kde úspěch může být nejhorší z různých kódů odpovědí.

V [příspěvku na blogu](https://apmtips.com/posts/2016-12-03-request-success-and-response-code/)si můžete přečíst další informace o kódu výsledku žádosti a stavový kód.

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další kroky

- [Zápis vlastní telemetrie žádostí](./api-custom-events-metrics.md#trackrequest)
- Viz [datový model](data-model.md) pro typy Application Insights a datový model.
- Naučte se, jak [nakonfigurovat ASP.NET Core](./asp-net.md) aplikaci pomocí Application Insights.
- Podívejte se na [platformy](./platforms.md) podporované nástrojem Application Insights.

