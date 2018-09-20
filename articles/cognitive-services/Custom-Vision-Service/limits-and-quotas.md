---
title: Omezení a kvóty – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Další informace o omezení a kvóty pro službu Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: 53884cfc2683832d9df2b215c92b6b683ba29d2c
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363459"
---
# <a name="limits-and-quotas"></a>Omezení a kvóty

Existují tři úrovně klíče pro službu Custom Vision Service. F0 a S0 prostředky jsou získány prostřednictvím webu Azure portal. Podrobnosti o cenách a transakce definice jsou na [stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  F0 projekty můžete upgradovat na S0 projekty.

Prostředky projektu omezené zkušební verze jsou připojeny k vaše vize vlastní přihlašovací údaje (to znamená, účtu AAD nebo účtu MSA.) Jsou určeny pro krátké zkušební verze služby.  Účty vytvořené během dřívější bezplatná verze preview, před zavedením Azure verze Preview (1. března 2018) si zachovají své předchozí kvóty omezené zkušební verze. 

||**Omezené zkušební verze**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Projekty|2|2|100|
|Trénovacích obrázků na projekt|5 000|5 000|50,000|
|Predikce / měsíc|10 000 |10 000|Unlimited|
|Značky / project|50|50|250|
|Iterace |10|10|10|
|Minimální označené obrázky značek klasifikace (50 + doporučeno) |5|5|5|
|Minimální označené obrázky na značku, detekce objektů (50 + doporučeno)|15|15|15|
|Jak dlouho se uchovávají předpovědi imagí|30 dní|30 dní|30 dní|
|[Predikce](https://go.microsoft.com/fwlink/?linkid=865445) operace s úložištěm (transakcí za sekundu)|2|2|10|
|[Predikce](https://go.microsoft.com/fwlink/?linkid=865445) operace bez úložiště (transakcí za sekundu)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (volání za sekundu rozhraní API)|2|2|10|
|[Další volání rozhraní API](https://go.microsoft.com/fwlink/?linkid=865446) (transakcí za sekundu)|10|10|10|
|Maximální velikost obrázku (školení pro nahrání image) |6MB|6MB|6MB|
|Maximální velikost obrázku (předpověď)|4MB|4MB|4MB|

Omezení *# trénovacích obrázků na projekt* a *# značky nebo projektu* se očekává, že zvýší v čase pro projekty S0. 
