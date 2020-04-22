---
title: Co je Video Indexer?
titleSuffix: Azure Media Services
description: Tento článek poskytuje přehled služby Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: juliako
ms.openlocfilehash: 1c7156da171a101f04db9f83df587f5b8bc71e8f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687101"
---
# <a name="what-is-video-indexer"></a>Co je Video Indexer?

Video Indexer (VI) je řešení AI Azure Media Services a součást značky Azure Cognitive Services. Video Indexer poskytuje schopnost extrahovat hluboké poznatky (bez potřeby analýzy dat nebo kódování dovednosti) pomocí modelů strojového učení na základě více kanálů (hlas, zpěv, vizuální). Můžete dále přizpůsobit a trénovat modely. Služba umožňuje hloubkové vyhledávání, snižuje provozní náklady, umožňuje nové možnosti zpeněžení a vytváří nové uživatelské prostředí na velkých archivech videí (s nízkými vstupními bariérami).

Chcete-li začít extrahovat přehledy pomocí video indexeru, musíte si vytvořit účet a nahrát videa. Když videa nahráváte do videoindexeru, analyzuje vizuály i zvuk spuštěním různých modelů AI. Jako Video Indexer analyzuje vaše video, přehledy, které jsou extrahovány modely AI.

Následující diagram je ilustrace a nikoli technické vysvětlení, jak Video Indexer funguje v back-endu.

