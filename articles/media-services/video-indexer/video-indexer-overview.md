---
title: Co je Video Indexer?
titlesuffix: Azure Media Services
description: Toto téma nabízí přehled služby Azure Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: ae8634fbfdaa250cbabda6189c6c2eeef8e5e4f1
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292215"
---
# <a name="what-is-video-indexer"></a>Co je Video Indexer?

Azure Video Indexer je cloudová aplikace sestavená s využitím služeb Azure Media Analytics, Azure Search, Cognitive Services (jako jsou rozhraní API pro rozpoznávání tváře, Microsoft Translator, rozhraní API pro počítačové zpracování obrazu a služba Custom Speech). Umožňuje extrahovat přehledy z videí pomocí modelů Video Indexeru popsaných níže:
 
- **Automatické rozpoznávání jazyka**: Automaticky identifikuje převládající mluvený jazyk. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, zjednodušená čínština, japonština, ruština a brazilská portugalština. Pokud jazyk nejde rozpoznat, přejde se zpátky k angličtině.
- **Přepis zvuku**: Převede řeč na text v 10 jazycích a umožňuje rozšíření. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, zjednodušená čínština, japonština, arabština, ruština a brazilská portugalština.
- **Tvorba titulků**: Vytvoří titulky ve třech formátech: VTT, TTML, SRT.
- **Dvoukanálové zpracování**: Pomocí automatické detekce oddělí přepis a sloučí vše do jediné časové osy.
- **Snížení šumu**: Vyčistí telefonní zvuk nebo záznamy s velkým šumem (pomocí filtrů Skypu).
- **Přizpůsobení přepisu (CRIS)**: Trénuje a spouští rozšířené vlastní modely převodu řeči na text k vytváření přepisů specifických pro dané odvětví.
- **Výčet mluvčích**: Mapuje a poznává, který mluvčí řekl která slova a kdy.
- **Statistiky mluvčích**: Poskytuje statistiky o poměru řeči jednotlivých mluvčích.
- **Vizuální rozpoznávání znaků (OCR)**: Extrahuje text, který je vizuálně zobrazený ve videu.
- **Extrakce klíčových snímků**: Zjistí stabilní klíčové snímky ve videu.
- **Analýza citového zabarvení**: Identifikuje kladná, záporná a neutrální zabarvení řeči a vizuálního textu.
- **Zmírnění vizuálního obsahu**: Detekuje vizuální obsah jen pro dospělé nebo nevhodný obsah.
- **Extrakce klíčových slov**: Extrahuje klíčová slova z řeči a vizuálního textu.
- **Identifikace popisků**: Identifikuje vizuální objekty a zobrazené akce.
- **Extrakce značek**: Extrahuje značky z řeči a vizuálního textu.
- **Detekce tváří**: Detekuje a seskupí tváře, které jsou ve videu zobrazené.
- **Extrakce miniatur tváří („nejlepší tvář“)**: Automaticky identifikuje nejvhodnější zachycenou tvář v každé skupině tváří (podle kvality, velikosti a čelní polohy) a extrahuje ji jako obrázek.
- **Identifikace celebrit:** Video Indexer automaticky identifikuje přes 1 milion celebrit, třeba světové vůdce, herce a herečky, sportovce, vědce nebo vůdčí osobnosti ze světa obchodu a technologií z celého světa. Data o těchto celebritách najdete taky na různých známých webech, třeba IMDB nebo v encyklopedii Wikipedia.
- **Identifikace tváře na základě účtu:** Video Indexer trénuje model pro konkrétní účet. Potom na základě modelu vytrénovaného speciálně pro videa v příslušném účtu dokáže rozpoznat tváře ve videu.
- **Zmírnění textového obsahu**: Detekuje explicitní text v přepisu zvuku.
- **Detekce záběrů**: Zjistí, kdy se ve videu změnila scéna.
- **Detekce černých snímků**: Identifikuje černé snímky ve videu.
- **Zvukové efekty**: Identifikuje zvukové efekty, jako je tleskání, řeč a ticho.
- **Dedukce témat**: Dedukuje hlavní témata z přepisů. Zahrnuje taxonomii [IPTC](https://iptc.org/standards/media-topics/) 1. úrovně.
- **Rozpoznávání emocí**: Identifikuje emoce podle náznaků v řeči a zvuku. Rozpozná tyto emoce: radost, smutek, hněv nebo strach.
- **Artefakty**: Extrahuje bohatou sadu artefaktů s „další úrovní podrobností“ pro každý z modelů.
- **Překlad**: Vytvoří překlady přepisu zvuku do 54 různých jazyků.

Jakmile Video Indexer dokončí zpracování a analýzu, můžete přehledné informace o videích revidovat, kurátorovat, prohledávat a publikovat.

Služba Video Indexer dokáže splnit vaše požadavky, ať už jste vývojář, nebo správce obsahu. Správci obsahu můžou pomocí webového portálu Video Indexer tuto službu využívat, aniž by museli napsat jediný řádek kódu. Další informace najdete v článku [Začínáme s webem Video Indexer](video-indexer-get-started.md). Vývojáři mohou využít rozhraní API ke zpracování obsahu ve velkém měřítku, viz téma věnované [použití rozhraní REST API služby Video Indexer](video-indexer-use-apis.md). Tato služba také zákazníkům umožňuje používat widgety k publikování datových proudů videa a extrahovaných přehledů ve vlastních aplikacích. Další informace najdete v tématu [Vložení vizuálních widgetů do vaší aplikace](video-indexer-embed-widgets.md).

K registraci této služby můžete využít existující účet služeb AAD, LinkedIn, Facebook, Google nebo MSA. Další informace najdete v tématu [Začínáme](video-indexer-get-started.md).

## <a name="scenarios"></a>Scénáře

Dál najdete několik scénářů, ve kterých se Video Indexer může hodit

- Hledání – Přehledy extrahované z videa se dají využít k vylepšení vyhledávání v knihovně videí. Například indexování mluveného slova a tváří umožňuje ve videu vyhledávat okamžiky, kdy konkrétní osoba říká určitá slova nebo kdy jsou dva lidé vidět pohromadě. Hledání založené na takovýchto přehledech z videí je užitečné pro zpravodajské agentury, vzdělávací instituce, provozovatele vysílání, vlastníky zábavního obsahu, podnikové obchodní aplikace a obecně pro libovolné obory využívající knihovnu videí, ve kterých musí uživatelé vyhledávat.

- Finanční zhodnocení – Video Indexer umožňuje zvýšit hodnotu videí. Například obory, které závisejí na výnosech z reklam (například sdělovací prostředky, sociální média atd.), mohou využít extrahované přehledy jako další signály pro reklamní server a poskytovat díky nim relevantnější reklamy (reklama na sportovní obuv je relevantnější během fotbalového utkání než při plaveckých soutěžích).

- Zapojení uživatelů – Video Insights se dá využít k lepšímu zapojení uživatelů díky možnosti předkládání relevantních videookamžiků. Jako příklad si představte vzdělávací video, které prvních 30 minut vysvětluje kulové plochy a dalších 30 minut pyramidy. Studentovi, který si čte o pyramidách, by víc pomohlo, kdyby se video nastavilo tak, aby se spustilo od značky 30 minut.

Další informace najdete na tomto [blogu](https://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Další postup

Jste připraveni začít pracovat s Video Indexerem. Další informace najdete v následujících článcích:

- [Začínáme s webem Video Indexer](video-indexer-get-started.md)
- [Zpracování obsahu pomocí rozhraní REST API služby Video Indexer](video-indexer-use-apis.md)
- [Vložení vizuálních widgetů do vaší aplikace](video-indexer-embed-widgets.md)
