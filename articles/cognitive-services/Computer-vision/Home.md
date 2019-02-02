---
title: Co je rozhraní API pro počítačové zpracování obrazu? -Počítačové zpracování obrazu
titlesuffix: Azure Cognitive Services
description: Služba počítačového zpracování obrazu umožňuje vývojářům používat pokročilé algoritmy, které zpracovávají obrázky a vrací informace.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c68e50d02a27097c9fa8a699468ce679162240a1
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561293"
---
# <a name="what-is-computer-vision"></a>Co je počítačové zpracování obrazu?

Služba Azure pro počítačové zpracování obrazu poskytuje vývojářům přístup ke složitým algoritmům tohoto zpracování obrázků a vracející informace. Když chcete analyzovat obrázek, můžete ho nahrát nebo zadat jeho adresu URL. Algoritmy pro zpracování obrázků můžete analyzovat obsah několika různými způsoby v závislosti na vizuální funkce, které vás zajímají. Pro počítačové zpracování obrazu můžete například zjistit, zda bitová kopie obsahuje obsah pro dospělé nebo pikantní nebo ho můžete vyhledat všechny lidské tváře v obrázku.

Počítačové zpracování obrazu ve vaší aplikaci můžete použít buď pomocí nativní sadou SDK nebo přímo vyvoláním rozhraní REST API. Tato stránka široce popisuje, co můžete dělat s pro počítačové zpracování obrazu.

## <a name="analyze-images-for-insight"></a>Díky analýze obrázků za vytváření přehledů

Můžete analýze obrázků můžete rozpoznat a poskytují přehled o jejich vizuální funkce a vlastnosti. Všechny funkce v následující tabulce jsou poskytovány [analyzovat Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) rozhraní API.

