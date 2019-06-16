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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340507"
---
# <a name="affiliation-entity"></a>Entita přidružení

<sub> * Následující atributy jsou specifická pro entity členství. (Ty = "5") </sub>

Name    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |Entity ID                              |Int64      |Je rovno
AfN     |Normalizovaný název přidružení        |String     |Je rovno
DAfN    |Zobrazovaný název přidružení       |String     |None
CC      |Citaci celkový počet přidružení           |Int32      |None  
ECC     |Citaci celkový odhadovaný počet přidružení |Int32      |None

## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name    | Popis               
--------|---------------------------    
PC      |Počet umístění na papír
