---
title: Uvědomili si tištěné a rukou psaný text – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s uznání tištěné a rukou psaný text v obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 6827bf5f983834dc5222a3f3028386f8bbcb253a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338096"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Rozpoznávání tištěného a ručně psaného textu

Pro počítačové zpracování obrazu můžete detekovat a extrahovat tištěné nebo rukou psaný text z obrázků různé objekty s různými povrchy a pozadími, jako je potvrzení, plakáty, vizitek, písmena samolepicí bločky nebo Tabule.

Rozpoznávání textu šetří čas a úsilí. Zvýšit produktivitu práce může být pořízení image textu místo zdlouhavého přepisování. Rozpoznávání textu umožňuje digitalizaci poznámek. Tato digitalizaci umožňuje implementovat rychlé a snadné vyhledávání. Už nebudete zavalení papíry.

## <a name="text-recognition-requirements"></a>Požadavky na rozpoznávání textu

Počítačové zpracování obrazu dokáže rozpoznat tištěné a rukou psaný text v imagích, které splňují následující požadavky:

- Obrázek musí uvedené ve formátu JPEG, PNG nebo BMP
- Velikost souboru obrázku musí být menší než 4 megabajty (MB).
- Rozměry obrázku musí být až 50 × 50 pixelů 4200 x 4200

> [!NOTE]
> Tato technologie je momentálně ve verzi preview a je dostupná jenom pro anglický text.

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [extrahují text pomocí technologie OCR](concept-extracting-text-ocr.md).
