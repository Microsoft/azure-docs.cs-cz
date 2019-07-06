---
title: Co je rozhraní API pro počítačové zpracování obrazu? -Počítačové zpracování obrazu
titlesuffix: Azure Cognitive Services
description: Služba počítačového zpracování obrazu umožňuje vývojářům používat pokročilé algoritmy, které zpracovávají obrázky a vrací informace.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f92d8ce8e95962558b3bdb7b4a4d8fe70c725f46
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604499"
---
# <a name="what-is-computer-vision"></a>Co je počítačové zpracování obrazu?

Služba Azure pro počítačové zpracování obrazu poskytuje vývojářům přístup ke složitým algoritmům, zpracování obrázků, jež vrací informace. Když chcete analyzovat obrázek, můžete ho nahrát nebo zadat jeho adresu URL. Algoritmy pro zpracování obrázků můžete analyzovat obsah několika různými způsoby v závislosti na vizuální funkce, které vás zajímají. Například pro počítačové zpracování obrazu můžete zjistit, zda obrázek obsahuje obsah pro dospělé nebo pikantního nebo vyhledání všech lidské tváře v obrázku.

Počítačové zpracování obrazu ve vaší aplikaci můžete použít buď pomocí nativní sadou SDK nebo přímo vyvoláním rozhraní REST API. Tato stránka široce popisuje, co můžete dělat s pro počítačové zpracování obrazu.

## <a name="analyze-images-for-insight"></a>Díky analýze obrázků za vytváření přehledů

Můžete analýze obrázků můžete rozpoznat a poskytují přehled o jejich vizuální funkce a vlastnosti. Všechny funkce v následující tabulce jsou poskytovány [analyzovat Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) rozhraní API.

