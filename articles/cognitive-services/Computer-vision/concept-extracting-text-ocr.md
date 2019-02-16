---
title: Extrahování textu s optické rozpoznávání znaků (OCR) – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s extrahují text s optické rozpoznávání znaků (OCR) pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb73eb9fdd6879a5fbe1fed820bf92b2d627b65
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310436"
---
# <a name="extract-text-with-optical-character-recognition"></a>Extrahovat text s optické rozpoznávání znaků

Počítačové zpracování obrazu funkce optické rozpoznávání znaků (OCR) detekuje textový obsah v obrázku a převede zjištěný text do datového proudu Strojově čitelný znaků. Výsledek můžete použít pro celou řadu účelů, jako je hledání, zdravotnickými záznamy, zabezpečení a bankovnictví. 

OCR podporuje 25 jazycích: Arabské, čínština (zjednodušená), Čínské tradiční čínština, čeština, dánština, holandština, angličtina, finština, francouzština, němčina, řečtina, maďarština, italština, japonština, korejština, norština, polština, portugalština, rumunština, ruština, srbština (cyrilice a latinky), slovenština, španělština, Švédština a turečtina. OCR automaticky zjišťuje jazyk zjištěné textu.

V případě potřeby OCR opravuje otočení rozpoznaný text tak, že vrací rotačních posun ve stupních, o osy vodorovný obrázek. OCR také poskytuje souřadnice rámec jednotlivých slov, jak je znázorněno na následujícím obrázku.

![Diagram znázorňující střídajících obrázek a text probíhá čtení a vymezeny](./Images/vision-overview-ocr.png)

## <a name="image-requirements"></a>Požadavky image

Počítačové zpracování obrazu může extrahovat text pomocí technologie OCR z imagí, které splňují následující požadavky:

* Obrázek musí být ve formátu JPEG, PNG, GIF nebo BMP.
* Velikost vstupního obrázku musí být až 50 × 50 pixelů 4200 x 4200
* Text v obrázku můžete otočit o žádné nadbytečné 90 stupňů plus malé úhel až 40 stupňů.

## <a name="improving-ocr-accuracy"></a>Zlepšení přesnosti optického rozpoznávání znaků

Přesnost rozpoznaného textu závisí na kvalitě obrázku. Nesprávné čtení může být způsobeno následujícími:

* rozostřené obrázky;
* rukopis nebo text psaný kurzívou;
* umělecké styly písma;
* malé písmo;
* složité pozadí, stíny nebo odlesky vržené na text nebo narušená perspektiva;
* příliš velká nebo chybějící velká písmena na začátku slov;
* dolní index, horní index nebo přeškrtnutý text.

### <a name="ocr-limitations"></a>OCR omezení

Na imagích, kde je dominantní textu počet falešně pozitivních výsledků mohou pocházet z částečně rozpoznaná slova. Na některé obrázky, zejména fotografie žádný text můžete přesnost mnohem lišit v závislosti na typu image.

## <a name="next-steps"></a>Další postup

Zobrazit [OCR referenční dokumentaci](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) Další informace.
