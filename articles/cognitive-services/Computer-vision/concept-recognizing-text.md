---
title: Vytištěno, rozpoznávání rukopisu textu-Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s rozpoznáváním vytištěného a rukopisného textu v obrázcích pomocí rozhraní API pro počítačové zpracování obrazu.
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
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566246"
---
# <a name="recognize-printed-and-handwritten-text"></a>Rozpoznávání tištěného a ručně psaného textu

Počítačové zpracování obrazu poskytuje několik služeb, které zjišťují a extrahují vytištěný nebo psaný text, který se zobrazuje na obrázcích. To je užitečné v nejrůznějších scénářích, jako jsou třeba pořizování, lékařské záznamy, zabezpečení a bankovnictví. Následující tři části obsahují podrobnosti o třech různých rozhraních API pro rozpoznávání textu, která jsou optimalizována pro různé případy použití.

## <a name="read-api"></a>Rozhraní API pro čtení

Rozhraní API pro čtení detekuje textový obsah v imagi pomocí našich nejnovějších modelů rozpoznávání a převede identifikovaný text na datový proud znaků, který je strojově čitelný. Je optimalizovaná pro textově náročné obrázky (například digitálně naskenované dokumenty) a pro image s velkým objemem vizuálního hluku. Určí, který model rozpoznávání se má použít pro jednotlivé řádky textu, a podporuje obrázky s tištěným i psaným textem. Rozhraní API pro čtení se spouští asynchronně, protože může trvat několik minut, než se výsledek vrátí.

Operace čtení zachovává původní řádek seskupení rozpoznaných slov ve výstupu. Každý řádek obsahuje souřadnice ohraničujícího pole a každé slovo v řádku má také vlastní souřadnice. Pokud bylo slovo rozpoznáno s nízkou jistotou, budou tyto informace také předány. Další informace najdete v tématu Referenční dokumentace k [rozhraní Read API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) a [Read API Preview](https://go.microsoft.com/fwlink/?linkid=2118322) .

> [!NOTE]
> Tato funkce je k dispozici pouze pro text v angličtině a španělštině (ve verzi Preview).

### <a name="image-requirements"></a>Požadavky image

Rozhraní API pro čtení funguje s imagemi, které splňují následující požadavky:

- Obrázek musí být uvedený ve formátu JPEG, PNG, BMP, PDF nebo TIFF.
- Rozměry obrázku musí být mezi 50 × 50 a 10000 × 10000 pixelů. Stránky PDF musí být 17 × 17 palců nebo menší.
- Velikost souboru obrázku musí být menší než 20 megabajtů (MB).

### <a name="limitations"></a>Omezení

Pokud používáte předplatné na bezplatné úrovni, rozhraní API pro čtení zpracuje jenom první dvě stránky dokumentu PDF nebo TIFF. U placeného předplatného se zpracuje až 200 stránek. Všimněte si také, že rozhraní API detekuje maximálně 300 řádků na stránku.

## <a name="ocr-optical-character-recognition-api"></a>Rozhraní API pro optické rozpoznávání znaků (OCR)

Rozhraní API pro optické rozpoznávání znaků (OCR) Počítačové zpracování obrazu je podobné rozhraní API pro čtení, ale provádí se synchronně a není optimalizované pro velké dokumenty. Používá starší model rozpoznávání, ale funguje s více jazyky; Úplný seznam podporovaných jazyků najdete v části [jazyková podpora](language-support.md#text-recognition) .

V případě potřeby optické rozpoznávání znaků vyhodnotí otočení rozpoznaného textu tím, že vrátí posun otáčení ve stupních o vodorovné ose obrázků. Optické rozpoznávání znaků také poskytuje souřadnice snímků každého slova, jak je vidět na následujícím obrázku.

![Otočení obrázku a jeho přečtení a vymezený text](./Images/vision-overview-ocr.png)

Další informace najdete v [referenčních dokumentaci OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) .

### <a name="image-requirements"></a>Požadavky image

Rozhraní API pro rozpoznávání OCR funguje na obrázcích, které splňují následující požadavky:

* Obrázek musí být uvedený ve formátu JPEG, PNG, GIF nebo BMP.
* Velikost vstupní image musí být mezi 50 x 50 a 4200 × 4200 pixelů.
* Text v obrázku se dá otočit o libovolný násobek 90 stupňů a malým úhlem až 40 stupňů.

### <a name="limitations"></a>Omezení

Na fotografiích, kde je text dominantní, můžou falešné kladné hodnoty pocházet z částečně rozpoznaných slov. Na některých fotografiích, zejména na fotografii bez jakéhokoli textu, přesnost se může lišit v závislosti na typu obrázku.

## <a name="recognize-text-api"></a>Rozhraní API pro Rozpoznávání textu

> [!NOTE]
> Rozhraní API pro Rozpoznávání textu se už nepoužívá, a to ve prospěch rozhraní API pro čtení. Rozhraní API pro čtení má podobné možnosti a je aktualizované pro zpracování souborů PDF, TIFF a vícestránkového souboru.

Rozhraní Rozpoznávání textu API se podobá optickému rozpoznávání znaků, ale provádí se asynchronně a používá aktualizované modely rozpoznávání. Další informace najdete v tématu [Referenční dokumentace k rozhraní rozpoznávání textu API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) .

### <a name="image-requirements"></a>Požadavky image

Rozhraní Rozpoznávání textu API funguje s imagemi, které splňují následující požadavky:

- Obrázek musí být uvedený ve formátu JPEG, PNG nebo BMP.
- Rozměry obrázku musí být mezi 50 × 50 a 4200 × 4200 pixelů.
- Velikost souboru obrázku musí být menší než 4 megabajty (MB).

## <a name="limitations"></a>Omezení

Přesnost operací rozpoznávání textu závisí na kvalitě obrázků. Následující faktory mohou způsobit nepřesné čtení:

* rozostřené obrázky;
* rukopis nebo text psaný kurzívou;
* umělecké styly písma;
* malé písmo;
* složité pozadí, stíny nebo odlesky vržené na text nebo narušená perspektiva;
* Příliš velká nebo chybějící velká písmena na začátku slova.
* dolní index, horní index nebo přeškrtnutý text.

## <a name="next-steps"></a>Další kroky

Pomocí rychlého startu pro [extrakci textu (čtení)](./QuickStarts/CSharp-hand-text.md) implementujte rozpoznávání textu v C# jednoduché aplikaci.
