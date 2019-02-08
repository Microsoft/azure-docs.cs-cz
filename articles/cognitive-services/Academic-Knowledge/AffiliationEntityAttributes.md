---
title: Přidružení atributy entit v rozhraní Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít s přidružení entit v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 82e6a5b66342e58e62da029d617cbd1d74c28149
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864362"
---
# <a name="affiliation-entity"></a>Entita přidružení

<sub> * Následující atributy jsou specifická pro entity členství. (Ty = "5") </sub>

Název    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
AfN     |Normalizovaný název přidružení        |String     |Rovná se
DAfN    |Zobrazovaný název přidružení       |String     |žádný
Kopie      |Citaci celkový počet přidružení           |Int32      |žádný  
ECC     |Citaci celkový odhadovaný počet přidružení |Int32      |žádný

## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
PC      |Počet umístění na papír
