---
title: Atributy entit Academic Graph – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další informace o atributech entity, která vám pomůže s využitím rozhraní Academic Graph rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: accc2803895f3892075cdd9877ca98344ab88bd1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884813"
---
# <a name="entity-attributes"></a>Atributy entit

Academic graph se skládá ze 7 typů entit. Všechny entity budou mít Entity ID a typ Entity.

## <a name="common-entity-attributes"></a>Běžné atributy entit
Název    |Popis                |Type       | Operace
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

