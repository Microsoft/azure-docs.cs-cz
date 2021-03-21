---
title: Rozložení – Nástroj pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s analýzou rozložení pomocí rozhraní API pro rozpoznávání formulářů – využití a omezení.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 73bef21f430bde1c6c2c95d7c3f685cccbbd9179
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467013"
---
# <a name="form-recognizer-layout-service"></a>Služba rozložení pro rozpoznávání formulářů

Nástroj pro rozpoznávání formulářů Azure může extrahovat text, tabulky, značky výběru a informace o struktuře z dokumentů pomocí služby rozvržení. Rozhraní API pro rozložení umožňuje zákazníkům pořizovat dokumenty v nejrůznějších formátech a vracet strukturované datové reprezentace dokumentů. Kombinuje naše výkonné funkce [optického rozpoznávání znaků (OCR)](../computer-vision/concept-recognizing-text.md) s modely hloubkového učení k extrakci textu, tabulek, značek výběru a struktury dokumentů. 

## <a name="what-does-the-layout-service-do"></a>Co služba rozložení dělá?

Rozhraní API pro rozložení extrahuje text, tabulky, značky výběru a informace o struktuře z dokumentů s mimořádnou přesností a vrátí uspořádanou, strukturovaný a odpověď JSON. Dokumenty můžou mít nejrůznější formáty a kvalitu, včetně telefonních snímků, naskenovaných dokumentů a digitálních souborů PDF. Rozhraní API pro rozložení bude přesně extrahovat strukturovaný výstup ze všech těchto dokumentů.

