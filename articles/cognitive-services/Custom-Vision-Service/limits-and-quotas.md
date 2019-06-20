---
title: Omezení a kvóty – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Další informace o omezení a kvóty pro službu Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 9cff5fdac39be2338305cd37a4b2328a28a48255
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269261"
---
# <a name="limits-and-quotas"></a>Omezení a kvóty

Existují dvě úrovně klíčů pro Custom Vision service. Můžete se zaregistrovat pro F0 (zdarma) nebo S0 (standard) předplatné na webu Azure portal. Zobrazit odpovídající [stránce ceny služby Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) podrobnosti o cenách a transakce.

Počet trénovacích obrázků na projekt a značky pro každý projekt se očekává se zvýšení časem pro projekty S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projekty|2|100|
|Trénovacích obrázků na projekt |5 000|100 000|
|Predikce / měsíc|10,000 |Unlimited|
|Značky / project|50|500|
|Iterace |10|10|
|Min označené obrázky značek klasifikace (50 + doporučeno) |5|5|
|Min označené obrázky na značku, detekce objektů (50 + doporučeno)|15|15|
|Jak dlouho se uchovávají předpovědi imagí|30 dní|30 dní|
|[Predikce](https://go.microsoft.com/fwlink/?linkid=865445) operace s úložištěm (transakcí za sekundu)|2|10|
|[Predikce](https://go.microsoft.com/fwlink/?linkid=865445) operace bez úložiště (transakcí za sekundu)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (volání za sekundu rozhraní API)|2|10|
|[Další volání rozhraní API](https://go.microsoft.com/fwlink/?linkid=865446) (transakcí za sekundu)|10|10|
|Maximální velikost obrázku (školení pro nahrání image) |6 MB|6 MB|
|Maximální velikost obrázku (předpověď)|4 MB|4 MB|
|Maximální počet oblastí na image školení zjištění objektu|200|200|
|Maximální počet značek na klasifikace obrázků|30|30|
