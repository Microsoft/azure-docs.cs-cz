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
ms.date: 09/23/2019
ms.author: juliako
ms.openlocfilehash: 2afc3a859ddb5378b6313c43d693842fdb5fce14
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296432"
---
# <a name="what-is-video-indexer"></a>Co je Video Indexer?

Video Indexer (VI) je řešení Azure Media Services AI a část značky Microsoft Cognitive Services. Video Indexer poskytuje možnost extrahovat podrobné přehledy (bez potřeby analýzy dat nebo dovedností v kódování) pomocí modelů strojového učení, které jsou založené na několika kanálech (Voice, vocals, Visual). Modely můžete dále přizpůsobit a naučit. Služba umožňuje hloubkové hledání, snižuje provozní náklady, umožňuje nové příležitosti finanční zhodnocení a nové uživatelské prostředí ve velkých archivech videí (s nízkými překážkami vstupu). 

Pokud chcete začít s extrakcí přehledů pomocí Video Indexer, musíte si vytvořit účet a nahrávat videa. Když nahrajete videa do Video Indexer, analyzují vizuály i zvuk spuštěním různých modelů AI. Jak Video Indexer analyzuje vaše video, přehledy, které jsou extrahovány modely.

Následující diagram představuje ilustraci a nejedná se o technické vysvětlení toho, jak Video Indexer funguje v back-endu.

