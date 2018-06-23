---
title: Academic grafu entity atributy pro Academic Knowledge API | Microsoft Docs
description: Další informace o atributech entity, které můžete použít s Academic grafu v Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f771969c3c6f05e5d2c99b1fbf593ae039ccb12c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342353"
---
# <a name="entity-attributes"></a>Atributy entity

Academic grafu se skládá z 7 typy entit. Všechny entity bude mít Entity ID a typ Entity.

## <a name="common-entity-attributes"></a>Mezi běžné atributy Entity
Název    |Popis                |Typ       | Operace
------- | ------------------------- | --------- | ----------------------------
ID      |ID entity                  |Int64      |Rovná se
Ty      |Typ entity                |výčet   |Rovná se

## <a name="entity-type-enum"></a>Entita typu výčtu
Název                                                            |hodnota
----------------------------------------------------------------|-----
[Paper](PaperEntityAttributes.md)                               |0
[Author](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Konferenční řady](JournalEntityAttributes.md)                 |3
[Instance konference](ConferenceInstanceEntityAttributes.md)    |4
[Affiliation](AffiliationEntityAttributes.md)                   |5
[Obor studia](FieldsOfStudyEntityAttributes.md)                      |6

