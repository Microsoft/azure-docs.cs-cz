---
title: Co je počítačové zpracování obrazu? - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Služba počítačového zpracování obrazu umožňuje vývojářům používat pokročilé algoritmy, které zpracovávají obrázky a vrací informace.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/27/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af49e80766d7ac4bd2d9ed677a2571fb0a5a3189
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80131750"
---
# <a name="what-is-computer-vision"></a>Co je počítačové zpracování obrazu?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Služba Počítačové zpracování počítače Azure poskytuje vývojářům přístup k pokročilým algoritmům, které zpracovávají ibi a vracejí informace, v závislosti na vizuálních funkcích, které vás zajímají. Počítačové vidění může například určit, zda obrázek obsahuje obsah pouze pro dospělé, nebo může najít všechny lidské tváře v obraze.

Počítačové zpracování počítače můžete použít ve vaší aplikaci prostřednictvím nativní sady SDK nebo vyvoláním rozhraní REST API přímo. Tato stránka zhruba popisuje, co můžete dělat s computer vision.

## <a name="computer-vision-for-digital-asset-management"></a>Počítačové vidění pro správu digitálních aktiv

Počítačové vidění může pohánět mnoho scénářů správy digitálních aktiv (DAM). DAM je obchodní proces organizace, ukládání a načítání multimediálních datových zdrojů a správy digitálních práv a oprávnění. Společnost může například chtít seskupit a identifikovat obrázky na základě viditelných log, tváří, objektů, barev a tak dále. Nebo můžete chtít automaticky [generovat titulky pro obrázky](./Tutorials/storage-lab-tutorial.md) a připojit klíčová slova, aby je bylo možné prohledávat. Řešení SLUŽBY DAM vše v jednom pomocí služeb Cognitive Services, Azure Cognitive Search a inteligentního vytváření sestav najdete v [tématu Průvodce akcelerátorem řešení dolování znalostí](https://github.com/Azure-Samples/azure-search-knowledge-mining) na GitHubu. Další příklady dam naleznete v úložišti [šablon řešení počítačového vidění.](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates)

## <a name="analyze-images-for-insight"></a>Analýza obrázků za účelem získání přehledu

Obrázky můžete analyzovat a zjistit a poskytnout přehled o jejich vizuálních funkcích a vlastnostech. Všechny funkce v následující tabulce jsou poskytovány rozhraním Analyzovat rozhraní API [pro obrázky.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)

| Akce | Popis |
| ------ | ----------- |
|**[Označování vizuálních vlastností](concept-tagging-images.md)**|Identifikujte a označte vizuální prvky v obraze ze sady tisíců rozpoznatelných objektů, živých věcí, scenérií a akcí. Pokud jsou značky nejednoznačné nebo nejsou všeobecně známé, odpověď rozhraní API poskytuje rady k objasnění kontextu značky. Označování se neomezuje na hlavní předmět, například postavu v popředí, ale zahrnuje také prostředí (interiér nebo exteriér), nábytek, nástroje, rostliny, zvířata, příslušenství, pomůcky atd.|
|**[Detekce objektů](concept-object-detection.md)**| Detekce objektů je podobná tagování, ale rozhraní API vrátí souřadnice ohraničovacího rámečku pro každou použitou značku. Pokud například obrázek obsahuje psa, kočku a osobu, operace Detekce tyto objekty zobrazí spolu s jejich souřadnicemi v obraze. Tuto funkci můžete použít ke zpracování dalších vztahů mezi objekty v obraze. Také vás nechá vědět, kdy je v obraze více instancí stejné značky.|
|**[Detekce značek](concept-brand-detection.md)**|Identifikujte komerční značky na obrázcích nebo videích z databáze tisíců globálních log. Pomocí této funkce můžete například zjistit, které značky jsou nejoblíbenější na sociálních médiích nebo nejrozšířenější v umístění mediálního produktu.|
|**[Kategorizace obrázku](concept-categorizing-images.md)**|Identifikuje a kategorizuje celý obrázek s využitím [taxonomie kategorií](Category-Taxonomy.md) s dědičnými hierarchiemi nadřízený/podřízený objekt. Kategorie je možné používat samostatně nebo s našimi novými modely označování.<br/>V současné době je jediným podporovaným jazykem pro označování a kategorizaci obrázků angličtina.|
|**[Popis obrázku](concept-describing-images.md)**|Vygeneruje popis celého obrázku v celých větách v čitelném jazyce. Algoritmy Počítačového zpracování obrazu generují různé popisy v závislosti na objektech identifikovaných na obrázku. Jednotlivé popisy se vyhodnotí a vygeneruje se pro ně skóre spolehlivosti. Pak se vrátí seznam seřazený od nejvyššího skóre spolehlivosti po nejnižší.|
|**[Rozpoznávání tváří](concept-detecting-faces.md)** |Rozpoznává tváře na obrázku a poskytuje informace o jednotlivých rozpoznaných tvářích. Počítačové zpracování obrazu pro každou rozpoznanou tvář vrátí souřadnice, obdélník, pohlaví a věk.<br/>Počítačové vidění poskytuje podmnožinu funkce [služby Face.](/azure/cognitive-services/face/) Službu Obličej můžete použít pro podrobnější analýzu, jako je identifikace obličeje a detekce pózy.|
|**[Rozpoznávání typů obrázků](concept-detecting-image-types.md)**|Rozpoznává charakteristiky obrázku, například jestli jde o perokresbu nebo s jakou pravděpodobností je obrázek klipart.|
|**[Rozpoznávání obsahu specifického doménu](concept-detecting-domain-content.md)**|S využitím doménových modelů rozpoznává a identifikuje obsah obrázku specifický pro doménu, například celebrity a památky. Pokud například obrázek obsahuje osoby, může počítačové zpracování obrazu použít model domény pro celebrity k určení, zda jsou lidé zjištění v obraze známými celebritami.|
|**[Rozpoznávání barevného schématu](concept-detecting-color-schemes.md)**|Analyzuje použité barvy na obrázku. Počítačové zpracování obrazu dokáže určit, jestli je obrázek černobílý nebo barevný, a u barevných obrázků identifikovat dominantní a doplňkové barvy.|
|**[Generovat miniaturu](concept-generating-thumbnails.md)**|Analyzuje obsah obrázku a vytvoří pro obrázek odpovídající miniaturu. Počítačové vidění nejprve vygeneruje vysoce kvalitní miniaturu a poté analyzuje objekty v obraze, aby *určiloblast zájmu*. Počítačové vidění pak plodiny obraz, aby vyhovovaly požadavkům oblasti zájmu. Vytvořená miniatura může mít podle vašich potřeb jiný poměr stran než původní obrázek.|
|**[Získejte oblast zájmu](concept-generating-thumbnails.md#area-of-interest)**|Analyzovat obsah obrázku vrátit souřadnice *oblasti zájmu*. Namísto oříznutí obrázku a generování miniatury vrátí funkce Počítačové vidění souřadnice ohraničovacího rámečku oblasti, takže volající aplikace může podle potřeby upravit původní obrázek.|

## <a name="extract-text-from-images"></a>Extrakce textu z obrázků

Rozhraní API [pro čtení](concept-recognizing-text.md#read-api) počítačového vidění můžete použít k extrahování tištěného a ručně psaného textu z obrázků do strojově čitelného datového proudu znaků. Rozhraní Read API používá naše nejnovější modely a pracuje s textem na různých površích a pozadích, jako jsou účtenky, plakáty, vizitky, dopisy a tabule. V současné době jsou angličtina a španělština jedinými podporovanými jazyky.

K extrahování tištěného textu v několika jazycích můžete také použít rozhraní API [pro optické rozpoznávání znaků (OCR).](concept-recognizing-text.md#ocr-optical-character-recognition-api) V případě potřeby opraví rozpoznávání OCR otočení rozpoznaného textu a poskytne souřadnice rámečku každého slova. Rozpoznávání OCR podporuje 25 jazyků a automaticky detekuje jazyk rozpoznaného textu.

## <a name="moderate-content-in-images"></a>Moderování obsahu obrázků

Počítačové vidění můžete použít k [detekci obsahu pro dospělé](concept-detecting-adult-content.md) v obrázku a vrátit skóre spolehlivosti pro různé klasifikace. Prahovou hodnotu pro označování obsahu lze nastavit na posuvné stupnici, aby vyhovovalvašim preferencím.

## <a name="use-containers"></a>Použití kontejnerů

[Pomocí kontejnerů počítačového vidění](computer-vision-how-to-install-containers.md) rozpoznejte tištěný a ručně psaný text místně instalací standardizovaného kontejneru Dockeru blíže k vašim datům.

## <a name="image-requirements"></a>Požadavky image

Počítačové zpracování obrazu dokáže analyzovat obrázky, které splňují následující požadavky:

- Obrázek musí být ve formátu JPEG, PNG, GIF nebo BMP.
- Velikost souboru obrázku musí být menší než 4 megabajty (MB).
- Rozměry obrázku musí být větší než 50 × 50 pixelů.
  - Pro rozhraní API pro čtení musí být rozměry bitové kopie mezi 50 x 50 a 10000 x 10000 pixelů.

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení

Stejně jako u všech služeb Cognitive Services by vývojáři, kteří používají službu Počítačové zpracování obrazu, měli znát zásady společnosti Microsoft týkající se zákaznických dat. Další informace najdete na [stránce Služby Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v Centru zabezpečení společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

Začněte s programem Computer Vision pomocí úvodního průvodce:

- [Úvodní příručka: Klientská knihovna Computer Vision .NET](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [Úvodní příručka: Klientská knihovna Computer Vision Python](./quickstarts-sdk/client-library.md?pivots=programming-language-python)
- [Úvodní příručka: Klientská knihovna Computer Vision Java](./quickstarts-sdk/client-library.md?pivots=programming-language-java)
