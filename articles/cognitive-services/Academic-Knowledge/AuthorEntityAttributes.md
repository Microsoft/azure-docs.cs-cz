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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609747"
---
# <a name="author-entity"></a>Autor Entity
<sub> * Následující atributy jsou specifická pro entity Autor. (Ty = '1') </sub>

Název    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |Entity ID                              |Int64      |Je rovno
AuN     |Normalizovaná jméno autora                 |String     |Je rovno
DAuN    |Zobrazované jméno autora                    |String     |None
CC      |Autor citace celkový počet            |Int32      |None  
ECC     |Autor citace celkový odhadovaný počet  |Int32      |None
E       |Rozšířený metadata (viz tabulka "Rozšířené atributy metadat")  |String     |None  


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
LKA.Afn     | Zobrazovaný název pro přidružení spojené s autorem  
LKA.AfId        | ID entity v umístění spojené s autorem
