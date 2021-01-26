---
title: Co je Video Indexer služby Azure Media Services?
titleSuffix: Azure Media Services
description: Tento článek obsahuje přehled služby Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/11/2020
ms.author: juliako
ms.openlocfilehash: 06f5e19718445f44dd2302faf280f083cce0774f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98783797"
---
# <a name="what-is-azure-media-services-video-indexer"></a>Co je Video Indexer služby Azure Media Services?

[!INCLUDE [regulation](./includes/regulation.md)]

Video Indexer (VI) je řešení Azure Media Services AI a součást značky Azure Cognitive Services. Video Indexer poskytuje možnost extrahovat podrobné přehledy (bez potřeby analýzy dat nebo dovedností v kódování) pomocí modelů strojového učení, které jsou založené na několika kanálech (Voice, vocals, Visual). Modely můžete dále přizpůsobit a naučit. Služba umožňuje hloubkové hledání, snižuje provozní náklady, umožňuje nové příležitosti finanční zhodnocení a vytváří nové uživatelské prostředí ve velkých archivech videí (s nízkými překážkami vstupu).

Pokud chcete začít s extrakcí přehledů pomocí Video Indexer, musíte si vytvořit účet a nahrávat videa. Když nahrajete videa do Video Indexer, analyzují vizuály i zvuk spuštěním různých modelů AI. Jak Video Indexer analyzuje vaše video, přehledy, které jsou extrahovány modely AI.

Když vytvoříte účet Video Indexer a připojíte ho k Media Services, uloží se soubory médií a souborů metadat do účtu služby Azure Storage přidruženého k tomuto Media Services účtu. Další informace najdete v tématu [Vytvoření účtu video indexer připojeného k Azure](connect-to-azure.md).

Následující diagram představuje ilustraci a nejedná se o technické vysvětlení toho, jak Video Indexer funguje v back-endu.

![Diagram toku Azure Media Services Video Indexer](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení

Důležitou připomínkou je, že je nutné dodržovat všechny použitelné zákony v používání Video Indexer a nesmíte používat Video Indexer ani žádnou službu Azure způsobem, který porušuje práva ostatních nebo kteří můžou být pro ostatní škodlivé.

Před nahráním videa nebo obrázku do Video Indexer musíte mít všechna správná práva k používání videa nebo obrázku, včetně, pokud to vyžaduje zákon, všech nezbytných souhlasů od jednotlivců (pokud existují) na videu nebo obrázku, pro použití, zpracování a ukládání svých dat v Video Indexer a Azure. Některé jurisdikce můžou u této kolekce stanovit zvláštní zákonné požadavky, online zpracování a ukládání určitých kategorií dat, jako je biometriková data. Než začnete používat Video Indexer a Azure pro zpracování a ukládání jakýchkoli dat, která se vztahují na zvláštní zákonné požadavky, musíte zajistit dodržování předpisů u všech právních požadavků, které se na vás můžou vztahovat.

Informace o dodržování předpisů, ochraně osobních údajů a zabezpečení v Video Indexer najdete na webu Microsoft [Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx). Pro závazky společnosti Microsoft, postupy pro zpracování a uchovávání dat, včetně toho, jak odstranit vaše data, přečtěte si [prohlášení o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement)od Microsoftu, [podmínky používání služeb Online Services](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") a [doplněk pro zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA). Pomocí Video Indexer souhlasíte s tím, že DPA a prohlášením o zásadách ochrany osobních údajů.

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

* **Přepis zvuku**: převede řeč na text v 12 jazycích a povoluje rozšíření. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, mandarínština, japonština, arabština, ruština, portugalština, hindština a korejština.
* **Automatické rozpoznávání jazyka**: Automaticky identifikuje převládající mluvený jazyk. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, mandarínština, japonština, ruština a portugalština. Pokud není možné s jistotou identifikovat jazyk, Video Indexer předpokládá, že mluveným jazykem je angličtina. Další informace najdete v tématu [Model identifikace jazyka](language-identification-model.md).
* **Identifikace a přepis vícejazyčného hlasu**: automaticky identifikuje mluvený jazyk v různých segmentech ze zvuku. Jednotlivé části mediálního souboru se odešlou k přepisu a tyto části přepisu se pak znovu spojí v jeden ucelený přepis. Další informace najdete v tématu [Automatická identifikace a přepis vícejazyčného obsahu](multi-language-identification-transcription.md).
* **Tvorba titulků**: Vytvoří titulky ve třech formátech: VTT, TTML, SRT.
* **Zpracování dvou kanálů**: automaticky detekuje samostatný přepis a sloučení s jednou časovou osou.
* **Redukce hluku**: vymaže zvukové a přenosové nahrávky v telefonním formátu (na základě filtrů Skypu).
* **Přizpůsobení přepisu** (položku CRI): vlacích vlastní řeč na textové modely pro vytváření přepisů specifických pro jednotlivé obory. Další informace najdete v tématu [přizpůsobení jazykového modelu z video indexer webu](customize-language-model-with-website.md) a [přizpůsobení jazykového modelu pomocí rozhraní API video indexer](customize-language-model-with-api.md).
* **Výčet mluvčího**: mapuje a rozumí, ke kterému paprsku mluvčího se slova a kdy. Šestnáct mluvčích se dá zjistit v jednom zvukovém souboru.
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

## <a name="supported-browsers"></a>Podporované prohlížeče

V následujícím seznamu jsou uvedeny podporované prohlížeče, které můžete použít pro Video Indexer web a pro aplikace, které vkládají widgety. V seznamu se zobrazí také minimální podporovaná verze prohlížeče:

- Edge, verze: 16
- Firefox, verze: 54
- Chrome, verze: 58
- Safari, verze: 11
- Opera, verze: 44
- Opera Mobile, verze: 59
- Prohlížeč Android verze: 81
- Prohlížeč Samsung, verze: 7
- Chrome pro Android, verze: 87
- Firefox pro Android, verze: 83

## <a name="next-steps"></a>Další kroky

Jste připraveni začít pracovat s Video Indexerem. Další informace najdete v následujících článcích:

- Začněte [s webem video indexer](video-indexer-get-started.md).
- [Zpracování obsahu pomocí Video Indexer REST API](video-indexer-use-apis.md).
- [Do své aplikace vložte vizuální widgety](video-indexer-embed-widgets.md).
