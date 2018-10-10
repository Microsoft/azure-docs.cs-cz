---
title: Atributy entit Academic Graph – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další informace o atributech entity, která vám pomůže s využitím rozhraní Academic Graph rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a203fdf6562dabb1b9d6e8ab5bb8f46ff6d5dc27
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902786"
---
# <a name="entity-attributes"></a>Atributy entit

Academic graph se skládá ze 7 typů entit. Všechny entity budou mít Entity ID a typ Entity.

## <a name="common-entity-attributes"></a>Běžné atributy entit
Název    |Popis                |Typ       | Operace
------- | ------------------------- | --------- | ----------------------------
ID      |ID entity                  |Int64      |Rovná se
Ty      |Typ entity                |Výčet   |Rovná se

## <a name="entity-type-enum"></a>Entitu typu enum
Název                                                            |hodnota
----------------------------------------------------------------|-----
[Paper](PaperEntityAttributes.md)                               |0
[Author](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Konference řady](JournalEntityAttributes.md)                 |3
[Konference Instance](ConferenceInstanceEntityAttributes.md)    |4
[Affiliation](AffiliationEntityAttributes.md)                   |5
[Pole studie](FieldsOfStudyEntityAttributes.md)                      |6

