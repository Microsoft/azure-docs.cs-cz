---
title: Přidružení atributy v Academic Knowledge API | Microsoft Docs
description: Další atributy, které můžete použít s přidružení entity v Academic Knowledge API v kognitivní služby.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a0ec67cb811ca207b3d038028491da2516028f0b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342354"
---
# <a name="affiliation-entity"></a>Přidružení Entity

<sub> * Následující atributy jsou specifické pro přidružení entity. (Ty = "5") </sub>

Název    |Popis                            |Typ       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
AfN     |Normalizovaný název přidružení        |Řetězec     |Rovná se
DAfN    |Zobrazovaný název přidružení       |Řetězec     |žádný
Kopie      |Přidružení citace celkový počet           |Int32      |žádný  
ECC     |Přidružení citace celkový odhadovaný počet |Int32      |žádný

## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
PC      |Počet dokumentu na přidružení