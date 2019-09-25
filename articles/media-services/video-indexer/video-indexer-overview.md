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
ms.openlocfilehash: 3c8d0d3ec0f11592610363e9150be47112995853
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219860"
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
* *Finanční zhodnocení*: Video Indexer může přispět k navýšení hodnoty videí. Například obory, které spoléhají na výnosy AD (například sdělovací média, sociální média atd.), můžou poskytovat relevantní reklamy pomocí extrahovaných přehledů jako další signály na server služby AD.
* *Moderování obsahu*: Použití textových a vizuálních modelů pro moderování obsahu k zajištění bezpečnosti uživatelů před nevhodným obsahem a ověření, že obsah, který publikujete, odpovídá hodnotám vaší organizace. Můžete automaticky blokovat určitá videa nebo upozornit uživatele týkající se obsahu. 
* *Doporučení*: Služba video Insights se dá využít ke zlepšení zapojení uživatelů tím, že zvýrazňuje relevantní video pro uživatele. Označením každého videa s dalšími metadaty můžete doporučit uživatelům nejdůležitější videa a zvýraznit část videa, která bude odpovídat jejich potřebám. 

## <a name="features"></a>Funkce

Následuje seznam přehledů, které můžete z videí načíst pomocí Video Indexer video a zvukové modely:

### <a name="video-insights"></a>Video přehledy

* **Rozpoznávání tváře**: Detekuje a seskupuje plošky, které se zobrazují ve videu.
* **Identifikace celebrit**: Video Indexer automaticky identifikuje více než 1 000 000 celebrit – jako jsou například špičkové vedoucí, aktéry, Actresses, sportovců, výzkumníki, obchodní a technické vedoucí po celém světě. Data o těchto celebritách najdete taky na různých známých webech, třeba IMDB nebo v encyklopedii Wikipedia.
* **Identifikace obličeje na základě účtu**: Video Indexer navlacích model pro určitý účet. Pak rozpoznává obličeje ve videu na základě trained model. Další informace najdete v tématu [Přizpůsobení modelu osoby z video indexer webu](customize-person-model-with-website.md) a [Přizpůsobení modelu osoby pomocí video indexer API](customize-person-model-with-api.md).
* **Extrakce miniatur pro obličeje** ("nejlepší plocha"): Automaticky identifikuje nejlépe zachycenou plošku v každé skupině ploch (na základě kvality, velikosti a čelní pozice) a extrahuje je jako prostředek obrázku.
* **Rozpoznávání vizuálního textu** (OCR): Extrahuje text, který je vizuálně zobrazen ve videu.
* **Moderování vizuálního obsahu**: Detekuje vizuály pro dospělé nebo pikantní.
* **Identifikace popisků**: Identifikuje zobrazené vizuální objekty a akce.
* **Segmentace scény**: Určuje, kdy se scény mění ve videu na základě vizuálních pomůcek. Scéna znázorňuje jednu událost a skládá se z řady po sobě jdoucích snímků, které jsou sémanticky spojeny.
* **Detekce snímku**: Určuje, kdy se ve videu mění snímek na základě vizuálních pomůcek. Snímek je řada snímků pořízených ze stejné kamery pro pohyb snímků. Další informace najdete v tématu scény, snímky a klíčové snímky.
* **Detekce černého snímku**: Identifikuje černé snímky prezentované ve videu.
* **Extrakce klíčového snímku**: Detekuje stabilní klíčové snímky ve videu.
* **Kumulovaný kredity**: Identifikujte začátek a konec kumulovaných kreditů na konci televizních pořadů a filmů.
* **Zjištění animovaných znaků** (Preview): detekce, seskupení a rozpoznávání znaků v animovaném obsahu prostřednictvím integrace s [Cognitive Services vlastní vize](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Další informace naleznete v tématu [animovaná detekce znaků](animated-characters-recognition.md).
* **Detekce typu redakčního snímku**: označení snímků na základě jejich typu (jako je třeba velký záběr, střední záběr, zavření, extrémní uzavření, dva snímky, více lidí, venku a interiér atd.). Další informace najdete v tématu [zjištění typu redakčního snímku](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Přehledy zvuku

* **Automatické rozpoznávání jazyka**: Automaticky identifikuje dominantní mluvený jazyk. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, ruština a brazilská portugalština. Pokud jazyk nelze identifikovat s jistotou, Video Indexer předpokládá, že mluvený jazyk je angličtina. Další informace najdete v tématu [model identifikace jazyka](language-identification-model.md).
* **Identifikace a přepis mluveného slova ve více jazycích** (Preview): Automaticky identifikuje mluvený jazyk v různých segmentech ze zvukového souboru, který odesílá jednotlivé segmenty mediálního souboru, aby se přepisu, a sloučí přepis zpátky do jednoho sjednoceného přepisu. Další informace najdete v tématu [Automatické určení a přepisovat vícejazyčného obsahu](multi-language-identification-transcription.md).
* **Přepis zvuku**: Převede řeč na text v 12 jazycích a povoluje rozšíření. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, arabština, ruština, brazilská portugalština, hindština a korejština.
* **Skryté titulky**: Vytvoří skryté titulky ve třech formátech: VTT, TTML, SRT.
* **Zpracování dvou kanálů**: Automaticky detekuje, oddělují a sloučí je s jednou časovou osou.
* **Snížení šumu**: Vymaže zvukové a přenosové nahrávky telefonního subsystému (na základě filtrů Skype).
* **Přizpůsobení přepisu** (Položku CRI): Vlaky s vlastním převodem na textové modely pro vytváření přepisů specifických pro konkrétní obor. Další informace najdete v tématu [přizpůsobení jazykového modelu z video indexer webu](customize-language-model-with-website.md) a [přizpůsobení jazykového modelu pomocí rozhraní API video indexer](customize-language-model-with-api.md).
* **Výčet mluvčího**: Mapuje a rozumí, ke kterému paprskovým paprskům jsou slova a kdy.
* **Statistika mluvčího**: Poskytuje statistiku pro poměry řeči pro reproduktory.
* **Moderování obsahu textu**: Detekuje explicitní text v přepisu zvuku.
* **Zvukové efekty**: Identifikuje zvukové efekty, jako je například ruční claps, řeč a netiché zpracování.
* **Detekce emoce**: Identifikuje emoce na základě řeči (co se říká) a hlasového tónoví (jak se říkáte). Emoce můžou mít radost, smutek, hněv nebo obavy.
* **Překlad**: Vytvoří překlady zvukového přepisu do různých jazyků 54.

### <a name="audio-and-video-insights-multi-channels"></a>Zvukové a video přehledy (více kanálů)

K dispozici bude při indexování v jednom kanálu částečný výsledek pro tyto modely.

* **Extrakce klíčových slov**: Extrahuje klíčová slova z mluvené řeči a vizuálního textu.
* **Extrakce pojmenovaných entit**: Extrahuje značky, umístění a lidi z mluvené řeči a vizuálního textu prostřednictvím zpracování přirozeného jazyka (NLP).
* **Odvození tématu**: Provádí odvození hlavních témat z přepisů. Zahrnuje se taxonomie IPTC na první úrovni.
* **Artefakty**: Extrahuje bohatou sadu artefaktů další úrovně podrobností pro každý model.
* **Analýza mínění**: Identifikuje kladné, záporné a neutrální zabarvení z mluvené řeči a vizuálního textu.

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
