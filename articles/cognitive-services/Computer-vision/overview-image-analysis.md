---
title: Co je analýza obrázků?
titleSuffix: Azure Cognitive Services
description: Služba analýza obrázků používá předem připravené modely AI k extrakci mnoha různých vizuálních funkcí z obrázků.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: počítačové zpracování obrazu, aplikace počítačové vidění, Služba Computer visioning Service
ms.openlocfilehash: 0258eb7c57bc0734b5c0a67644cbaa4f62a34537
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766910"
---
# <a name="what-is-image-analysis"></a>Co je analýza obrázků?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Služba analýzy Počítačové zpracování obrazu Image může z obrázků extrahovat širokou škálu vizuálních funkcí. Například může určit, jestli obrázek obsahuje obsah pro dospělé, najít konkrétní značky nebo objekty nebo najít lidské obličeje.

Analýzu obrázků můžete použít prostřednictvím sady Client Library SDK nebo přímo voláním [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v2-ga/operations/5d986960601faab4bf452005) . Začněte tím, že přejdete do kurzu [rychlý Start](quickstarts-sdk/image-analysis-client-library.md) .

Tato dokumentace obsahuje následující typy článků:
* [Rychlé starty](./quickstarts-sdk/image-analysis-client-library.md) jsou podrobné pokyny, které vám umožní volat službu a získat výsledky v krátké době. 
* [Příručky](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) návody obsahují pokyny k používání služby v konkrétnějším nebo přizpůsobeném způsobu.
* [Koncepční články](concept-tagging-images.md) poskytují podrobné vysvětlení funkcí a funkcí služby.
* [Kurzy](./tutorials/storage-lab-tutorial.md) jsou delší než příručky, které ukazují, jak tuto službu používat jako součást v širších obchodních řešeních.

## <a name="image-analysis-features"></a>Funkce analýzy obrázků

Obrázky můžete analyzovat a poskytnout tak přehled o jejich vizuálních funkcích a vlastnostech. Všechny funkce v následujícím seznamu jsou poskytovány rozhraním API pro [analýzu imagí](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) . Začněte pomocí [rychlého](./quickstarts-sdk/image-analysis-client-library.md) startu.


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

## <a name="image-requirements"></a>Požadavky image

Analýza obrázků funguje na obrázcích, které splňují následující požadavky:

- Obrázek musí být ve formátu JPEG, PNG, GIF nebo BMP.
- Velikost souboru obrázku musí být menší než 4 megabajty (MB).
- Rozměry obrázku musí být větší než 50 × 50 pixelů.

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech Cognitive Services by měli vývojáři, kteří používají Počítačové zpracování obrazu službu, znát zásady Microsoftu pro zákaznická data. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Začněte s analýzou obrázků pomocí Průvodce rychlým startem v preferovaném vývojovém jazyce:

- [Rychlý Start: Počítačové zpracování obrazu REST API nebo klientské knihovny](./quickstarts-sdk/image-analysis-client-library.md)
