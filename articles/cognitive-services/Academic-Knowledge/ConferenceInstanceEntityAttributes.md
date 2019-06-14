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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498742"
---
# <a name="conference-instance-entity"></a>Konference Instance Entity

<sub> * Následující atributy jsou specifická pro instanci entity místa konání konference. (Ty = "4") </sub>

Name    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |Entity ID                              |Int64      |Je rovno
CIN     |Konference normalizovaný název instance ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |String     |Je rovno
DCN     |Zobrazovaný název instance Conference ({ConferenceSeriesName}: {ConferenceInstanceYear})       |String     |None
CIL     |Umístění instance konference    |String     |Je rovno<br/>StartsWith
CISD    |Počáteční datum konference instance  |Datum       |Je rovno<br/>IsBetween
CIED    |Koncové datum konference instance    |Datum       |Je rovno<br/>IsBetween
CIARD   |Abstraktní registrace termín splnění instance konference  |Datum       |Je rovno<br/>IsBetween
CISDD   |Odeslání termín splnění instance konference     |Datum       |Je rovno<br/>IsBetween
CIFVD   |Konečná verze termín splnění instance konference  |Datum       |Je rovno<br/>IsBetween
CINDD   |Oznámení datum konference instance   |Datum       |Je rovno<br/>IsBetween
CD.T    |Název instance události konference   |Datum       |Je rovno<br/>IsBetween
CD.D    |Datum události instance konference    |Datum       |Je rovno<br/>IsBetween
PCS.CN  |Název řady konference instance |String     |Je rovno
PCS.CId |Konference řady ID instance |Int64    |Je rovno
CC      |Počet citace celkový počet instancí konference           |Int32      |None  
ECC     |Počet celkový odhadovaný citace konference instancí |Int32      |None


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name    | Popis               
--------|---------------------------    
FN      | Celý název instance konference
