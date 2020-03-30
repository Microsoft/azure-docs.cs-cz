---
title: Datový model pro telemetrii požadavků – Přehledy aplikací Azure
description: Datový model Application Insights pro telemetrii požadavků
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671898"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetrie požadavku: Datový model Application Insights

Položka telemetrie požadavku (v [application insights)](../../azure-monitor/app/app-insights-overview.md)představuje logickou posloupnost spuštění aktivovanou externím požadavkem pro vaši aplikaci. Každé spuštění požadavku je `ID` `url` identifikováno jedinečným a obsahuje všechny parametry spuštění. Můžete seskupit `name` požadavky `source` podle logiky a definovat tento požadavek. Spuštění kódu může `success` `fail` mít za `duration`následek nebo a má určité . Úspěšné i neúspěšné spuštění může být `resultCode`dále seskupeno podle . Počáteční čas pro telemetrii požadavku definovanou na úrovni obálky.

Telemetrie požadavku podporuje standardní model rozšiřitelnosti pomocí vlastní `properties` a `measurements`.

## <a name="name"></a>Name (Název)

Název požadavku představuje cestu kódu přijata ke zpracování požadavku. Nízká hodnota mohutnosti, která umožňuje lepší seskupení požadavků. Pro požadavky HTTP představuje metodu HTTP a `GET /values/{id}` šablonu `id` cesty URL, jako bez skutečné hodnoty.

Sada Application Insights web SDK odesílá název požadavku "tak, jak je" s ohledem na malá a velká písmena. Seskupení v ui je `GET /Home/Index` malá a velká `GET /home/INDEX` písmena, takže se počítá odděleně od i když často mají za následek stejný řadič a provádění akce. Důvodem je, že adresy URL obecně [rozlišují malá a velká písmena](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Možná budete chtít zjistit, zda `404` vše se stalo pro adresy URL zadali velkými písmeny. Další informace o kolekci názvů žádostí si můžete přečíst ASP.NET web ovou sadou SDK v [příspěvku](https://apmtips.com/blog/2015/02/23/request-name-and-url/)blogu .

Maximální délka: 1024 znaků

## <a name="id"></a>ID

Identifikátor instance volání požadavku. Používá se pro korelaci mezi požadavkem a jinými položkami telemetrie. ID by mělo být globálně jedinečné. Další informace naleznete na stránce [korelace.](../../azure-monitor/app/correlation.md)

Maximální délka: 128 znaků

## <a name="url"></a>URL

Adresa URL požadavku se všemi parametry řetězce dotazu.

Maximální délka: 2048 znaků

## <a name="source"></a>Zdroj

Zdroj požadavku. Příklady jsou instrumentace klíč volajícího nebo IP adresu volajícího. Další informace naleznete na stránce [korelace.](../../azure-monitor/app/correlation.md)

Maximální délka: 1024 znaků

## <a name="duration"></a>Doba trvání

Doba trvání požadavku `DD.HH:MM:SS.MMMMMM`ve formátu: . Musí být pozitivní `1000` a méně než dny. Toto pole je vyžadováno jako telemetrie požadavku představuje operaci s začátkem a koncem.

## <a name="response-code"></a>Kód odpovědi

Výsledek spuštění požadavku. Stavový kód HTTP pro požadavky HTTP. Může to `HRESULT` být typ hodnoty nebo výjimky pro jiné typy požadavků.

Maximální délka: 1024 znaků

## <a name="success"></a>Úspěch

Indikace úspěšného nebo neúspěšného volání. Toto pole je vyžadováno. Pokud není explicitně nastavena `false` - požadavek je považován za úspěšný. Tuto hodnotu `false` nastavte na hodnotu, pokud byla operace přerušena výjimkou nebo vrácenkód výsledku chyby.

Pro webové aplikace Application Insights definovat požadavek jako úspěšný, `400` pokud `401`je kód odpovědi menší nebo rovno . Existují však případy, kdy toto výchozí mapování neodpovídá sémantické aplikace. Kód `404` odezvy může označovat "žádné záznamy", které mohou být součástí pravidelného toku. Může také znamenat nefunkční spojení. Pro nefunkční odkazy můžete dokonce implementovat pokročilejší logiku. Nefunkční odkazy můžete označit jako chyby pouze v případě, že tyto odkazy jsou umístěny na stejném webu analýzou url referrer. Nebo je označte jako selhání při přístupu z mobilní aplikace společnosti. Podobně `301` a `302` označuje selhání při přístupu z klienta, který nepodporuje přesměrování.

Částečně přijatý `206` obsah může znamenat selhání celkové žádosti. Například koncový bod Application Insights obdrží dávku položek telemetrie jako jeden požadavek. Vrátí `206` se, když některé položky v dávce nebyly úspěšně zpracovány. Rostoucí míra `206` označuje problém, který je třeba prozkoumat. Podobná logika `207` platí pro multi-Status, kde úspěch může být nejhorší samostatné kódy odpovědí.

Můžete si přečíst více na žádost výsledek kód a stavový kód v [blogu](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další kroky

- [Zapsat vlastní telemetrii požadavků](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Viz [datový model](data-model.md) pro typy application insights a datový model.
- Přečtěte si, jak [nakonfigurovat ASP.NET základní](../../azure-monitor/app/asp-net.md) aplikaci pomocí Application Insights.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované Application Insights.
