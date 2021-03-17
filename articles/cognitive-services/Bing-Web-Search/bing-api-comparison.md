---
title: Co je rozhraní API pro vyhledávání Bingu?
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte o rozhraní API pro vyhledávání Bingu a o tom, jak můžete povolit rozpoznávání internetových hledání ve vašich aplikacích a službách.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 72b4755c6f01a10851e79cf274842f1599bc2c55
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349533"
---
# <a name="what-are-the-bing-search-apis"></a>Co je rozhraní API pro vyhledávání Bingu?

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Rozhraní API Vyhledávání Bingu umožňuje vytvářet aplikace a služby připojené k webu, které hledají webové stránky, obrázky, zprávy, polohu a další informace bez reklamy. Odesláním žádostí o hledání pomocí Vyhledávání Bingu rozhraní REST API nebo sad SDK získáte relevantní informace a obsah pro hledání na webu. V tomto článku se dozvíte o různých rozhraních API pro vyhledávání Bingu a o tom, jak můžete do svých aplikací a služeb integrovat vyhledávání rozpoznávání. Omezení cen a sazeb se můžou mezi rozhraními API lišit.

## <a name="the-bing-web-search-api"></a>rozhraní API Bingu pro vyhledávání na webu

[Rozhraní API Bingu pro vyhledávání na webu](../Bing-Web-Search/overview.md) vrátí webové stránky, obrázky, video, zprávy a další. Vyhledávací dotazy odeslané do tohoto rozhraní API můžete filtrovat, chcete-li zahrnout nebo vyloučit určité typy obsahu.

Zvažte použití rozhraní API Bingu pro vyhledávání na webu v aplikacích, které mohou vyžadovat hledání všech typů relevantního webového obsahu. Pokud vaše aplikace vyhledává konkrétní typ online obsahu, zvažte jedno z následujících rozhraní API pro vyhledávání:

## <a name="content-specific-bing-search-apis"></a>Rozhraní API pro vyhledávání v Bingu specifickém pro obsah

Následující rozhraní API pro vyhledávání Bingu vrátí konkrétní obsah z webu, jako jsou obrázky, novinky, místní firmy a videa.

| Rozhraní API Bingu | Popis |
| -- | -- |
| [Vyhledávání entit](../Bing-Entities-Search/overview.md) | Rozhraní API Bingu pro vyhledávání entit vrátí výsledky hledání obsahující entity, které mohou být osoby, místa nebo věci. V závislosti na dotazu vrátí rozhraní API jednu nebo více entit, které budou vyhovovat vyhledávacímu dotazu. Vyhledávací dotaz může zahrnovat zajímavosti jednotlivce, místní firmy, orientačních bodů, cíle a další. |
| [Vyhledávání obrázků](../Bing-Image-Search/overview.md) | Rozhraní API Bingu pro vyhledávání obrázků vám umožní vyhledat a najít vysoce kvalitní statické a animované obrázky podobné [Bing.com/images](https://www.Bing.com/images). Hledání můžete upřesnit tak, aby obsahovala nebo vyloučila obrázky podle atributu, včetně velikosti, barvy, licence a aktuálnosti. Můžete také Hledat vývojové obrázky, nahrávat obrázky, získat z nich přehledy a zobrazovat náhledy miniatur. |
| [Vyhledávání zpráv](../Bing-News-Search/search-the-web.md) | Rozhraní API Bingu pro vyhledávání zpráv vám umožní najít příběhy novinek, podobně jako [Bing.com/news](https://www.Bing.com/news). Rozhraní API vrátí články s příspěvky z několika zdrojů nebo konkrétních domén. Můžete hledat v různých kategoriích a získávat do nich články, hlavní příběhy a titulky. |
| [Vyhledávání videí](../Bing-Video-Search/overview.md) | Rozhraní API Bingu pro vyhledávání videí vám umožní najít videa napříč webem. Získejte vývojové videa, související obsah a náhledy miniatur. |
| [Vizuální vyhledávání](../Bing-visual-search/overview.md) | Nahrajte obrázek nebo použijte adresu URL, abyste získali přehledné informace, jako jsou vizuálně podobné produkty, obrázky a související hledání. |
 [Hledání místních obchodních aplikací](../bing-local-business-search/overview.md) | Rozhraní API pro místní vyhledávání Bingu umožňuje vašim aplikacím najít kontaktní a umístění informací o místních firmách na základě vyhledávacích dotazů. |

## <a name="the-bing-custom-search-api"></a>rozhraní API pro vlastní vyhledávání Bingu

Vytvořením vlastní instance hledání pomocí rozhraní [Vlastní vyhledávání Bingu](../Bing-Custom-Search/overview.md) API můžete vytvořit vyhledávání zaměřené na obsah a témata, která vás zajímají. Například po zadání domén, webů a konkrétních webových stránek, které Bing bude hledat, Vlastní vyhledávání Bingu přizpůsobí výsledky tomuto konkrétnímu obsahu. K dalšímu přizpůsobení možností vyhledávání můžete začlenit vlastní rozhraní API pro automatické návrhy, image a Vyhledávání videí Bingu.

## <a name="additional-bing-search-apis"></a>Další rozhraní API pro vyhledávání Bingu

Následující rozhraní API pro vyhledávání Bingu vám umožní vylepšit možnosti vyhledávání jejich kombinováním s jinými rozhraními API pro vyhledávání Bingu.

| Rozhraní API | Popis |
| -- | -- |
| [Automatické návrhy Bingu](../Bing-Autosuggest/get-suggested-search-terms.md) | Vylepšete možnosti prohledávání vaší aplikace pomocí rozhraní API pro automatické návrhy Bingu vrácením navrhovaných hledání v reálném čase.  |
| [Statistika Bingu](bing-web-stats.md) | Statistika Bingu poskytuje analýzy pro rozhraní API pro vyhledávání Bingu, které vaše aplikace používá. Mezi dostupné analýzy patří objem volání, nejčastější řetězce dotazů a geografická distribuce. |

## <a name="next-steps"></a>Další kroky

* [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) rozhraní vyhledávání Bingu API
* [Požadavky Bingu na zobrazení a použití](./use-display-requirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.