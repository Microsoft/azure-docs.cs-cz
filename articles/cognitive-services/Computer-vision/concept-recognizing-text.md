---
title: Tištěné, ručně psané rozpoznávání textu - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Koncepty související s rozpoznáváním tištěného a ručně psaného textu v obrázcích pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220157"
---
# <a name="recognize-printed-and-handwritten-text"></a>Rozpoznávání tištěného a ručně psaného textu

Aplikace Computer Vision poskytuje řadu služeb, které detekují a extrahují tištěný nebo ručně psaný text, který se zobrazuje v obrázcích. To je užitečné v různých scénářích, jako je pořizování poznámek, lékařské záznamy, zabezpečení a bankovnictví. V následujících třech částech jsou podrobně popsána tři různá nastavení API pro rozpoznávání textu, z nichž každá je optimalizována pro různé případy použití.

## <a name="read-api"></a>Čtení API

Rozhraní Read API detekuje textový obsah v obraze pomocí našich nejnovějších modelů rozpoznávání a převede identifikovaný text na strojově čitelný datový proud znaků. Je optimalizován pro obrázky náročné na text (například dokumenty, které byly digitálně naskenovány) a pro obrazy s velkým množstvím vizuálního šumu. Určí, který model rozpoznávání se má použít pro každý řádek textu a bude podporovat obrázky vytištěným i ručně psaným textem. Rozhraní Read API se spustí asynchronně, protože větší dokumenty může trvat několik minut vrátit výsledek.

Operace čtení udržuje původní seskupení řádků rozpoznaných slov ve výstupu. Každý řádek je dodáván s ohraničovacími souřadnicemi a každé slovo v řádku má také své vlastní souřadnice. Pokud slovo bylo uznáno s nízkou důvěrou, že informace jsou přenášeny také. Další informace najdete v [dokumentech pro odkazy na rozhraní READ API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) a v [referenčních dokumentech pro náhled rozhraní API pro čtení.](https://go.microsoft.com/fwlink/?linkid=2118322)

> [!NOTE]
> Tato funkce je k dispozici pouze pro anglický a španělský text (náhled).

### <a name="image-requirements"></a>Požadavky image

Rozhraní Read API funguje s obrázky, které splňují následující požadavky:

- Obraz musí být zobrazen ve formátu JPEG, PNG, BMP, PDF nebo TIFF.
- Rozměry obrazu musí být mezi 50 x 50 a 10000 x 10000 pixelů. Stránky PDF musí být 17 x 17 palců nebo menší.
- Velikost souboru obrázku musí být menší než 20 MEGABAJTŮ (MB).

### <a name="limitations"></a>Omezení

Pokud používáte odběr volného stupně, rozhraní READ API zpracuje pouze první dvě stránky dokumentu PDF nebo TIFF. S placeným předplatným zpracuje až 200 stránek. Všimněte si také, že rozhraní API detekuje maximálně 300 řádků na stránku.

## <a name="ocr-optical-character-recognition-api"></a>OCR (optické rozpoznávání znaků) API

Rozhraní API pro optické rozpoznávání znaků (OCR) počítačového vidění je podobné rozhraní API pro čtení, ale provádí se synchronně a není optimalizováno pro velké dokumenty. Používá dřívější model rozpoznávání, ale pracuje s více jazyky; úplný seznam podporovaných jazyků naleznete v tématu [Jazyková podpora.](language-support.md#text-recognition)

V případě potřeby opraví rozpoznávání ochlazování rozpoznaného textu vrácením rotačního posunu ve stupních kolem vodorovné osy obrazu. OCR také poskytuje souřadnice snímků každého slova, jak je znázorněno na následujícím obrázku.

![Otočený obraz a jeho text je čten a vymezen](./Images/vision-overview-ocr.png)

Další informace najdete v [referenčních dokumentech OCR.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)

### <a name="image-requirements"></a>Požadavky image

Rozhraní OCR API funguje na bitových kopiích, které splňují následující požadavky:

* Obrázek musí být zobrazen ve formátu JPEG, PNG, GIF nebo BMP.
* Velikost vstupního obrazu musí být mezi 50 x 50 a 4200 x 4200 pixelů.
* Text v obraze lze otáčet libovolným násobkem 90 stupňů plus malým úhlem až 40 stupňů.

### <a name="limitations"></a>Omezení

Na fotografiích, kde je dominantní text, mohou falešně pozitivní výsledky pocházet z částečně rozpoznaných slov. U některých fotografií, zejména fotografií bez textu, se přesnost může lišit v závislosti na typu obrázku.

## <a name="recognize-text-api"></a>Rozpoznat textové rozhraní API

> [!NOTE]
> Rozpoznat rozhraní API textu je zastaralá ve prospěch rozhraní API pro čtení. Rozhraní Read API má podobné funkce a je aktualizováno tak, aby zpracovávalo soubory PDF, TIFF a vícestránkové.

Rozhraní Recognize Text API je podobné rozpoznávání OCR, ale provádí asynchronně a používá aktualizované modely rozpoznávání. Další informace najdete v [tématu Rozpoznávání referenčních dokumentů rozhraní API textu.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)

### <a name="image-requirements"></a>Požadavky image

Rozhraní Recognize Text API funguje s obrázky, které splňují následující požadavky:

- Obrázek musí být zobrazen ve formátu JPEG, PNG nebo BMP.
- Rozměry obrazu musí být mezi 50 x 50 a 4200 x 4200 pixelů.
- Velikost souboru obrázku musí být menší než 4 MEGABAJTy (MB).

## <a name="limitations"></a>Omezení

Přesnost operací rozpoznávání textu závisí na kvalitě obrázků. Následující faktory mohou způsobit nepřesné čtení:

* rozostřené obrázky;
* rukopis nebo text psaný kurzívou;
* umělecké styly písma;
* malé písmo;
* složité pozadí, stíny nebo odlesky vržené na text nebo narušená perspektiva;
* Nadrozměrná nebo chybějící velká písmena na začátku slov.
* dolní index, horní index nebo přeškrtnutý text.

## <a name="next-steps"></a>Další kroky

Postupujte podle [extrahovat text (Read)](./QuickStarts/CSharp-hand-text.md) rychlý start implementovat rozpoznávání textu v jednoduché aplikaci C#.
