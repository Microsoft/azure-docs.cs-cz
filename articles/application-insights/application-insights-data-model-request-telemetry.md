---
title: Azure Application Insights Telemetrických dat Model - Telemetrie žádostí | Dokumentace Microsoftu
description: Application Insights datový model pro telemetrie žádostí
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: d3a2fe9b51e0e6e9de653abb1bfa56a2529218cb
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722459"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetrie žádostí: datový model Application Insights

Položky telemetrie požadavku (v [Application Insights](app-insights-overview.md)) představuje Logická posloupnost spuštění aktivuje externí žádostí do vaší aplikace. Každé provedení požadavku je identifikován jedinečný `ID` a `url` obsahující všechny parametry spuštění. Požadavky můžete seskupovat podle logického `name` a definovat `source` této žádosti. Spuštění kódu může vést k `success` nebo `fail` a má určitou `duration`. Auditování úspěšných a neúspěšných spuštění může být seskupené podle `resultCode`. Počáteční čas pro telemetrie žádostí, které jsou definované na úrovni obálky.

Žádost o telemetrii podporuje standardní rozšiřitelném modelu používání vlastní `properties` a `measurements`.

## <a name="name"></a>Název

Název žádosti představuje cestu kódu potřebný ke zpracování požadavku. Kardinalita s nízkou hodnotu umožňující lepší seskupení požadavků. Pro požadavky HTTP, to představuje metodu HTTP a šablonu cesty URL jako `GET /values/{id}` bez skutečného `id` hodnotu.

Application Insights web SDK odešle žádost o název "tak jak jsou" s ohledem na písmen. Seskupení podle uživatelského rozhraní je velká a malá písmena, `GET /Home/Index` se počítá samostatně z `GET /home/INDEX` i v případě, že jsou často výsledkem stejné provádění kontroleru a akce. Důvod, která je obecně jsou adresy URL [malá a velká písmena](http://www.w3.org/TR/WD-html40-970708/htmlweb.html). Můžete chtít zobrazit, když všechny `404` se naplnilo pro adresy URL zadané na velká písmena. Můžete si přečíst další na žádost o název kolekce pomocí sady SDK webové technologie ASP.Net v [blogový příspěvek](http://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maximální délka: 1024 znaků.

## <a name="id"></a>ID

Identifikátor instance volání žádosti. Slouží pro korelaci mezi žádostí a další položky telemetrie. ID by měl být globálně jedinečný. Další informace najdete v tématu [korelace](application-insights-correlation.md) stránky.

Maximální délka: 128 znaků

## <a name="url"></a>URL

Adresa URL požadavku se všemi parametry řetězce dotazu.

Maximální délka: 2 048 znaků

## <a name="source"></a>Zdroj

Zdroj žádosti. Příklady jsou Instrumentační klíč volajícího nebo ip adresy volajícího. Další informace najdete v tématu [korelace](application-insights-correlation.md) stránky.

Maximální délka: 1024 znaků.

## <a name="duration"></a>Doba trvání

Doba trvání ve formátu žádosti: `DD.HH:MM:SS.MMMMMM`. Musí být kladný a menší než `1000` dnů. Toto pole je povinné, protože telemetrie žádostí představuje operaci se na začátek a konec.

## <a name="response-code"></a>Kód odpovědi

Výsledek provádění požadavku. Stavový kód HTTP pro požadavky HTTP. To může být `HRESULT` typ hodnoty nebo výjimky pro ostatní typy požadavků.

Maximální délka: 1024 znaků.

## <a name="success"></a>Úspěch

Údaj o volání úspěšné nebo neúspěšné. Toto pole je povinné. Pokud není nastavený explicitně na `false` -žádost považována za k dosažení úspěchu. Nastavte tuto hodnotu na `false` Pokud operace se přerušila výjimka nebo vrátilo kód chyby výsledek.

Application Insights pro webové aplikace, definovat požadavek, jako neúspěšný, pokud kód odpovědi menší `400` nebo rovna hodnotě `401`. Ale existují případy, když toto výchozí mapování se neshoduje s sémantické aplikace. Kód odpovědi `404` může znamenat "žádné záznamy", které mohou být součástí regulární toku. Také může to znamenat poškozený odkaz. Nefunkční odkazy můžete dokonce implementovat složitější logiku. Nefunkční odkazy můžete označit jako selhání pouze v případě, že tyto odkazy jsou umístěné ve stejné lokalitě díky analýze odkaz poskytuje adresu url. Nebo označit je jako selhání při přístupu z mobilní aplikace vaší společnosti. Podobně `301` a `302` indikuje selhání při přístupu z klienta, který nepodporuje přesměrování.

Částečně přijmout obsah `206` může znamenat selhání celkový požadavek. Koncový bod služby Application Insights pro instanci přijímá dávky položky telemetrie jako jeden požadavek. Vrátí `206` kdy některé položky v dávce nebyly úspěšně zpracována. Rostoucí počet `206` indikuje problém, který je nutné prozkoumat. Podobná logika platí pro `207` více stav, kdy úspěch může být nejhorší ze samostatných kódů odpovědí.

Si můžete přečíst další výsledek na žádost kód a stavovým kódem [blogový příspěvek](http://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další postup

- [Napište vlastní požadavek telemetrii](app-insights-api-custom-events-metrics.md#trackrequest)
- Zobrazit [datový model](application-insights-data-model.md) pro typy a datový model Application Insights.
- Zjistěte, jak [konfigurace ASP.NET Core](app-insights-asp-net.md) aplikace pomocí Application Insights.
- Podívejte se na [platformy](app-insights-platforms.md) podporované službou Application Insights.
