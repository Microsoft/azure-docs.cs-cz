---
title: Co jsou rozhraní API pro vyhledávání Bingu?
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto článku najdete informace o rozhraní API pro vyhledávání Bingu a jak můžete zajistit ve vašich aplikacích a službách cognitive hledání na Internetu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 60de69c71df61cb53e446aa8759842f72c2acf5d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864175"
---
# <a name="what-are-the-bing-search-apis"></a>Co jsou rozhraní API pro vyhledávání Bingu?

Rozhraní API pro vyhledávání Bingu umožňují vytvářet připojené webové aplikace a služby, které webových stránek, obrázků, zpráv, umístění a další bez oznámení o inzerovaných programech naleznete. Pomocí zasílání požadavků search pomocí REST API pro vyhledávání Bingu nebo sad SDK, můžete získat důležité informace a obsah pro hledání na webu. Pomocí tohoto článku najdete informace o různých vyhledávání Bingu rozhraní API a jak integrovat kognitivní vyhledávání do vašich aplikací a služeb. Omezení frekvence a ceny se mohou lišit mezi rozhraním API.

## <a name="the-bing-web-search-api"></a>Rozhraní API webové vyhledávání Bingu

[API vyhledávání na webu Bingu](../Bing-Web-Search/index.yml) vrátí webové stránky, obrázky, videa, zprávy a další. Vyhledávací dotazy odeslané k tomuto rozhraní API se dá filtrovat zahrnout nebo vyloučit určité typy obsahu.

Zvažte použití rozhraní API Bingu pro vyhledávání webu v aplikacích, které může potřebovat vyhledat všechny typy relevantní webového obsahu. Pokud vaše aplikace vyhledá konkrétní typ online obsahu, zvažte možnost hledání rozhraní API níže: 

## <a name="content-specific-bing-search-apis"></a>Vyhledávání Bingu obsahu konkrétní rozhraní API

Následující vyhledávání Bingu rozhraní API vrátí konkrétní obsah z webu, jako jsou obrázky, zprávy, místních firmách a videa.

| Rozhraní API Bingu | Popis |
| -- | -- | 
| [Vyhledávání entit](../Bing-Entities-Search/index.yml) | Rozhraní API Bingu pro vyhledávání entit vrátí výsledky hledání obsahují entity, které mohou být lidé, místa nebo věcí. V závislosti na dotazu rozhraní API vrátí jeden nebo více entit, které odpovídají vyhledávacímu dotazu může obsahovat zajímavosti jednotlivce, místních firmách, památek, cíle a další. |
| [Vyhledávání obrázků](../Bing-Image-Search/index.yml) | Rozhraní API Bingu pro vyhledávání obrázků umožňuje hledat a najít vysoce kvalitní statické i animované imagí podobný [Bing.com/images](https://www.Bing.com/images). Můžete upřesnit hledání pro zahrnutí nebo vyloučení imagí atributem, včetně velikost, barvu, licence a aktuálnosti. Můžete také vyhledat populárních obrázků, nahrávání obrázků a získávat poznatky o nich a zobrazit náhledy. |
| [Vyhledávání zpráv](../Bing-News-Search/index.yml) | Rozhraní API pro vyhledávání zpráv Bingu umožňuje najít podobný příběhy [Bing.com/news](https://www.Bing.com/news). Rozhraní API vrátí články o novinkách z více zdrojů nebo konkrétní domény. Můžete vyhledat v kategoriích získat vzroste zájem o články, hlavní zprávy a titulky.
| [Vyhledávání videí](../Bing-Video-Search/index.yml) | API pro vyhledávání videí Bingu umožňuje vyhledat videa na webu. Získejte miniaturami, populárních videí a související obsah. |
| [Pro vizuální vyhledávání](../Bing-visual-search/index.yml) | Nahrajte obrázek nebo použijte adresu URL získat přehledné informace o tom, jako jsou vizuálně podobné produkty, obrázky a související hledání. |
 [Místní pracovní vyhledávání](../bing-local-business-search/index.yml) | Rozhraní API Bingu pro místní obchodní vyhledávání umožňuje vašim aplikacím vyhledávat kontakty a umístění pro místní podniky, a to na základě vyhledávacích dotazů. |

## <a name="the-bing-custom-search-api"></a>Rozhraní API pro vlastní vyhledávání Bingu

Vytvoření instance vlastního vyhledávání s [vlastní vyhledávání Bingu](../Bing-Custom-Search/index.yml) rozhraní API vám umožní vytvořit vyhledávací funkce, zaměřuje pouze na obsah a témata, které vás zajímají. Například po zadání domén, webů a konkrétní webové stránky, které budou pro vyhledávání Bingu, výsledky se dá přizpůsobit na míru tento konkrétní obsah. Můžete začlenit vlastní pro automatické návrhy Bingu, Image, a vyzkoušet rozhraní API pro Video Search můžete dále přizpůsobit hledání.  

## <a name="additional-bing-search-apis"></a>Rozhraní API pro vyhledávání Bingu další

Následující API pro vyhledávání Bingu umožní vylepšit vaše zkušenosti vyhledávání je kombinací jiných rozhraní API pro vyhledávání Bingu.

| Rozhraní API | Popis |
| -- | -- | 
| [Automatické návrhy Bingu](../Bing-Autosuggest/index.yml) | Zlepšete možnosti vyhledávání vaší aplikace pomocí rozhraní API pro automatické návrhy Bingu vrácením navrhované hledání v reálném čase.  |
| [Statistika Bingu](bing-web-stats.md) | Statistika Bingu poskytuje analýzy pro rozhraní API pro vyhledávání Bingu vaše aplikace používá. K dispozici analytics patří objemy volání, řetězce dotazu top a geografické distribuce. |

## <a name="next-steps"></a>Další postup

* Rozhraní API Bingu pro vyhledávání [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [Požadavky Bingu na zobrazení a použití](./use-display-requirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
