---
title: Co je Video Indexer služby Azure Media Services?
titleSuffix: Azure Media Services
description: Toto téma poskytuje přehled služby Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/06/2019
ms.author: juliako
ms.openlocfilehash: e3f60b5fb0693e40c9db040f7b14f487fce8f68e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860259"
---
# <a name="what-is-video-indexer"></a>Co je Video Indexer?

Video Indexer služby Azure Media Services je cloudová aplikace sestavená s využitím služeb Azure Media Analytics, Azure Search a Cognitive Services (jako jsou rozhraní API pro rozpoznávání tváře, Microsoft Translator, rozhraní API pro počítačové zpracování obrazu a služba Custom Speech). Umožňuje extrahovat přehledy z videí pomocí Video Indexer video a zvukové modely popsané níže:
  
## <a name="video-insights"></a>Video přehledy

- **Rozpoznávání tváře**: Detekuje a seskupuje plošky, které se zobrazují ve videu.
- **Identifikace celebrit**: Video Indexer automaticky identifikuje více než 1 000 000 celebrit – jako jsou například špičkové vedoucí, aktéry, Actresses, sportovců, výzkumníki, obchodní a technické vedoucí po celém světě. Data o těchto celebritách najdete taky na různých známých webech, třeba IMDB nebo v encyklopedii Wikipedia.
- **Identifikace obličeje na základě účtu**: Video Indexer navlacích model pro určitý účet. Pak rozpoznává obličeje ve videu na základě trained model. Další informace najdete v tématu [Přizpůsobení modelu osoby z video indexer webu](customize-person-model-with-website.md) a [Přizpůsobení modelu osoby pomocí video indexer API](customize-person-model-with-api.md).
- **Extrakce miniatur pro obličeje** ("nejlepší plocha"): Automaticky identifikuje nejlépe zachycenou plošku v každé skupině ploch (na základě kvality, velikosti a čelní pozice) a extrahuje je jako prostředek obrázku.
- **Rozpoznávání vizuálního textu** (OCR): Extrahuje text, který je vizuálně zobrazen ve videu.
- **Moderování vizuálního obsahu**: Detekuje vizuály pro dospělé nebo pikantní.
- **Identifikace popisků**: Identifikuje zobrazené vizuální objekty a akce.
- **Segmentace scény**: Určuje, kdy se scény mění ve videu na základě vizuálních pomůcek. Scéna znázorňuje jednu událost a skládá se z řady po sobě jdoucích snímků, které jsou sémanticky spojeny. 
- **Detekce snímku**: Určuje, kdy se ve videu mění snímek na základě vizuálních pomůcek. Snímek je řada snímků pořízených ze stejné kamery pro pohyb snímků. Další informace najdete v tématu [scény, snímky a klíčové snímky](scenes-shots-keyframes.md).
- **Detekce černého snímku**: Identifikuje černé snímky prezentované ve videu.
- **Extrakce klíčového snímku**: Detekuje stabilní klíčové snímky ve videu.
- **Kumulovaný kredity**: Identifikujte začátek a konec kumulovaných kreditů na konci televizních pořadů a filmů.
- **Zjištění animovaných znaků** (Preview): detekce, seskupení a rozpoznávání znaků v animovaném obsahu prostřednictvím integrace s [Cognitive Services vlastní vize](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Další informace naleznete v tématu [animovaná detekce znaků](animated-characters-recognition.md).
- **Detekce typu redakčního snímku**: označení snímků na základě jejich typu (jako je třeba velký záběr, střední záběr, zavření, extrémní uzavření, dva snímky, více lidí, venku a interiér atd.). Další informace najdete v tématu [zjištění typu redakčního snímku](scenes-shots-keyframes.md#editorial-shot-type-detection).

## <a name="audio-insights"></a>Přehledy zvuku

- **Automatické rozpoznávání jazyka**: Automaticky identifikuje dominantní mluvený jazyk. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, ruština a brazilská portugalština. Pokud jazyk nelze identifikovat s jistotou, Video Indexer předpokládá, že mluvený jazyk je angličtina. Další informace najdete v tématu [model identifikace jazyka](language-identification-model.md).
- **Identifikace a přepis mluveného slova ve více jazycích** (Preview): Automaticky identifikuje mluvený jazyk v různých segmentech ze zvukového souboru, který odesílá jednotlivé segmenty mediálního souboru, aby se přepisu, a sloučí přepis zpátky do jednoho sjednoceného přepisu. Další informace najdete v tématu [Automatické určení a přepisovat vícejazyčného obsahu](multi-language-identification-transcription.md).
- **Přepis zvuku**: Převede řeč na text v 12 jazycích a povoluje rozšíření. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, arabština, ruština, brazilská portugalština, hindština a korejština.
- **Skryté titulky**: Vytvoří skryté titulky ve třech formátech: VTT, TTML, SRT.
- **Zpracování dvou kanálů**: Automaticky detekuje, oddělují a sloučí je s jednou časovou osou.
- **Snížení šumu**: Vymaže zvukové a přenosové nahrávky telefonního subsystému (na základě filtrů Skype).
- **Přizpůsobení přepisu** (Položku CRI): Vlaky s vlastním převodem na textové modely pro vytváření přepisů specifických pro konkrétní obor. Další informace najdete v tématu [přizpůsobení jazykového modelu z video indexer webu](customize-language-model-with-website.md) a [přizpůsobení jazykového modelu pomocí rozhraní API video indexer](customize-language-model-with-api.md).
- **Výčet mluvčího**: Mapuje a rozumí, ke kterému paprskovým paprskům jsou slova a kdy.
- **Statistika mluvčího**: Poskytuje statistiku pro sluchátka s mluvenými poměry.
- **Moderování obsahu textu**: Detekuje explicitní text v přepisu zvuku.
- **Zvukové efekty**: Identifikuje zvukové efekty, jako je například ruční claps, řeč a netiché zpracování.
- **Detekce emoce**: Identifikuje emoce na základě řeči (co se říká) a hlasového tónoví (jak se říkáte).  Rozpozná tyto emoce: radost, smutek, hněv nebo strach.
- **Překlad**: Vytvoří překlady zvukového přepisu do různých jazyků 54.

## <a name="audio-and-video-insights-multi-channels"></a>Zvukové a video přehledy (více kanálů)

K dispozici bude při indexování v jednom kanálu částečný výsledek pro tyto modely.

- **Extrakce klíčových slov**: Extrahuje klíčová slova z mluvené řeči a vizuálního textu.
- **Extrakce pojmenovaných entit**: Extrahuje značky, umístění a lidi z mluvené řeči a vizuálního textu prostřednictvím zpracování přirozeného jazyka (NLP).
- **Odvození tématu**: Provádí odvození hlavních témat z přepisů. Zahrnuje se taxonomie IPTC na první úrovni.
- **Artefakty**: Extrahuje bohatou sadu artefaktů další úrovně podrobností pro každý model.
- **Analýza mínění**: Identifikuje kladné, záporné a neutrální zabarvení z mluvené řeči a vizuálního textu.
 
Jakmile Video Indexer dokončí zpracování a analýzu, můžete přehledné informace o videích revidovat, kurátorovat, prohledávat a publikovat.

Služba Video Indexer dokáže splnit vaše požadavky, ať už jste vývojář, nebo správce obsahu. Správci obsahu můžou pomocí webového portálu Video Indexer tuto službu využívat, aniž by museli napsat jediný řádek kódu. Další informace najdete v článku [Začínáme s webem Video Indexer](video-indexer-get-started.md). Vývojáři mohou využít rozhraní API ke zpracování obsahu ve velkém měřítku, viz téma věnované [použití rozhraní REST API služby Video Indexer](video-indexer-use-apis.md). Tato služba také zákazníkům umožňuje používat widgety k publikování datových proudů videa a extrahovaných přehledů ve vlastních aplikacích. Další informace najdete v tématu [Vložení vizuálních widgetů do vaší aplikace](video-indexer-embed-widgets.md).

K registraci této služby můžete využít existující účet služeb AAD, LinkedIn, Facebook, Google nebo MSA. Další informace najdete v tématu [Začínáme](video-indexer-get-started.md).

## <a name="scenarios"></a>Scénáře

Dál najdete několik scénářů, ve kterých se Video Indexer může hodit

- Hledání – Přehledy extrahované z videa se dají využít k vylepšení vyhledávání v knihovně videí. Například indexování mluveného slova a tváří umožňuje ve videu vyhledávat okamžiky, kdy konkrétní osoba říká určitá slova nebo kdy jsou dva lidé vidět pohromadě. Hledání založené na takovýchto přehledech z videí je užitečné pro zpravodajské agentury, vzdělávací instituce, provozovatele vysílání, vlastníky zábavního obsahu, podnikové obchodní aplikace a obecně pro libovolné obory využívající knihovnu videí, ve kterých musí uživatelé vyhledávat.
- Vytváření obsahu – přehledy extrahované z videí a efektivní vytváření obsahu, jako jsou přívěsy, obsah sociálních médií, novinové klipy atd. z existujícího obsahu v archivu organizace 
- Finanční zhodnocení – Video Indexer umožňuje zvýšit hodnotu videí. Například obory, které závisejí na výnosech z reklam (například sdělovací prostředky, sociální média atd.), mohou využít extrahované přehledy jako další signály pro reklamní server a poskytovat díky nim relevantnější reklamy (reklama na sportovní obuv je relevantnější během fotbalového utkání než při plaveckých soutěžích).
- Zapojení uživatelů – Video Insights se dá využít k lepšímu zapojení uživatelů díky možnosti předkládání relevantních videookamžiků. Jako příklad si představte vzdělávací video, které prvních 30 minut vysvětluje kulové plochy a dalších 30 minut pyramidy. Studentovi, který si čte o pyramidách, by víc pomohlo, kdyby se video nastavilo tak, aby se spustilo od značky 30 minut.

## <a name="next-steps"></a>Další postup

Jste připraveni začít pracovat s Video Indexerem. Další informace najdete v následujících článcích:

- [Začínáme s webem Video Indexer](video-indexer-get-started.md)
- [Zpracování obsahu pomocí rozhraní REST API služby Video Indexer](video-indexer-use-apis.md)
- [Vložení vizuálních widgetů do vaší aplikace](video-indexer-embed-widgets.md)
