---
title: Rozpoznat tištěné, rukou psaný text – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s uznání tištěné a rukou psaný text v obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313173"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Rozpoznávání tištěného a ručně psaného textu

Pro počítačové zpracování obrazu můžete detekovat a extrahovat tištěné nebo rukou psaný text z obrázků různé objekty s různými povrchy a pozadími, jako je potvrzení, plakáty, vizitek, písmena samolepicí bločky nebo Tabule.

Funkce rozpoznávání textu je velmi podobný [optické rozpoznávání znaků (OCR)](concept-extracting-text-ocr.md), ale na rozdíl od OCR provádí asynchronně a používá aktualizovat modely pro rozpoznávání.

> [!NOTE]
> Tato technologie je momentálně ve verzi preview a je dostupná jenom pro anglický text.

## <a name="text-recognition-requirements"></a>Požadavky na rozpoznávání textu

Počítačové zpracování obrazu dokáže rozpoznat tištěné a rukou psaný text v imagích, které splňují následující požadavky:

- Obrázek musí uvedené ve formátu JPEG, PNG nebo BMP
- Velikost souboru obrázku musí být menší než 4 megabajty (MB).
- Rozměry obrázku musí být až 50 × 50 pixelů 4200 x 4200

## <a name="next-steps"></a>Další postup

Najdete v článku [rozpoznání textu v dokumentaci](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) Další informace.