![Příklad rozložení](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Vyzkoušet

Chcete-li vyzkoušet službu rozložení pro rozpoznávání formulářů, použijte nástroj uživatelského rozhraní online Sample:

> [!div class="nextstepaction"]
> [Nástroj pro testování rozpoznávání OCR (FOTT)](https://fott-preview.azurewebsites.net)

K vyzkoušení rozhraní API pro rozložení pro rozpoznávání formulářů budete potřebovat předplatné Azure ([můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services)) a koncový bod a klíč [prostředku pro rozpoznávání](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) formuláře. 

![Ukázka snímku uživatelského rozhraní; analyzují se text, tabulky a značky výběru dokumentu.](./media/analyze-layout.png)

### <a name="input-requirements"></a>Požadavky na vstup 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Operace analyzovat rozložení

Nejdříve zavolejte operaci [Analýza rozložení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) . Analýza rozložení bere v dokumentu (soubor s obrázkem, TIFF nebo PDF) jako vstup a extrahuje text, tabulky, značky výběru a strukturu dokumentu. Volání vrátí pole hlavičky odpovědi s názvem `Operation-Location` . `Operation-Location`Hodnota je adresa URL, která obsahuje ID výsledku, které se má použít v dalším kroku.

|Hlavička odpovědi| Adresa URL výsledku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Výstup přirozeného pořadí čtení (jenom Latin)

Můžete určit pořadí, ve kterém jsou textové řádky ve výstupu s `readingOrder` parametrem dotazu. Použijte `natural` pro více uživatelsky přívětivého výstupu pořadí čtení, jak je znázorněno v následujícím příkladu. Tato funkce je podporována pouze v jazycích Latin.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Příklad pořadí čtení rozložení" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Vybrat čísla stránek nebo rozsahy pro extrakci textu

U rozsáhlých vícestránkových dokumentů použijte `pages` parametr dotazu k označení konkrétního čísla stránky nebo rozsahů stránek pro extrakci textu. Následující příklad ukazuje dokument s 10 stránkami a text extrahovaný pro oba případy – všechny stránky (1-10) a vybrané stránky (3-6).

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Výstup vybrané stránky rozložení":::

## <a name="the-get-analyze-layout-result-operation"></a>Operace získání výsledku rozložení analýzy

Druhým krokem je zavolat operaci [získat výsledky rozložení analýzy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) . Tato operace přijímá jako vstup ID výsledku, které bylo vytvořeno operací analyzovat rozložení. Vrátí odpověď JSON, která obsahuje pole **stav** s následujícími možnými hodnotami. 

|Pole| Typ | Možné hodnoty |
|:-----|:----:|:----|
|status | řetězec | `notStarted`: Operace analýzy nebyla spuštěna.<br /><br />`running`: Probíhá operace analýzy.<br /><br />`failed`: Operace analýzy se nezdařila.<br /><br />`succeeded`: Operace analýzy byla úspěšná.|

Tuto operaci zavolejte iterativní, dokud nevrátí `succeeded` hodnotu. Použijte interval 3 až 5 sekund, abyste předešli překročení sazby za sekundu (RPS).

Pokud má pole **stav** `succeeded` hodnotu, odpověď JSON bude zahrnovat extrahovaná rozložení, text, tabulky a značky výběru. Extrahovaná data obsahují extrahované textové řádky a slova, ohraničovací rámečky, vzhled textu s ručně psaným označením, tabulkami a značkami výběru se označený vybranými nebo nevybranými. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Ručně psaná klasifikace pro textové řádky (jenom latinku)

Odpověď zahrnuje klasifikaci, zda je každý textový řádek ve stylu rukopisu nebo ne, spolu s skóre spolehlivosti. Tato funkce je podporována pouze v jazycích Latin. Následující příklad ukazuje ručně vytvořenou klasifikaci textu v obrázku.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="Příklad klasifikace rukopisu":::

### <a name="sample-json-output"></a>Ukázkový výstup JSON

Odpověď na operaci *získání výsledku rozložení analýzy* je strukturovaná reprezentace dokumentu se všemi extrahovanými informacemi. Podívejte se na [ukázkový soubor dokumentu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) a jeho strukturovaný výstup výstupního [rozložení](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json).

Výstup JSON má dvě části:

* `readResults` uzel obsahuje všechny rozpoznané znaky textu a výběru. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. 
* `pageResults` uzel obsahuje tabulky a buňky extrahované s jejich ohraničujícími poli, spolehlivost a odkaz na řádky a slova v "readResults".

## <a name="example-output"></a>Příklad výstupu

### <a name="text"></a>Text

Rozhraní API pro rozložení extrahuje text z dokumentů (PDF, TIFF) a obrázků (JPG, PNG, BMP) s více textovými úhly a barvami. Přijímá fotky dokumentů, faxů, tištěných a psaných rukou (jenom v angličtině) a v kombinovaných režimech. Text se extrahuje s informacemi, které jsou k dispozici na řádcích, slovech, ohraničujících polích, hodnoceních spolehlivosti a stylu (rukou nebo jinak). Všechny textové informace jsou obsaženy v `readResults` části výstupu JSON. 

### <a name="tables"></a>Tabulky

Rozhraní API pro rozložení extrahuje tabulky z dokumentů (PDF, TIFF) a obrázků (JPG, PNG, BMP). Dokumenty mohou být prohledávány, fotografované nebo s více číslicemi. Tabulky mohou být složité se sloučenými buňkami nebo sloupci, s ohraničením nebo bez ohraničení a s lichými úhly. Informace o extrahované tabulce zahrnují počet sloupců a řádků, rozsah řádků a rozsah sloupců. Každá buňka je extrahována se svým ohraničujícím polem a odkazuje na text extrahovaný v `readResults` části. Informace o tabulce jsou umístěné v `pageResults` části výstupu JSON. 

![Příklad tabulek](./media/tables-example.jpg)

### <a name="selection-marks"></a>Značky výběru

Rozhraní API pro rozložení také extrahuje značky výběru z dokumentů. Mezi extrahované výběrové značky patří ohraničovací rámeček, důvěra a stav (vybraný/nevybraný). Informace o označení výběru jsou extrahovány v `readResults` části výstupu JSON. 

## <a name="next-steps"></a>Další kroky

* Vyzkoušejte si vlastní extrakci rozložení pomocí [Nástroje pro ukázkové uživatelské rozhraní pro rozpoznávání formulářů](https://fott-preview.azurewebsites.net/)
* Dokončete [rychlé zprovoznění pro rozpoznávání formulářů](quickstarts/client-library.md) , které vám pomohou začít s extrakcí rozložení ve vývojovém jazyce podle vašeho výběru.

## <a name="see-also"></a>Viz také

* [Co je služba Rozpoznávání formulářů?](./overview.md)
* [Referenční dokumentace REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
