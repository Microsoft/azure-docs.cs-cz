---
title: Azure Application Insights Telemetrická Data modelu - požadavku Telemetrie | Microsoft Docs
description: Aplikace Insights datový model pro požadavek telemetrie
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: e0bdaf132474d8e5eaac6a9c65093d27d673d343
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="request-telemetry-application-insights-data-model"></a>Žádosti o telemetrie: Application Insights datový model

Položku telemetrie požadavku (v [Application Insights](app-insights-overview.md)) představuje logické pořadí provádění aktivovány externí požadavek do vaší aplikace. Každé provedení požadavku je identifikována jedinečný `ID` a `url` obsahující všechny parametry spuštění. Požadavky můžete seskupovat podle logické `name` a definovat `source` této žádosti. Může mít za následek provádění kódu `success` nebo `fail` a má určitou `duration`. Úspěchy a chyby jednotlivými spuštěními může být seskupené ještě `resultCode`. Počáteční čas pro požadavek telemetrii definované na úrovni obálku.

Žádost o telemetrie podporuje standardní rozšíření modelu s použitím vlastní `properties` a `measurements`.

## <a name="name"></a>Název

Název žádosti představuje kód trasu pro zpracování požadavku. Hodnota nízkou mohutnost umožňující lepší seskupování požadavků. Pro požadavky HTTP představuje metodu HTTP a šablonu cesty URL jako `GET /values/{id}` bez skutečnou `id` hodnotu.

Application Insights webové SDK odešle žádost o název "tak, jak je s ohledem na případ písmeno. Seskupení v uživatelském rozhraní je malá a velká písmena, `GET /Home/Index` se počítá samostatně z `GET /home/INDEX` Přestože často výsledkem stejné provádění kontroleru a akce. Z důvodu, pro který je obecně se adresy URL [malá a velká písmena](http://www.w3.org/TR/WD-html40-970708/htmlweb.html). Můžete chtít zjistit, zda všechny `404` došlo adres URL, zadali velkými písmeny. Můžete si přečíst další na žádost o název kolekce pomocí sady SDK webové technologie ASP.Net v [příspěvku na blogu](http://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maximální délka: 1024 znaků

## <a name="id"></a>ID

Identifikátor instance volání požadavku. Slouží pro korelaci mezi žádostí a dalším položkám telemetrie. ID by měly být globálně jedinečný. Další informace najdete v tématu [korelace](application-insights-correlation.md) stránky.

Maximální délka: 128 znaků.

## <a name="url"></a>URL

Adresa URL požadavku se všemi parametry řetězce dotazu.

Maximální délka: 2 048 znaků

## <a name="source"></a>Zdroj

Zdroj žádosti. Příklady jsou klíč instrumentace volajícího nebo ip adresu volajícího. Další informace najdete v tématu [korelace](application-insights-correlation.md) stránky.

Maximální délka: 1024 znaků

## <a name="duration"></a>Doba trvání

Žádosti o trvání ve formátu: `DD.HH:MM:SS.MMMMMM`. Musí být kladné a menší než `1000` dnů. Toto pole je požadována, protože představuje telemetrická žádost o operaci s začátku a konci.

## <a name="response-code"></a>Kód odpovědi

Výsledek provádění požadavku. Stavový kód HTTP pro požadavky HTTP. To může být `HRESULT` hodnotu nebo výjimka typu pro ostatní typy požadavků.

Maximální délka: 1024 znaků

## <a name="success"></a>Úspěch

Údaj o volání úspěšná nebo neúspěšná. Toto pole je povinné. Pokud není nastavena explicitně na `false` -požadavek považován za úspěšný. Nastavte hodnotu `false` Pokud operace byla přerušena výjimkou nebo vrátila kód chyby výsledek.

Application Insights pro webové aplikace, definujte požadavku, jako se nezdařilo, pokud kód odpovědi menší `400` nebo rovna hodnotě `401`. Ale existují případy, když toto výchozí mapování neodpovídá sémantického aplikace. Kód odpovědi `404` může znamenat, že žádné záznamy,", které můžou být součástí regulární toku. Také může to znamenat poškozený odkaz. Nefunkční odkazy můžete implementovat i pokročilejší logiku. Poškozených odkazů můžete označit jako selhání pouze v případě, že tyto odkazy jsou umístěné ve stejné lokalitě analýzou adresy url odkazující server. Nebo označit je jako selhání při přístupu z mobilních aplikací společnosti. Podobně `301` a `302` označuje selhání při přístupu z klienta, který nepodporuje přesměrování.

Částečně přijmout obsah `206` může znamenat selhání celkové požadavku. Koncový bod služby Application Insights pro instanci přijímá dávky položky telemetrie jako jeden požadavek. Vrátí `206` při nebyly úspěšně zpracovány některé položky v dávce. Roste počet `206` označuje problém, který musí být prozkoumat. Podobně jako logika platí pro `207` více stav, kdy úspěch může být nejhorší ze samostatných kódů odpovědí.

Si můžete přečíst další výsledek na žádost kód a kód stavu v [příspěvku na blogu](http://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další postup

- [Psát vlastní požadavek telemetrii](app-insights-api-custom-events-metrics.md#trackrequest)
- V tématu [datový model](application-insights-data-model.md) Application Insights typy a data modelu.
- Zjistěte, jak [konfigurace ASP.NET Core](app-insights-asp-net.md) aplikace s Application Insights.
- Podívejte se na [platformy](app-insights-platforms.md) nepodporuje Application Insights.
