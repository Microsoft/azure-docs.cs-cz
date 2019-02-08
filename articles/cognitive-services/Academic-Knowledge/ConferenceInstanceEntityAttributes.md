---
title: Konference instanci entity atributy – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít s entitou konference Instance v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 183a307159adb5dfdb248eb0cf4862462a626db6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879186"
---
# <a name="conference-instance-entity"></a>Konference Instance Entity

<sub> * Následující atributy jsou specifická pro instanci entity místa konání konference. (Ty = "4") </sub>

Název    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
CIN     |Konference normalizovaný název instance ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |String     |Rovná se
DCN     |Zobrazovaný název instance Conference ({ConferenceSeriesName}: {ConferenceInstanceYear})       |String     |žádný
CIL     |Umístění instance konference    |String     |Je rovno<br/>StartsWith
CISD    |Počáteční datum konference instance  |Datum       |Je rovno<br/>IsBetween
CIED    |Koncové datum konference instance    |Datum       |Je rovno<br/>IsBetween
CIARD   |Abstraktní registrace termín splnění instance konference  |Datum       |Je rovno<br/>IsBetween
CISDD   |Odeslání termín splnění instance konference     |Datum       |Je rovno<br/>IsBetween
CIFVD   |Konečná verze termín splnění instance konference  |Datum       |Je rovno<br/>IsBetween
CINDD   |Oznámení datum konference instance   |Datum       |Je rovno<br/>IsBetween
CD.T    |Název instance události konference   |Datum       |Je rovno<br/>IsBetween
CD.D    |Datum události instance konference    |Datum       |Je rovno<br/>IsBetween
PCS.CN  |Název řady konference instance |String     |Rovná se
PCS.CId |Konference řady ID instance |Int64    |Rovná se
Kopie      |Počet citace celkový počet instancí konference           |Int32      |žádný  
ECC     |Počet celkový odhadovaný citace konference instancí |Int32      |žádný


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
FN      | Celý název instance konference
