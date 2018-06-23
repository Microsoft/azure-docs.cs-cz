---
title: Vytvořte atributy v Academic Knowledge API | Microsoft Docs
description: Další atributy, které můžete použít s Autor entity v Academic Knowledge API v kognitivní služby.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342355"
---
# <a name="author-entity"></a>Autor Entity
<sub> * Následující atributy jsou specifické pro Autor entity. (Ty = '1') </sub>

Název    |Popis                            |Typ       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
AuN     |Normalizovaný jméno autora                 |Řetězec     |Rovná se
DAuN    |Zobrazované jméno autora                    |Řetězec     |žádný
Kopie      |Autor citace celkový počet            |Int32      |žádný  
ECC     |Autor citace celkový odhadovaný počet  |Int32      |žádný
E       |Extended metadata (viz tabulka "Rozšířené atributy Meta")  |Řetězec     |žádný  


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
LKA. Afn     | Zobrazovaný název pro přidružení přidružené Autor  
LKA. AfId        | ID entity je přidružení přidružené Autor