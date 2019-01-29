---
title: Atributy entit Autor - rozhraní Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít s Autor entit v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 48758ac9ec8c993bbdb490229ae20fcce1fb0a49
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175175"
---
# <a name="author-entity"></a>Autor Entity
<sub> * Následující atributy jsou specifická pro entity Autor. (Ty = '1') </sub>

Name    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
AuN     |Normalizovaná jméno autora                 |Řetězec     |Rovná se
DAuN    |Zobrazované jméno autora                    |Řetězec     |žádný
Kopie      |Autor citace celkový počet            |Datový typ Int32      |žádný  
ECC     |Autor citace celkový odhadovaný počet  |Datový typ Int32      |žádný
E       |Rozšířený metadata (viz tabulka "Rozšířené atributy metadat")  |Řetězec     |žádný  


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name    | Popis               
--------|---------------------------    
LKA.Afn     | Zobrazovaný název pro přidružení spojené s autorem  
LKA.AfId        | ID entity v umístění spojené s autorem
