---
title: Omezení a kvóty pro vlastní vize služba – kognitivní služeb Azure | Microsoft Docs
description: Další informace o omezení a kvóty pro službu Azure Cognitives služby vlastní vize Service.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: 44666d5d7f2a51e4017c704205d21b1f6d06908c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343588"
---
# <a name="limits-and-quotas"></a>Omezení a kvóty

Existují tři úrovně klíče pro službu vize vlastní. F0 a S0 prostředky jsou získány prostřednictvím portálu Azure. Podrobnosti o cenách a transakce definice jsou na [stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  Projekty F0 se dají upgradovat na projekty S0.

Projekt prostředků omezené zkušební verze jsou připojené k vaší vlastní vize přihlášení (to znamená, že účtu AAD nebo účtu MSA.) Ty jsou určené pro krátké zkušební verze služby.  Účty vytvořené během časná bezplatnou verzi preview před zavedením Azure verze Preview (1. března 2018) se zachovat jejich předchozí kvóty pro omezené zkušební verze. 

||**Omezené zkušební verze**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Projekty|2|2|100|
|Školení bitové kopie na projektu|5 000|5 000|50,000|
|Predikce / měsíc|10 000 |10 000|Unlimited|
|Značky / project|50|50|250|
|Iterace |10|10|10|
|Minimální označené bitové kopie na značku, klasifikace (50 + doporučeno) |5|5|5|
|Minimální označené bitové kopie na značku, zjišťování objektu (50 + doporučeno)|15|15|15|
|Jak dlouho předpovědi, které jsou uloženy bitové kopie|30 dní|30 dní|30 dní|
|[Předpověď](https://go.microsoft.com/fwlink/?linkid=865445) operací s úložištěm (transakcí za sekundu)|2|2|10|
|[Předpověď](https://go.microsoft.com/fwlink/?linkid=865445) operations bez úložiště (transakcí za sekundu)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (volání za sekundu rozhraní API)|2|2|10|
|[Další volání rozhraní API](https://go.microsoft.com/fwlink/?linkid=865446) (transakcí za sekundu)|10|10|10|
|Maximální velikost bitové kopie (školení image odesílání) |6MB|6MB|6MB|
|Maximální velikost bitové kopie (předpověď)|4 MB VOLNÉHO MÍSTA|4 MB VOLNÉHO MÍSTA|4 MB VOLNÉHO MÍSTA|

Omezení *# školení obrázků na projektu* a *# značky nebo projektu* očekává se, že zvýšit v čase pro projekty S0. 
