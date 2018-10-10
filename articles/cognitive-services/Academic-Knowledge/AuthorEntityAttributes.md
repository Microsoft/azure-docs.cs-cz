---
title: Atributy entit Autor - rozhraní Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít s Autor entit v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 62e6da6d558a0494fb83115b1e307081099399d4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900682"
---
# <a name="author-entity"></a>Autor Entity
<sub> * Následující atributy jsou specifická pro entity Autor. (Ty = '1') </sub>

Název    |Popis                            |Typ       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
AuN     |Normalizovaná jméno autora                 |Řetězec     |Rovná se
DAuN    |Zobrazované jméno autora                    |Řetězec     |žádný
Kopie      |Autor citace celkový počet            |Datový typ Int32      |žádný  
ECC     |Autor citace celkový odhadovaný počet  |Datový typ Int32      |žádný
E       |Rozšířený metadata (viz tabulka "Rozšířené atributy metadat")  |Řetězec     |žádný  


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
LKA. Afn     | Zobrazovaný název pro přidružení spojené s autorem  
LKA. AfId        | ID entity v umístění spojené s autorem