![Vývojový diagram videoindexeru Služby Azure Media Services](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení

Jako důležité připomenutí musíte při používání videoindexeru dodržovat všechny platné zákony a nesmíte používat Video Indexer ani žádnou službu Azure způsobem, který porušuje práva ostatních nebo který může být škodlivý pro ostatní.

Před nahráním jakéhokoli videa/obrázku do videoindexeru musíte mít všechna příslušná práva k používání videa/obrázku, včetně všech potřebných souhlasů jednotlivců (pokud existuje) ve videu/obrázku, pro použití, zpracování a ukládání jejich dat v videoindexeru a Azure. Některé jurisdikce mohou stanovit zvláštní právní požadavky na shromažďování, on-line zpracování a uchovávání určitých kategorií údajů, jako jsou biometrické údaje. Před použitím Video Indexer a Azure pro zpracování a ukládání všech dat, na které se vztahují zvláštní právní požadavky, musíte zajistit dodržování všech takových právních požadavků, které se na vás mohou vztahovat.

Informace o dodržování předpisů, ochraně osobních údajů a zabezpečení v programu Video Indexer naleznete v [Centru zabezpečení společnosti](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx)Microsoft . Pokud jde o povinnosti společnosti Microsoft týkající se ochrany osobních údajů, postupy zpracování a uchovávání dat, včetně způsobu odstranění vašich dat, přečtěte si prohlášení společnosti Microsoft [o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement), [smluvních podmínkách online služeb](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") a [dodatku ke zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Používáním Video Indexeru souhlasíte s tím, že budete vázáni prohlášením o ochraně osobních údajů, dpa a prohlášením o ochraně osobních údajů.

## <a name="what-can-i-do-with-video-indexer"></a>Co mohu dělat s video indexerem?

Video Indexer postřehy lze použít pro mnoho scénářů, mezi nimi jsou:

* *Hloubkové vyhledávání*: Pomocí přehledů získaných z videa vylepšete možnosti vyhledávání v celé knihovně videí. Například indexování mluvených slov a tváří může umožnit vyhledávání okamžiků hledání okamžiků ve videu, kde osoba mluvila určitá slova nebo když byli dva lidé viděni společně. Vyhledávání založené na těchto informacích z videí se vztahuje na tiskové agentury, vzdělávací instituce, provozovatele vysílání, vlastníky zábavního obsahu, podnikové obchodní aplikace a obecně na jakékoli odvětví, které má videoknihovnu, proti které musí uživatelé vyhledávat.
* *Vytváření obsahu*: Vytvářejte upoutávky, zvýrazňujte navijáky, obsah sociálních médií nebo zpravodajské klipy na základě přehledů, které video indexer získává z vašeho obsahu. Díky klíčovým snímkům, značkám scén a časovým razítkům pro osoby a vzhledy popisků je proces vytváření mnohem hladší a jednodušší a umožňuje vám dostat se k částem videa, které potřebujete pro obsah, který vytváříte.
* *Usnadnění přístupu*: Bez ohledu na to, zda chcete, aby byl obsah dostupný pro osoby s postižením, nebo zda chcete, aby byl obsah distribuován do různých oblastí pomocí různých jazyků, můžete použít přepis a překlad poskytovaný indexerem videa ve více jazycích.
* *Zpeněžení*: Video Indexer může pomoci zvýšit hodnotu videa. Například odvětví, která spoléhají na příjmy z reklam (zpravodajská média, sociální média a tak dále), mohou poskytovat relevantní reklamy pomocí extrahovaných přehledů jako dalších signálů pro tiskový server.
* *Moderování obsahu*: Pomocí modelů moderování textového a vizuálního obsahu ochráníte uživatele před nevhodným obsahem a ověřte, zda publikovaný obsah odpovídá hodnotám vaší organizace. Můžete automaticky blokovat určitá videa nebo upozornit uživatele na obsah.
* *Doporučení:* Přehledy videa lze použít ke zlepšení zapojení uživatelů zvýrazněním relevantních okamžiků videa pro uživatele. Označením každého videa pomocí dalších metadat můžete uživatelům doporučit nejrelevantnější videa a zvýraznit části videa, které budou odpovídat jejich potřebám.

## <a name="features"></a>Funkce

V následujícím seznamu jsou uvedeny přehledy, které můžete z videí načíst pomocí modelů videa a zvuku video indexeru:

### <a name="video-insights"></a>Přehledy videa

* **Detekce tváří**: Detekuje a seskupí tváře, které jsou ve videu zobrazené.
* **Identifikace celebrit**: Video Indexer automaticky identifikuje více než 1 milion celebrit - jako jsou světoví lídři, herci, herečky, sportovci, výzkumníci, obchodní a tech lídři po celém světě. Údaje o těchto celebritách lze také nalézt na různých webových stránkách (IMDB, Wikipedia a tak dále).
* **Identifikace tváře na základě účtu:** Video Indexer trénuje model pro konkrétní účet. Potom rozpozná tváře ve videu na základě trénovaného modelu. Další informace naleznete [v tématu Přizpůsobení modelu osoby z webu Video Indexer](customize-person-model-with-website.md) a [Přizpůsobení modelu osoby pomocí rozhraní API pro indexer videa](customize-person-model-with-api.md).
* **Extrakce miniatur pro plochy** ("nejlepší plocha"): Automaticky identifikuje nejlepší zachycenou plochu v každé skupině ploch (na základě kvality, velikosti a čelní polohy) a extrahuje ji jako obrazový datový zdroj.
* **Vizuální rozpoznávání textu** (OCR): Extrahuje text, který je vizuálně zobrazen ve videu.
* **Zmírnění vizuálního obsahu**: Detekuje vizuální obsah jen pro dospělé nebo nevhodný obsah.
* **Identifikace popisků**: Identifikuje vizuální objekty a zobrazené akce.
* **Segmentace scény**: Určuje, kdy se scéna změní ve videu na základě vizuálních podnětů. Scéna zobrazuje jednu událost a skládá se z řady po sobě jdoucích snímků, které jsou sémanticky příbuzné.
* **Detekce záběrů**: Určuje, kdy se snímek změní ve videu na základě vizuálních podnětů. Snímek je série snímků pořízených ze stejné houfně. Další informace naleznete [v tématu Scény, snímky a klíčové snímky](scenes-shots-keyframes.md).
* **Detekce černých snímků**: Identifikuje černé snímky ve videu.
* **Extrakce klíčových snímků**: Zjistí stabilní klíčové snímky ve videu.
* **Rolling kredity**: Identifikuje začátek a konec válcování kreditů na konci televizních pořadů a filmů.
* **Detekce animovaných postav** (náhled): Detekce, seskupení a rozpoznávání postav v animovaném obsahu prostřednictvím integrace s [vlastní vizí služby Cognitive Services](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Další informace naleznete v [tématu Detekce animovaných znaků](animated-characters-recognition.md).
* **Redakční detekce typu snímku:** Označování snímků na základě jejich typu (jako široký záběr, střední záběr, zblízka, extrémní zblízka, dva výstřely, více lidí, venkovní a vnitřní a tak dále). Další informace naleznete [v tématu Redakční detekce typů záběrů](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Přehledy zvuku

* **Přepis zvuku**: Převádí řeč na text ve 12 jazycích a umožňuje rozšíření. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, mandarínská čínština, japonština, arabština, ruština, brazilská portugalština, hindština a korejština.
* **Automatické rozpoznávání jazyka**: Automaticky identifikuje převládající mluvený jazyk. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, mandarínská čínština, japonština, ruština a brazilská portugalština. Pokud jazyk nelze identifikovat s důvěrou, Video Indexer předpokládá, že mluvený jazyk je angličtina. Další informace naleznete v [tématu Model identifikace jazyka](language-identification-model.md).
* **Vícejazyčná identifikace a přepis řeči** (náhled): Automaticky identifikuje mluvený jazyk v různých segmentech od zvuku. Odešle každý segment mediálního souboru k přepisu a pak zkombinuje přepis zpět do jednoho jednotného přepisu. Další informace naleznete v tématu [Automaticky identifikovat a přepsat vícejazyčný obsah](multi-language-identification-transcription.md).
* **Tvorba titulků**: Vytvoří titulky ve třech formátech: VTT, TTML, SRT.
* **Zpracování dvou kanálů**: Auto detekuje samostatný přepis a sloučí se na jednu časovou osu.
* **Redukce šumu**: Vymaže zvukové nebo hlučné nahrávky telefonního subsystému (na základě filtrů Skype).
* **Přizpůsobení přepisu** (CRIS): Trénuje vlastní modely řeči na textové modely, aby vytvořily přepisy specifické pro dané odvětví. Další informace naleznete [v tématech Přizpůsobení jazykového modelu z webu Video Indexer](customize-language-model-with-website.md) a [Přizpůsobení jazykového modelu pomocí rozhraní API videoindexeru](customize-language-model-with-api.md).
* **Výčet mluvčího**: Mapuje a chápe, který řečník mluvil, která slova a kdy.
* **Statistiky mluvčích**: Poskytuje statistiky pro poměry řeči řečníků.
* **Zmírnění textového obsahu**: Detekuje explicitní text v přepisu zvuku.
* **Zvukové efekty**: Identifikuje zvukové efekty, jako jsou tleskání rukou, řeč a ticho.
* **Detekce emocí**: Identifikuje emoce na základě řeči (co se říká) a hlasové tonaliality (jak se to říká). Emoce může být radost, smutek, hněv, nebo strach.
* **Překlad**: Vytvoří překlady přepisu zvuku do 54 různých jazyků.

### <a name="audio-and-video-insights-multi-channels"></a>Přehledy zvuku a videa (více kanály)

Při indexování podle jednoho kanálu bude k dispozici částečný výsledek pro tyto modely.

* **Extrakce klíčových slov**: Extrahuje klíčová slova z řeči a vizuálního textu.
* **Extrakce pojmenovaných entit**: Extrahuje značky, umístění a osoby z řeči a vizuálního textu prostřednictvím zpracování přirozeného jazyka (NLP).
* **Dedukce témat**: Dedukuje hlavní témata z přepisů. V ceně je zahrnuta taxonomie IPTC 2.
* **Artefakty**: Extrahuje bohatou sadu artefaktů s „další úrovní podrobností“ pro každý z modelů.
* **Analýza citového zabarvení**: Identifikuje kladná, záporná a neutrální zabarvení řeči a vizuálního textu.

## <a name="how-can-i-get-started-with-video-indexer"></a>Jak mohu začít s Video Indexer?

K možnostem videoindexeru můžete přistupovat třemi způsoby:

* Portál Video Indexer: Snadno použitelné řešení, které umožňuje vyhodnotit produkt, spravovat účet a přizpůsobit modely.

    Další informace o portálu najdete [v tématu Začínáme s webem Video Indexer](video-indexer-get-started.md).  

* Integrace rozhraní API: Všechny funkce video indexeru jsou k dispozici prostřednictvím rozhraní REST API, které vám umožní integrovat řešení do vašich aplikací a infrastruktury.

    Chcete-li začít jako vývojář, [přečtěte si informace o použití rozhraní REST API služby Video Indexer](video-indexer-use-apis.md).

* Vložitelný widget: Umožňuje vložit přehledy, přehrávač a editor videa do aplikace.

    Další informace naleznete [v tématu Vložení vizuálních widgetů do aplikace](video-indexer-embed-widgets.md).

Pokud používáte web, přehledy se přidají jako metadata a budou viditelné na portálu. Pokud používáte rozhraní API, přehledy jsou k dispozici jako soubor JSON.

## <a name="next-steps"></a>Další kroky

Jste připraveni začít pracovat s Video Indexerem. Další informace najdete v těchto článcích:

- [Začínáme s webem Video Indexer](video-indexer-get-started.md).
- [Zpracovat obsah pomocí rozhraní REST API videoindexeru](video-indexer-use-apis.md).
- [Vložte vizuální widgety do aplikace](video-indexer-embed-widgets.md).
