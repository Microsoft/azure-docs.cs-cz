---
title: Přidružení atributy entit v rozhraní Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít s přidružení entit v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 177fe9da8bbe821a69eae02d89a225e5d4009331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900474"
---
# <a name="affiliation-entity"></a>Entita přidružení

<sub> * Následující atributy jsou specifická pro entity členství. (Ty = "5") </sub>

Název    |Popis                            |Typ       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
AfN     |Normalizovaný název přidružení        |Řetězec     |Rovná se
DAfN    |Zobrazovaný název přidružení       |Řetězec     |žádný
Kopie      |Citaci celkový počet přidružení           |Datový typ Int32      |žádný  
ECC     |Citaci celkový odhadovaný počet přidružení |Datový typ Int32      |žádný

## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
PC      |Počet umístění na papír