---
title: Extrahování textu pomocí technologie OCR
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Koncepty související s extrahují text s optické rozpoznávání znaků na použití pro počítačové zpracování obrazu ve službě Azure Cognitive Services.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 07efe49c53b13094f3f67db3b3becb57a7c473d8
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725367"
---
# <a name="extracting-text-with-ocr"></a>Extrahování textu pomocí technologie OCR

Optické rozpoznávání znaků (OCR) technologie pro počítačové zpracování obrazu detekuje textový obsah v obrázku a extrahuje zjištěný text do datového proudu Strojově čitelný znaků. Výsledek můžete použít k prohledávání a řadě dalších účelů, jako jsou zdravotnickými záznamy, zabezpečení a bankovnictví. Jazyk se detekuje automaticky. OCR šetří čas a zvyšuje pohodlí uživatelů tím, že jim pořídit fotografie textu místo zdlouhavého přepisování pořídili text.

OCR podporuje 25 jazycích. Tyto jazyky jsou: arabština, zjednodušené čínštiny, tradiční čínština, čeština, dánština, holandština, angličtina, finština, francouzština, němčina, řečtina, maďarština, italština, japonština, korejština, norština, polština, portugalština, rumunština, ruština, srbština (cyrilice a latinka) Slovenština, španělština, švédština a turečtina.

V případě potřeby řeší OCR otočení rozpoznaný text ve stupních okolo osy vodorovný obrázek. OCR obsahuje souřadnice rámec jednotlivých slov, jak je znázorněno na následujícím obrázku.

![Přehled technologie OCR](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Požadavky technologie OCR

Počítačové zpracování obrazu může extrahovat text pomocí technologie OCR z imagí, které splňují následující požadavky:

* Obrázek musí být ve formátu JPEG, PNG, GIF nebo BMP.
* Velikost vstupního obrázku musí být až 40 x 40 3200 x 3200 pixelů
* Na obrázku nesmí být větší než 10 megapixely k

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