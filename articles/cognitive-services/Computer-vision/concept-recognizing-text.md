---
title: Rozpoznat tištěné, rukou psaný text – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s uznání tištěné a rukou psaný text v obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6ee2b3282a5f895c61b9f0b55aa3353e534b6e37
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191037"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Rozpoznávání tištěného a ručně psaného textu

Pro počítačové zpracování obrazu můžete detekovat a extrahovat tištěné nebo rukou psaný text z obrázků různé objekty s různými povrchy a pozadími, jako je potvrzení, plakáty, vizitek, písmena samolepicí bločky nebo Tabule.

Rozpoznávání textu šetří čas a úsilí. Zvýšit produktivitu práce může být pořízení image textu místo zdlouhavého přepisování. Rozpoznávání textu umožňuje digitalizaci poznámek. Digitalizace umožňuje snadno a rychle implementovat vyhledávání. Také už nebudete tak zavaleni papíry.

## <a name="text-recognition-requirements"></a>Požadavky na rozpoznávání textu

Počítačové zpracování obrazu dokáže rozpoznat tištěné a rukou psaný text v imagích, které splňují následující požadavky:

- Obrázek musí uvedené ve formátu JPEG, PNG nebo BMP
- Velikost souboru obrázku musí být menší než 4 megabajty (MB).
- Rozměry obrázku musí být až 50 × 50 pixelů 4200 x 4200

> [!NOTE]
> Tato technologie je momentálně ve verzi preview a je dostupná jenom pro anglický text.

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [extrahují text pomocí technologie OCR](concept-extracting-text-ocr.md).
