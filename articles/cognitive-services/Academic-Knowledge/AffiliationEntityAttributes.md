---
title: Přidružení atributy entit v rozhraní Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít s přidružení entit v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 344b26b16f74cd44982e3c93fa69295792daa9a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190621"
---
# <a name="affiliation-entity"></a>Entita přidružení

<sub> * Následující atributy jsou specifická pro entity členství. (Ty = "5") </sub>

Name    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
AfN     |Normalizovaný název přidružení        |Řetězec     |Rovná se
DAfN    |Zobrazovaný název přidružení       |Řetězec     |žádný
Kopie      |Citaci celkový počet přidružení           |Datový typ Int32      |žádný  
ECC     |Citaci celkový odhadovaný počet přidružení |Datový typ Int32      |žádný

## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name    | Popis               
--------|---------------------------    
PC      |Počet umístění na papír
