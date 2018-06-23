---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: e37d3ef5b6f65ad31bc19f9f8c15350014d1c9ad
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343022"
---
Pomocí [anomálií vyhledávací API](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder), můžete nahrát data řady čas ve formátu JSON na koncový bod rozhraní API a výsledek pak přečte z odpovědi rozhraní API. Můžete nahrát data řady čas, každý datový bod zahrnuje:  
* Časové razítko - časové razítko pro datového bodu. Zajistěte, aby používal řetězec datum čas UTC, například "2017-08-01T00:00:00Z"
* Hodnota – měření tohoto datového bodu

Výsledky se zabývají:
* Období - periodicity, pomocí rozhraní API, která může zjišťovat anomálie
* WarningText - možné informace upozornění
* ExpectedValue - předpovězené hodnoty metodou učení základě modelu
* IsAnomaly - výsledek na tom, zda datové body jsou anomálií nebo není v obou směrech (špičky nebo vyhrazené IP adresy)
* IsAnomaly_Neg - výsledek na tom, zda datové body jsou anomálie v záporné směru (vyhrazené)
* IsAnomaly_Pos - výsledek na tom, zda datové body jsou anomálie v kladné směru (špičky)
* UpperMargin – součet ExpectedValue a UpperMargin Určuje, že je stále představit jako normální horní hranice, která datového bodu
* LowerMargin - (ExpectedValue - LowerMargin) Určuje dolní hranice, že datový bod je stále představit jako normální

Podrobnosti o kontrakt dat lze nalézt [zde](../apiref.md).

