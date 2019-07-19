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
ms.openlocfilehash: 523c839ad274b8234a79a89ec45be9a9a4551bf5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312047"
---
# <a name="what-is-computer-vision"></a>Co je počítačové zpracování obrazu?

Služba Počítačové zpracování obrazu v Azure poskytuje vývojářům přístup k pokročilým algoritmům, které zpracovávají obrázky a vracejí informace. Když chcete analyzovat obrázek, můžete ho nahrát nebo zadat jeho adresu URL. Algoritmy pro zpracování obrázků mohou analyzovat obsah několika různými způsoby, v závislosti na vizuálních funkcích, které vás zajímají. Počítačové zpracování obrazu například může určit, jestli obrázek obsahuje obsah pro dospělé nebo pikantní, nebo najít všechny lidské obličeje v obrázku.

Počítačové zpracování obrazu můžete v aplikaci použít buď pomocí nativní sady SDK, nebo vyvoláním REST API přímo. Tato stránka obsahuje širokou škálu toho, co můžete s Počítačové zpracování obrazu provádět.

## <a name="analyze-images-for-insight"></a>Analýza imagí pro přehled

Můžete analyzovat obrázky, abyste zjistili a poskytovali přehled o jejich vizuálních funkcích a vlastnostech. Všechny funkce v tabulce níže jsou poskytovány rozhraním API pro [analýzu imagí](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .

| Action | Popis |
| ------ | ----------- |
|**[Označování vizuálních vlastností](concept-tagging-images.md)**|Identifikujte a označte vizuální funkce na obrázku ze sady tisíců rozpoznatelných objektů, živých věcí, krajin a akcí. Pokud jsou značky dvojznačné nebo nejsou běžné znalosti, poskytuje odpověď rozhraní API nápovědu k objasnění kontextu značky. Označování se neomezuje na hlavní předmět, například postavu v popředí, ale zahrnuje také prostředí (interiér nebo exteriér), nábytek, nástroje, rostliny, zvířata, příslušenství, pomůcky atd.|
|**[Detekovat objekty](concept-object-detection.md)**| Detekce objektu je podobná označování, ale rozhraní API vrací souřadnice ohraničujícího pole pro každou použitou značku. Pokud například obrázek obsahuje pes, Cat a osobu, operace zjišťování zobrazí tyto objekty spolu s jejich souřadnicemi v obrázku. Tuto funkci můžete použít ke zpracování dalších vztahů mezi objekty v imagi. Také vám umožní zjistit, že je v obrázku více instancí stejné značky.|
|**[Detekovat značky](concept-brand-detection.md)**|Identifikujte komerční značky na obrázcích nebo videích z databáze tisíců globálních log. Tuto funkci můžete použít například k tomu, abyste zjistili, které značky jsou nejoblíbenější na sociálních médiích nebo ve většině druhů v mediálním umístění.|
|**[Kategorizace obrázku](concept-categorizing-images.md)**|Identifikuje a kategorizuje celý obrázek s využitím [taxonomie kategorií](Category-Taxonomy.md) s dědičnými hierarchiemi nadřízený/podřízený objekt. Kategorie je možné používat samostatně nebo s našimi novými modely označování.<br/>V současné době je jediným podporovaným jazykem pro označování a kategorizaci obrázků angličtina.|
|**[Popis obrázku](concept-describing-images.md)**|Vygeneruje popis celého obrázku v celých větách v čitelném jazyce. Algoritmy Počítačového zpracování obrazu generují různé popisy v závislosti na objektech identifikovaných na obrázku. Jednotlivé popisy se vyhodnotí a vygeneruje se pro ně skóre spolehlivosti. Pak se vrátí seznam seřazený od nejvyššího skóre spolehlivosti po nejnižší.|
|**[Rozpoznávání tváří](concept-detecting-faces.md)** |Rozpoznává tváře na obrázku a poskytuje informace o jednotlivých rozpoznaných tvářích. Počítačové zpracování obrazu pro každou rozpoznanou tvář vrátí souřadnice, obdélník, pohlaví a věk.<br/>Počítačové zpracování obrazu poskytuje podmnožinu funkcí služby [obličeje](/azure/cognitive-services/face/) . Službu obličeje můžete použít pro podrobnější analýzu, jako je například identifikace obličeje a detekce pozice.|
|**[Rozpoznávání typů obrázků](concept-detecting-image-types.md)**|Rozpoznává charakteristiky obrázku, například jestli jde o perokresbu nebo s jakou pravděpodobností je obrázek klipart.|
|**[Rozpoznávání obsahu specifického doménu](concept-detecting-domain-content.md)**|S využitím doménových modelů rozpoznává a identifikuje obsah obrázku specifický pro doménu, například celebrity a památky. Pokud například obrázek obsahuje lidi, Počítačové zpracování obrazu může použít doménový model pro celebrit k určení, jestli se lidem zjištěné v imagi říká celebrit.|
|**[Rozpoznávání barevného schématu](concept-detecting-color-schemes.md)**|Analyzuje použité barvy na obrázku. Počítačové zpracování obrazu dokáže určit, jestli je obrázek černobílý nebo barevný, a u barevných obrázků identifikovat dominantní a doplňkové barvy.|
|**[Vytvoření miniatury](concept-generating-thumbnails.md)**|Analyzuje obsah obrázku a vytvoří pro obrázek odpovídající miniaturu. Počítačové zpracování obrazu nejprve vygeneruje miniaturu s vysokou kvalitou a následně analyzuje objekty v rámci obrázku a určí *oblast zájmu*. Počítačové zpracování obrazu potom obrázek ořízne tak, aby odpovídal požadavkům oblasti zájmu. Vytvořená miniatura může mít podle vašich potřeb jiný poměr stran než původní obrázek.|
|**[Získat oblast zájmu](concept-generating-thumbnails.md#area-of-interest)**|Analyzujte obsah obrázku a vraťte tak souřadnice *oblasti zájmu*. Místo oříznutí obrázku a vygenerování miniatury Počítačové zpracování obrazu vrátí souřadnice ohraničujícího pole oblasti, aby volající aplikace mohl původní obrázek upravit podle potřeby.|

## <a name="extract-text-from-images"></a>Extrakce textu z obrázků

Pomocí [rozhraní počítačové zpracování obrazu Read API](concept-recognizing-text.md#read-api) můžete extrahovat vytištěné a ručně psaný text z obrázků do datového proudu znaků, který je strojově čitelný. Rozhraní API pro čtení používá naše nejnovější modely a pracuje s textem na nejrůznějších površích a na pozadí, jako jsou například příjmy, plakáty, obchodní karty, dopisy a tabule. V současné době je v současnosti jediným podporovaným jazykem angličtina.

Můžete také použít rozhraní API [optického rozpoznávání znaků (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) k extrakci vytištěného textu v několika jazycích. V případě potřeby optické rozpoznávání znaků opraví natočení rozpoznaného textu a poskytuje souřadnice rámce každého slova. Optické rozpoznávání znaků podporuje 25 jazyků a automaticky detekuje jazyk rozpoznaného textu.



## <a name="moderate-content-in-images"></a>Střední obsah v obrázcích

Počítačové zpracování obrazu můžete použít k [detekci obsahu pro dospělé a pikantní](concept-detecting-adult-content.md) v imagi a vracet hodnocení spolehlivosti pro obojí. Filtr pro pikantní pro dospělé a rozpoznávání obsahu můžete nastavit na klouzavé škále tak, aby vyhovoval vašim potřebám.

## <a name="use-containers"></a>Použití kontejnerů

[Pomocí počítačové zpracování obrazu kontejnerů](computer-vision-how-to-install-containers.md) můžete místně rozpoznávat vytištěný a ručně psaný text tím, že nainstalujete standardizovaný kontejner Docker blíž k vašim datům.

## <a name="image-requirements"></a>Požadavky image

Počítačové zpracování obrazu dokáže analyzovat obrázky, které splňují následující požadavky:

- Obrázek musí být ve formátu JPEG, PNG, GIF nebo BMP.
- Velikost souboru obrázku musí být menší než 4 megabajty (MB).
- Rozměry obrázku musí být větší než 50 × 50 pixelů.
  - Pro rozhraní API pro čtení musí být rozměry obrázku mezi 50 x 50 a 10000 × 10000 pixelů.

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech Cognitive Services by měli vývojáři, kteří používají Počítačové zpracování obrazu službu, znát zásady Microsoftu pro zákaznická data. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Začněte s Počítačové zpracování obrazu pomocí příručky pro rychlý Start:

- [Rychlé zprovoznění: Analýza obrázku](quickstarts-sdk/csharp-analyze-sdk.md)
- [Rychlé zprovoznění: Extrahování ručně psaného textu](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Rychlé zprovoznění: Vygenerovat miniaturu](quickstarts-sdk/csharp-thumb-sdk.md)
