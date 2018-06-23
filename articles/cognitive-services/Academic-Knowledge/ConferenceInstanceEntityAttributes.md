---
title: Konferenční instanci entity atributy v Academic Knowledge API | Microsoft Docs
description: Další atributy, které můžete použít s konferenční instanci entity v Academic Knowledge API v kognitivní služby.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ef2bca4346a4666905f3dfb7bd448720f3b0ef8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342369"
---
# <a name="conference-instance-entity"></a>Konferenční Instance Entity

<sub> * Následující atributy jsou specifické pro konferenční instance entity. (Ty = "4") </sub>

Název    |Popis                            |Typ       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
CIN     |Normalizovaný název instance konferenční ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Řetězec     |Rovná se
DCN     |Zobrazovaný název instance konferenční ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Řetězec     |žádný
SOUBOR CIL     |Umístění instance konference    |Řetězec     |Rovná se,<br/>StartsWith
CISD    |Počáteční datum konference instance  |Datum       |Rovná se,<br/>IsBetween
CIED    |Koncové datum instance konference    |Datum       |Rovná se,<br/>IsBetween
CIARD   |Abstraktní registrace datum splatnosti konferenční instance  |Datum       |Rovná se,<br/>IsBetween
CISDD   |Odeslání datum splatnosti konferenční instance     |Datum       |Rovná se,<br/>IsBetween
CIFVD   |Finální verzi datum splatnosti konferenční instance  |Datum       |Rovná se,<br/>IsBetween
CINDD   |Datum oznámení konference instance   |Datum       |Rovná se,<br/>IsBetween
CD. T    |Název události instance konference   |Datum       |Rovná se,<br/>IsBetween
CD. D    |Data události instance konference    |Datum       |Rovná se,<br/>IsBetween
POČÍTAČE. CN  |Název řady konferenční instance |Řetězec     |Rovná se
POČÍTAČE. CId |ID řady konferenční instance |Int64    |Rovná se
Kopie      |Počet konferenční instancí celkový citace           |Int32      |žádný  
ECC     |Počet konferenční instancí celkový odhadovaný citace |Int32      |žádný


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
FN      | Celý název konferenční instance