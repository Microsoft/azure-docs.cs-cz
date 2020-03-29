---
title: Limity a kvóty – služba Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje různé typy licenčních klíčů a o omezeních a kvótách pro službu Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081773"
---
# <a name="limits-and-quotas"></a>Omezení a kvóty

Existují dvě úrovně klíčů pro službu Vlastní vize. Prostřednictvím portálu Azure portal si můžete zaregistrovat předplatné F0 (zdarma) nebo S0 (standard). Podrobnosti o cenách a transakcích najdete na [odpovídající stránce s cenami služeb Cognitive Services.](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)

Počet obrázků školení na projekt a značky na projekt se očekává, že zvýšení v průběhu času pro projekty S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projekty|2|100|
|Trénování obrázků na projekt |5 000|100 000|
|Předpovědi / měsíc|10 000 |Unlimited|
|Tagy / projekt|50|500|
|Iterací |10|10|
|Min označené obrázky na značku, klasifikace (50 + doporučeno) |5|5|
|Min označené obrázky na značku, detekce objektů (doporučeno 50+|15|15|
|Jak dlouho jsou uloženy predikční obrázky|30 dní|30 dní|
|[Predikční](https://go.microsoft.com/fwlink/?linkid=865445) operace s úložištěm (transakce za sekundu)|2|10|
|[Predikce](https://go.microsoft.com/fwlink/?linkid=865445) operace bez úložiště (transakce za sekundu)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (volání rozhraní API za sekundu)|2|10|
|[Další volání rozhraní API](https://go.microsoft.com/fwlink/?linkid=865446) (transakce za sekundu)|10|10|
|Přijaté typy obrázků|jpg, png, bmp, gif|jpg, png, bmp, gif|
|Min. výška/šířka obrázku v obrazových bodech|256 (viz poznámka)|256 (viz poznámka)|
|Maximální výška/šířka obrazu v obrazových bodech|Bez omezení|Bez omezení|
|Maximální velikost obrázku (tréninkový obrázek upload) |6 MB|6 MB|
|Maximální velikost obrázku (predikce)|4 MB|4 MB|
|Maximální počet oblastí na obrázek školení detekce objektů|300|300|
|Maximální počet značek na obrázek klasifikace|100|100|

> [!NOTE]
> Obrázky menší než 256 pixelů budou akceptovány, ale zvětšené.
