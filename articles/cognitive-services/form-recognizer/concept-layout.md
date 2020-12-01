---
title: Rozložení – Nástroj pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s analýzou rozložení pomocí rozhraní API pro rozpoznávání formulářů – využití a omezení.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 60ef490bbba4dca4f01b82967ad6ea8636e7bd4e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353693"
---
# <a name="form-recognizer-layout-service"></a>Služba rozložení pro rozpoznávání formulářů

Nástroj pro rozpoznávání formulářů Azure může extrahovat text, tabulky, značky výběru a informace o struktuře z dokumentů pomocí služby rozvržení. Rozhraní API pro rozložení umožňuje zákazníkům pořizovat dokumenty v nejrůznějších formátech a vracet strukturovaná data a reprezentace dokumentu. Kombinuje naše výkonné funkce [optického rozpoznávání znaků (OCR)](../computer-vision/concept-recognizing-text.md) s dokumentem, který popisuje modely hloubkového učení pro extrakci textu, tabulek, značek výběru a struktury dokumentů. 

## <a name="what-does-the-layout-service-do"></a>Co služba rozložení dělá?

Rozhraní API pro rozložení extrahuje text, tabulky, značky výběru a informace o struktuře z dokumentů s mimořádnou přesností a vrátí je v organizované strukturované odpovědi JSON. Dokumenty můžou být z nejrůznějších formátů a kvality, včetně telefonicky zachycených obrázků, naskenovaných dokumentů a digitálních souborů PDF. Rozhraní API pro rozložení extrahuje strukturovaný výstup ze všech těchto dokumentů.

![Příklad rozložení](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Vyzkoušet

Chcete-li vyzkoušet službu rozložení pro rozpoznávání formulářů, použijte nástroj uživatelského rozhraní online Sample:

> [!div class="nextstepaction"]
> [Ukázka uživatelského rozhraní](https://fott-preview.azurewebsites.net/)

K vyzkoušení rozhraní API pro rozložení pro rozpoznávání formulářů budete potřebovat předplatné Azure ([můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services)) a koncový bod a klíč [prostředku pro rozpoznávání](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) formuláře. 

![Ukázka snímku uživatelského rozhraní; analyzují se text, tabulky a značky výběru dokumentu.](./media/analyze-layout.png)

### <a name="input-requirements"></a>Požadavky na vstup 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Operace analyzovat rozložení

Operace [analyzovat rozložení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) bere dokument (soubor s obrázkem, TIFF nebo PDF) jako vstup a extrahuje text, tabulky, značky výběru a strukturu dokumentu. Volání vrátí pole hlavičky odpovědi s názvem `Operation-Location` . `Operation-Location`Hodnota je adresa URL, která obsahuje ID výsledku, které se má použít v dalším kroku.

|Hlavička odpovědi| Adresa URL výsledku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>Operace získání výsledku rozložení analýzy

Druhým krokem je zavolat operaci [získat výsledky rozložení analýzy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) . Tato operace přijímá jako vstup ID výsledku, které bylo vytvořeno operací analyzovat rozložení. Vrátí odpověď JSON, která obsahuje pole **stav** s následujícími možnými hodnotami. 

|Pole| Typ | Možné hodnoty |
|:-----|:----:|:----|
|status | řetězec | `notStarted`: Operace analýzy nebyla spuštěna.<br /><br />`running`: Probíhá operace analýzy.<br /><br />`failed`: Operace analýzy se nezdařila.<br /><br />`succeeded`: Operace analýzy byla úspěšná.|

Tuto operaci zavoláte iterativní, dokud se nevrátí `succeeded` hodnotou. Použijte interval 3 až 5 sekund, abyste předešli překročení sazby za sekundu (RPS).

Když pole **status** má `succeeded` hodnotu, odpověď JSON bude obsahovat výsledky extrakce rozložení, text, tabulky a vyextrahované značky výběru. Extrahovaná data obsahují extrahované textové řádky a slova, ohraničovací rámeček, vzhled textu psaný rukou, tabulky a značky výběru s označením vybrané/nevybrané. 

### <a name="sample-json-output"></a>Ukázkový výstup JSON

Odpověď na operaci získání výsledku analýzy rozložení bude strukturovaná reprezentace dokumentu se všemi extrahovanými informacemi. Podívejte se na [ukázkový soubor dokumentu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) a jeho strukturovaný výstup výstupního [rozložení](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json).

Výstup JSON má dvě části: 
* `"readResults"` uzel obsahuje všechny rozpoznané znaky textu a výběru. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. 
* `"pageResults"` uzel obsahuje tabulky a buňky extrahované s jejich ohraničujícími poli, spolehlivost a odkaz na řádky a slova v "readResults".

## <a name="example-output"></a>Příklad výstupu

### <a name="text"></a>Text

Rozložení extrahuje text z dokumentů (PDF, TIFF) a obrázků (jpg, PNG, BMP) s různými úhly textu, barvami, úhly, fotografiemi dokumentů, faxů, tištěnými, ručně psanými (jenom v angličtině) a kombinovanými režimy. Text se extrahuje informacemi na řádcích, slovech, ohraničovacích polích, hodnoceních spolehlivosti a stylem (rukou nebo jiným). Všechny textové informace jsou obsaženy v `"readResults"` části výstupu JSON. 

### <a name="tables"></a>Tabulky

Rozložení extrahuje tabulky z dokumentů (PDF, TIFF) a obrázků (jpg, PNG, BMP). Dokumenty mohou být prohledávány, fotografované nebo s více číslicemi. Tabulky mohou být složité tabulky se sloučenými buňkami nebo sloupci, s ohraničením nebo bez ohraničení a s lichými úhly. Extrahované tabulky zahrnují počet sloupců a řádků, rozsah řádků a rozsah sloupců. Každá buňka je extrahována se svým ohraničujícím polem a odkazuje na text extrahovaný v `"readResults"` části. Informace o tabulce jsou umístěné v `"pageResults"` části výstupu JSON. 

![Příklad tabulek](./media/tables-example.jpg)

### <a name="selection-marks"></a>Značky výběru

Rozložení také extrahuje značky výběru z dokumentů. Extrahované značky výběru zahrnují ohraničovací rámeček, spolehlivost a stav (vybraný/nevybraný). Informace o označení výběru jsou extrahovány v `"readResults"` části výstupu JSON. 

## <a name="next-steps"></a>Další kroky

- Vyzkoušejte si vlastní extrakci rozložení pomocí [ukázkového uživatelského rozhraní pro rozpoznávání formulářů](https://fott-preview.azurewebsites.net/)
- Dokončete [rychlý Start klientské knihovny pro rozpoznávání formulářů](quickstarts/client-library.md) a začněte s extrakcí rozložení v jazyce podle vašeho výběru.
- Nebo použijte rychlý Úvod k extrakci [dat rozložení](./QuickStarts/python-layout.md) k implementaci extrakce dat rozložení pomocí Pythonu a REST API.

## <a name="see-also"></a>Viz také

* [Co je služba Rozpoznávání formulářů?](./overview.md)
* [Referenční dokumentace REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)