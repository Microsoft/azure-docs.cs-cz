---
title: Rozpoznání tisk/rukou textu pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s uznání tištěné a rukou psaný text v obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bcaa990cc2186a5f1eecdbbca91804c92370277c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66357188"
---
# <a name="recognize-printed-and-handwritten-text"></a>Rozpoznávání tištěného a ručně psaného textu

Počítačové zpracování obrazu poskytuje několik služeb, které detekovat a extrahovat tištěné nebo rukou psaný text, který se zobrazí v obrázcích. To je užitečné v širokou škálu scénářů, jako je poznámek, zdravotnickými záznamy, zabezpečení a bankovnictví. Následující tři části Podrobnosti o třech různých rozpoznávání textu rozhraní API, každý optimalizovaná pro použití v odlišných situacích.

## <a name="read-api"></a>Rozhraní API pro čtení

Rozhraní API pro čtení detekuje textový obsah v obrázku pomocí naší nejnovější modely pro rozpoznávání a převede zjištěný text do datového proudu Strojově čitelný znaků. Je optimalizovaný pro Image hodně textu (jako jsou dokumenty, které už je zkontrolovaných digitálně) a pro Image s velkým množstvím visual šumu. Určuje, které model rozpoznávání pro každý řádek textu, podporu imagí pomocí tištěné a rukou psaný text. Rozhraní API pro čtení provádí asynchronně, protože větších může trvat několik minut, než vrácení výsledku.

Operace čtení se řídí původní řádek seskupení rozpoznaná slova do výstupu. Každý řádek obsahuje pole souřadnice ohraničujícího a jednotlivých slov v rámci řádku má také svůj vlastní souřadnice. Pokud u slov velká bylo rozpoznáno s nízkou spolehlivostí, tyto informace předávají také. Zobrazit [referenční dokumentace rozhraní API pro čtení](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) Další informace.

> [!NOTE]
> Tato funkce dostupná jenom pro anglický text.

### <a name="image-requirements"></a>Požadavky image

Rozhraní API pro čtení funguje s imagí, které splňují následující požadavky:

- Obrázek musí uvedené ve formátu JPEG, PNG, BMP, PDF nebo ve formátu TIFF.
- Rozměry obrázku musí být až 50 × 50 pixelů 4200 x 4200. PDF stránky musí být 17 x 17 palců nebo menší.
- Velikost souboru obrázku musí být menší než 20 megabajtů (MB).

### <a name="limitations"></a>Omezení

Pokud používáte předplatné bezplatné vrstvy, rozhraní API pro čtení zpracuje pouze prvních dvou stránkách dokumentu PDF nebo ve formátu TIFF. S placeným předplatným zpracuje až 200 stránek. Všimněte si také, že rozhraní API zjistí maximálně 300 řádků na stránce.

## <a name="ocr-optical-character-recognition-api"></a>OCR (optické rozpoznávání znaků) rozhraní API

Rozhraní API pro počítačové zpracování obrazu optické rozpoznávání znaků (OCR) je podobný rozhraní API pro čtení, ale je provedena synchronně a není optimalizovaná pro velké dokumenty. Používá starší model rozpoznávání ale pracuje s více jazyky; Zobrazit [jazykovou podporu](language-support.md#text-recognition) úplný seznam podporovaných jazyků.

V případě potřeby OCR opravuje otočení rozpoznaný text tak, že vrací rotačních posun ve stupních, o osy vodorovný obrázek. OCR také poskytuje souřadnice rámec jednotlivých slov, jak je znázorněno na následujícím obrázku.

![Obrázek se otočí a jeho textu se čtení a vymezeny](./Images/vision-overview-ocr.png)

Zobrazit [OCR referenční dokumenty](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) Další informace.

### <a name="image-requirements"></a>Požadavky image

Rozhraní API technologie OCR funguje na imagích, které splňují následující požadavky:

* Obrázek musí uvedené ve formátu JPEG, PNG, BMP nebo GIF.
* Velikost vstupního obrázku musí být až 50 × 50 pixelů 4200 x 4200.
* Text v obrázku můžete otočit o žádné nadbytečné 90 stupňů plus malé úhel až 40 stupňů.

### <a name="limitations"></a>Omezení

Na fotografie, kde je dominantní textu počet falešně pozitivních výsledků mohou pocházet z částečně rozpoznaná slova. Na některé fotografie, zejména fotografie bez jakýkoli text, přesnosti se může lišit v závislosti na typu image.

## <a name="recognize-text-api"></a>Rozpoznání textu rozhraní API

> [!NOTE]
> Rozhraní API pro rozpoznávání textu je zastaralé a místo toho použití rozhraní API pro čtení. Rozhraní API pro čtení má podobné možnosti a aktualizaci PDF, TIFF a více stránkovací soubory.

Rozhraní API pro rozpoznávání textu je podobný OCR, ale provádí asynchronně a používá modely pro rozpoznávání aktualizované. Zobrazit [referenční dokumentace rozhraní API pro rozpoznávání textu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) Další informace.

### <a name="image-requirements"></a>Požadavky image

Rozhraní API pro rozpoznávání textu pracuje s imagí, které splňují následující požadavky:

- Obrázek musí uvedené ve formátu JPEG, PNG nebo BMP.
- Rozměry obrázku musí být až 50 × 50 pixelů 4200 x 4200.
- Velikost souboru obrázku musí být menší než 4 megabajtů (MB).

## <a name="limitations"></a>Omezení

Přesnost operace s textem rozpoznávání závisí na kvalitu bitové kopie. Tyto faktory mohou způsobit nesprávné čtení:

* rozostřené obrázky;
* rukopis nebo text psaný kurzívou;
* umělecké styly písma;
* malé písmo;
* složité pozadí, stíny nebo odlesky vržené na text nebo narušená perspektiva;
* Příliš velký nebo chybějící velká písmena na začátku slova.
* dolní index, horní index nebo přeškrtnutý text.

## <a name="next-steps"></a>Další postup

Postupujte podle [extrahovat tištěný text (OCR)](./quickstarts/csharp-print-text.md) rychlý start k provádění rozpoznávání textu v jednoduché C# aplikace.