![Diagram toku](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>Co se dá dělat s Video Indexer?

Přehledy Video Indexer je možné použít u mnoha scénářů, mimo jiné:

* *Hloubkové vyhledávání* – pomocí Insights extrahovaných z videa můžete vylepšit možnosti vyhledávání napříč knihovnou videí. Například indexování mluveného slova a obličeje může umožňovat hledání ve videu, kde osoba čelí určitým slovům, nebo když se dvě lidi viděli společně. Hledání založené na takovýchto přehledech z videí je užitečné pro zpravodajské agentury, vzdělávací instituce, provozovatele vysílání, vlastníky zábavního obsahu, podnikové obchodní aplikace a obecně pro libovolné obory využívající knihovnu videí, ve kterých musí uživatelé vyhledávat.
* *Vytváření obsahu*: vytváření přípojných vozidel, světelná cívky, obsah sociálních médií nebo novinové klipy na základě přehledů video indexer z obsahu. Klíčové snímky, značky scény a časová razítka pro osoby a vzhledy štítků usnadňují proces vytváření mnohem hladší a jednodušší a umožňují vám získat právo na zajímavé části videa, které potřebujete pro obsah, který vytváříte.
* *Usnadnění*: zda chcete zpřístupnit obsah pro osoby s postižením nebo pokud chcete, aby byl obsah distribuován do různých oblastí pomocí různých jazyků, můžete použít přepis a překlad poskytovaný videem. Indexer v několika jazycích.
* *Finanční zhodnocení*: video indexer může přispět k navýšení hodnoty videí. Například obory, které spoléhají na výnosy AD (například sdělovací média, sociální média atd.), můžou poskytovat relevantní reklamy pomocí extrahovaných přehledů jako další signály na server služby AD.
* *Moderování obsahu*: Použití textových a vizuálních modelů pro moderování obsahu k zajištění bezpečnosti uživatelů před nevhodným obsahem a ověření, že obsah, který publikujete, odpovídá hodnotám vaší organizace. Můžete automaticky blokovat určitá videa nebo upozornit uživatele týkající se obsahu. 
* *Doporučení*: ke zlepšení zapojení uživatelů je možné využít video přehledy, které uživatelům zvýrazní relevantní video. Označením každého videa s dalšími metadaty můžete doporučit uživatelům nejdůležitější videa a zvýraznit část videa, která bude odpovídat jejich potřebám. 

## <a name="features"></a>Funkce

Následuje seznam přehledů, které můžete z videí načíst pomocí Video Indexer video a zvukové modely:

### <a name="video-insights"></a>Video přehledy

* **Detekce tváří**: Detekuje a seskupí tváře, které jsou ve videu zobrazené.
* **Celebrit Identification**: video indexer automaticky identifikuje více než 1 000 000 celebrit, jako jsou například vedoucí World, Actors, Actresses, sportovců, výzkumníki, obchodní a technické vedoucí po celém světě. Data o těchto celebritách najdete taky na různých známých webech, třeba IMDB nebo v encyklopedii Wikipedia.
* **Identifikace tváře na základě účtu:** Video Indexer trénuje model pro konkrétní účet. Pak rozpoznává obličeje ve videu na základě trained model. Další informace najdete v tématu [Přizpůsobení modelu osoby z video indexer webu](customize-person-model-with-website.md) a [Přizpůsobení modelu osoby pomocí video indexer API](customize-person-model-with-api.md).
* **Extrakce miniatur pro obličeje** ("nejlepší plocha"): automaticky identifikuje nejlépe zachycenou plošku v každé skupině ploch (na základě kvality, velikosti a čelní pozice) a extrahuje je jako prostředek obrázku.
* **Rozpoznávání vizuálního textu** (OCR): extrahuje text, který je vizuálně zobrazen ve videu.
* **Zmírnění vizuálního obsahu**: Detekuje vizuální obsah jen pro dospělé nebo nevhodný obsah.
* **Identifikace popisků**: Identifikuje vizuální objekty a zobrazené akce.
* **Segmentace scény**: Určuje, kdy se scény mění ve videu na základě vizuálních pomůcek. Scéna znázorňuje jednu událost a skládá se z řady po sobě jdoucích snímků, které jsou sémanticky spojeny.
* **Detekce snímku**: Určuje, kdy se ve videu mění snímek na základě vizuálních pomůcek. Snímek je řada snímků pořízených ze stejné kamery pro pohyb snímků. Další informace najdete v tématu scény, snímky a klíčové snímky.
* **Detekce černých snímků**: Identifikuje černé snímky ve videu.
* **Extrakce klíčových snímků**: Zjistí stabilní klíčové snímky ve videu.
* **Kumulovaný kredity**: Identifikujte začátek a konec kumulovaných kreditů na konci televizních pořadů a filmů.
* **Detekce animovaných znaků** (Preview): detekce, seskupení a rozpoznávání znaků v animovaném obsahu prostřednictvím integrace s [Cognitive Services vlastní vize](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Další informace naleznete v tématu [animovaná detekce znaků](animated-characters-recognition.md).
* **Detekce typu redakčního snímku**: označení snímků na základě jejich typu (jako je třeba velký záběr, střední záběr, zavření, extrémní uzavření, dva snímky, více lidí, venku a interiér atd.). Další informace najdete v tématu [zjištění typu redakčního snímku](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Přehledy zvuku

* **Automatické rozpoznávání jazyka**: Automaticky identifikuje převládající mluvený jazyk. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, ruština a brazilská portugalština. Pokud jazyk nelze identifikovat s jistotou, Video Indexer předpokládá, že mluvený jazyk je angličtina. Další informace najdete v tématu [model identifikace jazyka](language-identification-model.md).
* **Identifikace a přepis řeči ve více jazycích** (Preview): automaticky identifikuje mluvený jazyk v různých segmentech ze zvukového souboru, který odesílá jednotlivé segmenty mediálního souboru, které se přepisu, a sloučí přepis zpátky do jednoho Unified přepis. Další informace najdete v tématu [Automatické určení a přepisovat vícejazyčného obsahu](multi-language-identification-transcription.md).
* **Přepis zvuku**: převede řeč na text v 12 jazycích a povoluje rozšíření. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, arabština, ruština, brazilská portugalština, hindština a korejština.
* **Tvorba titulků**: Vytvoří titulky ve třech formátech: VTT, TTML, SRT.
* **Dvoukanálové zpracování**: Pomocí automatické detekce oddělí přepis a sloučí vše do jediné časové osy.
* **Redukce hluku**: vymaže zvukové a přenosové nahrávky v telefonním formátu (na základě filtrů Skypu).
* **Přizpůsobení přepisu** (položku CRI): vlacích vlastní řeč na textové modely pro vytváření přepisů specifických pro jednotlivé obory. Další informace najdete v tématu [přizpůsobení jazykového modelu z video indexer webu](customize-language-model-with-website.md) a [přizpůsobení jazykového modelu pomocí rozhraní API video indexer](customize-language-model-with-api.md).
* **Výčet mluvčího**: mapuje a rozumí, ke kterému paprsku mluvčího se slova a kdy.
* **Statistika mluvčího**: poskytuje statistiku pro poměr řeči mluvčích.
* **Zmírnění textového obsahu**: Detekuje explicitní text v přepisu zvuku.
* **Zvukové efekty**: Identifikuje zvukové efekty, jako je tleskání, řeč a ticho.
* **Detekce emoce**: identifikuje emoce na základě řeči (co se říká) a hlasového tónoví (jak se říkáte). Emoce můžou mít radost, smutek, hněv nebo obavy.
* **Překlad**: Vytvoří překlady přepisu zvuku do 54 různých jazyků.

### <a name="audio-and-video-insights-multi-channels"></a>Zvukové a video přehledy (více kanálů)

K dispozici bude při indexování v jednom kanálu částečný výsledek pro tyto modely.

* **Extrakce klíčových slov**: extrahuje klíčová slova z mluvené řeči a vizuálního textu.
* **Extrakce pojmenovaných entit**: extrahuje značky, umístění a lidi z mluvené řeči a vizuálního textu prostřednictvím zpracování přirozeného jazyka (NLP).
* **Dedukce témat**: Dedukuje hlavní témata z přepisů. Je zahrnutá taxonomie IPTC na druhé úrovni.
* **Artefakty**: Extrahuje bohatou sadu artefaktů s „další úrovní podrobností“ pro každý z modelů.
* **Analýza citového zabarvení**: Identifikuje kladná, záporná a neutrální zabarvení řeči a vizuálního textu.

## <a name="how-can-i-get-started-with-video-indexer"></a>Jak můžu začít s Video Indexer?

K funkcím Video Indexer můžete přistupovat pomocí tří různých způsobů: 

* Portál Video Indexer – snadno použitelné řešení, které umožňuje vyhodnotit produkt, spravovat účet a přizpůsobovat modely. 

    Další informace o portálu najdete v tématu Začínáme [s webem video indexer](video-indexer-get-started.md).  
* Integrace rozhraní API – všechny možnosti Video Indexer jsou k dispozici prostřednictvím REST API, které umožňují integrovat řešení do aplikací a infrastruktury. 

    Pokud chcete začít pracovat jako vývojář, přečtěte si téma [použití Video Indexer REST API](video-indexer-use-apis.md). 
* Widget Emendable – umožňuje do své aplikace vkládat prostředí pro video indexery, přehrávače a editory videa. 

    Další informace najdete v tématu [vkládání vizuálních pomůcek do aplikace](video-indexer-embed-widgets.md). 

Pokud web používáte, přehledy se přidávají jako metadata a zobrazují se na portálu. Pokud používáte rozhraní API, přehledy jsou dostupné jako soubor JSON. 

## <a name="next-steps"></a>Další kroky

Jste připraveni začít pracovat s Video Indexerem. Další informace najdete v následujících článcích:

- [Začínáme s webem Video Indexer](video-indexer-get-started.md)
- [Zpracování obsahu pomocí rozhraní REST API služby Video Indexer](video-indexer-use-apis.md)
- [Vložení vizuálních widgetů do vaší aplikace](video-indexer-embed-widgets.md)