| Akce | Popis |
| ------ | ----------- |
|**[Označování vizuálních vlastností](concept-tagging-images.md)**|Identifikovat a označit vizuální funkce do image ze sady tisíce rozpoznatelných objektů, živých věcí, krajin a akcí. Když jsou značky nejednoznačný nebo není odpovědi rozhraní API poskytuje informace pro objasnění kontextu značky. Označování se neomezuje na hlavní předmět, například postavu v popředí, ale zahrnuje také prostředí (interiér nebo exteriér), nábytek, nástroje, rostliny, zvířata, příslušenství, pomůcky atd.|
|**[Zjišťování objektů](concept-object-detection.md)**| Detekce objektů je podobný označování, ale rozhraní API vrací ohraničující pole souřadnic pro jednotlivé značky použít. Například pokud image obsahuje pes, cat a osoby, rozpoznat operace se zobrazí seznam těchto objektů spolu s jejich souřadnice na obrázku. Tato funkce slouží ke zpracování další vztahy mezi objekty v obrázku. Umožňuje taky vědět, že existuje více instancí stejné značky v obraze.|
|**[Zjišťovat značky](concept-brand-detection.md)**|Identifikace obchodních značek na obrázcích nebo videa z databáze tisíc globální loga. Tuto funkci můžete použít třeba ke zjišťování, které značky jsou nejoblíbenější na sociálních sítích nebo nejrozšířenější v umístění média produktu.|
|**[Kategorizace obrázku](concept-categorizing-images.md)**|Identifikuje a kategorizuje celý obrázek s využitím [taxonomie kategorií](Category-Taxonomy.md) s dědičnými hierarchiemi nadřízený/podřízený objekt. Kategorie je možné používat samostatně nebo s našimi novými modely označování.<br/>V současné době je jediným podporovaným jazykem pro označování a kategorizaci obrázků angličtina.|
|**[Popis obrázku](concept-describing-images.md)**|Vygeneruje popis celého obrázku v celých větách v čitelném jazyce. Algoritmy Počítačového zpracování obrazu generují různé popisy v závislosti na objektech identifikovaných na obrázku. Jednotlivé popisy se vyhodnotí a vygeneruje se pro ně skóre spolehlivosti. Pak se vrátí seznam seřazený od nejvyššího skóre spolehlivosti po nejnižší.|
|**[Rozpoznávání tváří](concept-detecting-faces.md)** |Rozpoznává tváře na obrázku a poskytuje informace o jednotlivých rozpoznaných tvářích. Počítačové zpracování obrazu pro každou rozpoznanou tvář vrátí souřadnice, obdélník, pohlaví a věk.<br/>Počítačové zpracování obrazu poskytuje podmnožinu [pro rozpoznávání tváře](/azure/cognitive-services/face/) služby funkce. Můžete použít službu pro rozpoznávání tváře podrobnější analýzu, jako je rozpoznávání obličeje identifikace a představovat zjišťování.|
|**[Rozpoznávání typů obrázků](concept-detecting-image-types.md)**|Rozpoznává charakteristiky obrázku, například jestli jde o perokresbu nebo s jakou pravděpodobností je obrázek klipart.|
|**[Rozpoznávání obsahu specifického doménu](concept-detecting-domain-content.md)**|S využitím doménových modelů rozpoznává a identifikuje obsah obrázku specifický pro doménu, například celebrity a památky. Například pokud image obsahuje lidé, počítačové zpracování obrazu můžete doménový model pro celebrit zjistí, zda jsou lidé zjistil v bitové kopii známý celebrit.|
|**[Rozpoznávání barevného schématu](concept-detecting-color-schemes.md)**|Analyzuje použité barvy na obrázku. Počítačové zpracování obrazu dokáže určit, jestli je obrázek černobílý nebo barevný, a u barevných obrázků identifikovat dominantní a doplňkové barvy.|
|**[Vytvoření miniatury](concept-generating-thumbnails.md)**|Analyzuje obsah obrázku a vytvoří pro obrázek odpovídající miniaturu. Počítačové zpracování obrazu nejprve vygeneruje vysoce kvalitní miniaturu a poté analyzuje objekty v obrázku určit *oblast zájmu*. Počítačové zpracování obrazu potom obrázek ořízne tak, aby odpovídal požadavkům oblasti zájmu. Vytvořená miniatura může mít podle vašich potřeb jiný poměr stran než původní obrázek.|
|**[Získat oblasti zájmu](concept-generating-thumbnails.md#area-of-interest)**|Analyzovat obsah tohoto obrázku, který má vrátit souřadnice *oblast zájmu*. Místo oříznutí obrázku a generování miniatury, vrátí pro počítačové zpracování obrazu souřadnice ohraničujícího pole oblasti, takže původní obrázek podle potřeby můžete upravit volající aplikace.|

## <a name="extract-text-from-images"></a>Rozbalte text z obrázků

Můžete použít pro počítačové zpracování obrazu [rozhraní API pro čtení](concept-recognizing-text.md#read-api) pro extrakci tištěné a rukou psaný text z obrázků do datového proudu Strojově čitelný znaků. Rozhraní API pro čtení používá naše nejnovější modely a pracuje s textem na širokou škálu povrchy a pozadími, jako je například potvrzení, plakáty, vizitek, písmena samolepicí bločky nebo Tabule. V současné době je jediný podporovaný jazyk je angličtina.

Můžete také použít [optické rozpoznávání znaků (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) rozhraní API k extrakci tisk textu v několika jazycích. V případě potřeby OCR opravuje otočení rozpoznaný text a poskytuje souřadnice rámec jednotlivých slov. OCR podporuje 25 jazycích a jazyk textové rozpoznaných se detekuje automaticky.



## <a name="moderate-content-in-images"></a>Moderování obsahu v obrázcích

Můžete použít pro počítačové zpracování obrazu k [detekci obsahu pro dospělé nebo pikantního obsahu](concept-detecting-adult-content.md) v image a vrátit skóre spolehlivosti pro obojí. Nastavte filtr pro zjišťování obsahu pro dospělé nebo pikantního na posuvné stupnice pro plnění vašich předvolbách.

## <a name="use-containers"></a>Použití kontejnerů

[Kontejnery pro počítačové zpracování obrazu](computer-vision-how-to-install-containers.md) rozpoznat tištěné a rukou psaný text místně nainstalováním standardizované kontejner Dockeru blíž ke svým datům.

## <a name="image-requirements"></a>Požadavky image

Počítačové zpracování obrazu dokáže analyzovat obrázky, které splňují následující požadavky:

- Obrázek musí být ve formátu JPEG, PNG, GIF nebo BMP.
- Velikost souboru obrázku musí být menší než 4 megabajty (MB).
- Rozměry obrázku musí být větší než 50 × 50 pixelů.
  - Pro účely OCR musí mít vstupní obrázek rozměry 50 × 50 až 4200 × 4200 pixelů.

## <a name="data-privacy-and-security"></a>Ochrany osobních údajů a zabezpečení

Jako se všemi služeb Cognitive Services, vývojáře, kteří používají službu pro počítačové zpracování obrazu měli vědět zásady společnosti Microsoft na zákaznická data. Zobrazit [stránku služeb Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na webu Microsoft Trust Center další informace.

## <a name="next-steps"></a>Další postup

Začínáme s pro počítačové zpracování obrazu podle příručky rychlý start:

- [Rychlé zprovoznění: Analýza obrázku](quickstarts-sdk/csharp-analyze-sdk.md)
- [Rychlé zprovoznění: Extrahujte rukou psaný text](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Rychlé zprovoznění: Vytvoření miniatury](quickstarts-sdk/csharp-thumb-sdk.md)
