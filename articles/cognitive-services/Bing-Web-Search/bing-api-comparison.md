---
title: Co jsou vyhledávací api Bingu?
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte o vyhledávacích apich Bingu a o tom, jak můžete povolit kognitivní vyhledávání na internetu ve svých aplikacích a službách.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 82b1f13562a49284059c25bcbd39a33daf949dcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74775536"
---
# <a name="what-are-the-bing-search-apis"></a>Co jsou vyhledávací api Bingu?

Rozhraní API Vyhledávání Bingu umožňuje vytvářet aplikace a služby připojené k webu, které hledají webové stránky, obrázky, zprávy, polohu a další informace bez reklamy. Odesláním žádostí o vyhledávání pomocí souborů REST API pro vyhledávání Bing u. V tomto článku se dozvíte o různých vyhledávacích api bingu a o tom, jak můžete integrovat kognitivní vyhledávání do aplikací a služeb. Ceny a omezení sazeb se mohou u jednotlivých api lišit.

## <a name="the-bing-web-search-api"></a>Rozhraní API pro vyhledávání na webu Bingu

Rozhraní [API pro vyhledávání na webu Bingu](../Bing-Web-Search/overview.md) vrací webové stránky, obrázky, video, zprávy a další. Vyhledávací dotazy odeslané do tohoto rozhraní API můžete filtrovat tak, aby zahrnovaly nebo vyloučily určité typy obsahu.

Zvažte použití rozhraní API pro vyhledávání na webu Bingu v aplikacích, které mohou vyžadovat hledání všech typů relevantního webového obsahu. Pokud vaše aplikace hledá konkrétní typ online obsahu, zvažte jeden z vyhledávacích api níže:

## <a name="content-specific-bing-search-apis"></a>Hledání ve vyhledávacích apich pro daný obsah bingu

Následující vyhledávací api Bingu vracejí určitý obsah z webu, jako jsou obrázky, zprávy, místní firmy a videa.

| Rozhraní API Bingu | Popis |
| -- | -- |
| [Vyhledávání entit](../Bing-Entities-Search/overview.md) | Rozhraní API pro vyhledávání entit Bingu vrátí výsledky hledání obsahující entity, což mohou být osoby, místa nebo věci. V závislosti na dotazu rozhraní API vrátí jednu nebo více entit, které splňují vyhledávací dotaz. Vyhledávací dotaz může obsahovat významné osoby, místní firmy, orientační body, cíle a další. |
| [Vyhledávání obrázků](../Bing-Image-Search/overview.md) | Rozhraní API pro vyhledávání obrázků bingu umožňuje vyhledávat a vyhledávat statické a animované obrázky ve vysoké kvalitě podobné [Bing.com/images](https://www.Bing.com/images). Hledání můžete upřesnit tak, aby zahrnovala nebo vyloučila obrázky podle atributu, včetně velikosti, barvy, licence a čerstvosti. Můžete také vyhledávat populární obrázky, nahrávat obrázky, abyste o nich získali přehled, a zobrazit náhledy miniatur. |
| [Vyhledávání zpráv](../Bing-News-Search/search-the-web.md) | Rozhraní API pro vyhledávání zpráv Bing udává podobné novinové články [jako Bing.com/news](https://www.Bing.com/news). Rozhraní API vrátí zpravodajské články z více zdrojů nebo konkrétních domén. Můžete vyhledávat napříč kategoriemi a získat trendy články, hlavní články a titulky. |
| [Vyhledávání videí](../Bing-Video-Search/overview.md) | Rozhraní API pro vyhledávání videí Bing umožňuje vyhledávat videa na webu. Získejte populární videa, související obsah a náhledy miniatur. |
| [Vizuální vyhledávání](../Bing-visual-search/overview.md) | Nahrajte obrázek nebo použijte adresu URL, abyste získali podrobné informace o něm, jako jsou vizuálně podobné produkty, obrázky a související vyhledávání. |
 [Vyhledávání místních podniků](../bing-local-business-search/overview.md) | Rozhraní API pro vyhledávání místních firem Bingu umožňuje aplikacím vyhledávat kontaktní a lokalizační informace o místních firmách na základě vyhledávacích dotazů. |

## <a name="the-bing-custom-search-api"></a>Rozhraní API pro vlastní vyhledávání Bingu

Vytvoření vlastní instance vyhledávání pomocí [rozhraní API pro vlastní vyhledávání Bingu](../Bing-Custom-Search/overview.md) umožňuje vytvořit prostředí pro vyhledávání zaměřené pouze na obsah a témata, která vás zajímají. Například po zadání domén, webů a konkrétních webových stránek, které bude Bing vyhledávat, přizpůsobí vlastní vyhledávání Bingvýsledky tomuto konkrétnímu obsahu. Můžete začlenit vlastní automatické návrhy Bingu, obrázky a hledání videa API pro další přizpůsobení vyhledávání.

## <a name="additional-bing-search-apis"></a>Další vyhledávací přístupová pole Bing

Následující vyhledávací api Bingu umožňují zlepšit vyhledávání tím, že je zkombinujete s jinými vyhledávacími api Bingu.

| rozhraní API | Popis |
| -- | -- |
| [Automatické návrhy Bingu](../Bing-Autosuggest/get-suggested-search-terms.md) | Zlepšete možnosti hledání aplikace pomocí rozhraní API automatického návrhu Bingu vrácením doporučených vyhledávání v reálném čase.  |
| [Statistiky Bingu](bing-web-stats.md) | Statistika Bingu poskytuje analýzy pro vyhledávací api Bingu, která vaše aplikace používá. Některé z dostupných analýz zahrnují objem volání, řetězce horních dotazů a geografické rozložení. |

## <a name="next-steps"></a>Další kroky

* [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) rozhraní API pro vyhledávání bingu
* [Požadavky Bingu na zobrazení a použití](./use-display-requirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