| Akce | Popis |
| ------ | ----------- |
|**[Označování vizuálních vlastností](concept-tagging-images.md)**|Identifikovat a označit vizuální funkce do image ze sady tisíce rozpoznatelných objektů, živých věcí, krajin a akcí. Když jsou značky nejednoznačný nebo není odpovědi rozhraní API poskytuje "pomocné parametry pro objasnění významu značky v kontextu známé nastavení. Označování se neomezuje na hlavní předmět, například postavu v popředí, ale zahrnuje také prostředí (interiér nebo exteriér), nábytek, nástroje, rostliny, zvířata, příslušenství, pomůcky atd.|
|**[Zjišťování objektů](concept-object-detection.md)**| Detekce objektů je podobný označování, ale rozhraní API vrací ohraničující pole souřadnic pro jednotlivé značky použít. Například pokud image obsahuje pes, cat a osoby, rozpoznat operace se zobrazí seznam těchto objektů spolu s jejich souřadnice na obrázku. Tato funkce slouží ke zpracování další vztahy mezi objekty v obrázku. Umožňuje taky vědět, že existuje více instancí stejné značky v obraze.|
|**[Zjišťovat značky](concept-brand-detection.md)**|Identifikace obchodních značek na obrázcích nebo videa z databáze tisíc globální loga. Tuto funkci můžete použít třeba ke zjišťování, které značky jsou nejoblíbenější na sociálních sítích nebo nejrozšířenější v umístění média produktu.|
|**[Kategorizace obrázku](concept-categorizing-images.md)**|Identifikuje a kategorizuje celý obrázek s využitím [taxonomie kategorií](Category-Taxonomy.md) s dědičnými hierarchiemi nadřízený/podřízený objekt. Kategorie je možné používat samostatně nebo s našimi novými modely označování.<br/>V současné době je jediným podporovaným jazykem pro označování a kategorizaci obrázků angličtina.|
|**[Popis obrázku](concept-describing-images.md)**|Vygeneruje popis celého obrázku v celých větách v čitelném jazyce. Algoritmy Počítačového zpracování obrazu generují různé popisy v závislosti na objektech identifikovaných na obrázku. Jednotlivé popisy se vyhodnotí a vygeneruje se pro ně skóre spolehlivosti. Pak se vrátí seznam seřazený od nejvyššího skóre spolehlivosti po nejnižší.|
|**[Rozpoznávání tváří](concept-detecting-faces.md)** |Rozpoznává tváře na obrázku a poskytuje informace o jednotlivých rozpoznaných tvářích. Počítačové zpracování obrazu pro každou rozpoznanou tvář vrátí souřadnice, obdélník, pohlaví a věk.<br/>Počítačové zpracování obrazu poskytuje podmnožinu funkcí, které najdete ve službě [Rozpoznávání tváře](/azure/cognitive-services/face/), kterou můžete využít k podrobnější analýze, například identifikaci tváří a rozpoznávání pózy.|
|**[Rozpoznávání typů obrázků](concept-detecting-image-types.md)**|Rozpoznává charakteristiky obrázku, například jestli jde o perokresbu nebo s jakou pravděpodobností je obrázek klipart.|
|**[Rozpoznávání obsahu specifického doménu](concept-detecting-domain-content.md)**|S využitím doménových modelů rozpoznává a identifikuje obsah obrázku specifický pro doménu, například celebrity a památky. Pokud jsou na obrázku například lidé, může Počítačové zpracování obrazu využít doménový model pro celebrity, který je součástí služby, a určit, jestli se lidé rozpoznaní na obrázku shodují se známými celebritami.|
|**[Rozpoznávání barevného schématu](concept-detecting-color-schemes.md)**|Analyzuje použité barvy na obrázku. Počítačové zpracování obrazu dokáže určit, jestli je obrázek černobílý nebo barevný, a u barevných obrázků identifikovat dominantní a doplňkové barvy.|
|**[Vytvoření miniatury](concept-generating-thumbnails.md)**|Analyzuje obsah obrázku a vytvoří pro obrázek odpovídající miniaturu. Počítačové zpracování obrazu nejprve vygeneruje vysoce kvalitní miniaturu a poté analyzuje objekty v obrázku určit *oblast zájmu*. Počítačové zpracování obrazu potom obrázek ořízne tak, aby odpovídal požadavkům oblasti zájmu. Vytvořená miniatura může mít podle vašich potřeb jiný poměr stran než původní obrázek.|
|**[Získat oblasti zájmu](concept-generating-thumbnails.md#area-of-interest)**|Analyzovat obsah tohoto obrázku, který má vrátit souřadnice *oblast zájmu*. To je stejné funkce, která se používá k vytvoření miniatury, ale místo oříznutí obrázku, pro počítačové zpracování obrazu Vrátí souřadnice ohraničujícího pole oblasti, takže původní obrázek podle potřeby můžete upravit volající aplikace.|


## <a name="extract-text-from-images"></a>Rozbalte text z obrázků

Pomocí Počítačového zpracování obrazu můžete z obrázku [extrahovat text s využitím technologie OCR](concept-extracting-text-ocr.md) do strojově čitelného proudu znaků. V případě potřeby technologie OCR opraví otočení rozpoznaného textu ve stupních okolo vodorovné osy obrázku a poskytne souřadnice jednotlivých slov. Technologie OCR podporuje 25 jazyků a automaticky rozpoznává jazyk extrahovaného textu.

Na obrázku můžete také [rozpoznávat tištěný a rukou psaný text](concept-recognizing-text.md). Počítačové zpracování obrazu dokáže rozpoznat a extrahovat tištěný i rukou psaný text z obrázků různých objektů s různými povrchy a pozadími, jako jsou například účtenky, plakáty, vizitky, dopisy a tabule. Rozpoznávání tištěného a rukou psaného textu je ve verzi Preview a jediným podporovaným jazykem je angličtina.  

## <a name="moderate-content-in-images"></a>Moderování obsahu v obrázcích

Pomocí Počítačového zpracování obrazu můžete na obrázku [detekovat nevhodný obsah a obsah pro dospělé](concept-detecting-adult-content.md) díky tomu, že hodnotí, s jakou pravděpodobností daný obrázek obsahuje nevhodný obsah nebo obsah pro dospělé a pro obojí generuje skóre spolehlivosti. Filtr detekce nevhodného obsahu a obsahu pro dospělé je možné podle potřeby nastavit na posuvné stupnici.

## <a name="use-containers"></a>Použití kontejnerů

[Kontejnery pro počítačové zpracování obrazu](computer-vision-how-to-install-containers.md) rozpoznat místně, tisk a rukou psaný text nainstalováním standardizované kontejner Dockeru blíž ke svým datům.

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
