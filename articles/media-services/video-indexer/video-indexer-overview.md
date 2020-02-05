---
title: Co je Video Indexer?
titleSuffix: Azure Media Services
description: Tento článek obsahuje přehled služby Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/02/2020
ms.author: juliako
ms.openlocfilehash: aee4390d9b892eddbd7dc59887f5268e4c35d3f7
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989786"
---
# <a name="what-is-video-indexer"></a>Co je Video Indexer?

Video Indexer (VI) je řešení Azure Media Services AI a součást značky Azure Cognitive Services. Video Indexer poskytuje možnost extrahovat podrobné přehledy (bez potřeby analýzy dat nebo dovedností v kódování) pomocí modelů strojového učení, které jsou založené na několika kanálech (Voice, vocals, Visual). Modely můžete dále přizpůsobit a naučit. Služba umožňuje hloubkové hledání, snižuje provozní náklady, umožňuje nové příležitosti finanční zhodnocení a vytváří nové uživatelské prostředí ve velkých archivech videí (s nízkými překážkami vstupu).

Pokud chcete začít s extrakcí přehledů pomocí Video Indexer, musíte si vytvořit účet a nahrávat videa. Když nahrajete videa do Video Indexer, analyzují vizuály i zvuk spuštěním různých modelů AI. Jak Video Indexer analyzuje vaše video, přehledy, které jsou extrahovány modely AI.

Následující diagram představuje ilustraci a nejedná se o technické vysvětlení toho, jak Video Indexer funguje v back-endu.

![Diagram toku Azure Media Services Video Indexer](./media/video-indexer-overview/model-chart.png)

## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení
 
