---
title: Extrahování textu pomocí technologie OCR – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s extrahují text s optické rozpoznávání znaků (OCR) pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 52b6265722d5cfbf8baf54e1785ace627aa0892f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341663"
---
# <a name="extracting-text-with-ocr"></a>Extrahování textu pomocí technologie OCR

Optické rozpoznávání znaků (OCR) technologie pro počítačové zpracování obrazu detekuje textový obsah v obrázku a extrahuje zjištěný text do datového proudu Strojově čitelný znaků. Výsledek můžete použít k prohledávání a řadě dalších účelů, jako jsou zdravotnickými záznamy, zabezpečení a bankovnictví. Jazyk se detekuje automaticky. OCR šetří čas a zvyšuje pohodlí uživatelů tím, že jim pořídit fotografie textu místo zdlouhavého přepisování pořídili text.

OCR podporuje 25 jazycích. Tyto jazyky jsou: arabština, zjednodušené čínštiny, tradiční čínština, čeština, dánština, holandština, angličtina, finština, francouzština, němčina, řečtina, maďarština, italština, japonština, korejština, norština, polština, portugalština, rumunština, ruština, srbština (cyrilice a latinka) Slovenština, španělština, švédština a turečtina.

V případě potřeby řeší OCR otočení rozpoznaný text ve stupních okolo osy vodorovný obrázek. OCR obsahuje souřadnice rámec jednotlivých slov, jak je znázorněno na následujícím obrázku.

![Přehled technologie OCR](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Požadavky technologie OCR

Počítačové zpracování obrazu může extrahovat text pomocí technologie OCR z imagí, které splňují následující požadavky:

* Obrázek musí být ve formátu JPEG, PNG, GIF nebo BMP.
* Velikost vstupního obrázku musí být až 50 × 50 pixelů 4200 x 4200


Vstupního obrázku můžete otočit o žádné nadbytečné 90 stupňů plus malé úhel až 40 stupňů.

## <a name="improving-ocr-accuracy"></a>Zlepšení přesnosti optického rozpoznávání znaků

Rozpoznávání textu přesnost závisí na kvalitu obrazu. Nesprávné čtení může být způsobeno v následujících případech:

* Použitím bitové kopie.
* Rukou psaný nebo kurzívových text.
* Styly umělecký písem.
* Malá velikost textu.
* Komplexní pozadí, stíny nebo oslnění přes text nebo Perspektiva narušení.
* Příliš velký nebo chybějící velká písmena na začátku slova
* Dolní index, horní index nebo přeškrtnutý text.

### <a name="ocr-limitations"></a>OCR omezení

Na fotografie, kde je dominantní textu počet falešně pozitivních výsledků mohou pocházet z částečně rozpoznaná slova. V některých fotografie, zejména fotografie žádný text můžete přesnost mnohem lišit v závislosti na typ bitové kopie.

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [uznání tištěné a rukou psaný text](concept-recognizing-text.md).
