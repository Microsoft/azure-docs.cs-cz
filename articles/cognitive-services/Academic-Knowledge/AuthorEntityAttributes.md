---
title: Atributy entit autora – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Seznamte se s atributy, které můžete použít s entitou Author v Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705092"
---
# <a name="author-entity"></a>Vytvořit entitu
<sub>* Následující atributy jsou specifické pro entitu Author. (Ty = ' 1 ')</sub>

Name    |Popis                            |type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID entity                              |Int64      |Je rovno
AuN     |Normalizovaný Název autora                 |Řetězec     |Je rovno
DAuN    |Zobrazované jméno autora                    |Řetězec     |žádný
CC      |Celkový počet citací autora            |Int32      |žádný  
ECC     |Celkový počet předpokládaných citací pro autora  |Int32      |žádný
E       |Rozšířená metadata (viz tabulka rozšířené meta atributy)  |Řetězec     |žádný  


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name    | Popis               
--------|---------------------------    
LKA.Afn     | zobrazované jméno afilace přidružené k autorovi  
LKA.AfId        | ID entity přidružení přidružené k autorovi
