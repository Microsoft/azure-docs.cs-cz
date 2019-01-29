---
title: Konference instanci entity atributy – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít s entitou konference Instance v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: db025f377a3fab2f788252db0c8e3555837a6de8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196102"
---
# <a name="conference-instance-entity"></a>Konference Instance Entity

<sub> * Následující atributy jsou specifická pro instanci entity místa konání konference. (Ty = "4") </sub>

Name    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
CIN     |Konference normalizovaný název instance ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Řetězec     |Rovná se
DCN     |Zobrazovaný název instance Conference ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Řetězec     |žádný
CIL     |Umístění instance konference    |Řetězec     |Je rovno<br/>StartsWith
CISD    |Počáteční datum konference instance  |Datum       |Je rovno<br/>IsBetween
CIED    |Koncové datum konference instance    |Datum       |Je rovno<br/>IsBetween
CIARD   |Abstraktní registrace termín splnění instance konference  |Datum       |Je rovno<br/>IsBetween
CISDD   |Odeslání termín splnění instance konference     |Datum       |Je rovno<br/>IsBetween
CIFVD   |Konečná verze termín splnění instance konference  |Datum       |Je rovno<br/>IsBetween
CINDD   |Oznámení datum konference instance   |Datum       |Je rovno<br/>IsBetween
CD.T    |Název instance události konference   |Datum       |Je rovno<br/>IsBetween
CD.D    |Datum události instance konference    |Datum       |Je rovno<br/>IsBetween
PCS.CN  |Název řady konference instance |Řetězec     |Rovná se
PCS.CId |Konference řady ID instance |Int64    |Rovná se
Kopie      |Počet citace celkový počet instancí konference           |Datový typ Int32      |žádný  
ECC     |Počet celkový odhadovaný citace konference instancí |Datový typ Int32      |žádný


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name    | Popis               
--------|---------------------------    
FN      | Celý název instance konference
