---
title: Co je počítačové zpracování obrazu?
titleSuffix: Azure Cognitive Services
description: Služba Počítačové zpracování obrazu poskytuje přístup k pokročilým algoritmům pro zpracování imagí a vracení informací.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: počítačové zpracování obrazu, aplikace počítačové vidění, Služba Computer visioning Service
ms.openlocfilehash: 62d82388d975f36c422f7eb1e9feff8ce2725d54
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033285"
---
# <a name="what-is-computer-vision"></a>Co je počítačové zpracování obrazu?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Služba Počítačové zpracování obrazu v Azure vám poskytuje přístup k pokročilým algoritmům, které zpracovávají obrázky a vracejí informace na základě vizuálních funkcí, které vás zajímají. Počítačové zpracování obrazu například může určit, jestli obrázek obsahuje obsah pro dospělé, najít konkrétní značky nebo objekty nebo najít lidské obličeje.

Můžete vytvořit Počítačové zpracování obrazu aplikace prostřednictvím [sady Client Library SDK](./quickstarts-sdk/client-library.md) nebo voláním [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) přímo. Tato stránka obsahuje širokou škálu toho, co můžete s Počítačové zpracování obrazu provádět.

## <a name="optical-character-recognition-ocr"></a>optické rozpoznávání znaků (OCR),

Počítačové zpracování obrazu obsahuje možnosti [optického rozpoznávání znaků (OCR)](concept-recognizing-text.md) . Nové rozhraní API pro čtení můžete použít k extrakci vytištěného a rukopisného textu z obrázků a dokumentů. Používá nejnovější modely a pracuje s textem na různých površích a na pozadí. Patří mezi ně příjmy, plakáty, vizitky, dopisy a tabule. Tato dvě rozhraní API pro optické rozpoznávání znaků podporují extrakci vytištěného textu v [několika jazycích](./language-support.md). Začněte pomocí [rychlého](./quickstarts-sdk/client-library.md) startu.

## <a name="computer-vision-for-digital-asset-management"></a>Počítačové zpracování obrazu pro správu digitálních prostředků

