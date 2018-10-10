---
title: Konference instanci entity atributy – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít s entitou konference Instance v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 6111ad00044943f12b2e098c4fd07ffb40185799
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902408"
---
# <a name="conference-instance-entity"></a>Konference Instance Entity

<sub> * Následující atributy jsou specifická pro instanci entity místa konání konference. (Ty = "4") </sub>

Název    |Popis                            |Typ       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
CIN     |Konference normalizovaný název instance ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Řetězec     |Rovná se
DCN     |Zobrazovaný název instance Conference ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Řetězec     |žádný
SOUBOR CIL SE NAČTE     |Umístění instance konference    |Řetězec     |Je rovno<br/>StartsWith
CISD    |Počáteční datum konference instance  |Datum       |Je rovno<br/>IsBetween
CIED    |Koncové datum konference instance    |Datum       |Je rovno<br/>IsBetween
CIARD   |Abstraktní registrace termín splnění instance konference  |Datum       |Je rovno<br/>IsBetween
CISDD   |Odeslání termín splnění instance konference     |Datum       |Je rovno<br/>IsBetween
CIFVD   |Konečná verze termín splnění instance konference  |Datum       |Je rovno<br/>IsBetween
CINDD   |Oznámení datum konference instance   |Datum       |Je rovno<br/>IsBetween
CD. T    |Název instance události konference   |Datum       |Je rovno<br/>IsBetween
CD. D    |Datum události instance konference    |Datum       |Je rovno<br/>IsBetween
POČÍTAČE. CN  |Název řady konference instance |Řetězec     |Rovná se
POČÍTAČE. CId |Konference řady ID instance |Int64    |Rovná se
Kopie      |Počet citace celkový počet instancí konference           |Datový typ Int32      |žádný  
ECC     |Počet celkový odhadovaný citace konference instancí |Datový typ Int32      |žádný


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
FN      | Celý název instance konference