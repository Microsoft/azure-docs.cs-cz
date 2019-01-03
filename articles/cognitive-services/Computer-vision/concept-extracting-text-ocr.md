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
ms.custom: seodec18
ms.openlocfilehash: 07d3f5d365fa5c552ccb61c97532a9931b7e282e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583676"
---
# <a name="extracting-text-with-optical-character-recognition"></a>Extrahování textu s optické rozpoznávání znaků

Optické rozpoznávání znaků (OCR) technologie pro počítačové zpracování obrazu detekuje textový obsah v obrázku a extrahuje zjištěný text do datového proudu Strojově čitelný znaků. Výsledek můžete použít k vyhledávání nebo k různým jiným účelům, třeba jako lékařské záznamy, pro zabezpečení nebo bankovnictví. Jazyk se rozpoznává automaticky. Technologie OCR šetří čas a zvyšuje pohodlí uživatelů, protože umožňuje text místo přepisování vyfotografovat.

OCR podporuje 25 jazyků. Tyto jazyky jsou: Arabské, čínština (zjednodušená), Čínské tradiční čínština, čeština, dánština, holandština, angličtina, finština, francouzština, němčina, řečtina, maďarština, italština, japonština, korejština, norština, polština, portugalština, rumunština, ruština, srbština (cyrilice a latinky), slovenština, španělština, Švédština a turečtina.

V případě potřeby technologie OCR opraví otočení rozpoznaného textu ve stupních kolem vodorovné osy obrázku. OCR obsahuje souřadnice rámec jednotlivých slov, jak je znázorněno na následujícím obrázku.

![Diagram znázorňující střídajících obrázek a text probíhá čtení a vymezeny](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Požadavky technologie OCR

Počítačové zpracování obrazu může extrahovat text pomocí technologie OCR z imagí, které splňují následující požadavky:

* Obrázek musí být ve formátu JPEG, PNG, GIF nebo BMP.
* Velikost vstupního obrázku musí být až 50 × 50 pixelů 4200 x 4200


Vstupního obrázku můžete otočit o žádné nadbytečné 90 stupňů plus malé úhel až 40 stupňů.

## <a name="improving-ocr-accuracy"></a>Zlepšení přesnosti optického rozpoznávání znaků

Přesnost rozpoznaného textu závisí na kvalitě obrázku. Nepřesnosti při čtení mohou způsobovat následující situace:

* rozostřené obrázky;
* rukopis nebo text psaný kurzívou;
* umělecké styly písma;
* malé písmo;
* složité pozadí, stíny nebo odlesky vržené na text nebo narušená perspektiva;
* příliš velká nebo chybějící velká písmena na začátku slov;
* dolní index, horní index nebo přeškrtnutý text.

### <a name="ocr-limitations"></a>OCR omezení

Na fotografie, kde je dominantní textu počet falešně pozitivních výsledků mohou pocházet z částečně rozpoznaná slova. V některých fotografie, zejména fotografie žádný text můžete přesnost mnohem lišit v závislosti na typ bitové kopie.

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [uznání tištěné a rukou psaný text](concept-recognizing-text.md).
