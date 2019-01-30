---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 3cc0e521e43f6855397a19fe34fce99da3e20494
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228848"
---
S [rozhraní API pro vyhledávač anomálií](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder), můžete nahrát data časových řad ve formátu JSON na koncový bod rozhraní API a pak si můžete přečíst výsledek z odpovědi rozhraní API. Můžete nahrát data časových řad, datových bodů zahrnuje:  
* Časové razítko – časové razítko pro datový bod. Ujistěte se, že používá UTC řetězce data a času, například "2017-08-01T00:00:00Z"
* Hodnota – měření tohoto datového bodu

Výsledky se skládá ze:
* Období - periodicitu, který používá rozhraní API pro detekci anomálií
* WarningText – je to možné upozornění
* Model založený na ExpectedValue - předpovězené hodnoty učení
* IsAnomaly - výsledek toho, zda datové body se anomálie nebo není v obou směrech (špičky nebo poklesy)
* IsAnomaly_Neg - výsledek toho, zda datové body se anomálie ve směru záporná (DIP)
* IsAnomaly_Pos - výsledek toho, zda datové body se anomálie v kladné směru (špičky)
* UpperMargin – součet ExpectedValue a UpperMargin Určuje, že je stále představit jako obvykle horní mez, která datového bodu
* LowerMargin - (ExpectedValue - LowerMargin) Určuje dolní mez, datový bod je stále si mysleli, že jako obvykle

Najdete podrobnosti o kontraktu dat [tady](../apiref.md).

