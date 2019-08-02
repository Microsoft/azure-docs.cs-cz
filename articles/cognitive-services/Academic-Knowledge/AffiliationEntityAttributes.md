---
title: Atributy entity přidružení v Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Seznamte se s atributy, které můžete použít spolu s entitou afilace v Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705114"
---
# <a name="affiliation-entity"></a>Entita přidružení

<sub>* Následující atributy jsou specifické pro entitu přidružení. (Ty = ' 5 ')</sub>

Name    |Popis                            |type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID entity                              |Int64      |Je rovno
AfN     |Normalizovaný název přidružení        |Řetězec     |Je rovno
DAfN    |Zobrazovaný název afilace       |Řetězec     |žádný
CC      |Celkový počet citací v přidružení           |Int32      |žádný  
ECC     |Celkový počet odhadovaných citací v přidružení |Int32      |žádný

## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name    | Popis               
--------|---------------------------    
PC      |Počet papírů afilace
