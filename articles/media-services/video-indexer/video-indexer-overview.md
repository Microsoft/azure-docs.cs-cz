---
title: Co je Video Indexer?
titlesuffix: Azure Media Services
description: Toto téma nabízí přehled služby Azure Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: c1c81ba0f168759ab9cc05f3441e07ff03275e84
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417376"
---
# <a name="what-is-video-indexer"></a>Co je Video Indexer?

Azure Video Indexer je cloudová aplikace sestavená s využitím služeb Azure Media Analytics, Azure Search, Cognitive Services (jako jsou rozhraní API pro rozpoznávání tváře, Microsoft Translator, rozhraní API pro počítačové zpracování obrazu a služba Custom Speech). Umožňuje extrahovat přehledy z videí pomocí video a audiostreamů modely Video Indexer je popsáno níže:
  
## <a name="video-insights"></a>Nové poznatky z videí

- **Rozpoznávání tváře**: Zjistí a skupiny tváří povolí, nebude ve videu.
- **Identifikace celebrit**: Video Indexer automaticky identifikuje přes 1 millión celebrit – například vedoucí pracovníci world, actors a actresses, sportovců z celého světa, výzkumní pracovníci, obchodní a technické vedoucí po celém světě. Data o těchto celebritách najdete taky na různých známých webech, třeba IMDB nebo v encyklopedii Wikipedia.
- **Identifikace tváře založené na účtu**: Video Indexer trénovat modelu pro zadaný účet. Potom na základě modelu vytrénovaného speciálně pro videa v příslušném účtu dokáže rozpoznat tváře ve videu.
- **Miniatura extrakce pro plošky** ("co nejlepší pro rozpoznávání tváře"): Automaticky identifikuje nejvhodnější zachycené tváří v každé skupině tváří (podle kvality, velikost a umístění čelní) a rozbalte ho jako prostředek obrázku.
- **Rozpoznávání textu Visual** (OCR): Extrahuje text, který se zobrazí vizuální ve videu.
- **Vizuální moderování obsahu**: Zjistí pro dospělé nebo pikantního vizuály.
- **Popisky identifikace**: Určuje vizuální objekty a zobrazené akce.
- **Snímek detekce**: Určuje, když se změní scény ve videu.
- **Černé rámců**: Identifikuje černého rámce uvedené ve videu.
- **Extrakce klíčových snímků**: Stabilní klíčové snímky rozpozná ve videu.

## <a name="audio-insights"></a>Zvukový insights

- **Automatické rozpoznávání jazyka**: Automaticky identifikuje dominantní mluvený jazyk. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, zjednodušená čínština, japonština, ruština a brazilská portugalština. Pokud jazyk nejde rozpoznat, přejde se zpátky k angličtině.
- **Přepisování zvukového záznamu**: Převede řeči na text v 12 jazycích a umožňuje rozšíření. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, arabština, ruština, portugalština (Brazílie), hindština a korejštině.
- **Skryté titulky**: Vytvoří titulků v tří formátů: VTT, TTML, SRT.
- **Dvě kanálu zpracování**: Automaticky detekuje, oddělte přepisu a sloučí do jediné časové osy.
- **Noise snížení**: Vymaže nahoru nahrávky zvuku nebo hlučného telefonního subsystému (na základě Skype filtrů).
- **Přizpůsobení přepisu** (CRIS): Trénovat a spustí rozšířenou vlastní převod řeči na text modely a vytvořte záznamy o studiu specifických pro dané odvětví.
- **Výčet mluvčího**: Mapuje a rozumí, které mluvčího paprsků která slova a kdy.
- **Statistiky mluvčího**: Poskytuje statistiky pro přednášející řeči poměry.
- **Textové moderování obsahu**: Detekuje explicitní text v přepisu zvuku.
- **Zvukové efekty**: Identifikuje zvukové efekty, jako je ruční přípitky, zpracování řeči a nečinnosti.
- **Rozpoznávání emocí z výrazu**: Identifikuje emoce podle pomůcky pro zpracování řeči a zvuku. Rozpozná tyto emoce: radost, smutek, hněv nebo strach.
- **Překlad**: Vytvoří překlady přepisu zvuku na 54 různých jazycích.

## <a name="audio-and-video-insights-multi-channels"></a>Audio a video insights (s více kanálů)

Indexování pomocí částečných výsledků jeden kanál pro tyto modely kdy bude k dispozici

- **Extrakce klíčových slov**: Extrahuje klíčových slov z řeči a vizuální text.
- **Opatří extrakce**: Extrahuje značky z pro zpracování řeči a vizuální text.
- **Odvození tématu**: Díky odvození hlavní témata z záznamy o studiu. Součástí je taxonomie IPTC 1. úrovně.
- **Artefakty**: Extrahuje bohatou sadu "Další úroveň podrobností" artefakty pro každou modelů.
- **Analýza subjektivního hodnocení**: Identifikuje kladná, záporná a neutrální zabarvení řeči a vizuální text.
 
  
 
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