Důležitou připomínkou je, že je nutné dodržovat všechny použitelné zákony v používání Video Indexer a nesmíte používat Video Indexer ani žádnou jinou službu Azure způsobem, který porušuje práva jiných nebo může být škodlivá jiným osobám. Před nahráním videí, včetně jakýchkoli biometrických dat, do služby Video Indexer pro zpracování a ukládání, musíte mít všechna patřičná práva, včetně příslušných souhlasů, od jednotlivých jednotlivců ve videu. Pokud se chcete dozvědět o dodržování předpisů, ochraně osobních údajů a zabezpečení v Video Indexer, [podmínky Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)Microsoftu. Pro závazky ochrany osobních údajů Microsoftu a jejich zpracování si přečtěte [prohlášení o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement)od Microsoftu, [podmínky online služeb](https://www.microsoft.com/licensing/product-licensing/products) ("OST") a [doplněk pro zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA). Další informace o ochraně osobních údajů, včetně uchovávání, odstranění nebo zničení dat, jsou k dispozici v OST a [zde](faq.md). Když použijete Video Indexer, souhlasíte s tím, že jste vázáni na Cognitive Services podmínky, OST, DPA a prohlášení o zásadách ochrany osobních údajů.

## <a name="what-can-i-do-with-video-indexer"></a>Co se dá dělat s Video Indexer?

Přehledy Video Indexer je možné použít u mnoha scénářů, mimo jiné:

* *Hloubkové vyhledávání*: k vylepšení možností vyhledávání v knihovně videí použijte přehledy získané z videa. Například indexování mluveného slova a obličeje může umožňovat hledání ve videu, kde osoba čelí určitým slovům, nebo když se dvě lidi viděli společně. Hledání na základě těchto poznatků z videí se vztahuje na diskusní úřady, vzdělávací instituce, vysílání, spoluvlastníky obsahu, podnikové obchodní aplikace a obecně na všechny obory, které mají knihovnu videí, na kterou uživatelé potřebují Hledat.
* *Vytváření obsahu*: vytváření přípojných vozidel, světelná cívky, obsah sociálních médií nebo novinové klipy na základě přehledů video indexer z obsahu. Klíčové snímky, značky scény a časová razítka pro osoby a vzhledy štítků usnadňují proces vytváření mnohem hladší a jednodušší a umožňuje dostat se k částem videa, které potřebujete pro obsah, který vytváříte.
* *Usnadnění*: zda chcete zpřístupnit obsah pro osoby s postižením nebo pokud chcete, aby byl obsah distribuován do různých oblastí pomocí různých jazyků, můžete použít přepis a překlady poskytované video indexerem v několika jazycích.
* *Finanční zhodnocení*: video indexer může přispět k navýšení hodnoty videí. Například obory, které spoléhají na výnosy AD (sdělovací média, sociální média atd.), můžou poskytovat relevantní reklamy pomocí extrahovaných přehledů jako další signály na server služby AD.
* *Moderování obsahu*: Použití textových a vizuálních modelů pro moderování obsahu k zajištění bezpečnosti uživatelů před nevhodným obsahem a ověření, že obsah, který publikujete, odpovídá hodnotám vaší organizace. Můžete automaticky blokovat určitá videa nebo upozornit uživatele na obsah.
* *Doporučení*: ke zlepšení zapojení uživatelů je možné využít video přehledy, které uživatelům zvýrazní relevantní video. Označením jednotlivých videí s dalšími metadaty můžete uživatelům doporučit nejdůležitější videa a zvýraznit části videa, které budou vyhovovat jejich potřebám.

## <a name="features"></a>Funkce

Následující seznam obsahuje přehledy, které můžete z videí načíst pomocí Video Indexer video a zvukové modely:

### <a name="video-insights"></a>Video přehledy

* **Detekce tváří**: Detekuje a seskupí tváře, které jsou ve videu zobrazené.
* **Identifikace celebrit**: video indexer automaticky identifikuje přes 1 000 000 celebrit, jako jsou World vedoucí, aktéry, Actresses, sportovců, výzkumníki, obchodní a technické vedoucí na celém světě. Data o těchto celebrit se taky dají najít na různých webech (IMDB, Wikipedii atd.).
* **Identifikace tváře na základě účtu:** Video Indexer trénuje model pro konkrétní účet. Pak rozpoznává obličeje ve videu na základě trained model. Další informace najdete v tématu [Přizpůsobení modelu osoby z video indexer webu](customize-person-model-with-website.md) a [Přizpůsobení modelu osoby pomocí video indexer API](customize-person-model-with-api.md).
* **Extrakce miniatur pro obličeje** ("nejlepší plocha"): automaticky identifikuje nejlépe zachycenou plošku v každé skupině ploch (na základě kvality, velikosti a čelní pozice) a extrahuje je jako prostředek obrázku.
* **Rozpoznávání vizuálního textu** (OCR): extrahuje text, který je vizuálně zobrazen ve videu.
* **Zmírnění vizuálního obsahu**: Detekuje vizuální obsah jen pro dospělé nebo nevhodný obsah.
* **Identifikace popisků**: Identifikuje vizuální objekty a zobrazené akce.
* **Segmentace scény**: Určuje, kdy se scény mění ve videu na základě vizuálních pomůcek. Scéna znázorňuje jednu událost a skládá se z řady po sobě jdoucích snímků, které jsou sémanticky spojeny.
* **Detekce snímku**: Určuje, kdy se ve videu mění snímek na základě vizuálních pomůcek. Snímek je řada snímků pořízených ze stejné kamery pro pohyb snímků. Další informace najdete v tématu [scény, snímky a klíčové snímky](scenes-shots-keyframes.md).
* **Detekce černých snímků**: Identifikuje černé snímky ve videu.
* **Extrakce klíčových snímků**: Zjistí stabilní klíčové snímky ve videu.
* **Kumulovaný kredity**: Určuje začátek a konec kumulovaných kreditů na konci televizních pořadů a filmů.
* **Detekce animovaných znaků** (Preview): detekce, seskupení a rozpoznávání znaků v animovaném obsahu prostřednictvím integrace s [Cognitive Services vlastní vize](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Další informace naleznete v tématu [animovaná detekce znaků](animated-characters-recognition.md).
* **Detekce typu redakčního snímku**: označení snímků na základě jejich typu (jako je třeba celá kopie, střední záběr, zavření, extrémní uzavření, dva snímky, více lidí, venkovní a vnitřní a další). Další informace najdete v tématu [zjištění typu redakčního snímku](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Přehledy zvuku

* **Automatické rozpoznávání jazyka**: Automaticky identifikuje převládající mluvený jazyk. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, ruština a brazilská portugalština. Pokud jazyk nemůžete identifikovat s jistotou, Video Indexer předpokládá, že mluvený jazyk je angličtina. Další informace najdete v tématu [model identifikace jazyka](language-identification-model.md).
* **Identifikace a přepis mluveného slova ve více jazycích** (Preview): automaticky identifikuje mluvený jazyk v různých segmentech ze zvuku. Pošle každý segment mediálního souboru, který se má přepisu, a pak ho zkombinuje zpátky do jednoho sjednoceného přepisu. Další informace najdete v tématu [Automatické určení a přepisovat vícejazyčného obsahu](multi-language-identification-transcription.md).
* **Přepis zvuku**: převede řeč na text v 12 jazycích a povoluje rozšíření. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), japonština, arabština, ruština, brazilská portugalština, hindština a korejština.
* **Tvorba titulků**: Vytvoří titulky ve třech formátech: VTT, TTML, SRT.
* **Zpracování dvou kanálů**: automaticky detekuje samostatný přepis a sloučení s jednou časovou osou.
* **Redukce hluku**: vymaže zvukové a přenosové nahrávky v telefonním formátu (na základě filtrů Skypu).
* **Přizpůsobení přepisu** (položku CRI): vlacích vlastní řeč na textové modely pro vytváření přepisů specifických pro jednotlivé obory. Další informace najdete v tématu [přizpůsobení jazykového modelu z video indexer webu](customize-language-model-with-website.md) a [přizpůsobení jazykového modelu pomocí rozhraní API video indexer](customize-language-model-with-api.md).
* **Výčet mluvčího**: mapuje a rozumí, ke kterému paprsku mluvčího se slova a kdy.
* **Statistika mluvčího**: poskytuje statistiku pro poměr řeči mluvčích.
* **Zmírnění textového obsahu**: Detekuje explicitní text v přepisu zvuku.
* **Zvukové efekty**: identifikuje zvukové efekty, jako je claps, řeč a tiché.
* **Detekce emoce**: identifikuje emoce na základě řeči (co se říká) a hlasového tónového (jak se říká). Emoce můžou mít radost, smutek, hněv nebo obavy.
* **Překlad**: Vytvoří překlady přepisu zvuku do 54 různých jazyků.

### <a name="audio-and-video-insights-multi-channels"></a>Zvukové a video přehledy (více kanálů)

Při indexování v jednom kanálu budou k dispozici částečný výsledek pro tyto modely.

* **Extrakce klíčových slov**: extrahuje klíčová slova z mluvené řeči a vizuálního textu.
* **Extrakce pojmenovaných entit**: extrahuje značky, umístění a lidi z mluvené řeči a vizuálního textu prostřednictvím zpracování přirozeného jazyka (NLP).
* **Dedukce témat**: Dedukuje hlavní témata z přepisů. Je zahrnutá taxonomie IPTC na druhé úrovni.
* **Artefakty**: Extrahuje bohatou sadu artefaktů s „další úrovní podrobností“ pro každý z modelů.
* **Analýza citového zabarvení**: Identifikuje kladná, záporná a neutrální zabarvení řeči a vizuálního textu.

## <a name="how-can-i-get-started-with-video-indexer"></a>Jak můžu začít s Video Indexer?

K funkcím Video Indexer máte přístup třemi způsoby:

* Video Indexer Portal: snadno použitelné řešení, které umožňuje vyhodnotit produkt, spravovat účet a přizpůsobovat modely.

    Další informace o portálu najdete v tématu Začínáme [s webem video indexer](video-indexer-get-started.md).  

* Integrace rozhraní API: všechny možnosti Video Indexer jsou dostupné prostřednictvím REST API, která umožňuje integrovat řešení do vašich aplikací a infrastruktury.

    Pokud chcete začít pracovat jako vývojář, přečtěte si téma [použití Video Indexer REST API](video-indexer-use-apis.md).

* Upravitelný widget: umožňuje vložit do své aplikace prostředí Video Indexer Insights, přehrávače a editoru.

    Další informace najdete v tématu [vkládání vizuálních pomůcek do aplikace](video-indexer-embed-widgets.md).

Pokud web používáte, přehledy se přidávají jako metadata a zobrazují se na portálu. Pokud používáte rozhraní API, přehledy jsou dostupné jako soubor JSON.

## <a name="next-steps"></a>Další kroky

Jste připraveni začít pracovat s Video Indexerem. Další informace najdete v těchto článcích:

- Začněte [s webem video indexer](video-indexer-get-started.md).
- [Zpracování obsahu pomocí Video Indexer REST API](video-indexer-use-apis.md).
- [Do své aplikace vložte vizuální widgety](video-indexer-embed-widgets.md).
