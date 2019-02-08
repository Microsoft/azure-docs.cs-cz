---
title: Atributy entit Autor - rozhraní Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít s Autor entit v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878931"
---
# <a name="author-entity"></a>Autor Entity
<sub> * Následující atributy jsou specifická pro entity Autor. (Ty = '1') </sub>

Název    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
AuN     |Normalizovaná jméno autora                 |String     |Rovná se
DAuN    |Zobrazované jméno autora                    |String     |žádný
Kopie      |Autor citace celkový počet            |Int32      |žádný  
ECC     |Autor citace celkový odhadovaný počet  |Int32      |žádný
E       |Rozšířený metadata (viz tabulka "Rozšířené atributy metadat")  |String     |žádný  


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
LKA.Afn     | Zobrazovaný název pro přidružení spojené s autorem  
LKA.AfId        | ID entity v umístění spojené s autorem