Počítačové zpracování obrazu může výkon mnoha scénářů správy digitálních prostředků (přehradit). Přehradní je obchodní proces organizace, ukládání a načítání multimediálních prostředků a správu digitálních práv a oprávnění. Společnost může například chtít seskupit a identifikovat obrázky na základě viditelných log, plošek, objektů, barev a tak dále. Nebo můžete chtít automaticky [vygenerovat titulky pro obrázky](./Tutorials/storage-lab-tutorial.md) a připojit klíčová slova, aby je bylo možné prohledávat. Pro přehradní řešení all-in-One pomocí Cognitive Services, Azure Kognitivní hledání a inteligentního generování sestav si Projděte [příručku akcelerátoru řešení](https://github.com/Azure-Samples/azure-search-knowledge-mining) pro vyhledávání znalostí na GitHubu. Další příklady přehrad naleznete v tématu [počítačové zpracování obrazu – úložiště šablon řešení](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) .

## <a name="analyze-images-for-insight"></a>Analýza obrázků za účelem získání přehledu

Obrázky můžete analyzovat a poskytnout tak přehled o jejich vizuálních funkcích a vlastnostech. Všechny funkce v tabulce níže jsou poskytovány rozhraním API pro [analýzu imagí](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Začněte pomocí [rychlého](./quickstarts-sdk/client-library.md) startu.


### <a name="tag-visual-features"></a>Označování vizuálních vlastností

Identifikujte a označte vizuální funkce na obrázku ze sady tisíců rozpoznatelných objektů, živých věcí, krajin a akcí. Pokud jsou značky dvojznačné nebo nejsou běžné znalosti, poskytuje odpověď rozhraní API nápovědu k objasnění kontextu značky. Označování se neomezuje na hlavní předmět, například postavu v popředí, ale zahrnuje také prostředí (interiér nebo exteriér), nábytek, nástroje, rostliny, zvířata, příslušenství, pomůcky atd. [Označování vizuálních vlastností](concept-tagging-images.md)

### <a name="detect-objects"></a>Detekovat objekty

Detekce objektu je podobná označování, ale rozhraní API vrací souřadnice ohraničujícího pole pro každou použitou značku. Pokud například obrázek obsahuje pes, Cat a osobu, operace zjišťování zobrazí tyto objekty spolu s jejich souřadnicemi v obrázku. Tuto funkci můžete použít ke zpracování dalších vztahů mezi objekty v imagi. Také vám umožní zjistit, že je v obrázku více instancí stejné značky. [Detekovat objekty](concept-object-detection.md)

### <a name="detect-brands"></a>Detekovat značky

Identifikujte komerční značky na obrázcích nebo videích z databáze tisíců globálních log. Tuto funkci můžete použít například k tomu, abyste zjistili, které značky jsou nejoblíbenější na sociálních médiích nebo ve většině druhů v mediálním umístění. [Detekovat značky](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Kategorizace obrázku

Identifikuje a kategorizuje celý obrázek s využitím [taxonomie kategorií](Category-Taxonomy.md) s dědičnými hierarchiemi nadřízený/podřízený objekt. Kategorie je možné používat samostatně nebo s našimi novými modely označování.<br/>V současné době je jediným podporovaným jazykem pro označování a kategorizaci obrázků angličtina. [Kategorizace obrázku](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Popis obrázku

Vygeneruje popis celého obrázku v celých větách v čitelném jazyce. Algoritmy Počítačového zpracování obrazu generují různé popisy v závislosti na objektech identifikovaných na obrázku. Jednotlivé popisy se vyhodnotí a vygeneruje se pro ně skóre spolehlivosti. Pak se vrátí seznam seřazený od nejvyššího skóre spolehlivosti po nejnižší. [Popis obrázku](concept-describing-images.md)

### <a name="detect-faces"></a>Rozpoznávání tváří

Rozpoznává tváře na obrázku a poskytuje informace o jednotlivých rozpoznaných tvářích. Počítačové zpracování obrazu pro každou rozpoznanou tvář vrátí souřadnice, obdélník, pohlaví a věk.<br/>Počítačové zpracování obrazu poskytuje podmnožinu funkcí služby [obličeje](../face/index.yml) . Službu obličeje můžete použít pro podrobnější analýzu, jako je například identifikace obličeje a detekce pozice. [Rozpoznávání tváří](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Rozpoznávání typů obrázků

Rozpoznává charakteristiky obrázku, například jestli jde o perokresbu nebo s jakou pravděpodobností je obrázek klipart. [Rozpoznávání typů obrázků](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Rozpoznávání obsahu specifického doménu

S využitím doménových modelů rozpoznává a identifikuje obsah obrázku specifický pro doménu, například celebrity a památky. Pokud například obrázek obsahuje lidi, Počítačové zpracování obrazu může použít doménový model pro celebrit k určení, jestli se lidem zjištěné v imagi říká celebrit. [Rozpoznávání obsahu specifického doménu](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Rozpoznávání barevného schématu

Analyzuje použité barvy na obrázku. Počítačové zpracování obrazu dokáže určit, jestli je obrázek černobílý nebo barevný, a u barevných obrázků identifikovat dominantní a doplňkové barvy. [Rozpoznávání barevného schématu](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Vytvoření miniatury

Analyzuje obsah obrázku a vytvoří pro obrázek odpovídající miniaturu. Počítačové zpracování obrazu nejprve vygeneruje miniaturu s vysokou kvalitou a následně analyzuje objekty v rámci obrázku a určí *oblast zájmu*. Počítačové zpracování obrazu potom obrázek ořízne tak, aby odpovídal požadavkům oblasti zájmu. Vytvořená miniatura může mít podle vašich potřeb jiný poměr stran než původní obrázek. [Vytvoření miniatury](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>Získat oblast zájmu

Analyzujte obsah obrázku a vraťte tak souřadnice *oblasti zájmu*. Místo oříznutí obrázku a vygenerování miniatury Počítačové zpracování obrazu vrátí souřadnice ohraničujícího pole oblasti, aby volající aplikace mohl původní obrázek upravit podle potřeby. [Získat oblast zájmu](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Moderování obsahu obrázků

Počítačové zpracování obrazu můžete použít ke [zjištění obsahu pro dospělé](concept-detecting-adult-content.md) v imagi a vracet hodnocení spolehlivosti pro různé klasifikace. Prahová hodnota pro obsah pro označování obsahu se dá nastavit na klouzavé stupnici, aby vyhovovala vašim potřebám.

## <a name="deploy-on-premises-using-docker-containers"></a>Nasazení místně pomocí kontejnerů Docker

Použijte kontejnery Počítačové zpracování obrazu k nasazení funkcí rozhraní API místně. Tyto kontejnery Docker umožňují přiblížit službu k vašim datům pro účely dodržování předpisů, zabezpečení nebo jiných provozních důvodů. Počítačové zpracování obrazu nabízí následující kontejnery:

* [Počítačové zpracování obrazu čtení kontejneru OCR (Preview)](computer-vision-how-to-install-containers.md) umožňuje rozpoznat vytištěný a rukou psaný text v obrázcích.
* [Kontejner počítačové zpracování obrazu prostorové analýzy (Preview)](spatial-analysis-container.md) vám umožňuje analyzovat streamování videa v reálném čase, abyste pochopili prostorové vztahy mezi lidmi a jejich pohybem prostřednictvím fyzických prostředí.

## <a name="image-requirements"></a>Požadavky image

Počítačové zpracování obrazu dokáže analyzovat obrázky, které splňují následující požadavky:

- Obrázek musí být ve formátu JPEG, PNG, GIF nebo BMP.
- Velikost souboru obrázku musí být menší než 4 megabajty (MB).
- Rozměry obrázku musí být větší než 50 × 50 pixelů.
  - Pro rozhraní API pro čtení musí být rozměry obrázku mezi 50 x 50 a 10000 × 10000 pixelů.

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech Cognitive Services by měli vývojáři, kteří používají Počítačové zpracování obrazu službu, znát zásady Microsoftu pro zákaznická data. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Začněte s Počítačové zpracování obrazu pomocí Průvodce rychlým startem v preferovaném vývojovém jazyce:

- [Rychlý Start: Počítačové zpracování obrazu REST API nebo klientské knihovny](./quickstarts-sdk/client-library.md)
