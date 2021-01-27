---
title: Omezení a kvóty – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Tento článek popisuje různé typy licenčních klíčů a omezení a kvóty pro Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: pafarley
ms.openlocfilehash: 3392cc5f3ee9daef1ae8397f6829f4ca7a42373a
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871396"
---
# <a name="limits-and-quotas"></a>Omezení a kvóty

Existují dvě úrovně klíčů pro službu Custom Vision. Předplatné F0 (Free) nebo S0 (Standard) si můžete zaregistrovat prostřednictvím Azure Portal. Podrobnosti o cenách a transakcích najdete na [stránce s cenami odpovídající Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) .

Pro projekty S0 se očekává, že počet školicích obrázků na projekt a značek na projekt se bude zvětšovat v čase.

|Faktor|**F0**|**S0**|
|-----|-----|-----|
|Projekty|2|100|
|Školení imagí na projekt |5 000|100 000|
|Předpovědi za měsíc|10 000 |Unlimited|
|Značky/projekt|50|500|
|Iterace |10|10|
|Minimální počet označených obrázků na značku, klasifikace (maximálně 50 + doporučeno) |5|5|
|Minimální počet označených obrázků na značku, detekce objektu (maximálně 50 + doporučeno)|15|15|
|Jak dlouho jsou obrázky předpovědi uloženy|30 dní|30 dní|
|[Předpovědi](https://go.microsoft.com/fwlink/?linkid=865445) operací s úložištěm (transakcí za sekundu)|2|10|
|[Předpovědi](https://go.microsoft.com/fwlink/?linkid=865445) operací bez úložiště (transakcí za sekundu)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (volání rozhraní API za sekundu)|2|10|
|[Jiná volání rozhraní API](https://go.microsoft.com/fwlink/?linkid=865446) (transakcí za sekundu)|10|10|
|Přijímané typy obrázků|jpg, PNG, BMP, GIF|jpg, PNG, BMP, GIF|
|Minimální výška a šířka obrázku v pixelech|256 (viz poznámku)|256 (viz poznámku)|
|Maximální výška a šířka obrázku v pixelech|10 240|10 240|
|Maximální velikost obrázku (školicí obrázek při nahrávání) |6 MB|6 MB|
|Maximální velikost obrázku (předpověď)|4 MB|4 MB|
|Maximální počet oblastí na obrázek (detekce objektu)|300|300|
|Maximální počet značek na obrázek (klasifikace)|100|100|

> [!NOTE]
> Obrázky menší než 256 pixelů budou přijímány, ale budou zvětšeny.
> Poměr stran obrázku by neměl být větší než 25.
